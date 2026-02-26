# Pytanie 40: Co to jest „programowanie dynamiczne"?

## Kluczowe pojęcia

- **Programowanie dynamiczne (dynamic programming, DP)** — technika projektowania algorytmów polegająca na rozwiązywaniu problemu przez podział na mniejsze, nakładające się podproblemy, rozwiązanie każdego podproblemu dokładnie raz i zapamiętanie wyniku w celu uniknięcia powtórnych obliczeń. Termin wprowadził Richard Bellman w latach 50. XX wieku. Programowanie dynamiczne łączy dwie kluczowe własności: optymalną podstrukturę i nakładające się podproblemy.
- **Podproblemy nakładające się (overlapping subproblems)** — własność problemu polegająca na tym, że rekurencyjne rozwiązanie wielokrotnie rozwiązuje te same podproblemy. Przykładowo, naiwne obliczanie $F(n) = F(n-1) + F(n-2)$ powoduje wielokrotne obliczanie tych samych wartości $F(k)$. Nakładanie się podproblemów odróżnia DP od metody „dziel i zwyciężaj", gdzie podproblemy są rozłączne.
- **Optymalna podstruktura (optimal substructure)** — własność problemu, w której optymalne rozwiązanie problemu zawiera w sobie optymalne rozwiązania podproblemów. Formalnie: jeśli $S^*$ jest optymalnym rozwiązaniem problemu $P$, to fragmenty $S^*$ odpowiadające podproblemom $P_1, P_2, \ldots$ są optymalnymi rozwiązaniami tych podproblemów. Bez tej własności nie można budować rozwiązania globalnego z rozwiązań lokalnych.
- **Memoizacja (memoization)** — technika zapamiętywania wyników już obliczonych podproblemów w tablicy (lub słowniku), stosowana w podejściu top-down. Gdy funkcja rekurencyjna jest wywoływana z argumentami, dla których wynik już obliczono, zwraca zapamiętaną wartość zamiast ponownie wykonywać obliczenia. Memoizacja redukuje złożoność z wykładniczej do wielomianowej w wielu problemach DP.
- **Tabulacja (tabulation)** — technika wypełniania tablicy wyników podproblemów w porządku od najmniejszych do największych (bottom-up), bez użycia rekurencji. Każda komórka tablicy jest obliczana na podstawie wcześniej wypełnionych komórek. Tabulacja eliminuje narzut stosu rekurencji i jest zwykle bardziej efektywna pamięciowo.

## Zasada optymalności Bellmana

### Sformułowanie

**Zasada optymalności Bellmana** (Bellman's Principle of Optimality, 1957):

> Optymalna strategia ma tę własność, że niezależnie od stanu początkowego i podjętej decyzji, pozostałe decyzje muszą stanowić optymalną strategię względem stanu wynikającego z pierwszej decyzji.

Formalnie: niech problem $P$ ma optymalną podstrukturę. Jeśli optymalne rozwiązanie problemu $P$ przechodzi przez podproblem $P'$, to fragment rozwiązania odpowiadający $P'$ jest optymalnym rozwiązaniem $P'$.

### Równanie Bellmana

Dla problemu optymalizacyjnego z etapami (stages) zasadę optymalności zapisujemy jako **równanie Bellmana** (rekurencję DP):

$\text{OPT}(i) = \min_{d \in D(i)} \left[ c(i, d) + \text{OPT}(\text{next}(i, d)) \right]$

gdzie:
- $\text{OPT}(i)$ — optymalna wartość dla stanu $i$
- $D(i)$ — zbiór dopuszczalnych decyzji w stanie $i$
- $c(i, d)$ — koszt decyzji $d$ w stanie $i$
- $\text{next}(i, d)$ — stan po podjęciu decyzji $d$

### Konsekwencje

Zasada optymalności pozwala:
1. Zdefiniować rekurencję opisującą optymalne rozwiązanie
2. Rozwiązać problem „od końca" lub „od początku", budując rozwiązanie z podproblemów
3. Uniknąć przeglądu zupełnego (brute force) — zamiast sprawdzać wszystkie kombinacje, budujemy rozwiązanie przyrostowo

**Uwaga:** Nie wszystkie problemy optymalizacyjne mają optymalną podstrukturę. Kontrprzykład: problem najdłuższej ścieżki prostej w grafie ogólnym (bez wag ujemnych) — optymalna ścieżka z $A$ do $C$ przez $B$ nie musi zawierać optymalnej ścieżki z $A$ do $B$, bo ścieżki mogą się nakładać.

## Podejście top-down (memoizacja) vs bottom-up (tabulacja)

Programowanie dynamiczne można implementować na dwa sposoby:

### Top-down (memoizacja)

Podejście top-down zachowuje naturalną strukturę rekurencyjną problemu, dodając zapamiętywanie wyników:

```
ALGORYTM DP_TopDown(problem)
  memo ← pusta tablica/słownik

  FUNKCJA Rozwiąż(podproblem):
    JEŚLI podproblem ∈ memo:
      ZWRÓĆ memo[podproblem]
    JEŚLI podproblem jest przypadkiem bazowym:
      wynik ← wartość bazowa
    W PRZECIWNYM RAZIE:
      wynik ← kombinacja Rozwiąż(mniejsze podproblemy)
    memo[podproblem] ← wynik
    ZWRÓĆ wynik

  ZWRÓĆ Rozwiąż(problem)
```

**Zalety:**
- Naturalna struktura rekurencyjna — łatwa do zaprojektowania
- Oblicza tylko potrzebne podproblemy (lazy evaluation)
- Łatwiejsze do zaimplementowania, gdy przestrzeń stanów jest nieregularna

**Wady:**
- Narzut stosu rekurencji (ryzyko przepełnienia dla dużych danych)
- Narzut wywołań funkcji i sprawdzania memo

### Bottom-up (tabulacja)

Podejście bottom-up wypełnia tablicę iteracyjnie, od najmniejszych podproblemów:

```
ALGORYTM DP_BottomUp(problem)
  Utwórz tablicę dp odpowiedniego rozmiaru
  Wypełnij przypadki bazowe w dp

  DLA KAŻDEGO podproblemu w porządku rosnącym:
    dp[podproblem] ← kombinacja dp[mniejsze podproblemy]

  ZWRÓĆ dp[problem]
```

**Zalety:**
- Brak narzutu rekurencji — iteracyjne wypełnianie tablicy
- Możliwość optymalizacji pamięci (np. przechowywanie tylko ostatnich wierszy tablicy)
- Zwykle szybsze w praktyce (mniejszy stały współczynnik)

**Wady:**
- Wymaga określenia porządku obliczania podproblemów
- Oblicza wszystkie podproblemy, nawet te niepotrzebne

### Porównanie

| Cecha | Top-down (memoizacja) | Bottom-up (tabulacja) |
|---|---|---|
| Styl | Rekurencyjny | Iteracyjny |
| Kolejność obliczeń | Od problemu do podproblemów | Od podproblemów do problemu |
| Oblicza niepotrzebne podproblemy | Nie (lazy) | Tak (eager) |
| Narzut stosu | Tak | Nie |
| Optymalizacja pamięci | Trudniejsza | Łatwiejsza |
| Łatwość implementacji | Zwykle łatwiejsze | Wymaga analizy zależności |
| Złożoność czasowa | Taka sama (asymptotycznie) | Taka sama (asymptotycznie) |

## Identyfikacja problemów DP

Aby rozpoznać, czy problem nadaje się do rozwiązania programowaniem dynamicznym, należy sprawdzić dwie własności:

### 1. Optymalna podstruktura

Pytanie: Czy optymalne rozwiązanie problemu można zbudować z optymalnych rozwiązań podproblemów?

**Jak sprawdzić:**
- Sformułuj rozwiązanie jako sekwencję decyzji
- Pokaż, że po podjęciu pierwszej (lub ostatniej) decyzji, pozostały problem jest podproblemem tego samego typu
- Pokaż, że optymalne rozwiązanie reszty jest częścią optymalnego rozwiązania całości

### 2. Nakładające się podproblemy

Pytanie: Czy rekurencyjne rozwiązanie wielokrotnie rozwiązuje te same podproblemy?

**Jak sprawdzić:**
- Narysuj drzewo rekurencji
- Sprawdź, czy te same węzły (podproblemy) pojawiają się wielokrotnie
- Jeśli podproblemy są rozłączne → użyj „dziel i zwyciężaj" zamiast DP

### Schemat projektowania algorytmu DP

```
PROJEKTOWANIE ALGORYTMU DP — 4 KROKI

┌─────────────────────────────────────────────────────┐
│ 1. ZDEFINIUJ PODPROBLEMY                            │
│    Określ, co oznacza dp[i] (lub dp[i][j] itd.)    │
│    Ile jest podproblemów? → rozmiar tablicy         │
│                                                     │
│ 2. ZAPISZ REKURENCJĘ (równanie Bellmana)            │
│    dp[i] = f(dp[mniejsze podproblemy])              │
│    Określ przypadki bazowe                          │
│                                                     │
│ 3. OKREŚL PORZĄDEK OBLICZEŃ                         │
│    W jakiej kolejności wypełniać tablicę?           │
│    Każdy podproblem musi zależeć od już obliczonych │
│                                                     │
│ 4. ODCZYTAJ WYNIK                                   │
│    Gdzie w tablicy znajduje się odpowiedź?          │
│    (opcjonalnie) Odtwórz rozwiązanie (backtracking) │
└─────────────────────────────────────────────────────┘
```

## Złożoność algorytmów DP

Złożoność czasowa algorytmu DP wynika z dwóch czynników:

$T(n) = (\text{liczba podproblemów}) \times (\text{czas na podproblem})$

- **Liczba podproblemów** — rozmiar tablicy DP (np. $n$, $n^2$, $n \times W$)
- **Czas na podproblem** — czas obliczenia jednej komórki tablicy (zwykle $O(1)$ lub $O(n)$)

Złożoność pamięciowa to rozmiar tablicy DP, choć często można ją zredukować, przechowując tylko potrzebne wiersze/kolumny.

### Porównanie z podejściem naiwnym

| Problem | Brute force | DP |
|---|---|---|
| Fibonacci | $O(2^n)$ | $O(n)$ |
| Problem plecakowy 0-1 | $O(2^n)$ | $O(nW)$ |
| LCS | $O(2^{m+n})$ | $O(mn)$ |
| Mnożenie łańcucha macierzy | $O(4^n / n^{3/2})$ | $O(n^3)$ |

## Przykłady

### Przykład 1: Ciąg Fibonacciego

Ciąg Fibonacciego to klasyczny przykład ilustrujący ideę programowania dynamicznego.

**Definicja:** $F(0) = 0,\; F(1) = 1,\; F(n) = F(n-1) + F(n-2)$ dla $n \geq 2$

#### Podejście naiwne (rekurencja bez memoizacji)

```
FUNKCJA Fib_Naiwne(n):
  JEŚLI n ≤ 1:
    ZWRÓĆ n
  ZWRÓĆ Fib_Naiwne(n-1) + Fib_Naiwne(n-2)
```

Złożoność: $O(2^n)$ — wykładnicza, bo te same podproblemy są obliczane wielokrotnie.

Drzewo rekurencji dla $F(5)$:

```
                    F(5)
                   /    \
               F(4)      F(3)
              /    \      /   \
          F(3)    F(2)  F(2)  F(1)
         /   \   /  \   /  \
      F(2) F(1) F(1) F(0) F(1) F(0)
      / \
   F(1) F(0)
```

Widać, że $F(3)$ jest obliczane 2 razy, $F(2)$ — 3 razy, $F(1)$ — 5 razy.

#### Podejście top-down (memoizacja)

```
FUNKCJA Fib_Memo(n):
  memo ← tablica [-1, -1, ..., -1] rozmiaru n+1
  
  FUNKCJA F(k):
    JEŚLI memo[k] ≠ -1:
      ZWRÓĆ memo[k]
    JEŚLI k ≤ 1:
      memo[k] ← k
    W PRZECIWNYM RAZIE:
      memo[k] ← F(k-1) + F(k-2)
    ZWRÓĆ memo[k]
  
  ZWRÓĆ F(n)
```

Złożoność: $O(n)$ czasowa, $O(n)$ pamięciowa.

#### Podejście bottom-up (tabulacja)

```
FUNKCJA Fib_Tab(n):
  JEŚLI n ≤ 1:
    ZWRÓĆ n
  dp ← tablica rozmiaru n+1
  dp[0] ← 0
  dp[1] ← 1
  DLA i ← 2 DO n:
    dp[i] ← dp[i-1] + dp[i-2]
  ZWRÓĆ dp[n]
```

Złożoność: $O(n)$ czasowa, $O(n)$ pamięciowa.

#### Optymalizacja pamięci

Ponieważ $F(n)$ zależy tylko od dwóch poprzednich wartości, wystarczy przechowywać dwie zmienne:

```
FUNKCJA Fib_Opt(n):
  JEŚLI n ≤ 1:
    ZWRÓĆ n
  prev2 ← 0
  prev1 ← 1
  DLA i ← 2 DO n:
    bieżący ← prev1 + prev2
    prev2 ← prev1
    prev1 ← bieżący
  ZWRÓĆ prev1
```

Złożoność: $O(n)$ czasowa, $O(1)$ pamięciowa.

### Przykład 2: Problem plecakowy 0-1 (Knapsack)

**Problem:** Danych jest $n$ przedmiotów, każdy o wadze $w_i$ i wartości $v_i$, oraz plecak o pojemności $W$. Wybrać podzbiór przedmiotów maksymalizujący łączną wartość, nie przekraczając pojemności plecaka. Każdy przedmiot można wziąć co najwyżej raz.

#### Krok 1: Definicja podproblemów

$\text{dp}[i][j]$ = maksymalna wartość, jaką można uzyskać z pierwszych $i$ przedmiotów przy pojemności plecaka $j$.

#### Krok 2: Rekurencja

Dla przedmiotu $i$ mamy dwie opcje — wziąć go lub nie:

$$\text{dp}[i][j] = \begin{cases} \text{dp}[i-1][j] & \text{jeśli } w_i > j \text{ (przedmiot nie mieści się)} \\ \max\left(\text{dp}[i-1][j],\; \text{dp}[i-1][j - w_i] + v_i\right) & \text{jeśli } w_i \leq j \end{cases}$$

Przypadek bazowy: $\text{dp}[0][j] = 0$ dla każdego $j$ (brak przedmiotów → wartość 0).

#### Krok 3: Pseudokod (bottom-up)

```
ALGORYTM Plecak01(w[], v[], n, W)
  Wejście: wagi w[1..n], wartości v[1..n], liczba przedmiotów n, pojemność W
  Wyjście: maksymalna wartość

  dp ← tablica (n+1) × (W+1) wypełniona zerami

  DLA i ← 1 DO n:
    DLA j ← 0 DO W:
      JEŚLI w[i] > j:
        dp[i][j] ← dp[i-1][j]          // nie bierzemy przedmiotu i
      W PRZECIWNYM RAZIE:
        dp[i][j] ← max(dp[i-1][j],      // nie bierzemy
                        dp[i-1][j - w[i]] + v[i])  // bierzemy
  
  ZWRÓĆ dp[n][W]
```

Złożoność: $O(nW)$ czasowa, $O(nW)$ pamięciowa (pseudowielomianowa — $W$ jest wartością, nie rozmiarem wejścia).

#### Krok 4: Przykład z tablicą DP

Dane: $n = 4$ przedmioty, pojemność $W = 7$

| Przedmiot | Waga $w_i$ | Wartość $v_i$ |
|---|---|---|
| 1 | 1 | 1 |
| 2 | 3 | 4 |
| 3 | 4 | 5 |
| 4 | 5 | 7 |

Tablica DP:

| $i \backslash j$ | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
|---|---|---|---|---|---|---|---|---|
| 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| 1 | 0 | **1** | 1 | 1 | 1 | 1 | 1 | 1 |
| 2 | 0 | 1 | 1 | **4** | **5** | 5 | 5 | 5 |
| 3 | 0 | 1 | 1 | 4 | 5 | **6** | 6 | **9** |
| 4 | 0 | 1 | 1 | 4 | 5 | 7 | **8** | 9 |

Wynik: $\text{dp}[4][7] = 9$ (bierzemy przedmioty 2 i 3: waga $3 + 4 = 7$, wartość $4 + 5 = 9$).

#### Odtwarzanie rozwiązania (backtracking)

```
ALGORYTM OdtwórzRozwiązanie(dp, w[], n, W)
  wybrane ← pusta lista
  j ← W
  DLA i ← n W DÓŁ DO 1:
    JEŚLI dp[i][j] ≠ dp[i-1][j]:
      // przedmiot i został wybrany
      DODAJ i DO wybrane
      j ← j - w[i]
  ZWRÓĆ wybrane
```

### Przykład 3: Najdłuższy wspólny podciąg (LCS)

**Problem:** Dane są dwa ciągi $X = x_1 x_2 \ldots x_m$ i $Y = y_1 y_2 \ldots y_n$. Znaleźć najdłuższy podciąg (subsequence) wspólny dla obu ciągów. Podciąg nie musi być spójny — elementy muszą występować w tej samej kolejności, ale nie muszą być sąsiednie.

#### Krok 1: Definicja podproblemów

$\text{dp}[i][j]$ = długość najdłuższego wspólnego podciągu ciągów $X[1..i]$ i $Y[1..j]$.

#### Krok 2: Rekurencja

$$\text{dp}[i][j] = \begin{cases} 0 & \text{jeśli } i = 0 \text{ lub } j = 0 \\ \text{dp}[i-1][j-1] + 1 & \text{jeśli } x_i = y_j \\ \max(\text{dp}[i-1][j],\; \text{dp}[i][j-1]) & \text{jeśli } x_i \neq y_j \end{cases}$$

Intuicja:
- Jeśli ostatnie znaki się zgadzają ($x_i = y_j$), to ten znak jest częścią LCS — dodajemy 1 do LCS prefiksów bez tych znaków
- Jeśli się nie zgadzają, LCS jest dłuższym z: LCS bez ostatniego znaku $X$ lub LCS bez ostatniego znaku $Y$

#### Krok 3: Pseudokod (bottom-up)

```
ALGORYTM LCS(X, Y)
  Wejście: ciągi X[1..m], Y[1..n]
  Wyjście: długość najdłuższego wspólnego podciągu

  m ← długość(X)
  n ← długość(Y)
  dp ← tablica (m+1) × (n+1) wypełniona zerami

  DLA i ← 1 DO m:
    DLA j ← 1 DO n:
      JEŚLI X[i] = Y[j]:
        dp[i][j] ← dp[i-1][j-1] + 1
      W PRZECIWNYM RAZIE:
        dp[i][j] ← max(dp[i-1][j], dp[i][j-1])

  ZWRÓĆ dp[m][n]
```

Złożoność: $O(mn)$ czasowa, $O(mn)$ pamięciowa (można zredukować do $O(\min(m,n))$).

#### Krok 4: Przykład z tablicą DP

$X = \text{ABCBDAB}$, $Y = \text{BDCABA}$

| $i \backslash j$ | ∅ | B | D | C | A | B | A |
|---|---|---|---|---|---|---|---|
| ∅ | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| A | 0 | 0 | 0 | 0 | **1** | 1 | 1 |
| B | 0 | **1** | 1 | 1 | 1 | **2** | 2 |
| C | 0 | 1 | 1 | **2** | 2 | 2 | 2 |
| B | 0 | 1 | 1 | 2 | 2 | **3** | 3 |
| D | 0 | 1 | **2** | 2 | 2 | 3 | 3 |
| A | 0 | 1 | 2 | 2 | **3** | 3 | **4** |
| B | 0 | 1 | 2 | 2 | 3 | **4** | 4 |

Wynik: $\text{dp}[7][6] = 4$. Jeden z najdłuższych wspólnych podciągów to **BCBA** (lub BCAB, BDAB).

#### Odtwarzanie LCS (backtracking)

```
ALGORYTM OdtwórzLCS(dp, X, Y, i, j)
  JEŚLI i = 0 LUB j = 0:
    ZWRÓĆ ""
  JEŚLI X[i] = Y[j]:
    ZWRÓĆ OdtwórzLCS(dp, X, Y, i-1, j-1) + X[i]
  JEŚLI dp[i-1][j] ≥ dp[i][j-1]:
    ZWRÓĆ OdtwórzLCS(dp, X, Y, i-1, j)
  W PRZECIWNYM RAZIE:
    ZWRÓĆ OdtwórzLCS(dp, X, Y, i, j-1)
```

## Programowanie dynamiczne a inne techniki

### DP vs „dziel i zwyciężaj" (divide and conquer)

| Cecha | Dziel i zwyciężaj | Programowanie dynamiczne |
|---|---|---|
| Podproblemy | Rozłączne | Nakładające się |
| Zapamiętywanie | Nie | Tak (memo/tablica) |
| Podejście | Top-down (rekurencja) | Top-down lub bottom-up |
| Przykłady | Mergesort, Quicksort | Fibonacci, Plecak, LCS |

### DP vs algorytmy zachłanne (greedy)

| Cecha | Algorytm zachłanny | Programowanie dynamiczne |
|---|---|---|
| Decyzje | Lokalne, nieodwracalne | Globalne, rozważa wszystkie opcje |
| Gwarancja optymalności | Tylko dla problemów z własnością zachłannego wyboru | Zawsze (jeśli problem ma optymalną podstrukturę) |
| Złożoność | Zwykle mniejsza | Zwykle większa |
| Przykład | Plecak ułamkowy | Plecak 0-1 |

## Podsumowanie

1. **Programowanie dynamiczne** to technika algorytmiczna polegająca na rozwiązywaniu problemu przez podział na nakładające się podproblemy, rozwiązanie każdego dokładnie raz i zapamiętanie wyniku. Wymaga dwóch własności: optymalnej podstruktury i nakładających się podproblemów.

2. **Zasada optymalności Bellmana** stanowi fundament DP — optymalne rozwiązanie problemu zawiera optymalne rozwiązania podproblemów. Pozwala to zapisać rekurencję (równanie Bellmana) definiującą optymalne rozwiązanie.

3. Dwa podejścia implementacyjne: **top-down (memoizacja)** — rekurencja z zapamiętywaniem wyników, oblicza tylko potrzebne podproblemy; **bottom-up (tabulacja)** — iteracyjne wypełnianie tablicy od najmniejszych podproblemów, bez narzutu rekurencji.

4. Projektowanie algorytmu DP wymaga czterech kroków: (1) zdefiniowanie podproblemów, (2) zapisanie rekurencji z przypadkami bazowymi, (3) określenie porządku obliczeń, (4) odczytanie wyniku i opcjonalne odtworzenie rozwiązania.

5. Złożoność algorytmu DP to iloczyn liczby podproblemów i czasu na podproblem. DP redukuje złożoność z wykładniczej do wielomianowej (lub pseudowielomianowej) w wielu klasycznych problemach: Fibonacci $O(2^n) \to O(n)$, plecak 0-1 $O(2^n) \to O(nW)$, LCS $O(2^{m+n}) \to O(mn)$.

## Powiązane pytania

- [Pytanie 27: Złożoność obliczeniowa](27-zlozonosc-obliczeniowa.md)
- [Pytanie 46: Algorytm zachłanny](46-algorytm-zachlanny.md)
