# Pytanie 50: Przedstawić różnice podczas wykonywania zapytań bazo-danowych po stronie klienta oraz po stronie systemu bazo-danowego, oraz co to są procedury bazo-danowe.

## Kluczowe pojęcia

- **Zapytanie po stronie klienta** — zapytanie SQL konstruowane i wysyłane przez aplikację kliencką (np. aplikację webową, desktopową) do serwera bazy danych. Klient buduje tekst zapytania, przesyła go przez sieć, serwer parsuje, optymalizuje i wykonuje zapytanie, a następnie zwraca wynik do klienta. Każde zapytanie wymaga osobnej transmisji sieciowej i ponownej kompilacji planu wykonania (chyba że stosowane jest cache'owanie planów).
- **Zapytanie po stronie serwera** — zapytanie wykonywane bezpośrednio w silniku bazy danych, najczęściej w ramach procedury składowanej, triggera lub widoku. Zapytanie nie wymaga transmisji sieciowej (poza wywołaniem procedury), a plan wykonania może być skompilowany i zapisany w cache przy pierwszym wywołaniu, co przyspiesza kolejne wykonania.
- **Procedura składowana (stored procedure)** — nazwany, prekompilowany blok kodu SQL (i/lub języka proceduralnego, np. PL/SQL, T-SQL, PL/pgSQL) przechowywany w bazie danych. Procedura może przyjmować parametry wejściowe i wyjściowe, zawierać logikę sterującą (IF, LOOP, CURSOR), wykonywać wiele zapytań i zwracać wyniki. Jest wywoływana przez klienta za pomocą instrukcji `CALL` lub `EXEC`.
- **Trigger (wyzwalacz)** — specjalny rodzaj procedury składowanej, który jest automatycznie uruchamiany przez SZBD w odpowiedzi na określone zdarzenie na tabeli lub widoku (INSERT, UPDATE, DELETE). Triggery mogą być uruchamiane BEFORE (przed) lub AFTER (po) zdarzeniu, a także INSTEAD OF (zamiast) zdarzenia. Służą do wymuszania reguł biznesowych, audytu i automatycznej synchronizacji danych.
- **Widok (view)** — nazwane, zapisane zapytanie SELECT, które zachowuje się jak wirtualna tabela. Widok nie przechowuje danych fizycznie (z wyjątkiem widoków zmaterializowanych) — przy każdym odwołaniu zapytanie definiujące widok jest wykonywane. Widoki upraszczają złożone zapytania, zapewniają abstrakcję i mogą ograniczać dostęp do danych (bezpieczeństwo).
- **Optymalizacja zapytań** — proces, w którym optymalizator zapytań SZBD analizuje zapytanie SQL i wybiera najefektywniejszy plan wykonania (execution plan). Optymalizator uwzględnia statystyki tabel, dostępne indeksy, koszty operacji I/O i CPU. Procedury składowane korzystają z optymalizacji, ponieważ ich plany mogą być cache'owane i ponownie wykorzystywane.

## Architektura klient-serwer w bazach danych

### Model komunikacji

W architekturze klient-serwer bazy danych wyróżniamy dwa główne komponenty:

```
┌─────────────────┐         sieć (TCP/IP)        ┌─────────────────────┐
│   KLIENT (app)  │ ──── zapytanie SQL ────────→  │   SERWER BD (SZBD)  │
│                 │                                │                     │
│  - buduje SQL   │                                │  - parsuje SQL      │
│  - wysyła       │ ←──── wynik (ResultSet) ────── │  - optymalizuje     │
│  - przetwarza   │                                │  - wykonuje         │
│    wynik        │                                │  - zwraca dane      │
└─────────────────┘                                └─────────────────────┘
```

### Warstwy przetwarzania

| Warstwa | Klient | Serwer |
|---|---|---|
| Prezentacja | Wyświetlanie danych użytkownikowi | — |
| Logika aplikacji | Budowanie zapytań, walidacja danych | Procedury składowane, triggery |
| Dostęp do danych | Wysyłanie SQL przez sterownik (JDBC, ODBC) | Parsowanie, optymalizacja, wykonanie |
| Przechowywanie | — | Pliki danych, indeksy, logi |

### Protokoły komunikacji

Klient komunikuje się z serwerem BD za pomocą protokołów specyficznych dla SZBD:
- **MySQL** — MySQL Protocol (port 3306)
- **PostgreSQL** — Frontend/Backend Protocol (port 5432)
- **SQL Server** — TDS (Tabular Data Stream, port 1433)
- **Oracle** — Oracle Net / TNS (port 1521)

Sterowniki (JDBC, ODBC, ADO.NET) abstrakcją te protokoły, udostępniając jednolity interfejs programistyczny.

## Przetwarzanie zapytań — klient vs serwer

### Zapytanie po stronie klienta

Typowy przebieg wykonania zapytania z poziomu klienta:

1. **Aplikacja buduje zapytanie SQL** — jako tekst (string), często z dynamicznie wstawianymi parametrami
2. **Wysłanie przez sieć** — zapytanie jest przesyłane do serwera BD (koszt sieciowy)
3. **Parsowanie (parsing)** — serwer analizuje składnię SQL
4. **Optymalizacja** — optymalizator tworzy plan wykonania
5. **Wykonanie** — silnik wykonuje plan (odczyty z dysku, operacje na indeksach)
6. **Zwrot wyniku** — cały zbiór wynikowy jest przesyłany do klienta przez sieć
7. **Przetwarzanie po stronie klienta** — aplikacja iteruje po wynikach, filtruje, formatuje

**Problemy zapytań klienckich:**
- Każde zapytanie wymaga transmisji sieciowej (latencja)
- Powtarzane zapytania wymagają ponownego parsowania i optymalizacji (chyba że SZBD cache'uje plany)
- Ryzyko SQL injection przy dynamicznym budowaniu zapytań
- Duże zbiory wynikowe obciążają sieć
- Logika biznesowa rozproszona między klientem a bazą

### Zapytanie po stronie serwera (procedura składowana)

Typowy przebieg wykonania procedury składowanej:

1. **Aplikacja wywołuje procedurę** — `CALL nazwa_procedury(parametry)` — przesyłana jest tylko nazwa i parametry
2. **Serwer lokalizuje procedurę** — w katalogu systemowym
3. **Wykorzystanie skompilowanego planu** — plan wykonania jest cache'owany po pierwszej kompilacji
4. **Wykonanie wielu zapytań** — procedura może wykonać wiele operacji SQL bez komunikacji sieciowej
5. **Zwrot wyniku** — tylko końcowy wynik jest przesyłany do klienta

**Zalety zapytań serwerowych:**
- Minimalna transmisja sieciowa (tylko wywołanie + wynik)
- Prekompilowany plan wykonania (szybsze kolejne wywołania)
- Logika biznesowa blisko danych (brak przesyłania pośrednich wyników)
- Lepsza kontrola bezpieczeństwa (klient nie musi znać struktury tabel)

### Porównanie wydajności

| Aspekt | Zapytanie klienckie | Procedura składowana |
|---|---|---|
| Ruch sieciowy | Duży (pełne SQL + wyniki pośrednie) | Mały (nazwa + parametry + wynik końcowy) |
| Parsowanie SQL | Przy każdym wywołaniu | Raz (przy kompilacji) |
| Optymalizacja | Przy każdym wywołaniu* | Raz (plan cache'owany) |
| Bezpieczeństwo | Ryzyko SQL injection | Parametryzacja wbudowana |
| Elastyczność | Wysoka (dynamiczne SQL) | Niższa (zmiana wymaga ALTER PROCEDURE) |
| Debugowanie | Łatwiejsze (w kodzie aplikacji) | Trudniejsze (wymaga narzędzi SZBD) |
| Przenośność | Wyższa (standardowy SQL) | Niższa (składnia zależna od SZBD) |

*\* Nowoczesne SZBD cache'ują plany również dla zapytań ad-hoc (prepared statements).*

## Procedury składowane

### Definicja i cel

Procedura składowana to nazwany program przechowywany w bazie danych, napisany w rozszerzeniu proceduralnym SQL (PL/SQL w Oracle, T-SQL w SQL Server, PL/pgSQL w PostgreSQL). Procedury:
- Enkapsulują logikę biznesową na poziomie bazy danych
- Redukują ruch sieciowy
- Zapewniają spójny interfejs dostępu do danych
- Umożliwiają kontrolę uprawnień (GRANT EXECUTE)

### Składnia (przykład PostgreSQL / PL/pgSQL)

```sql
CREATE OR REPLACE PROCEDURE przenies_srodki(
    p_z_konta   INT,
    p_na_konto  INT,
    p_kwota     DECIMAL(10,2)
)
LANGUAGE plpgsql
AS $$
DECLARE
    v_saldo DECIMAL(10,2);
BEGIN
    -- Sprawdzenie salda konta źródłowego
    SELECT saldo INTO v_saldo
    FROM konta
    WHERE id_konta = p_z_konta
    FOR UPDATE;  -- blokada wiersza

    IF v_saldo < p_kwota THEN
        RAISE EXCEPTION 'Niewystarczające środki: saldo = %, kwota = %',
                         v_saldo, p_kwota;
    END IF;

    -- Obciążenie konta źródłowego
    UPDATE konta
    SET saldo = saldo - p_kwota
    WHERE id_konta = p_z_konta;

    -- Zasilenie konta docelowego
    UPDATE konta
    SET saldo = saldo + p_kwota
    WHERE id_konta = p_na_konto;

    COMMIT;
END;
$$;
```

Wywołanie:

```sql
CALL przenies_srodki(1001, 1002, 500.00);
```

### Funkcje składowane vs procedury

| Cecha | Procedura (PROCEDURE) | Funkcja (FUNCTION) |
|---|---|---|
| Zwracanie wartości | Przez parametry OUT lub zbiór wynikowy | Zwraca wartość (RETURN) |
| Użycie w SELECT | Nie | Tak (`SELECT moja_funkcja(...)`) |
| Transakcje | Może zarządzać (COMMIT/ROLLBACK) | Zwykle nie (zależy od SZBD) |
| Wywołanie | `CALL procedura(...)` | `SELECT funkcja(...)` |

### Zalety procedur składowanych

1. **Wydajność** — prekompilowany plan wykonania, brak wielokrotnego parsowania
2. **Bezpieczeństwo** — klient nie potrzebuje bezpośredniego dostępu do tabel (tylko EXECUTE na procedurze)
3. **Redukcja ruchu sieciowego** — przesyłane są tylko parametry i wynik końcowy
4. **Spójność** — logika biznesowa w jednym miejscu, współdzielona przez wiele aplikacji
5. **Atomowość** — procedura może zawierać transakcję (BEGIN...COMMIT/ROLLBACK)

### Wady procedur składowanych

1. **Przenośność** — składnia PL/SQL ≠ T-SQL ≠ PL/pgSQL (vendor lock-in)
2. **Debugowanie** — trudniejsze niż w kodzie aplikacji (ograniczone narzędzia)
3. **Wersjonowanie** — trudniejsze zarządzanie wersjami niż w kodzie aplikacji (brak natywnej integracji z Git)
4. **Skalowalność** — obciążenie serwera BD logiką biznesową (serwer BD jest trudniejszy do skalowania horyzontalnego niż serwer aplikacji)
5. **Testowanie** — trudniejsze pisanie testów jednostkowych dla procedur

## Triggery (wyzwalacze)

### Definicja i typy

Trigger to procedura składowana uruchamiana automatycznie w odpowiedzi na zdarzenie DML (INSERT, UPDATE, DELETE) lub DDL (CREATE, ALTER, DROP).

**Typy triggerów:**

| Typ | Moment uruchomienia | Zastosowanie |
|---|---|---|
| BEFORE | Przed operacją DML | Walidacja, modyfikacja danych wejściowych |
| AFTER | Po operacji DML | Audyt, synchronizacja, logowanie |
| INSTEAD OF | Zamiast operacji (na widokach) | Aktualizowalne widoki |

**Granularność:**
- **Row-level (FOR EACH ROW)** — uruchamiany dla każdego wiersza dotkniętego operacją
- **Statement-level (FOR EACH STATEMENT)** — uruchamiany raz na całą instrukcję

### Przykład triggera (PostgreSQL)

```sql
-- Trigger audytowy: logowanie zmian w tabeli pracownicy
CREATE OR REPLACE FUNCTION log_zmiana_pracownika()
RETURNS TRIGGER
LANGUAGE plpgsql
AS $$
BEGIN
    INSERT INTO audit_pracownicy(
        id_pracownika, operacja, stare_dane, nowe_dane, data_zmiany
    ) VALUES (
        COALESCE(NEW.id, OLD.id),
        TG_OP,                          -- 'INSERT', 'UPDATE' lub 'DELETE'
        row_to_json(OLD)::TEXT,
        row_to_json(NEW)::TEXT,
        NOW()
    );
    RETURN NEW;
END;
$$;

CREATE TRIGGER trg_audit_pracownicy
AFTER INSERT OR UPDATE OR DELETE ON pracownicy
FOR EACH ROW
EXECUTE FUNCTION log_zmiana_pracownika();
```

### Zalety i wady triggerów

**Zalety:**
- Automatyczne wymuszanie reguł biznesowych
- Audyt i logowanie bez zmian w kodzie aplikacji
- Spójność danych niezależna od aplikacji klienckiej

**Wady:**
- Ukryta logika (trudna do śledzenia — „magiczne" zachowanie)
- Kaskadowe triggery mogą powodować trudne do debugowania problemy
- Wpływ na wydajność operacji DML

## Widoki (views)

### Definicja i rodzaje

Widok to nazwane zapytanie SELECT zapisane w bazie danych, które zachowuje się jak wirtualna tabela.

```sql
-- Widok: aktywni klienci z łączną wartością zamówień
CREATE VIEW v_aktywni_klienci AS
SELECT
    k.id_klienta,
    k.imie,
    k.nazwisko,
    COUNT(z.id_zamowienia) AS liczba_zamowien,
    SUM(z.wartosc) AS laczna_wartosc
FROM klienci k
JOIN zamowienia z ON k.id_klienta = z.id_klienta
WHERE z.data_zamowienia >= CURRENT_DATE - INTERVAL '1 year'
GROUP BY k.id_klienta, k.imie, k.nazwisko;
```

Użycie:

```sql
SELECT * FROM v_aktywni_klienci WHERE laczna_wartosc > 10000;
```

**Rodzaje widoków:**

| Rodzaj | Opis |
|---|---|
| Zwykły (view) | Zapytanie wykonywane przy każdym odwołaniu |
| Zmaterializowany (materialized view) | Wynik zapisany fizycznie, odświeżany okresowo |
| Aktualizowalny (updatable view) | Pozwala na INSERT/UPDATE/DELETE (proste widoki) |

### Zastosowania widoków

1. **Uproszczenie zapytań** — złożone JOIN-y ukryte za prostym interfejsem
2. **Bezpieczeństwo** — ograniczenie widocznych kolumn/wierszy (np. ukrycie danych wrażliwych)
3. **Abstrakcja** — zmiana struktury tabel bez wpływu na aplikacje korzystające z widoków
4. **Wydajność** — widoki zmaterializowane przyspieszają złożone zapytania analityczne

## Przykłady

### Przykład 1: Procedura składowana w SQL (T-SQL / SQL Server)

```sql
-- Procedura: wyszukiwanie zamówień klienta z filtrowaniem
CREATE PROCEDURE dbo.PobierzZamowieniaKlienta
    @id_klienta   INT,
    @data_od      DATE = NULL,
    @data_do      DATE = NULL
AS
BEGIN
    SET NOCOUNT ON;

    SELECT
        z.id_zamowienia,
        z.data_zamowienia,
        z.wartosc,
        z.status
    FROM zamowienia z
    WHERE z.id_klienta = @id_klienta
      AND (@data_od IS NULL OR z.data_zamowienia >= @data_od)
      AND (@data_do IS NULL OR z.data_zamowienia <= @data_do)
    ORDER BY z.data_zamowienia DESC;
END;
```

Wywołanie:

```sql
EXEC dbo.PobierzZamowieniaKlienta @id_klienta = 42, @data_od = '2024-01-01';
```

### Przykład 2: Porównanie zapytania z klienta vs procedury składowanej

**Scenariusz:** Aplikacja musi pobrać raport sprzedaży — łączną wartość zamówień per kategoria produktu za ostatni miesiąc.

**Podejście 1 — zapytania z klienta (pseudokod aplikacji):**

```python
# Klient wysyła 3 osobne zapytania przez sieć

# Zapytanie 1: pobranie kategorii
kategorie = db.query("SELECT id, nazwa FROM kategorie")          # → sieć

# Zapytanie 2: pobranie zamówień z ostatniego miesiąca
zamowienia = db.query("""
    SELECT p.id_kategorii, SUM(pz.cena * pz.ilosc) AS wartosc
    FROM pozycje_zamowien pz
    JOIN produkty p ON pz.id_produktu = p.id_produktu
    JOIN zamowienia z ON pz.id_zamowienia = z.id_zamowienia
    WHERE z.data_zamowienia >= CURRENT_DATE - INTERVAL '1 month'
    GROUP BY p.id_kategorii
""")                                                              # → sieć

# Zapytanie 3: złączenie po stronie klienta (w pamięci aplikacji)
raport = []
for kat in kategorie:
    wartosc = next((z['wartosc'] for z in zamowienia
                     if z['id_kategorii'] == kat['id']), 0)
    raport.append({'kategoria': kat['nazwa'], 'wartosc': wartosc})
```

**Podejście 2 — procedura składowana:**

```sql
CREATE PROCEDURE raport_sprzedazy_miesiac()
LANGUAGE plpgsql
AS $$
BEGIN
    -- Jedno wywołanie, cała logika po stronie serwera
    RETURN QUERY
    SELECT
        k.nazwa AS kategoria,
        COALESCE(SUM(pz.cena * pz.ilosc), 0) AS wartosc
    FROM kategorie k
    LEFT JOIN produkty p ON k.id = p.id_kategorii
    LEFT JOIN pozycje_zamowien pz ON p.id_produktu = pz.id_produktu
    LEFT JOIN zamowienia z ON pz.id_zamowienia = z.id_zamowienia
        AND z.data_zamowienia >= CURRENT_DATE - INTERVAL '1 month'
    GROUP BY k.nazwa
    ORDER BY wartosc DESC;
END;
$$;
```

Wywołanie z klienta:

```python
raport = db.query("CALL raport_sprzedazy_miesiac()")  # → 1 transmisja sieciowa
```

**Porównanie:**

| Aspekt | Podejście klienckie | Procedura składowana |
|---|---|---|
| Transmisje sieciowe | 2+ (osobne zapytania) | 1 (wywołanie procedury) |
| Przetwarzanie danych | Częściowo po stronie klienta | Całkowicie po stronie serwera |
| Dane przesyłane | Pośrednie zbiory wynikowe | Tylko wynik końcowy |
| Optymalizacja | Osobna dla każdego zapytania | Jeden zoptymalizowany plan |
| Utrzymanie kodu | Logika w kodzie aplikacji | Logika w bazie danych |

## Podsumowanie

1. **Architektura klient-serwer BD** rozdziela odpowiedzialności: klient buduje i wysyła zapytania, serwer parsuje, optymalizuje i wykonuje je. Komunikacja odbywa się przez sieć za pomocą protokołów specyficznych dla SZBD.

2. **Zapytania klienckie** są elastyczne i łatwe do debugowania, ale generują większy ruch sieciowy, wymagają ponownego parsowania i są podatne na SQL injection. **Zapytania serwerowe** (procedury składowane) minimalizują ruch sieciowy, korzystają z prekompilowanych planów i zapewniają lepsze bezpieczeństwo.

3. **Procedury składowane** to nazwane, prekompilowane bloki kodu SQL/proceduralnego przechowywane w bazie. Enkapsulują logikę biznesową, redukują ruch sieciowy i zapewniają spójny interfejs. Ich wadą jest zależność od konkretnego SZBD i trudniejsze debugowanie.

4. **Triggery** automatycznie reagują na zdarzenia DML (INSERT/UPDATE/DELETE), wymuszając reguły biznesowe i audyt bez zmian w kodzie aplikacji. Należy ich używać ostrożnie ze względu na ukrytą logikę i potencjalny wpływ na wydajność.

5. **Widoki** upraszczają złożone zapytania, zapewniają abstrakcję i bezpieczeństwo. Widoki zmaterializowane dodatkowo przyspieszają zapytania analityczne kosztem aktualności danych.

## Powiązane pytania

- [Pytanie 49: Scharakteryzować strukturę relacyjnych baz danych](49-relacyjne-bazy-danych.md)
- [Pytanie 31: Programowanie po stronie serwera i klienta](31-programowanie-serwer-klient.md)
