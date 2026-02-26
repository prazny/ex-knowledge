# Pytanie 42: Proszę podać zalety i wady (listy liniowej | tablicy | hasza) zastosowanej(go) do realizacji zbioru, w którym chcemy implementować operacje member, insert, delete.

## Kluczowe pojęcia

- **Lista liniowa (linked list)** — dynamiczna struktura danych, w której elementy (węzły) są połączone wskaźnikami. Każdy węzeł zawiera wartość oraz wskaźnik na następny element (lista jednokierunkowa) lub na następny i poprzedni (lista dwukierunkowa). Lista nie wymaga ciągłego bloku pamięci — węzły mogą być rozrzucone w pamięci. Wstawianie i usuwanie na początku listy jest operacją $O(1)$, ale wyszukiwanie wymaga przejścia sekwencyjnego $O(n)$.
- **Tablica (array)** — statyczna (lub dynamiczna) struktura danych przechowująca elementy w ciągłym bloku pamięci, indeksowana liczbami całkowitymi. Dostęp do elementu po indeksie jest operacją $O(1)$ dzięki arytmetyce adresów. Wstawianie i usuwanie w środku tablicy wymaga przesunięcia elementów — $O(n)$. Tablica posortowana umożliwia wyszukiwanie binarne w $O(\log n)$.
- **Tablica haszująca (hash table)** — struktura danych realizująca słownik (zbiór par klucz-wartość) za pomocą funkcji haszującej $h: U \to \{0, 1, \ldots, m-1\}$, która mapuje klucze z uniwersum $U$ na indeksy tablicy o rozmiarze $m$. Średni czas operacji member, insert, delete wynosi $O(1)$ przy dobrze dobranej funkcji haszującej i niskim współczynniku zapełnienia.
- **Złożoność operacji** — miara kosztu czasowego wykonania operacji na strukturze danych. Rozróżniamy złożoność pesymistyczną (najgorszy przypadek), średnią (oczekiwaną) i optymistyczną (najlepszy przypadek). Dla implementacji zbioru kluczowe są trzy operacje: member (wyszukiwanie), insert (wstawianie), delete (usuwanie).
- **Kolizja (collision)** — sytuacja, w której dwa różne klucze $k_1 \neq k_2$ mają tę samą wartość funkcji haszującej: $h(k_1) = h(k_2)$. Kolizje są nieuniknione, gdy $|U| > m$ (zasada szufladkowa Dirichleta). Metody rozwiązywania kolizji to łańcuchowanie (chaining) i adresowanie otwarte (open addressing).
- **Funkcja haszująca (hash function)** — funkcja $h: U \to \{0, 1, \ldots, m-1\}$ przekształcająca klucz w indeks tablicy. Dobra funkcja haszująca powinna: (1) być szybka do obliczenia — $O(1)$, (2) równomiernie rozpraszać klucze po tablicy (minimalizować kolizje), (3) być deterministyczna. Przykłady: metoda dzielenia $h(k) = k \bmod m$, metoda mnożenia $h(k) = \lfloor m \cdot (k \cdot A \bmod 1) \rfloor$ dla stałej $0 < A < 1$.

## Operacje na zbiorze — definicje

Rozważamy zbiór $S$ przechowujący $n$ elementów, na którym chcemy realizować trzy podstawowe operacje:

1. **member(x, S)** — sprawdza, czy element $x$ należy do zbioru $S$. Zwraca `true` jeśli $x \in S$, `false` w przeciwnym przypadku.
2. **insert(x, S)** — wstawia element $x$ do zbioru $S$. Po operacji: $S' = S \cup \{x\}$. Jeśli $x \in S$, zbiór nie zmienia się (elementy zbioru są unikalne).
3. **delete(x, S)** — usuwa element $x$ ze zbioru $S$. Po operacji: $S' = S \setminus \{x\}$. Jeśli $x \notin S$, zbiór nie zmienia się.

## Analiza struktury 1: Lista liniowa

### Opis implementacji

Zbiór jest przechowywany jako lista jednokierunkowa (lub dwukierunkowa) z węzłami zawierającymi elementy zbioru. Lista może być **nieposortowana** lub **posortowana**.

```
Struktura węzła listy:
┌───────────┬──────────┐
│  wartość   │  next ──→│ (wskaźnik na następny węzeł)
└───────────┴──────────┘

Lista nieposortowana (zbiór {5, 2, 8, 1}):
HEAD → [5|→] → [2|→] → [8|→] → [1|∅]

Lista posortowana (zbiór {1, 2, 5, 8}):
HEAD → [1|→] → [2|→] → [5|→] → [8|∅]
```

### Operacje na liście nieposortowanej

**member(x, S)** — przeszukiwanie sekwencyjne od HEAD do końca listy:

```
ALGORYTM Member_Lista(x, HEAD)
  p ← HEAD
  DOPÓKI p ≠ NULL:
    JEŚLI p.wartość = x:
      ZWRÓĆ true
    p ← p.next
  ZWRÓĆ false
```

- Pesymistyczna: $O(n)$ — element na końcu lub nieobecny
- Średnia: $O(n)$ — średnio przeszukujemy $n/2$ elementów
- Optymistyczna: $O(1)$ — element jest pierwszy

**insert(x, S)** — najpierw member(x), potem wstawienie na początek:

```
ALGORYTM Insert_Lista(x, HEAD)
  JEŚLI Member_Lista(x, HEAD) = true:
    ZWRÓĆ HEAD                    // element już istnieje
  nowy ← utwórz węzeł(x)
  nowy.next ← HEAD
  HEAD ← nowy
  ZWRÓĆ HEAD
```

- Pesymistyczna: $O(n)$ — wymaga sprawdzenia member ($O(n)$) + wstawienie ($O(1)$)
- Średnia: $O(n)$
- Optymistyczna: $O(1)$ — jeśli pomijamy sprawdzenie duplikatów (np. wiemy, że element jest nowy)

**delete(x, S)** — wyszukanie elementu i usunięcie węzła:

```
ALGORYTM Delete_Lista(x, HEAD)
  JEŚLI HEAD = NULL:
    ZWRÓĆ HEAD
  JEŚLI HEAD.wartość = x:
    ZWRÓĆ HEAD.next              // usunięcie pierwszego elementu
  p ← HEAD
  DOPÓKI p.next ≠ NULL:
    JEŚLI p.next.wartość = x:
      p.next ← p.next.next      // pominięcie usuwanego węzła
      ZWRÓĆ HEAD
    p ← p.next
  ZWRÓĆ HEAD                     // element nie znaleziony
```

- Pesymistyczna: $O(n)$ — element na końcu lub nieobecny
- Średnia: $O(n)$
- Optymistyczna: $O(1)$ — element jest pierwszy

### Operacje na liście posortowanej

Sortowanie listy pozwala na wcześniejsze zakończenie wyszukiwania (gdy napotkamy element większy od szukanego), ale nie zmienia asymptotycznej złożoności:

- **member(x)**: $O(n)$ pesymistycznie, $O(n/2)$ średnio (poprawa o stały czynnik)
- **insert(x)**: $O(n)$ — trzeba znaleźć właściwe miejsce wstawienia
- **delete(x)**: $O(n)$ — trzeba znaleźć element

**Uwaga:** W liście nie można zastosować wyszukiwania binarnego, ponieważ nie ma dostępu swobodnego do elementów (brak indeksowania).

### Zalety listy liniowej

1. **Dynamiczny rozmiar** — lista rośnie i kurczy się w miarę potrzeb, nie wymaga z góry określonego rozmiaru
2. **Efektywne wstawianie/usuwanie na początku** — $O(1)$ bez przesuwania elementów
3. **Brak marnowania pamięci** — zajmuje dokładnie tyle pamięci, ile potrzeba (plus narzut wskaźników)
4. **Prostota implementacji** — łatwa do zaimplementowania i zrozumienia

### Wady listy liniowej

1. **Wolne wyszukiwanie** — $O(n)$ w każdym przypadku (brak dostępu swobodnego)
2. **Narzut pamięciowy** — każdy węzeł przechowuje dodatkowy wskaźnik (8 bajtów na 64-bitowej architekturze)
3. **Słaba lokalność pamięci (cache locality)** — węzły rozrzucone w pamięci powodują częste cache miss
4. **Brak wyszukiwania binarnego** — nawet posortowana lista wymaga przeszukiwania sekwencyjnego

## Analiza struktury 2: Tablica

### Opis implementacji

Zbiór jest przechowywany w tablicy o rozmiarze $n$ (lub z zapasem). Tablica może być **nieposortowana** lub **posortowana**.

```
Tablica nieposortowana (zbiór {5, 2, 8, 1}):
┌─────┬─────┬─────┬─────┬─────┬─────┐
│  5  │  2  │  8  │  1  │     │     │
├─────┼─────┼─────┼─────┼─────┼─────┤
│ [0] │ [1] │ [2] │ [3] │ [4] │ [5] │
└─────┴─────┴─────┴─────┴─────┴─────┘
  n = 4, rozmiar tablicy = 6

Tablica posortowana (zbiór {1, 2, 5, 8}):
┌─────┬─────┬─────┬─────┬─────┬─────┐
│  1  │  2  │  5  │  8  │     │     │
├─────┼─────┼─────┼─────┼─────┼─────┤
│ [0] │ [1] │ [2] │ [3] │ [4] │ [5] │
└─────┴─────┴─────┴─────┴─────┴─────┘
```

### Operacje na tablicy nieposortowanej

**member(x, A, n)** — przeszukiwanie liniowe:

- Pesymistyczna: $O(n)$
- Średnia: $O(n)$
- Optymistyczna: $O(1)$

**insert(x, A, n)** — wstawienie na koniec (po sprawdzeniu member):

- Pesymistyczna: $O(n)$ — sprawdzenie member + wstawienie $O(1)$
- Bez sprawdzenia duplikatów: $O(1)$

**delete(x, A, n)** — znalezienie elementu i zastąpienie ostatnim:

```
ALGORYTM Delete_Tablica_Nieposortowana(x, A, n)
  DLA i ← 0 DO n-1:
    JEŚLI A[i] = x:
      A[i] ← A[n-1]           // zastąp ostatnim elementem
      n ← n - 1
      ZWRÓĆ
```

- Pesymistyczna: $O(n)$ — wyszukanie elementu
- Średnia: $O(n)$
- Optymistyczna: $O(1)$ — element na pierwszej pozycji

### Operacje na tablicy posortowanej

**member(x, A, n)** — wyszukiwanie binarne:

```
ALGORYTM Member_Tablica_Posortowana(x, A, n)
  lo ← 0
  hi ← n - 1
  DOPÓKI lo ≤ hi:
    mid ← ⌊(lo + hi) / 2⌋
    JEŚLI A[mid] = x:
      ZWRÓĆ true
    JEŚLI A[mid] < x:
      lo ← mid + 1
    W PRZECIWNYM RAZIE:
      hi ← mid - 1
  ZWRÓĆ false
```

- Pesymistyczna: $O(\log n)$
- Średnia: $O(\log n)$
- Optymistyczna: $O(1)$ — element w środku tablicy

**insert(x, A, n)** — znalezienie pozycji (binarnie) + przesunięcie elementów:

- Pesymistyczna: $O(n)$ — przesunięcie do $n$ elementów
- Średnia: $O(n)$ — średnio przesuwamy $n/2$ elementów
- Optymistyczna: $O(\log n)$ — wstawienie na koniec (element największy)

**delete(x, A, n)** — znalezienie elementu (binarnie) + przesunięcie:

- Pesymistyczna: $O(n)$ — przesunięcie elementów po usunięciu
- Średnia: $O(n)$
- Optymistyczna: $O(\log n)$ — usunięcie ostatniego elementu

### Zalety tablicy

1. **Szybkie wyszukiwanie w tablicy posortowanej** — $O(\log n)$ dzięki wyszukiwaniu binarnemu
2. **Doskonała lokalność pamięci** — elementy w ciągłym bloku pamięci, efektywne wykorzystanie cache
3. **Brak narzutu wskaźników** — nie przechowuje dodatkowych wskaźników
4. **Dostęp swobodny** — $O(1)$ dostęp do elementu po indeksie

### Wady tablicy

1. **Stały rozmiar** — wymaga z góry określonego rozmiaru (lub kosztownej realokacji w tablicy dynamicznej)
2. **Kosztowne wstawianie i usuwanie** — $O(n)$ z powodu przesuwania elementów (tablica posortowana)
3. **Marnowanie pamięci** — jeśli tablica jest znacznie większa niż liczba elementów
4. **Realokacja** — tablica dynamiczna wymaga kopiowania przy przekroczeniu pojemności — $O(n)$ amortyzowane $O(1)$

## Analiza struktury 3: Tablica haszująca

### Opis implementacji

Zbiór jest przechowywany w tablicy $T[0..m-1]$, gdzie pozycja elementu $x$ jest wyznaczana przez funkcję haszującą $h(x)$. Rozmiar tablicy $m$ jest zwykle dobierany jako liczba pierwsza lub potęga dwójki.

**Współczynnik zapełnienia (load factor):**

$\alpha = \frac{n}{m}$

gdzie $n$ to liczba elementów w zbiorze, $m$ to rozmiar tablicy. Dla efektywnego działania utrzymujemy $\alpha < 1$ (adresowanie otwarte) lub $\alpha \approx 1$ (łańcuchowanie).

### Funkcje haszujące

**Metoda dzielenia:**

$h(k) = k \bmod m$

Prosta, ale wrażliwa na wybór $m$. Najlepiej, gdy $m$ jest liczbą pierwszą daleką od potęg dwójki.

**Metoda mnożenia:**

$h(k) = \lfloor m \cdot (k \cdot A \bmod 1) \rfloor$

gdzie $0 < A < 1$. Knuth zaleca $A \approx (\sqrt{5} - 1)/2 \approx 0{,}6180$.

**Haszowanie uniwersalne** — losowy wybór funkcji haszującej z rodziny $\mathcal{H}$, co gwarantuje, że dla dowolnych dwóch kluczy $k_1 \neq k_2$:

$\Pr_{h \in \mathcal{H}}[h(k_1) = h(k_2)] \leq \frac{1}{m}$

### Rozwiązywanie kolizji: Łańcuchowanie (chaining)

Każda komórka tablicy $T[i]$ zawiera listę (łańcuch) wszystkich elementów, dla których $h(x) = i$.

```
Tablica haszująca z łańcuchowaniem (m = 7):
Zbiór: {5, 12, 15, 2, 19, 8}
Funkcja: h(k) = k mod 7

T[0]: → [∅]
T[1]: → [15] → [8] → [∅]       h(15)=1, h(8)=1  ← KOLIZJA
T[2]: → [2] → [∅]               h(2)=2
T[3]: → [∅]
T[4]: → [∅]
T[5]: → [5] → [12] → [19] → [∅]  h(5)=5, h(12)=5, h(19)=5  ← KOLIZJE
T[6]: → [∅]
```

**Operacje z łańcuchowaniem:**

```
ALGORYTM Member_Hash_Chain(x, T, m)
  i ← h(x)
  Przeszukaj listę T[i] w poszukiwaniu x
  ZWRÓĆ true jeśli znaleziono, false w przeciwnym razie

ALGORYTM Insert_Hash_Chain(x, T, m)
  i ← h(x)
  JEŚLI x nie występuje w liście T[i]:
    Wstaw x na początek listy T[i]

ALGORYTM Delete_Hash_Chain(x, T, m)
  i ← h(x)
  Usuń x z listy T[i] (jeśli istnieje)
```

**Złożoność z łańcuchowaniem:**

Przy założeniu prostego równomiernego haszowania (simple uniform hashing), gdzie każdy klucz z równym prawdopodobieństwem trafia do dowolnej komórki:

- **member (wyszukiwanie nieudane):** $\Theta(1 + \alpha)$ średnio — obliczenie $h(x)$ w $O(1)$ + przejście listy o średniej długości $\alpha$
- **member (wyszukiwanie udane):** $\Theta(1 + \alpha/2)$ średnio
- **insert:** $O(1)$ (wstawienie na początek listy) + koszt member jeśli sprawdzamy duplikaty
- **delete:** $\Theta(1 + \alpha)$ średnio — wymaga znalezienia elementu

Gdy $\alpha = O(1)$ (tj. $m = \Theta(n)$), wszystkie operacje działają w **oczekiwanym czasie $O(1)$**.

**Pesymistycznie:** $O(n)$ — gdy wszystkie elementy trafią do jednej komórki (degeneracja do listy).

### Rozwiązywanie kolizji: Adresowanie otwarte (open addressing)

Wszystkie elementy są przechowywane bezpośrednio w tablicy (bez list). Gdy komórka $h(x)$ jest zajęta, próbujemy kolejnych komórek według sekwencji próbkowania.

**Sekwencja próbkowania** $h(k, 0), h(k, 1), h(k, 2), \ldots$ — permutacja $\{0, 1, \ldots, m-1\}$.

**Metody próbkowania:**

1. **Próbkowanie liniowe (linear probing):**

$h(k, i) = (h'(k) + i) \bmod m$

Prosta, ale podatna na **klasteryzację pierwotną** — grupy zajętych komórek rosną.

2. **Próbkowanie kwadratowe (quadratic probing):**

$h(k, i) = (h'(k) + c_1 \cdot i + c_2 \cdot i^2) \bmod m$

Redukuje klasteryzację pierwotną, ale podatna na **klasteryzację wtórną**.

3. **Podwójne haszowanie (double hashing):**

$h(k, i) = (h_1(k) + i \cdot h_2(k)) \bmod m$

Najlepsza metoda — zbliżona do idealnego równomiernego haszowania. Wymaga, aby $h_2(k)$ było względnie pierwsze z $m$.

```
Adresowanie otwarte — próbkowanie liniowe (m = 7):
Wstawianie: 5, 12, 15, 2, 19

h(k) = k mod 7

Wstaw 5:  h(5)=5   → T[5]=5
Wstaw 12: h(12)=5  → T[5] zajęte → T[6]=12     (kolizja, przeskok)
Wstaw 15: h(15)=1  → T[1]=15
Wstaw 2:  h(2)=2   → T[2]=2
Wstaw 19: h(19)=5  → T[5] zajęte → T[6] zajęte → T[0]=19

Stan tablicy:
┌──────┬──────┬──────┬──────┬──────┬──────┬──────┐
│  19  │  15  │   2  │      │      │   5  │  12  │
├──────┼──────┼──────┼──────┼──────┼──────┼──────┤
│ [0]  │ [1]  │ [2]  │ [3]  │ [4]  │ [5]  │ [6]  │
└──────┴──────┴──────┴──────┴──────┴──────┴──────┘
```

**Operacje z adresowaniem otwartym:**

```
ALGORYTM Member_Hash_Open(x, T, m)
  i ← 0
  POWTARZAJ:
    j ← h(x, i)
    JEŚLI T[j] = x:
      ZWRÓĆ true
    JEŚLI T[j] = PUSTE:
      ZWRÓĆ false
    i ← i + 1
  DOPÓKI i < m
  ZWRÓĆ false

ALGORYTM Insert_Hash_Open(x, T, m)
  i ← 0
  POWTARZAJ:
    j ← h(x, i)
    JEŚLI T[j] = PUSTE lub T[j] = USUNIĘTE:
      T[j] ← x
      ZWRÓĆ j
    i ← i + 1
  DOPÓKI i < m
  BŁĄD "Tablica pełna"

ALGORYTM Delete_Hash_Open(x, T, m)
  // Nie można po prostu ustawić T[j] = PUSTE
  // (przerwałoby sekwencję próbkowania)
  // Zamiast tego oznaczamy jako USUNIĘTE (lazy deletion)
  Znajdź pozycję j elementu x
  T[j] ← USUNIĘTE
```

**Złożoność z adresowaniem otwartym (równomierne haszowanie):**

- **Wyszukiwanie nieudane:** $\frac{1}{1 - \alpha}$ prób średnio
- **Wyszukiwanie udane:** $\frac{1}{\alpha} \ln \frac{1}{1 - \alpha}$ prób średnio

Dla $\alpha = 0{,}5$: średnio 2 próby (nieudane), 1,39 próby (udane).
Dla $\alpha = 0{,}75$: średnio 4 próby (nieudane), 1,85 próby (udane).
Dla $\alpha = 0{,}9$: średnio 10 prób (nieudane), 2,56 próby (udane).

### Zalety tablicy haszującej

1. **Najszybsze operacje średnio** — $O(1)$ oczekiwany czas dla member, insert, delete
2. **Skalowalność** — wydajność nie zależy od $n$, o ile $\alpha$ jest kontrolowany
3. **Uniwersalność** — działa dla dowolnych typów kluczy (wystarczy zdefiniować funkcję haszującą)
4. **Dynamiczne tablice haszujące** — rehashing pozwala na automatyczne powiększanie tablicy

### Wady tablicy haszującej

1. **Pesymistyczny przypadek $O(n)$** — degeneracja przy złej funkcji haszującej lub wielu kolizjach
2. **Brak porządku** — elementy nie są posortowane, nie można efektywnie iterować w porządku
3. **Narzut pamięciowy** — wymaga tablicy większej niż $n$ ($m > n$), wskaźniki w łańcuchach
4. **Złożoność implementacji** — wymaga doboru funkcji haszującej, strategii rozwiązywania kolizji, rehashingu
5. **Rehashing** — powiększenie tablicy wymaga ponownego haszowania wszystkich elementów — $O(n)$
6. **Wrażliwość na funkcję haszującą** — zła funkcja może drastycznie pogorszyć wydajność

## Przykłady

### Przykład 1: Tabela porównawcza złożoności

| Struktura | Operacja | Pesymistyczna | Średnia | Optymistyczna |
|---|---|---|---|---|
| **Lista nieposortowana** | member | $O(n)$ | $O(n)$ | $O(1)$ |
| | insert | $O(n)$* | $O(n)$* | $O(1)$** |
| | delete | $O(n)$ | $O(n)$ | $O(1)$ |
| **Lista posortowana** | member | $O(n)$ | $O(n)$ | $O(1)$ |
| | insert | $O(n)$ | $O(n)$ | $O(1)$ |
| | delete | $O(n)$ | $O(n)$ | $O(1)$ |
| **Tablica nieposortowana** | member | $O(n)$ | $O(n)$ | $O(1)$ |
| | insert | $O(n)$* | $O(n)$* | $O(1)$** |
| | delete | $O(n)$ | $O(n)$ | $O(1)$ |
| **Tablica posortowana** | member | $O(\log n)$ | $O(\log n)$ | $O(1)$ |
| | insert | $O(n)$ | $O(n)$ | $O(\log n)$ |
| | delete | $O(n)$ | $O(n)$ | $O(\log n)$ |
| **Hash (łańcuchowanie)** | member | $O(n)$ | $O(1)$*** | $O(1)$ |
| | insert | $O(n)$ | $O(1)$*** | $O(1)$ |
| | delete | $O(n)$ | $O(1)$*** | $O(1)$ |
| **Hash (adr. otwarte)** | member | $O(n)$ | $O(1)$*** | $O(1)$ |
| | insert | $O(n)$ | $O(1)$*** | $O(1)$ |
| | delete | $O(n)$ | $O(1)$*** | $O(1)$ |

\* Ze sprawdzeniem duplikatów (wymaga member).
\** Bez sprawdzenia duplikatów.
\*** Przy założeniu $\alpha = O(1)$ i dobrej funkcji haszującej.

### Przykład 2: Diagram tablicy haszującej z kolizjami (łańcuchowanie)

Wstawiamy elementy zbioru $S = \{10, 22, 31, 4, 15, 28, 17, 88, 59\}$ do tablicy haszującej z łańcuchowaniem, $m = 9$, $h(k) = k \bmod 9$:

```
Obliczenie wartości haszujących:
h(10) = 10 mod 9 = 1
h(22) = 22 mod 9 = 4
h(31) = 31 mod 9 = 4  ← kolizja z 22
h(4)  =  4 mod 9 = 4  ← kolizja z 22, 31
h(15) = 15 mod 9 = 6
h(28) = 28 mod 9 = 1  ← kolizja z 10
h(17) = 17 mod 9 = 8
h(88) = 88 mod 9 = 7
h(59) = 59 mod 9 = 5

Tablica haszująca (łańcuchowanie):
┌─────┬──────────────────────────────┐
│ [0] │ → [∅]                        │
│ [1] │ → [28] → [10] → [∅]         │  ← 2 elementy (kolizja)
│ [2] │ → [∅]                        │
│ [3] │ → [∅]                        │
│ [4] │ → [4] → [31] → [22] → [∅]   │  ← 3 elementy (kolizje!)
│ [5] │ → [59] → [∅]                 │
│ [6] │ → [15] → [∅]                 │
│ [7] │ → [88] → [∅]                 │
│ [8] │ → [17] → [∅]                 │
└─────┴──────────────────────────────┘

Współczynnik zapełnienia: α = 9/9 = 1.0
Kolizje: pozycje [1] i [4]
Najdłuższy łańcuch: 3 (pozycja [4])
```

**Operacja member(31):**
1. Oblicz $h(31) = 31 \bmod 9 = 4$
2. Przejdź listę $T[4]$: $4 \neq 31$, $31 = 31$ → znaleziono! ✓
3. Liczba porównań: 2

**Operacja member(7):**
1. Oblicz $h(7) = 7 \bmod 9 = 7$
2. Przejdź listę $T[7]$: $88 \neq 7$, koniec listy → nie znaleziono ✗
3. Liczba porównań: 1

### Przykład 3: Porównanie praktyczne — scenariusz użycia

Rozważmy zbiór $n = 10\,000$ elementów i $100\,000$ operacji member:

| Struktura | Czas member (średni) | Łączny czas (szacunkowy) |
|---|---|---|
| Lista nieposortowana | $\sim 5\,000$ porównań | $\sim 500\,000\,000$ operacji |
| Tablica posortowana | $\sim 14$ porównań ($\log_2 10000$) | $\sim 1\,400\,000$ operacji |
| Tablica haszująca ($\alpha = 0{,}75$) | $\sim 1{,}5$ próby | $\sim 150\,000$ operacji |

Tablica haszująca jest **$\sim 3\,300\times$ szybsza** niż lista i **$\sim 9\times$ szybsza** niż tablica posortowana dla operacji wyszukiwania.

## Podsumowanie

1. **Lista liniowa** jest najprostszą implementacją zbioru, ale najwolniejszą — wszystkie operacje mają złożoność $O(n)$. Nadaje się do małych zbiorów lub gdy dominują operacje wstawiania/usuwania na początku. Sortowanie listy nie poprawia asymptotycznej złożoności, ponieważ brak dostępu swobodnego uniemożliwia wyszukiwanie binarne.

2. **Tablica** w wersji posortowanej oferuje szybkie wyszukiwanie $O(\log n)$ dzięki wyszukiwaniu binarnemu, ale wstawianie i usuwanie pozostają kosztowne — $O(n)$ z powodu przesuwania elementów. Doskonała lokalność pamięci (cache-friendly). Najlepsza, gdy operacje member dominują, a insert/delete są rzadkie.

3. **Tablica haszująca** zapewnia najlepszą średnią wydajność — $O(1)$ oczekiwany czas dla wszystkich trzech operacji. Jest to struktura z wyboru dla implementacji zbiorów w praktyce. Wymaga jednak dobrej funkcji haszującej i kontroli współczynnika zapełnienia. W najgorszym przypadku degeneruje do $O(n)$.

4. **Wybór struktury** zależy od profilu operacji:
   - Dominuje member → tablica posortowana lub tablica haszująca
   - Dominuje insert/delete → tablica haszująca
   - Mały zbiór ($n < 100$) → dowolna struktura (różnice nieistotne)
   - Potrzebny porządek elementów → tablica posortowana (hash nie zachowuje porządku)
   - Potrzebna gwarancja pesymistyczna → tablica posortowana ($O(\log n)$ member) lub zrównoważone drzewo BST ($O(\log n)$ wszystkie operacje)

5. **Kolizje w tablicach haszujących** są nieuniknione i rozwiązywane przez łańcuchowanie (listy w komórkach) lub adresowanie otwarte (próbkowanie kolejnych komórek). Łańcuchowanie jest prostsze i toleruje $\alpha > 1$, adresowanie otwarte jest bardziej cache-friendly, ale wymaga $\alpha < 1$.

## Powiązane pytania

- [Pytanie 27: Złożoność obliczeniowa](27-zlozonosc-obliczeniowa.md)
- [Pytanie 43: Algorytm sortowania](43-algorytm-sortowania.md)
- [Pytanie 44: Przechowywanie macierzy rzadkich](44-macierze-rzadkie.md)
