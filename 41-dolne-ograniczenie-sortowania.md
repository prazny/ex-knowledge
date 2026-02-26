# Pytanie 41: Czy możliwe jest skonstruowanie algorytmu sortowania opartego o porównywanie sortowanych obiektów, który miałby złożoność obliczeniową lepszą niż O(n lg n)?

## Kluczowe pojęcia

- **Dolne ograniczenie (lower bound)** — minimalna liczba operacji, jaką musi wykonać każdy algorytm rozwiązujący dany problem w modelu obliczeniowym. Dolne ograniczenie $\Omega(f(n))$ oznacza, że nie istnieje algorytm w danym modelu, który rozwiązuje problem w czasie asymptotycznie mniejszym niż $f(n)$. Dla sortowania porównawczego dolne ograniczenie wynosi $\Omega(n \log n)$ — jest to fundamentalny wynik teorii algorytmów.
- **Drzewo decyzyjne (decision tree)** — model obliczeniowy reprezentujący działanie algorytmu sortowania porównawczego jako pełne drzewo binarne. Każdy węzeł wewnętrzny odpowiada porównaniu dwóch elementów ($a_i \leq a_j$?), każda krawędź odpowiada wynikowi porównania (tak/nie), a każdy liść odpowiada jednej permutacji wejścia — konkretnemu uporządkowaniu elementów. Wysokość drzewa decyzyjnego to liczba porównań w najgorszym przypadku.
- **Sortowanie porównawcze (comparison-based sorting)** — klasa algorytmów sortowania, w których jedyną operacją pozwalającą uzyskać informację o porządku elementów jest porównanie pary elementów ($a_i < a_j$, $a_i \leq a_j$, $a_i = a_j$). Do tej klasy należą: mergesort, quicksort, heapsort, insertion sort, bubble sort. Algorytmy porównawcze nie mogą sortować szybciej niż $\Omega(n \log n)$ w najgorszym przypadku.
- **Permutacja i $n!$** — zbiór wszystkich możliwych uporządkowań $n$ elementów ma moc $n!$ (silnia). Algorytm sortowania musi być w stanie rozróżnić każdą z $n!$ permutacji, ponieważ dla każdej z nich wynik sortowania jest inny. Stąd drzewo decyzyjne musi mieć co najmniej $n!$ liści.
- **Logarytm silni** — kluczowa wielkość w dowodzie dolnego ograniczenia. Ze wzoru Stirlinga: $\log_2(n!) = \Theta(n \log n)$. Dokładniej: $\log_2(n!) = n \log_2 n - n \log_2 e + O(\log n)$. Ponieważ drzewo binarne o $n!$ liściach ma wysokość co najmniej $\lceil \log_2(n!) \rceil$, każdy algorytm porównawczy musi wykonać co najmniej $\Omega(n \log n)$ porównań.
- **Sortowanie nieoparte na porównaniach (non-comparison sort)** — klasa algorytmów sortowania, które nie opierają się wyłącznie na porównywaniu par elementów, lecz wykorzystują dodatkowe informacje o strukturze danych (np. zakres wartości, liczbę cyfr). Algorytmy te mogą osiągnąć złożoność liniową $O(n)$, omijając dolne ograniczenie $\Omega(n \log n)$, ponieważ działają w innym modelu obliczeniowym. Przykłady: counting sort, radix sort, bucket sort.

## Model drzewa decyzyjnego

### Definicja modelu

Drzewo decyzyjne to abstrakcyjny model opisujący działanie dowolnego algorytmu sortowania porównawczego. Formalizuje on sekwencję porównań wykonywanych przez algorytm.

**Definicja formalna:** Drzewo decyzyjne dla sortowania $n$ elementów to drzewo binarne $T$, w którym:

1. **Węzeł wewnętrzny** oznaczony $a_i : a_j$ reprezentuje porównanie elementu $a_i$ z elementem $a_j$
2. **Lewe poddrzewo** odpowiada przypadkowi $a_i \leq a_j$
3. **Prawe poddrzewo** odpowiada przypadkowi $a_i > a_j$
4. **Liść** jest oznaczony permutacją $\pi = \langle \pi(1), \pi(2), \ldots, \pi(n) \rangle$, która opisuje wynikowy porządek elementów

Każda ścieżka od korzenia do liścia odpowiada jednej możliwej sekwencji porównań i ich wyników dla pewnego wejścia. Długość najdłuższej ścieżki (wysokość drzewa) to złożoność algorytmu w najgorszym przypadku.

### Własności drzewa decyzyjnego

Aby algorytm sortowania był **poprawny**, drzewo decyzyjne musi spełniać:

1. **Kompletność** — dla każdej permutacji wejściowej istnieje co najmniej jeden liść odpowiadający tej permutacji. Ponieważ istnieje $n!$ permutacji $n$ elementów, drzewo musi mieć co najmniej $n!$ liści:

$$l \geq n!$$

gdzie $l$ to liczba liści drzewa.

2. **Osiągalność** — każdy liść musi być osiągalny z korzenia (istnieje wejście, które prowadzi do tego liścia).

3. **Determinizm** — w każdym węźle wewnętrznym wynik porównania jednoznacznie określa, do którego poddrzewa przechodzimy.

### Związek wysokości drzewa z liczbą liści

Fundamentalna własność drzew binarnych:

> Drzewo binarne o wysokości $h$ ma co najwyżej $2^h$ liści.

**Dowód (przez indukcję):**
- Baza: drzewo o wysokości $h = 0$ ma $1 = 2^0$ liść ✓
- Krok: drzewo o wysokości $h$ składa się z korzenia i dwóch poddrzew o wysokości co najwyżej $h - 1$. Liczba liści $\leq 2^{h-1} + 2^{h-1} = 2^h$ ✓

Stąd, jeśli drzewo ma $l$ liści, to jego wysokość spełnia:

$$h \geq \lceil \log_2 l \rceil$$

## Dowód dolnego ograniczenia $\Omega(n \log n)$

### Twierdzenie

**Twierdzenie:** Każdy algorytm sortowania porównawczego wymaga $\Omega(n \log n)$ porównań w najgorszym przypadku.

### Dowód

Niech $A$ będzie dowolnym algorytmem sortowania porównawczego dla $n$ elementów i niech $T$ będzie jego drzewem decyzyjnym.

**Krok 1.** Algorytm $A$ musi poprawnie sortować każdą z $n!$ permutacji wejściowych. Zatem drzewo $T$ musi mieć co najmniej $n!$ liści:

$$l \geq n!$$

**Krok 2.** Drzewo binarne o wysokości $h$ ma co najwyżej $2^h$ liści, więc:

$$2^h \geq l \geq n!$$

**Krok 3.** Logarytmując obie strony:

$$h \geq \log_2(n!)$$

**Krok 4.** Oszacowanie $\log_2(n!)$ od dołu. Korzystamy z nierówności:

$$n! = n \cdot (n-1) \cdot (n-2) \cdots 2 \cdot 1 \geq \left(\frac{n}{2}\right)^{n/2}$$

ponieważ co najmniej $n/2$ czynników jest nie mniejszych niż $n/2$. Zatem:

$$\log_2(n!) \geq \frac{n}{2} \log_2 \frac{n}{2} = \frac{n}{2} \log_2 n - \frac{n}{2}$$

Stąd:

$$h \geq \frac{n}{2} \log_2 n - \frac{n}{2} = \Omega(n \log n)$$

**Krok 5 (dokładniejsze oszacowanie — wzór Stirlinga).** Przybliżenie Stirlinga:

$$n! \approx \sqrt{2\pi n} \left(\frac{n}{e}\right)^n$$

Logarytmując:

$$\log_2(n!) = n \log_2 n - n \log_2 e + \frac{1}{2}\log_2(2\pi n) = n \log_2 n - \Theta(n)$$

Zatem:

$$h \geq \log_2(n!) = n \log_2 n - \Theta(n) = \Omega(n \log n) \qquad \blacksquare$$

### Interpretacja wyniku

Dolne ograniczenie $\Omega(n \log n)$ oznacza, że:

1. **Żaden algorytm porównawczy** — niezależnie od tego, jak sprytnie dobiera porównania — nie może sortować $n$ elementów w mniej niż $\Omega(n \log n)$ porównań w najgorszym przypadku
2. Algorytmy takie jak **mergesort** ($O(n \log n)$ w najgorszym przypadku) i **heapsort** ($O(n \log n)$ w najgorszym przypadku) są **asymptotycznie optymalne** — osiągają dolne ograniczenie
3. Dolne ograniczenie dotyczy **najgorszego przypadku**. W przypadku średnim dolne ograniczenie również wynosi $\Omega(n \log n)$ (dowód analogiczny, z użyciem teorii informacji)

### Dolne ograniczenie a konkretne algorytmy

| Algorytm | Najgorszy przypadek | Osiąga dolne ograniczenie? |
|---|---|---|
| Mergesort | $O(n \log n)$ | ✓ Tak — optymalny |
| Heapsort | $O(n \log n)$ | ✓ Tak — optymalny |
| Quicksort | $O(n^2)$ | ✗ Nie (najgorszy przypadek) |
| Quicksort (średni) | $O(n \log n)$ | ✓ Tak (w średnim przypadku) |
| Insertion sort | $O(n^2)$ | ✗ Nie |
| Bubble sort | $O(n^2)$ | ✗ Nie |

## Algorytmy sortowania nieoporównawcze

Dolne ograniczenie $\Omega(n \log n)$ dotyczy wyłącznie algorytmów porównawczych. Algorytmy, które wykorzystują dodatkowe informacje o danych (np. zakres wartości), mogą sortować w czasie liniowym $O(n)$.

### Counting sort (sortowanie przez zliczanie)

**Idea:** Jeśli elementy są liczbami całkowitymi z zakresu $[0, k]$, to zamiast porównywać elementy, zliczamy wystąpienia każdej wartości.

**Założenia:**
- Elementy wejściowe to liczby całkowite z zakresu $[0, k]$
- $k = O(n)$ (zakres wartości jest liniowy względem liczby elementów)

```
ALGORYTM CountingSort(A, n, k)
  Wejście: tablica A[1..n] z elementami z zakresu [0, k]
  Wyjście: posortowana tablica B[1..n]

  // Krok 1: Inicjalizacja tablicy zliczającej
  C ← tablica [0..k] wypełniona zerami

  // Krok 2: Zliczanie wystąpień
  DLA i ← 1 DO n:
    C[A[i]] ← C[A[i]] + 1
  // C[j] = liczba elementów równych j

  // Krok 3: Prefiksy sum (pozycje końcowe)
  DLA j ← 1 DO k:
    C[j] ← C[j] + C[j-1]
  // C[j] = liczba elementów ≤ j

  // Krok 4: Budowanie tablicy wynikowej (od końca — stabilność)
  B ← tablica [1..n]
  DLA i ← n W DÓŁ DO 1:
    B[C[A[i]]] ← A[i]
    C[A[i]] ← C[A[i]] - 1

  ZWRÓĆ B
```

**Złożoność:**
- Czasowa: $O(n + k)$ — liniowa, gdy $k = O(n)$
- Pamięciowa: $O(n + k)$
- **Stabilny** — zachowuje względny porządek elementów o tej samej wartości

**Dlaczego omija dolne ograniczenie?** Counting sort nie wykonuje żadnych porównań między elementami wejściowymi. Zamiast tego wykorzystuje wartości elementów jako indeksy tablicy. Działa w modelu RAM (Random Access Machine), a nie w modelu porównawczym — dlatego dolne ograniczenie $\Omega(n \log n)$ go nie dotyczy.

### Radix sort (sortowanie pozycyjne)

**Idea:** Sortuje elementy cyfra po cyfrze, od najmniej znaczącej do najbardziej znaczącej (LSD — Least Significant Digit), używając stabilnego sortowania pomocniczego (np. counting sort) dla każdej pozycji.

**Założenia:**
- Elementy to $d$-cyfrowe liczby w systemie o podstawie $r$ (radix)
- Każda cyfra przyjmuje wartości z zakresu $[0, r-1]$

```
ALGORYTM RadixSort(A, n, d)
  Wejście: tablica A[1..n] z liczbami d-cyfrowymi
  Wyjście: posortowana tablica A

  DLA pozycja ← 1 DO d:    // od najmniej znaczącej cyfry
    Sortuj A stabilnie według cyfry na pozycji 'pozycja'
    // (np. counting sort z k = r - 1)

  ZWRÓĆ A
```

**Złożoność:**
- Czasowa: $O(d \cdot (n + r))$ — jeśli $d$ i $r$ są stałe lub $O(1)$, to złożoność jest $O(n)$
- Dla $n$ liczb z zakresu $[0, n^c - 1]$: wybieramy $r = n$, wtedy $d = c$ i złożoność to $O(cn) = O(n)$

### Bucket sort (sortowanie kubełkowe)

**Idea:** Rozdziela elementy do kubełków na podstawie ich wartości, sortuje każdy kubełek osobno, a następnie łączy wyniki.

**Założenia:**
- Elementy są równomiernie rozłożone w przedziale $[0, 1)$

**Złożoność:** $O(n)$ oczekiwana (przy założeniu równomiernego rozkładu).

### Porównanie algorytmów porównawczych i nieporównawczych

| Cecha | Sortowanie porównawcze | Sortowanie nieporównawcze |
|---|---|---|
| Dolne ograniczenie | $\Omega(n \log n)$ | Brak (może być $O(n)$) |
| Model obliczeniowy | Porównania par elementów | RAM (dostęp do wartości) |
| Wymagania o danych | Brak (dowolne elementy z porządkiem) | Specyficzne (zakres, typ danych) |
| Uniwersalność | Uniwersalne | Ograniczone do specyficznych typów |
| Przykłady | Mergesort, Heapsort, Quicksort | Counting sort, Radix sort, Bucket sort |
| Stabilność | Zależy od algorytmu | Counting sort i Radix sort — stabilne |

## Przykłady

### Przykład 1: Drzewo decyzyjne dla 3 elementów

Rozważmy sortowanie trzech elementów $\langle a_1, a_2, a_3 \rangle$. Istnieje $3! = 6$ permutacji, więc drzewo decyzyjne musi mieć co najmniej 6 liści. Minimalna wysokość: $\lceil \log_2 6 \rceil = 3$.

Poniżej drzewo decyzyjne realizujące sortowanie przez wstawianie (insertion sort) dla 3 elementów:

```
                        a₁ ≤ a₂ ?
                       /          \
                    TAK            NIE
                   /                  \
             a₂ ≤ a₃ ?            a₁ ≤ a₃ ?
            /        \            /        \
         TAK         NIE       TAK         NIE
          |            |         |            |
    [a₁,a₂,a₃]   a₁ ≤ a₃?  [a₂,a₁,a₃]  a₂ ≤ a₃ ?
                  /      \                /      \
               TAK       NIE          TAK       NIE
                |          |            |          |
          [a₁,a₃,a₂] [a₃,a₁,a₂] [a₂,a₃,a₁] [a₃,a₂,a₁]
```

**Analiza:**
- Drzewo ma 6 liści — po jednym dla każdej permutacji ✓
- Wysokość drzewa = 3 — w najgorszym przypadku potrzeba 3 porównań
- Dolne ograniczenie: $\lceil \log_2(3!) \rceil = \lceil \log_2 6 \rceil = \lceil 2{,}585 \rceil = 3$ porównania
- To drzewo jest **optymalne** — osiąga dolne ograniczenie

**Ścieżka przykładowa:** Dla wejścia $\langle 3, 1, 2 \rangle$:
1. $a_1 \leq a_2$? → $3 \leq 1$? → NIE → prawe poddrzewo
2. $a_1 \leq a_3$? → $3 \leq 2$? → NIE → prawe poddrzewo
3. $a_2 \leq a_3$? → $1 \leq 2$? → TAK → liść $\langle a_2, a_3, a_1 \rangle = \langle 1, 2, 3 \rangle$ ✓

### Przykład 2: Counting sort jako kontrprzykład dla dolnego ograniczenia

Pokażemy, że counting sort sortuje w czasie $O(n)$, co jest lepsze niż $\Omega(n \log n)$.

**Dane wejściowe:** $A = [4, 2, 2, 8, 3, 3, 1]$, $n = 7$, $k = 8$

**Krok 1 — Zliczanie wystąpień:**

| Wartość | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
|---|---|---|---|---|---|---|---|---|---|
| $C[j]$ (zliczanie) | 0 | 1 | 2 | 2 | 1 | 0 | 0 | 0 | 1 |

**Krok 2 — Prefiksy sum (pozycje końcowe):**

| Wartość | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
|---|---|---|---|---|---|---|---|---|---|
| $C[j]$ (prefiksy) | 0 | 1 | 3 | 5 | 6 | 6 | 6 | 6 | 7 |

Interpretacja: $C[j]$ = liczba elementów $\leq j$, czyli element o wartości $j$ trafia na pozycję $C[j]$.

**Krok 3 — Budowanie tablicy wynikowej (od końca):**

| Iteracja ($i$) | $A[i]$ | $C[A[i]]$ | $B[C[A[i]]] \leftarrow A[i]$ | Nowe $C[A[i]]$ |
|---|---|---|---|---|
| $i = 7$ | 1 | 1 | $B[1] = 1$ | $C[1] = 0$ |
| $i = 6$ | 3 | 5 | $B[5] = 3$ | $C[3] = 4$ |
| $i = 5$ | 3 | 4 | $B[4] = 3$ | $C[3] = 3$ |
| $i = 4$ | 8 | 7 | $B[7] = 8$ | $C[8] = 6$ |
| $i = 3$ | 2 | 3 | $B[3] = 2$ | $C[2] = 2$ |
| $i = 2$ | 2 | 2 | $B[2] = 2$ | $C[2] = 1$ |
| $i = 1$ | 4 | 6 | $B[6] = 4$ | $C[4] = 5$ |

**Wynik:** $B = [1, 2, 2, 3, 3, 4, 8]$ ✓

**Analiza złożoności:**
- Krok 1 (inicjalizacja $C$): $O(k)$
- Krok 2 (zliczanie): $O(n)$
- Krok 3 (prefiksy): $O(k)$
- Krok 4 (budowanie $B$): $O(n)$
- **Łącznie:** $O(n + k)$

Dla $k = O(n)$ złożoność wynosi $O(n)$ — **liniowa**, co jest lepsze niż $\Omega(n \log n)$.

**Dlaczego to nie jest sprzeczność z dolnym ograniczeniem?**

Counting sort **nie jest algorytmem porównawczym** — nigdzie nie porównuje elementów $a_i$ z $a_j$. Zamiast tego używa wartości elementów jako indeksów tablicy (operacja $C[A[i]]$). Działa w modelu RAM, nie w modelu drzewa decyzyjnego. Dolne ograniczenie $\Omega(n \log n)$ dotyczy wyłącznie modelu porównawczego, więc counting sort go nie narusza.

**Ograniczenia counting sort:**
- Wymaga znajomości zakresu wartości $[0, k]$
- Dla dużego $k$ (np. $k = n^2$) złożoność rośnie do $O(n + k)$, co może być gorsze niż $O(n \log n)$
- Działa tylko dla danych dyskretnych (liczby całkowite lub elementy z ograniczonego zbioru)

## Podsumowanie

1. **Odpowiedź na pytanie: NIE** — nie jest możliwe skonstruowanie algorytmu sortowania porównawczego o złożoności lepszej niż $O(n \log n)$ w najgorszym przypadku. Jest to udowodnione matematycznie za pomocą modelu drzewa decyzyjnego.

2. **Model drzewa decyzyjnego** formalizuje działanie algorytmu porównawczego jako drzewo binarne, w którym węzły to porównania, a liście to permutacje. Poprawny algorytm musi mieć co najmniej $n!$ liści, co wymusza wysokość drzewa $h \geq \log_2(n!) = \Omega(n \log n)$.

3. **Dowód** opiera się na dwóch faktach: (1) drzewo binarne o wysokości $h$ ma co najwyżej $2^h$ liści, (2) $\log_2(n!) = \Theta(n \log n)$ (ze wzoru Stirlinga). Stąd minimalna liczba porównań w najgorszym przypadku to $\Omega(n \log n)$.

4. **Algorytmy optymalne** — mergesort i heapsort osiągają dolne ograniczenie $O(n \log n)$ w najgorszym przypadku, co czyni je asymptotycznie optymalnymi wśród algorytmów porównawczych.

5. **Algorytmy nieporównawcze** (counting sort, radix sort, bucket sort) mogą sortować w czasie liniowym $O(n)$, ale wymagają dodatkowych założeń o danych (zakres wartości, typ danych). Działają w modelu RAM, nie w modelu porównawczym, dlatego dolne ograniczenie $\Omega(n \log n)$ ich nie dotyczy.

## Powiązane pytania

- [Pytanie 27: Złożoność obliczeniowa](27-zlozonosc-obliczeniowa.md)
- [Pytanie 43: Algorytm sortowania](43-algorytm-sortowania.md)