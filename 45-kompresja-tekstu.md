# Pytanie 45: Proszę opisać dowolny algorytm kompresji, który można zastosować do pliku tekstowego.

## Kluczowe pojęcia

- **Kompresja bezstratna (lossless compression)** — metoda redukcji rozmiaru danych, w której oryginalne dane mogą być odtworzone w 100% z postaci skompresowanej. W odróżnieniu od kompresji stratnej (lossy), nie traci się żadnej informacji. Kompresja bezstratna jest jedynym dopuszczalnym rodzajem kompresji dla plików tekstowych, kodu źródłowego, baz danych i dokumentów, ponieważ utrata nawet jednego bitu zmienia znaczenie tekstu.
- **Kodowanie Huffmana** — algorytm kompresji bezstratnej oparty na przypisywaniu krótszych kodów binarnych symbolom o wyższej częstotliwości występowania, a dłuższych — symbolom rzadszym. Wykorzystuje drzewo binarne (drzewo Huffmana) do generowania optymalnych kodów prefiksowych. Złożoność budowy drzewa: $O(n \log n)$, gdzie $n$ to liczba różnych symboli. Kodowanie Huffmana jest optymalne wśród kodów prefiksowych — żaden inny kod prefiksowy nie daje krótszego średniego kodu.
- **LZW (Lempel-Ziv-Welch)** — algorytm kompresji słownikowej, w którym powtarzające się sekwencje znaków są zastępowane odwołaniami do dynamicznie budowanego słownika. Słownik jest inicjalizowany pojedynczymi znakami alfabetu i rozbudowywany w trakcie kompresji o nowo napotkane sekwencje. LZW jest podstawą formatów GIF i TIFF oraz narzędzia `compress` w systemach Unix. Nie wymaga przesyłania słownika — dekoder odtwarza go identycznie.
- **RLE (Run-Length Encoding)** — najprostsza metoda kompresji bezstratnej, polegająca na zastępowaniu ciągów powtarzających się symboli parą (symbol, liczba powtórzeń). Efektywna dla danych z długimi seriami identycznych wartości (np. grafika rastrowa, tekst z wieloma spacjami). Dla tekstu naturalnego RLE jest zwykle nieefektywna, ponieważ długie serie identycznych znaków występują rzadko.
- **Entropia (Shannon)** — miara średniej ilości informacji przypadającej na symbol w źródle danych. Dla źródła o alfabecie $\{s_1, \ldots, s_n\}$ z prawdopodobieństwami $\{p_1, \ldots, p_n\}$ entropia wynosi $H = -\sum_{i=1}^{n} p_i \log_2 p_i$ bitów/symbol. Entropia wyznacza teoretyczną dolną granicę średniej długości kodu — żaden algorytm kompresji bezstratnej nie może osiągnąć średniej długości kodu mniejszej niż $H$ bitów na symbol (twierdzenie Shannona o kodowaniu źródłowym).
- **Współczynnik kompresji** — miara efektywności kompresji, definiowana jako stosunek rozmiaru danych po kompresji do rozmiaru danych przed kompresją: $r = \frac{|C|}{|D|}$, gdzie $|C|$ to rozmiar skompresowany, a $|D|$ to rozmiar oryginalny. Wartość $r < 1$ oznacza skuteczną kompresję (np. $r = 0{,}4$ oznacza redukcję do 40% oryginalnego rozmiaru). Alternatywnie stosuje się **stopień kompresji** $= 1/r$ (np. kompresja 2,5:1).

## Kodowanie Huffmana

### Idea algorytmu

Kodowanie Huffmana opiera się na prostej obserwacji: jeśli niektóre symbole występują częściej niż inne, to opłaca się przypisać im krótsze kody binarne. W standardowym kodowaniu ASCII każdy znak zajmuje 8 bitów, niezależnie od częstotliwości. Huffman eliminuje tę nadmiarowość.

Kody Huffmana są **kodami prefiksowymi** — żaden kod nie jest prefiksem innego kodu. Dzięki temu dekodowanie jest jednoznaczne i nie wymaga separatorów między kodami.

### Algorytm budowy drzewa Huffmana

Drzewo Huffmana buduje się metodą zachłanną (bottom-up):

1. Oblicz częstotliwość każdego symbolu w tekście.
2. Utwórz listę węzłów-liści, po jednym na każdy symbol, z wagą równą częstotliwości.
3. Dopóki lista zawiera więcej niż jeden węzeł:
   a. Wybierz dwa węzły o najmniejszych wagach.
   b. Utwórz nowy węzeł wewnętrzny z wagą równą sumie wag dzieci.
   c. Przypisz wybranym węzłom role lewego (0) i prawego (1) dziecka.
   d. Usuń dwa wybrane węzły z listy i dodaj nowy węzeł.
4. Ostatni węzeł na liście jest korzeniem drzewa Huffmana.
5. Kod każdego symbolu odczytujemy, przechodząc od korzenia do liścia: lewo = 0, prawo = 1.

### Pseudokod

```
ALGORYTM BudujDrzewoHuffmana(tekst)
  freq ← ObliczCzęstotliwości(tekst)
  Q ← kolejka priorytetowa (min-heap)

  DLA KAŻDEGO symbolu s z freq:
    węzeł ← NowyLiść(symbol=s, waga=freq[s])
    Q.wstaw(węzeł)

  DOPÓKI |Q| > 1:
    lewy ← Q.wyciągnijMin()
    prawy ← Q.wyciągnijMin()
    rodzic ← NowyWęzeł(waga = lewy.waga + prawy.waga,
                        leweDziecko = lewy,
                        praweDziecko = prawy)
    Q.wstaw(rodzic)

  korzeń ← Q.wyciągnijMin()
  ZWRÓĆ korzeń


ALGORYTM GenerujKody(węzeł, prefiks, tabela)
  JEŚLI węzeł jest liściem:
    tabela[węzeł.symbol] ← prefiks
    ZWRÓĆ
  GenerujKody(węzeł.leweDziecko, prefiks + "0", tabela)
  GenerujKody(węzeł.praweDziecko, prefiks + "1", tabela)


ALGORYTM KompresjaHuffmana(tekst)
  korzeń ← BudujDrzewoHuffmana(tekst)
  tabela ← pusta mapa
  GenerujKody(korzeń, "", tabela)

  wynik ← ""
  DLA KAŻDEGO znaku c w tekst:
    wynik ← wynik + tabela[c]
  ZWRÓĆ wynik, korzeń


ALGORYTM DekompresjaHuffmana(bity, korzeń)
  wynik ← ""
  węzeł ← korzeń
  DLA KAŻDEGO bitu b w bity:
    JEŚLI b = 0:
      węzeł ← węzeł.leweDziecko
    W PRZECIWNYM RAZIE:
      węzeł ← węzeł.praweDziecko
    JEŚLI węzeł jest liściem:
      wynik ← wynik + węzeł.symbol
      węzeł ← korzeń
  ZWRÓĆ wynik
```

### Złożoność

| Operacja | Złożoność |
|---|---|
| Obliczenie częstotliwości | $O(N)$, gdzie $N$ = długość tekstu |
| Budowa drzewa (z min-heapem) | $O(n \log n)$, gdzie $n$ = liczba różnych symboli |
| Generowanie kodów | $O(n)$ |
| Kodowanie tekstu | $O(N)$ |
| Dekodowanie | $O(N')$, gdzie $N'$ = liczba bitów |
| **Łącznie** | $O(N + n \log n)$ |

### Optymalność

Kodowanie Huffmana jest **optymalne wśród kodów prefiksowych** — minimalizuje średnią długość kodu:

$L = \sum_{i=1}^{n} p_i \cdot l_i$

gdzie $p_i$ to prawdopodobieństwo symbolu $s_i$, a $l_i$ to długość jego kodu. Zachodzi:

$H \leq L < H + 1$

gdzie $H$ to entropia źródła. Oznacza to, że Huffman jest co najwyżej o 1 bit na symbol gorszy od teoretycznego optimum.

## LZW (Lempel-Ziv-Welch)

### Idea algorytmu

LZW jest algorytmem **kompresji słownikowej** — zamiast kodować pojedyncze symbole na podstawie ich częstotliwości, rozpoznaje i koduje powtarzające się sekwencje znaków. Słownik jest budowany dynamicznie podczas kompresji i nie musi być przesyłany razem z danymi — dekoder odtwarza go identycznie.

LZW jest rozwinięciem algorytmu LZ78 (Lempel-Ziv, 1978), zaproponowanym przez Terry'ego Welcha w 1984 roku.

### Algorytm kompresji LZW

```
ALGORYTM KompresjaLZW(tekst)
  // Inicjalizacja słownika pojedynczymi znakami
  słownik ← mapa: każdy znak c → kod (np. ASCII)
  następnyKod ← 256  // pierwszy wolny kod po ASCII

  w ← ""  // bieżące słowo
  wynik ← []

  DLA KAŻDEGO znaku c w tekst:
    wc ← w + c
    JEŚLI wc JEST W słowniku:
      w ← wc
    W PRZECIWNYM RAZIE:
      wynik.dodaj(słownik[w])     // wyślij kod dla w
      słownik[wc] ← następnyKod  // dodaj wc do słownika
      następnyKod ← następnyKod + 1
      w ← c

  JEŚLI w ≠ "":
    wynik.dodaj(słownik[w])

  ZWRÓĆ wynik
```

### Algorytm dekompresji LZW

```
ALGORYTM DekompresjaLZW(kody)
  // Inicjalizacja słownika odwrotnego
  słownik ← mapa: kod → znak (odwrotność ASCII)
  następnyKod ← 256

  w ← słownik[kody[0]]
  wynik ← w

  DLA i ← 1 DO |kody|-1:
    k ← kody[i]
    JEŚLI k JEST W słowniku:
      wpis ← słownik[k]
    W PRZECIWNYM RAZIE JEŚLI k = następnyKod:
      wpis ← w + w[0]   // przypadek specjalny
    W PRZECIWNYM RAZIE:
      BŁĄD "Nieprawidłowy kod"

    wynik ← wynik + wpis
    słownik[następnyKod] ← w + wpis[0]
    następnyKod ← następnyKod + 1
    w ← wpis

  ZWRÓĆ wynik
```

### Właściwości LZW

| Cecha | Wartość |
|---|---|
| Typ kompresji | Słownikowa, bezstratna |
| Złożoność czasowa | $O(N)$ — liniowa względem długości tekstu |
| Złożoność pamięciowa | $O(|\text{słownik}|)$ — rozmiar słownika |
| Przesyłanie słownika | Nie jest wymagane |
| Zastosowania | GIF, TIFF, Unix `compress`, PDF |
| Efektywność | Bardzo dobra dla tekstu z powtarzającymi się wzorcami |

## RLE (Run-Length Encoding)

### Idea

RLE zastępuje ciągi powtarzających się symboli parą (liczba powtórzeń, symbol):

```
Wejście:  AAAAAABBCCCCDDDDDDDD
Wyjście:  6A2B4C8D

Wejście:  ABCDE
Wyjście:  1A1B1C1D1E  ← RLE zwiększa rozmiar!
```

### Pseudokod

```
ALGORYTM KompresjaRLE(tekst)
  wynik ← ""
  i ← 0
  DOPÓKI i < |tekst|:
    bieżący ← tekst[i]
    licznik ← 1
    DOPÓKI i + licznik < |tekst| ORAZ tekst[i + licznik] = bieżący:
      licznik ← licznik + 1
    wynik ← wynik + ToString(licznik) + bieżący
    i ← i + licznik
  ZWRÓĆ wynik
```

### Właściwości RLE

| Cecha | Wartość |
|---|---|
| Złożoność czasowa | $O(N)$ |
| Efektywność | Wysoka dla danych z długimi seriami (grafika, fax) |
| Dla tekstu naturalnego | Zwykle **nieefektywna** — może zwiększyć rozmiar |
| Zastosowania | BMP, PCX, faks (ITU-T T.4), jako etap wstępny |

## Porównanie metod kompresji

| Cecha | Huffman | LZW | RLE |
|---|---|---|---|
| Typ | Statystyczna | Słownikowa | Seryjna |
| Wymaga analizy wstępnej | Tak (2 przebiegi) | Nie (1 przebieg) | Nie (1 przebieg) |
| Adaptacyjność | Statyczna* | Adaptacyjna | Brak |
| Efektywność dla tekstu | Dobra | Bardzo dobra | Słaba |
| Efektywność dla danych binarnych | Dobra | Dobra | Zmienna |
| Złożoność implementacji | Średnia | Średnia | Niska |
| Optymalność | Optymalna (kody prefiksowe) | Asymptotycznie optymalna | Brak gwarancji |
| Typowe zastosowania | JPEG (DC), ZIP (deflate) | GIF, TIFF, PDF | BMP, faks |

*Istnieje wariant adaptacyjny kodowania Huffmana, w którym drzewo jest aktualizowane dynamicznie.

### Kombinacje metod

W praktyce metody kompresji są często łączone:

- **DEFLATE** (ZIP, gzip, PNG) = LZ77 + Huffman
- **bzip2** = BWT (Burrows-Wheeler Transform) + MTF (Move-to-Front) + Huffman
- **zstd** (Zstandard) = LZ77 + Huffman + FSE (Finite State Entropy)

## Entropia jako granica kompresji

### Twierdzenie Shannona o kodowaniu źródłowym

Entropia źródła informacji wyznacza fundamentalną granicę kompresji bezstratnej.

Dla źródła bezpamięciowego o alfabecie $\{s_1, \ldots, s_n\}$ z prawdopodobieństwami $\{p_1, \ldots, p_n\}$:

$H = -\sum_{i=1}^{n} p_i \log_2 p_i \quad [\text{bitów/symbol}]$

**Twierdzenie Shannona:** Średnia długość kodu bezstratnego nie może być mniejsza niż entropia źródła:

$L_{\text{avg}} \geq H$

Równość zachodzi, gdy $p_i = 2^{-l_i}$ dla każdego symbolu (tj. prawdopodobieństwa są ujemnymi potęgami dwójki).

### Przykład obliczenia entropii

Dla tekstu „ABRACADABRA" (11 znaków):

| Symbol | Częstotliwość | Prawdopodobieństwo $p_i$ | $-p_i \log_2 p_i$ |
|---|---|---|---|
| A | 5 | 5/11 ≈ 0,4545 | 0,5180 |
| B | 2 | 2/11 ≈ 0,1818 | 0,4506 |
| R | 2 | 2/11 ≈ 0,1818 | 0,4506 |
| C | 1 | 1/11 ≈ 0,0909 | 0,3122 |
| D | 1 | 1/11 ≈ 0,0909 | 0,3122 |

$H = 0{,}5180 + 0{,}4506 + 0{,}4506 + 0{,}3122 + 0{,}3122 = 2{,}0436$ bitów/symbol

Minimalna wielkość skompresowanego tekstu: $11 \times 2{,}0436 \approx 22{,}5$ bitów (w porównaniu z $11 \times 8 = 88$ bitów w ASCII).

## Przykłady

### Kompresja Huffmana krok po kroku

**Tekst wejściowy:** „ABRACADABRA" (11 znaków)

**Krok 1 — Obliczenie częstotliwości:**

```
A: 5    B: 2    R: 2    C: 1    D: 1
```

**Krok 2 — Budowa drzewa Huffmana:**

```
Kolejka priorytetowa (min-heap):
Początek: [C:1] [D:1] [B:2] [R:2] [A:5]

Iteracja 1: Łączymy C(1) i D(1) → CD(2)
  [B:2] [R:2] [CD:2] [A:5]

Iteracja 2: Łączymy B(2) i R(2) → BR(4)
  [CD:2] [A:5] [BR:4]

Iteracja 3: Łączymy CD(2) i A(5) → ACD(7)
  [BR:4] [ACD:7]

Iteracja 4: Łączymy BR(4) i ACD(7) → korzeń(11)
```

**Drzewo Huffmana:**

```
           [11]
          /    \
        0/      \1
       [4]      [7]
      /    \   /    \
    0/     1\ 0/    1\
   [B:2] [R:2] [A:5] [2]
                     /    \
                   0/      \1
                  [C:1]   [D:1]
```

**Krok 3 — Odczytanie kodów:**

| Symbol | Ścieżka w drzewie | Kod Huffmana | Długość |
|---|---|---|---|
| A | prawo → lewo | `10` | 2 bity |
| B | lewo → lewo | `00` | 2 bity |
| R | lewo → prawo | `01` | 2 bity |
| C | prawo → prawo → lewo | `110` | 3 bity |
| D | prawo → prawo → prawo | `111` | 3 bity |

**Krok 4 — Kodowanie tekstu:**

```
Tekst:    A   B   R   A   C   A   D   A   B   R   A
Kody:     10  00  01  10  110 10  111 10  00  01  10
```

Wynik: `10000110110101111000110`

**Krok 5 — Analiza efektywności:**

```
Rozmiar oryginalny (ASCII):  11 × 8 = 88 bitów
Rozmiar skompresowany:       5×2 + 2×2 + 2×2 + 1×3 + 1×3 = 24 bity
Współczynnik kompresji:      r = 24/88 ≈ 0,273 (kompresja do ~27%)
Stopień kompresji:           88/24 ≈ 3,67:1

Średnia długość kodu:        L = 24/11 ≈ 2,18 bitów/symbol
Entropia:                    H ≈ 2,04 bitów/symbol
Nadmiarowość:                L - H ≈ 0,14 bitów/symbol
```

Kodowanie Huffmana osiąga średnią długość kodu bliską entropii — jest niemal optymalne.

**Krok 6 — Weryfikacja dekompresji:**

```
Bity:   1 0 0 0 0 1 1 0 1 1 0 1 0 1 1 1 1 0 0 0 0 1 1 0
Dekodowanie (od korzenia):
  10     → A
  00     → B
  01     → R
  10     → A
  110    → C
  10     → A
  111    → D
  10     → A
  00     → B
  01     → R
  10     → A

Wynik: ABRACADABRA ✓
```

### Przykład kompresji LZW

**Tekst wejściowy:** „ABABABABAB"

```
Słownik początkowy: A=65, B=66

Krok | w    | c | wc   | W słowniku? | Wynik      | Nowy wpis
-----|------|---|------|-------------|------------|----------
1    | ""   | A | A    | Tak         | —          | —
2    | A    | B | AB   | Nie         | 65 (A)     | AB=256
3    | B    | A | BA   | Nie         | 66 (B)     | BA=257
4    | A    | B | AB   | Tak         | —          | —
5    | AB   | A | ABA  | Nie         | 256 (AB)   | ABA=258
6    | A    | B | AB   | Tak         | —          | —
7    | AB   | A | ABA  | Tak         | —          | —
8    | ABA  | B | ABAB | Nie         | 258 (ABA)  | ABAB=259
9    | B    | — | —    | —           | 66 (B)     | —

Wynik: [65, 66, 256, 258, 66]

Oryginał:       10 znaków × 8 bitów = 80 bitów
Skompresowany:  5 kodów × 9 bitów   = 45 bitów
Współczynnik:   r = 45/80 = 0,5625 (kompresja do ~56%)
```

Im dłuższy tekst z powtarzającymi się wzorcami, tym lepszy współczynnik kompresji LZW.

## Podsumowanie

- **Kompresja bezstratna** gwarantuje pełne odtworzenie oryginalnych danych — jest jedynym dopuszczalnym rodzajem kompresji dla tekstu.
- **Kodowanie Huffmana** przypisuje krótsze kody częstszym symbolom, budując optymalne drzewo binarne metodą zachłanną. Jest optymalne wśród kodów prefiksowych, osiągając średnią długość kodu $L$ taką, że $H \leq L < H + 1$.
- **LZW** jest algorytmem słownikowym — rozpoznaje powtarzające się sekwencje i zastępuje je kodami. Słownik budowany jest dynamicznie i nie wymaga przesyłania. Szczególnie efektywny dla tekstu z powtarzającymi się wzorcami.
- **RLE** jest najprostszą metodą kompresji, efektywną tylko dla danych z długimi seriami identycznych symboli. Dla tekstu naturalnego jest zwykle nieefektywna.
- **Entropia Shannona** $H = -\sum p_i \log_2 p_i$ wyznacza teoretyczną dolną granicę kompresji bezstratnej — żaden algorytm nie może osiągnąć średniej długości kodu mniejszej niż $H$ bitów/symbol.
- W praktyce metody kompresji są łączone (np. DEFLATE = LZ77 + Huffman), aby osiągnąć lepsze wyniki niż każda metoda osobno.

## Powiązane pytania

- [Pytanie 27: Złożoność obliczeniowa](27-zlozonosc-obliczeniowa.md)
- [Pytanie 46: Algorytm zachłanny](46-algorytm-zachlanny.md)
