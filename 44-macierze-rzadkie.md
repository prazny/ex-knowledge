# Pytanie 44: Jak można przechowywać w programie macierz rzadką?

## Kluczowe pojęcia

- **Macierz rzadka (sparse matrix)** — macierz, w której większość elementów ma wartość zero. Formalnie, macierz $A \in \mathbb{R}^{m \times n}$ jest rzadka, gdy liczba elementów niezerowych $\text{nnz}(A) \ll m \cdot n$. W praktyce macierz uznaje się za rzadką, gdy $\text{nnz} = O(m + n)$ lub gdy stosunek $\text{nnz} / (m \cdot n)$ (gęstość) jest mały (np. poniżej 10%). Macierze rzadkie występują powszechnie w metodach elementów skończonych, grafach, przetwarzaniu języka naturalnego i uczeniu maszynowym.
- **CSR (Compressed Sparse Row)** — format przechowywania macierzy rzadkiej kompresujący dane wierszami. Używa trzech tablic: `values` (wartości niezerowe), `col_indices` (indeksy kolumn) i `row_ptr` (wskaźniki początków wierszy). Jest najczęściej stosowanym formatem do operacji arytmetycznych na macierzach rzadkich, szczególnie mnożenia macierz-wektor. Złożoność pamięciowa: $O(\text{nnz} + m + 1)$.
- **CSC (Compressed Sparse Column)** — format analogiczny do CSR, ale kompresujący dane kolumnami. Używa tablic: `values`, `row_indices` i `col_ptr`. Preferowany, gdy operacje wymagają dostępu kolumnowego (np. rozwiązywanie układów równań metodami bezpośrednimi). Złożoność pamięciowa: $O(\text{nnz} + n + 1)$.
- **COO (Coordinate Format)** — najprostszy format przechowywania macierzy rzadkiej. Każdy element niezerowy jest reprezentowany jako trójka $(i, j, v)$ — wiersz, kolumna, wartość. Używa trzech tablic: `row`, `col`, `values`, każda o długości $\text{nnz}$. Łatwy do budowania (dodawanie elementów w $O(1)$), ale nieefektywny do operacji arytmetycznych. Złożoność pamięciowa: $O(3 \cdot \text{nnz})$.
- **DOK (Dictionary of Keys)** — format słownikowy, w którym elementy niezerowe są przechowywane w tablicy haszującej z kluczem $(i, j)$ i wartością $v$. Umożliwia szybki dostęp losowy do pojedynczych elementów w $O(1)$ średnio. Idealny do inkrementalnego budowania macierzy, ale nieefektywny do operacji macierzowych.
- **LIL (List of Lists)** — format listy list, w którym każdy wiersz macierzy jest reprezentowany jako lista par (indeks kolumny, wartość). Efektywny do budowania macierzy wiersz po wierszu i modyfikacji struktury. Złożoność wstawiania elementu: $O(k)$, gdzie $k$ to liczba niezerowych elementów w danym wierszu.
- **Oszczędność pamięci** — główna motywacja stosowania formatów rzadkich. Macierz gęsta $m \times n$ wymaga $O(m \cdot n)$ pamięci, podczas gdy format rzadki wymaga $O(\text{nnz})$ (plus metadane). Dla macierzy $10\,000 \times 10\,000$ z $50\,000$ elementami niezerowymi: format gęsty zajmuje $\sim 800$ MB (double), a CSR $\sim 0{,}8$ MB — oszczędność $\sim 1000\times$.

## Definicja macierzy rzadkiej

### Formalna definicja

Macierz $A \in \mathbb{R}^{m \times n}$ nazywamy **rzadką**, jeśli liczba jej elementów niezerowych $\text{nnz}(A)$ jest na tyle mała w stosunku do $m \cdot n$, że opłaca się stosować specjalne struktury danych zamiast standardowej tablicy dwuwymiarowej.

**Gęstość macierzy:**

$$d = \frac{\text{nnz}(A)}{m \cdot n}$$

Macierz jest rzadka, gdy $d \ll 1$ (typowo $d < 0{,}01$ do $0{,}1$).

### Źródła macierzy rzadkich

| Dziedzina | Przykład | Typowa gęstość |
|---|---|---|
| Metoda elementów skończonych (MES) | Macierz sztywności | $d \approx 10^{-3}$ – $10^{-5}$ |
| Grafy i sieci | Macierz sąsiedztwa | $d = O(1/n)$ |
| Przetwarzanie języka naturalnego | Macierz term-dokument | $d \approx 10^{-4}$ |
| Uczenie maszynowe | Macierz cech (one-hot) | $d \approx 10^{-3}$ |
| Równania różniczkowe cząstkowe | Dyskretyzacja operatorów | $d \approx 10^{-4}$ – $10^{-6}$ |

### Motywacja — oszczędność pamięci i czasu

Przechowywanie macierzy rzadkiej w formacie gęstym (tablica 2D) jest marnotrawstwem:

```
Macierz 10 000 x 10 000, nnz = 50 000 (gestosc 0,05%):

Format gesty:  10 000 x 10 000 x 8 bajtow = 800 000 000 bajtow ~ 763 MB
Format CSR:    50 000 x (8+4) + 10 001 x 4 ~ 640 040 bajtow    ~ 0,6 MB

Oszczednosc pamieci: ~1 270x
```

Operacje na macierzy gęstej (np. mnożenie macierz-wektor) mają złożoność $O(m \cdot n)$, podczas gdy na macierzy rzadkiej — $O(\text{nnz})$, co dla rzadkich macierzy jest dramatycznie szybsze.

## Formaty przechowywania macierzy rzadkich

### Macierz przykładowa

Wszystkie formaty zilustrujemy na tej samej macierzy $A \in \mathbb{R}^{4 \times 5}$:

$$A = \begin{pmatrix} 10 & 0 & 0 & 0 & -2 \\ 3 & 9 & 0 & 0 & 0 \\ 0 & 7 & 8 & 7 & 0 \\ 0 & 0 & 0 & 0 & 5 \end{pmatrix}$$

Parametry: $m = 4$, $n = 5$, $\text{nnz} = 8$, gęstość $d = 8/20 = 0{,}4$.

### Format COO (Coordinate Format)

Najprostszy format — każdy element niezerowy jest przechowywany jako trójka $(i, j, v)$.

**Struktura danych:**

```
row[]    -- tablica indeksow wierszy (dlugosc nnz)
col[]    -- tablica indeksow kolumn (dlugosc nnz)
values[] -- tablica wartosci (dlugosc nnz)
```

**Macierz A w formacie COO:**

```
Elementy niezerowe (wiersz, kolumna, wartosc):
(0,0,10), (0,4,-2), (1,0,3), (1,1,9), (2,1,7), (2,2,8), (2,3,7), (3,4,5)

Tablice COO:
indeks:    0    1    2    3    4    5    6    7
         +----+----+----+----+----+----+----+----+
row[]    |  0 |  0 |  1 |  1 |  2 |  2 |  2 |  3 |
         +----+----+----+----+----+----+----+----+
col[]    |  0 |  4 |  0 |  1 |  1 |  2 |  3 |  4 |
         +----+----+----+----+----+----+----+----+
values[] | 10 | -2 |  3 |  9 |  7 |  8 |  7 |  5 |
         +----+----+----+----+----+----+----+----+

Pamiec: 3 x nnz = 3 x 8 = 24 wartosci
```

**Właściwości COO:**

| Cecha | Wartość |
|---|---|
| Pamięć | $3 \cdot \text{nnz}$ (int + int + float na element) |
| Dodanie elementu | $O(1)$ (dopisanie na koniec) |
| Dostęp do elementu $(i,j)$ | $O(\text{nnz})$ (przeszukiwanie liniowe) |
| Mnożenie macierz-wektor | $O(\text{nnz})$, ale z losowym dostępem do wektora wynikowego |
| Budowanie | Bardzo łatwe — idealne jako format wejściowy |

### Format CSR (Compressed Sparse Row)

Format CSR kompresuje informację o wierszach, eliminując powtarzające się indeksy wierszy z COO.

**Struktura danych:**

```
values[]      -- wartosci niezerowe, uporzadkowane wierszami (dlugosc nnz)
col_indices[] -- indeksy kolumn odpowiadajace wartosciom (dlugosc nnz)
row_ptr[]     -- wskazniki na poczatek kazdego wiersza w tablicach
                 values/col_indices (dlugosc m+1)
                 row_ptr[i] = indeks pierwszego elementu wiersza i
                 row_ptr[m] = nnz (wartownik)
```

**Macierz A w formacie CSR:**

```
Wiersz 0: (kol 0, 10), (kol 4, -2)              -> 2 elementy, start: indeks 0
Wiersz 1: (kol 0,  3), (kol 1,  9)              -> 2 elementy, start: indeks 2
Wiersz 2: (kol 1,  7), (kol 2,  8), (kol 3, 7) -> 3 elementy, start: indeks 4
Wiersz 3: (kol 4,  5)                            -> 1 element,  start: indeks 7

Tablice CSR:
indeks:         0    1    2    3    4    5    6    7
              +----+----+----+----+----+----+----+----+
values[]      | 10 | -2 |  3 |  9 |  7 |  8 |  7 |  5 |
              +----+----+----+----+----+----+----+----+
col_indices[] |  0 |  4 |  0 |  1 |  1 |  2 |  3 |  4 |
              +----+----+----+----+----+----+----+----+

indeks:       0    1    2    3    4
            +----+----+----+----+----+
row_ptr[]   |  0 |  2 |  4 |  7 |  8 |
            +----+----+----+----+----+
              ^    ^    ^    ^    ^
             w0   w1   w2   w3  nnz

Odczyt wiersza i:
  elementy wiersza i = values[row_ptr[i] .. row_ptr[i+1]-1]
  kolumny wiersza i  = col_indices[row_ptr[i] .. row_ptr[i+1]-1]

Przyklad -- wiersz 2:
  row_ptr[2]=4, row_ptr[3]=7
  values[4..6]      = [7, 8, 7]
  col_indices[4..6] = [1, 2, 3]
  -> A[2,1]=7, A[2,2]=8, A[2,3]=7

Pamiec: 2 x nnz + (m+1) = 2 x 8 + 5 = 21 wartosci
```

**Algorytm mnożenia macierz-wektor $y = A \cdot x$ w formacie CSR:**

```
ALGORYTM SpMV_CSR(values, col_indices, row_ptr, x, m)
  y <- tablica zer o rozmiarze m
  DLA i <- 0 DO m-1:
    DLA k <- row_ptr[i] DO row_ptr[i+1]-1:
      y[i] <- y[i] + values[k] * x[col_indices[k]]
  ZWROC y
```

Złożoność: $O(\text{nnz})$ — dokładnie tyle mnożeń i dodawań, ile elementów niezerowych.

**Właściwości CSR:**

| Cecha | Wartość |
|---|---|
| Pamięć | $2 \cdot \text{nnz} + (m + 1)$ |
| Dostęp do wiersza $i$ | $O(1)$ (odczyt row_ptr[i] i row_ptr[i+1]) |
| Dostęp do elementu $(i,j)$ | $O(\log k_i)$ (wyszukiwanie binarne w wierszu, $k_i$ = nnz w wierszu $i$) |
| Mnożenie macierz-wektor | $O(\text{nnz})$ — sekwencyjny dostęp, cache-friendly |
| Modyfikacja struktury | Kosztowna — wymaga przebudowy tablic |

### Format CSC (Compressed Sparse Column)

Format CSC jest transpozycją CSR — kompresuje informację o kolumnach zamiast wierszy.

**Struktura danych:**

```
values[]      -- wartosci niezerowe, uporzadkowane kolumnami (dlugosc nnz)
row_indices[] -- indeksy wierszy odpowiadajace wartosciom (dlugosc nnz)
col_ptr[]     -- wskazniki na poczatek kazdej kolumny (dlugosc n+1)
```

**Macierz A w formacie CSC:**

```
Kolumna 0: (wiersz 0, 10), (wiersz 1, 3)        -> 2 elementy, start: indeks 0
Kolumna 1: (wiersz 1,  9), (wiersz 2, 7)         -> 2 elementy, start: indeks 2
Kolumna 2: (wiersz 2,  8)                         -> 1 element,  start: indeks 4
Kolumna 3: (wiersz 2,  7)                         -> 1 element,  start: indeks 5
Kolumna 4: (wiersz 0, -2), (wiersz 3, 5)         -> 2 elementy, start: indeks 6

Tablice CSC:
indeks:         0    1    2    3    4    5    6    7
              +----+----+----+----+----+----+----+----+
values[]      | 10 |  3 |  9 |  7 |  8 |  7 | -2 |  5 |
              +----+----+----+----+----+----+----+----+
row_indices[] |  0 |  1 |  1 |  2 |  2 |  2 |  0 |  3 |
              +----+----+----+----+----+----+----+----+

indeks:       0    1    2    3    4    5
            +----+----+----+----+----+----+
col_ptr[]   |  0 |  2 |  4 |  5 |  6 |  8 |
            +----+----+----+----+----+----+
              ^    ^    ^    ^    ^    ^
             k0   k1   k2   k3   k4  nnz

Odczyt kolumny j:
  elementy kolumny j = values[col_ptr[j] .. col_ptr[j+1]-1]
  wiersze kolumny j  = row_indices[col_ptr[j] .. col_ptr[j+1]-1]

Przyklad -- kolumna 4:
  col_ptr[4]=6, col_ptr[5]=8
  values[6..7]      = [-2, 5]
  row_indices[6..7] = [0, 3]
  -> A[0,4]=-2, A[3,4]=5

Pamiec: 2 x nnz + (n+1) = 2 x 8 + 6 = 22 wartosci
```

**Właściwości CSC:**

| Cecha | Wartość |
|---|---|
| Pamięć | $2 \cdot \text{nnz} + (n + 1)$ |
| Dostęp do kolumny $j$ | $O(1)$ |
| Dostęp do elementu $(i,j)$ | $O(\log k_j)$ (wyszukiwanie binarne w kolumnie) |
| Mnożenie $A^T \cdot x$ | $O(\text{nnz})$ — efektywne, sekwencyjny dostęp kolumnowy |
| Rozwiązywanie układów trójkątnych | Preferowany format dla metod bezpośrednich (np. LU) |

### Format DOK (Dictionary of Keys)

Format słownikowy — elementy niezerowe przechowywane w tablicy haszującej.

**Struktura danych:**

```
dict: HashMap<(int, int), float>
  klucz = (wiersz, kolumna)
  wartosc = wartosc elementu
```

**Macierz A w formacie DOK:**

```
DOK = {
  (0,0): 10,
  (0,4): -2,
  (1,0):  3,
  (1,1):  9,
  (2,1):  7,
  (2,2):  8,
  (2,3):  7,
  (3,4):  5
}

Dostep: DOK[(2,2)] = 8    -> O(1) srednio
Wstawienie: DOK[(3,3)] = 4 -> O(1) srednio
Usuniecie: del DOK[(0,4)]  -> O(1) srednio

Pamiec: nnz x (2 x int + float + narzut haszowania)
        ~ nnz x ~40 bajtow (zalezne od implementacji)
```

**Właściwości DOK:**

| Cecha | Wartość |
|---|---|
| Pamięć | $\sim 3\text{–}5 \cdot \text{nnz}$ (narzut tablicy haszującej) |
| Dostęp do elementu $(i,j)$ | $O(1)$ średnio |
| Wstawienie/usunięcie elementu | $O(1)$ średnio |
| Mnożenie macierz-wektor | Nieefektywne — brak sekwencyjnego dostępu |
| Iteracja po wierszu/kolumnie | $O(\text{nnz})$ — wymaga filtrowania |

### Format LIL (List of Lists)

Każdy wiersz macierzy jest przechowywany jako posortowana lista par (indeks kolumny, wartość).

**Struktura danych:**

```
rows[] -- tablica list, rows[i] = lista par (col, val) dla wiersza i
          kazda lista jest posortowana po indeksie kolumny
```

**Macierz A w formacie LIL:**

```
rows[0]: [(0, 10), (4, -2)]
rows[1]: [(0,  3), (1,  9)]
rows[2]: [(1,  7), (2,  8), (3, 7)]
rows[3]: [(4,  5)]

Schemat struktury:
+---------+
| rows[0] | -> [(0,10)] -> [(4,-2)] -> null
+---------+
| rows[1] | -> [(0, 3)] -> [(1, 9)] -> null
+---------+
| rows[2] | -> [(1, 7)] -> [(2, 8)] -> [(3, 7)] -> null
+---------+
| rows[3] | -> [(4, 5)] -> null
+---------+

Dostep do A[2,2]:
  Przeszukaj rows[2] -> znaleziono (2, 8) -> wartosc = 8

Pamiec: nnz x (int + float + wskaznik) + m x wskaznik
```

**Właściwości LIL:**

| Cecha | Wartość |
|---|---|
| Pamięć | $\sim 3 \cdot \text{nnz} + m$ (narzut list) |
| Dostęp do elementu $(i,j)$ | $O(k_i)$ — przeszukiwanie listy wiersza $i$ |
| Wstawienie elementu | $O(k_i)$ — wstawienie z zachowaniem porządku |
| Dostęp do wiersza $i$ | $O(1)$ — bezpośredni dostęp do listy |
| Budowanie wiersz po wierszu | Efektywne — naturalna struktura |

## Porównanie formatów

### Porównanie pamięci

Dla macierzy $m \times n$ z $\text{nnz}$ elementami niezerowymi (int = 4 bajty, float = 8 bajtów):

| Format | Pamięć (wartości) | Pamięć (bajty) | Dla $m=n=10^4$, nnz=$5 \cdot 10^4$ |
|---|---|---|---|
| Gęsty | $m \cdot n$ | $8mn$ | 800 MB |
| COO | $3 \cdot \text{nnz}$ | $16 \cdot \text{nnz}$ | 0,76 MB |
| CSR | $2 \cdot \text{nnz} + m + 1$ | $12 \cdot \text{nnz} + 4(m+1)$ | 0,61 MB |
| CSC | $2 \cdot \text{nnz} + n + 1$ | $12 \cdot \text{nnz} + 4(n+1)$ | 0,61 MB |
| DOK | $\sim 5 \cdot \text{nnz}$ | $\sim 40 \cdot \text{nnz}$ | 1,9 MB |
| LIL | $\sim 3 \cdot \text{nnz} + m$ | $\sim 20 \cdot \text{nnz} + 8m$ | 1,1 MB |

### Porównanie operacji

| Operacja | COO | CSR | CSC | DOK | LIL |
|---|---|---|---|---|---|
| Budowanie (inkrementalne) | ★★★ | ★ | ★ | ★★★ | ★★ |
| Dostęp do elementu $(i,j)$ | ★ | ★★ | ★★ | ★★★ | ★★ |
| Dostęp do wiersza $i$ | ★ | ★★★ | ★ | ★ | ★★★ |
| Dostęp do kolumny $j$ | ★ | ★ | ★★★ | ★ | ★ |
| Mnożenie macierz-wektor | ★★ | ★★★ | ★★ | ★ | ★★ |
| Modyfikacja struktury | ★★ | ★ | ★ | ★★★ | ★★ |
| Oszczędność pamięci | ★★ | ★★★ | ★★★ | ★ | ★★ |

Legenda: ★ = słabo, ★★ = średnio, ★★★ = bardzo dobrze

### Kiedy stosować który format?

```
Schemat wyboru formatu:

                    +---------------------+
                    | Budowanie macierzy?  |
                    +----------+----------+
                               |
                    +----------v----------+
              TAK <-| Znana struktura?    |-> NIE
              |     +---------------------+     |
              |                                  |
     +--------v--------+              +---------v---------+
     | Buduj w CSR/CSC |              | Buduj w COO / DOK |
     | bezposrednio    |              | potem konwertuj    |
     +--------+--------+              +---------+---------+
              |                                  |
              +----------------+-----------------+
                               |
                    +----------v----------+
                    | Operacje docelowe?  |
                    +----------+----------+
                               |
              +--------+-------+-------+--------+
              |        |               |        |
        +-----v-----+  +-----v-----+ +----v----+ +----v----+
        | Mnozenie  |  | Rozwiaz.  | | Dostep  | | Modyfik.|
        | A*x       |  | ukladow   | | losowy  | | strukt. |
        +-----+-----+  +-----+-----+ +----+----+ +----+----+
              |              |             |            |
        +-----v-----+  +----v------+ +----v----+ +----v----+
        |    CSR     |  |    CSC    | |   DOK   | | LIL/DOK |
        +-----------+  +----------+  +---------+ +---------+
```

**Typowy przepływ pracy:**
1. **Budowanie** — COO lub DOK (łatwe dodawanie elementów)
2. **Konwersja** — do CSR lub CSC (jednorazowy koszt $O(\text{nnz})$)
3. **Obliczenia** — CSR (mnożenie macierz-wektor) lub CSC (rozwiązywanie układów)

## Konwersja między formatami

### COO do CSR

Algorytm konwersji z formatu COO do CSR:

```
ALGORYTM COO_do_CSR(row, col, values, nnz, m)
  // Krok 1: Policz elementy w kazdym wierszu
  count[] <- tablica zer o rozmiarze m
  DLA k <- 0 DO nnz-1:
    count[row[k]] <- count[row[k]] + 1

  // Krok 2: Oblicz row_ptr (suma prefiksowa)
  row_ptr[0] <- 0
  DLA i <- 0 DO m-1:
    row_ptr[i+1] <- row_ptr[i] + count[i]

  // Krok 3: Wypelnij tablice CSR
  pos[] <- kopia row_ptr[0..m-1]   // pozycje zapisu
  csr_values[] <- tablica o rozmiarze nnz
  csr_col[] <- tablica o rozmiarze nnz
  DLA k <- 0 DO nnz-1:
    i <- row[k]
    idx <- pos[i]
    csr_values[idx] <- values[k]
    csr_col[idx] <- col[k]
    pos[i] <- pos[i] + 1

  ZWROC (csr_values, csr_col, row_ptr)
```

Złożoność: $O(\text{nnz} + m)$ — liniowa.

### CSR do CSC (transpozycja formatu)

Konwersja CSR do CSC jest równoważna transpozycji macierzy w formacie CSR:

```
ALGORYTM CSR_do_CSC(values, col_indices, row_ptr, m, n, nnz)
  // Krok 1: Policz elementy w kazdej kolumnie
  count[] <- tablica zer o rozmiarze n
  DLA k <- 0 DO nnz-1:
    count[col_indices[k]] <- count[col_indices[k]] + 1

  // Krok 2: Oblicz col_ptr (suma prefiksowa)
  col_ptr[0] <- 0
  DLA j <- 0 DO n-1:
    col_ptr[j+1] <- col_ptr[j] + count[j]

  // Krok 3: Wypelnij tablice CSC
  pos[] <- kopia col_ptr[0..n-1]
  csc_values[] <- tablica o rozmiarze nnz
  csc_row[] <- tablica o rozmiarze nnz
  DLA i <- 0 DO m-1:
    DLA k <- row_ptr[i] DO row_ptr[i+1]-1:
      j <- col_indices[k]
      idx <- pos[j]
      csc_values[idx] <- values[k]
      csc_row[idx] <- i
      pos[j] <- pos[j] + 1

  ZWROC (csc_values, csc_row, col_ptr)
```

Złożoność: $O(\text{nnz} + m + n)$.

## Przykłady

### Przykład 1: Ta sama macierz w trzech formatach

Macierz $A$:

$$A = \begin{pmatrix} 10 & 0 & 0 & 0 & -2 \\ 3 & 9 & 0 & 0 & 0 \\ 0 & 7 & 8 & 7 & 0 \\ 0 & 0 & 0 & 0 & 5 \end{pmatrix}$$

```
=== FORMAT COO ===
row[]    = [ 0,  0,  1,  1,  2,  2,  2,  3]
col[]    = [ 0,  4,  0,  1,  1,  2,  3,  4]
values[] = [10, -2,  3,  9,  7,  8,  7,  5]
Pamiec: 3 x 8 = 24 wartosci

=== FORMAT CSR ===
values[]      = [10, -2,  3,  9,  7,  8,  7,  5]
col_indices[] = [ 0,  4,  0,  1,  1,  2,  3,  4]
row_ptr[]     = [ 0,  2,  4,  7,  8]
                  ^    ^    ^    ^    ^
                 w0   w1   w2   w3  nnz
Pamiec: 2 x 8 + 5 = 21 wartosci

=== FORMAT CSC ===
values[]      = [10,  3,  9,  7,  8,  7, -2,  5]
row_indices[] = [ 0,  1,  1,  2,  2,  2,  0,  3]
col_ptr[]     = [ 0,  2,  4,  5,  6,  8]
                  ^    ^    ^    ^    ^    ^
                 k0   k1   k2   k3   k4  nnz
Pamiec: 2 x 8 + 6 = 22 wartosci
```

### Przykład 2: Mnożenie macierz-wektor $y = A \cdot x$ w CSR

Dla $x = (1, 2, 3, 4, 5)^T$:

```
Obliczenie y = A * x w formacie CSR:

Wiersz 0: row_ptr[0]=0, row_ptr[1]=2
  y[0] = values[0]*x[col[0]] + values[1]*x[col[1]]
       = 10*x[0] + (-2)*x[4]
       = 10*1 + (-2)*5 = 0

Wiersz 1: row_ptr[1]=2, row_ptr[2]=4
  y[1] = values[2]*x[col[2]] + values[3]*x[col[3]]
       = 3*x[0] + 9*x[1]
       = 3*1 + 9*2 = 21

Wiersz 2: row_ptr[2]=4, row_ptr[3]=7
  y[2] = values[4]*x[col[4]] + values[5]*x[col[5]] + values[6]*x[col[6]]
       = 7*x[1] + 8*x[2] + 7*x[3]
       = 7*2 + 8*3 + 7*4 = 66

Wiersz 3: row_ptr[3]=7, row_ptr[4]=8
  y[3] = values[7]*x[col[7]]
       = 5*x[4]
       = 5*5 = 25

Wynik: y = (0, 21, 66, 25)^T

Liczba operacji: 8 mnozen + 5 dodawan = 13 operacji
(format gesty: 20 mnozen + 16 dodawan = 36 operacji)
```

### Przykład 3: Macierz pasmowa — typowy wzorzec rzadkości

Macierz trójdiagonalna $T \in \mathbb{R}^{5 \times 5}$ (typowa dla MRS/FDM):

$$T = \begin{pmatrix} 2 & -1 & 0 & 0 & 0 \\ -1 & 2 & -1 & 0 & 0 \\ 0 & -1 & 2 & -1 & 0 \\ 0 & 0 & -1 & 2 & -1 \\ 0 & 0 & 0 & -1 & 2 \end{pmatrix}$$

```
Parametry: m = n = 5, nnz = 13, gestosc = 13/25 = 0,52

Format CSR:
values[]      = [2,-1, -1,2,-1, -1,2,-1, -1,2,-1, -1,2]
col_indices[] = [0, 1,  0,1, 2,  1,2, 3,  2,3, 4,  3,4]
row_ptr[]     = [0, 2, 5, 8, 11, 13]

Dla macierzy n x n trojdiagonalnej:
  nnz = 3n - 2
  Pamiec CSR: 2(3n-2) + (n+1) = 7n - 3
  Pamiec gesta: n^2
  Oszczednosc: ~n/7 razy mniej pamieci

  n = 1 000 000:
    Gesta: ~7,5 TB
    CSR:   ~53 MB
```

## Podsumowanie

1. **Macierz rzadka** to macierz, w której większość elementów jest zerowa. Przechowywanie jej w formacie gęstym (tablica 2D) jest marnotrawstwem pamięci i czasu obliczeniowego. Specjalne formaty rzadkie przechowują jedynie elementy niezerowe wraz z informacją o ich pozycji.

2. **Pięć głównych formatów** przechowywania macierzy rzadkich to: COO (trójki współrzędnych), CSR (kompresja wierszowa), CSC (kompresja kolumnowa), DOK (słownik kluczy) i LIL (lista list). Każdy format ma inne zalety i jest optymalny dla innych zastosowań.

3. **CSR jest najważniejszym formatem** w praktyce — zapewnia efektywne mnożenie macierz-wektor ($O(\text{nnz})$), kompaktowe przechowywanie ($2 \cdot \text{nnz} + m + 1$) i sekwencyjny dostęp do pamięci (cache-friendly). Jest standardem w bibliotekach numerycznych (SciPy, MATLAB, Eigen).

4. **Typowy przepływ pracy** polega na budowaniu macierzy w formacie łatwym do modyfikacji (COO lub DOK), a następnie jednorazowej konwersji do formatu obliczeniowego (CSR lub CSC). Konwersja ma złożoność $O(\text{nnz} + m)$.

5. **Oszczędność pamięci** jest dramatyczna dla dużych macierzy rzadkich — format CSR dla macierzy $10^4 \times 10^4$ z $5 \cdot 10^4$ elementami niezerowymi zajmuje $\sim 1000\times$ mniej pamięci niż format gęsty. Operacje arytmetyczne są proporcjonalnie szybsze — $O(\text{nnz})$ zamiast $O(m \cdot n)$.

## Powiązane pytania

- [Pytanie 42: Listy, tablice, tablice haszujące](42-listy-tablice-hasze.md)
- [Pytanie 38: Metody iteracyjne dla układów równań liniowych](38-metody-iteracyjne-uklady-rownan.md)
