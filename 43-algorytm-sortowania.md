# Pytanie 43: Proszę opisać dowolny algorytm sortowania tablicy i przeanalizować jego złożoność obliczeniową.

## Kluczowe pojęcia

- **Quicksort (sortowanie szybkie)** — algorytm sortowania porównawczego oparty na strategii „dziel i zwyciężaj". Wybiera element zwany pivotem, dzieli tablicę na dwie części (elementy mniejsze i większe od pivota), a następnie rekurencyjnie sortuje obie części. Średnia złożoność czasowa wynosi $O(n \log n)$, ale w najgorszym przypadku (np. tablica już posortowana przy złym wyborze pivota) degraduje się do $O(n^2)$. Sortuje in-place — nie wymaga dodatkowej tablicy wynikowej. Nie jest stabilny.
- **Mergesort (sortowanie przez scalanie)** — algorytm sortowania porównawczego oparty na strategii „dziel i zwyciężaj". Dzieli tablicę na dwie połowy, rekurencyjnie sortuje każdą z nich, a następnie scala dwie posortowane połowy w jedną posortowaną tablicę. Złożoność czasowa wynosi $O(n \log n)$ we wszystkich przypadkach (najlepszy, średni, najgorszy). Jest stabilny, ale wymaga dodatkowej pamięci $O(n)$ na scalanie — nie jest in-place.
- **Heapsort (sortowanie przez kopcowanie)** — algorytm sortowania porównawczego wykorzystujący strukturę kopca binarnego (max-heap). Buduje kopiec z tablicy wejściowej, a następnie wielokrotnie usuwa element maksymalny z korzenia kopca i umieszcza go na końcu tablicy. Złożoność czasowa wynosi $O(n \log n)$ we wszystkich przypadkach. Sortuje in-place ($O(1)$ dodatkowej pamięci), ale nie jest stabilny.
- **Złożoność czasowa** — miara liczby operacji elementarnych (porównań, zamian) wykonywanych przez algorytm w funkcji rozmiaru danych wejściowych $n$. Rozróżniamy złożoność w najlepszym przypadku (best case), średnim przypadku (average case) i najgorszym przypadku (worst case). Dla algorytmów sortowania porównawczego dolne ograniczenie wynosi $\Omega(n \log n)$.
- **Złożoność pamięciowa** — ilość dodatkowej pamięci (poza tablicą wejściową) wymaganej przez algorytm. Algorytmy in-place używają $O(1)$ dodatkowej pamięci (nie licząc stosu rekursji), natomiast algorytmy takie jak mergesort wymagają $O(n)$ dodatkowej pamięci na tablicę pomocniczą.
- **Stabilność (stability)** — algorytm sortowania jest stabilny, jeśli zachowuje względny porządek elementów o równych kluczach. Formalnie: jeśli $a_i = a_j$ i $i < j$ w tablicy wejściowej, to $a_i$ pojawia się przed $a_j$ w tablicy wynikowej. Stabilność jest istotna przy sortowaniu wielokluczowym (np. sortowanie rekordów najpierw po nazwisku, potem po imieniu). Mergesort jest stabilny; quicksort i heapsort — nie.
- **In-place** — algorytm sortowania jest in-place, jeśli używa co najwyżej $O(1)$ dodatkowej pamięci (poza tablicą wejściową i stosem rekursji). Quicksort i heapsort są in-place; mergesort standardowo nie jest (wymaga $O(n)$ pamięci pomocniczej).

## Algorytm 1: Quicksort

### Idea algorytmu

Quicksort działa według strategii „dziel i zwyciężaj" (divide and conquer):

1. **Wybierz pivot** — element podziałowy z tablicy
2. **Partycjonuj** — przeorganizuj tablicę tak, aby elementy mniejsze od pivota znalazły się po lewej stronie, a większe — po prawej
3. **Rekurencja** — rekurencyjnie posortuj lewą i prawą część tablicy

### Pseudokod

```
ALGORYTM QuickSort(A, lo, hi)
  Wejście: tablica A, indeksy lo i hi (zakres do posortowania)
  Wyjście: tablica A posortowana w zakresie [lo..hi]

  JEŚLI lo < hi:
    p ← Partition(A, lo, hi)
    QuickSort(A, lo, p - 1)
    QuickSort(A, p + 1, hi)
```

```
ALGORYTM Partition(A, lo, hi)
  // Schemat partycjonowania Lomuto
  pivot ← A[hi]          // pivot = ostatni element
  i ← lo - 1             // indeks granicy elementów ≤ pivot

  DLA j ← lo DO hi - 1:
    JEŚLI A[j] ≤ pivot:
      i ← i + 1
      ZAMIEŃ A[i] ↔ A[j]

  ZAMIEŃ A[i + 1] ↔ A[hi]   // umieść pivot na właściwej pozycji
  ZWRÓĆ i + 1               // zwróć indeks pivota
```

### Schemat partycjonowania Hoare'a (alternatywny)

```
ALGORYTM Partition_Hoare(A, lo, hi)
  pivot ← A[lo]
  i ← lo - 1
  j ← hi + 1

  POWTARZAJ:
    POWTARZAJ: j ← j - 1  DOPÓKI A[j] > pivot
    POWTARZAJ: i ← i + 1  DOPÓKI A[i] < pivot

    JEŚLI i < j:
      ZAMIEŃ A[i] ↔ A[j]
    W PRZECIWNYM RAZIE:
      ZWRÓĆ j
```

Schemat Hoare'a jest w praktyce szybszy od Lomuto (mniej zamian), ale trudniejszy w implementacji.

### Analiza złożoności quicksort

**Najgorszy przypadek — $O(n^2)$:**

Występuje, gdy pivot jest zawsze elementem minimalnym lub maksymalnym (np. tablica już posortowana, a pivot = ostatni element). Wtedy partycjonowanie dzieli tablicę na podtablicę o rozmiarze $n - 1$ i pustą podtablicę:

$T(n) = T(n - 1) + T(0) + \Theta(n) = T(n - 1) + \Theta(n)$

Rozwiązanie: $T(n) = \Theta(n^2)$

**Najlepszy przypadek — $O(n \log n)$:**

Występuje, gdy pivot zawsze dzieli tablicę na dwie równe połowy:

$T(n) = 2T(n/2) + \Theta(n)$

Z twierdzenia o rekurencji (Master Theorem, przypadek 2): $T(n) = \Theta(n \log n)$

**Średni przypadek — $O(n \log n)$:**

Przy losowym wyborze pivota oczekiwana złożoność wynosi $O(n \log n)$. Nawet podział 9:1 (pivot w 10. percentylu) daje:

$T(n) = T(9n/10) + T(n/10) + \Theta(n) = O(n \log n)$

Głębokość rekursji wynosi $O(\log n)$, ponieważ $\log_{10/9} n = O(\log n)$.

### Strategie wyboru pivota

| Strategia | Opis | Złożoność najgorszego przypadku |
|---|---|---|
| Ostatni element | $\text{pivot} = A[hi]$ | $O(n^2)$ dla posortowanych danych |
| Losowy element | $\text{pivot} = A[\text{random}(lo, hi)]$ | $O(n^2)$ z prawdopodobieństwem $\to 0$ |
| Mediana z trzech | $\text{pivot} = \text{mediana}(A[lo], A[\lfloor(lo+hi)/2\rfloor], A[hi])$ | $O(n^2)$ (rzadko w praktyce) |
| Mediana median | Deterministyczny wybór mediany | $O(n \log n)$ gwarantowane |

W praktyce **mediana z trzech** lub **losowy pivot** eliminują najgorszy przypadek z bardzo wysokim prawdopodobieństwem.

### Właściwości quicksort

| Cecha | Wartość |
|---|---|
| Złożoność czasowa (najgorszy) | $O(n^2)$ |
| Złożoność czasowa (średni) | $O(n \log n)$ |
| Złożoność czasowa (najlepszy) | $O(n \log n)$ |
| Złożoność pamięciowa | $O(\log n)$ (stos rekursji) |
| In-place | Tak |
| Stabilny | Nie |

## Algorytm 2: Mergesort

### Idea algorytmu

Mergesort również stosuje strategię „dziel i zwyciężaj":

1. **Dziel** — podziel tablicę na dwie połowy
2. **Zwyciężaj** — rekurencyjnie posortuj każdą połowę
3. **Scalaj** — scal dwie posortowane połowy w jedną posortowaną tablicę

### Pseudokod

```
ALGORYTM MergeSort(A, lo, hi)
  Wejście: tablica A, indeksy lo i hi
  Wyjście: tablica A posortowana w zakresie [lo..hi]

  JEŚLI lo < hi:
    mid ← ⌊(lo + hi) / 2⌋
    MergeSort(A, lo, mid)
    MergeSort(A, mid + 1, hi)
    Merge(A, lo, mid, hi)
```

```
ALGORYTM Merge(A, lo, mid, hi)
  // Scalanie dwóch posortowanych podtablic A[lo..mid] i A[mid+1..hi]
  n1 ← mid - lo + 1
  n2 ← hi - mid

  // Kopiuj do tablic pomocniczych
  L ← tablica [1..n1]
  R ← tablica [1..n2]
  DLA i ← 1 DO n1: L[i] ← A[lo + i - 1]
  DLA j ← 1 DO n2: R[j] ← A[mid + j]

  // Scalanie
  i ← 1, j ← 1, k ← lo
  DOPÓKI i ≤ n1 ORAZ j ≤ n2:
    JEŚLI L[i] ≤ R[j]:       // ≤ zapewnia stabilność
      A[k] ← L[i]
      i ← i + 1
    W PRZECIWNYM RAZIE:
      A[k] ← R[j]
      j ← j + 1
    k ← k + 1

  // Kopiuj pozostałe elementy
  DOPÓKI i ≤ n1: A[k] ← L[i]; i ← i + 1; k ← k + 1
  DOPÓKI j ≤ n2: A[k] ← R[j]; j ← j + 1; k ← k + 1
```

### Analiza złożoności mergesort

Rekurencja mergesort ma zawsze tę samą strukturę — tablica jest dzielona na dwie równe połowy niezależnie od danych wejściowych:

$T(n) = 2T(n/2) + \Theta(n)$

gdzie $\Theta(n)$ to koszt scalania dwóch podtablic.

Z twierdzenia o rekurencji (Master Theorem, przypadek 2, $a = 2$, $b = 2$, $f(n) = \Theta(n)$):

$T(n) = \Theta(n \log n)$

**Złożoność jest taka sama we wszystkich przypadkach** — najlepszym, średnim i najgorszym — ponieważ podział i scalanie nie zależą od rozkładu danych.

### Drzewo rekursji mergesort

Dla tablicy o rozmiarze $n$:

```
Poziom 0:  [n]                          koszt: n
Poziom 1:  [n/2]     [n/2]              koszt: n
Poziom 2:  [n/4] [n/4] [n/4] [n/4]     koszt: n
  ...
Poziom k:  2^k podproblemów × n/2^k     koszt: n
  ...
Poziom log₂n: [1] [1] ... [1]           koszt: n

Łącznie: log₂n poziomów × n = O(n log n)
```

### Właściwości mergesort

| Cecha | Wartość |
|---|---|
| Złożoność czasowa (najgorszy) | $O(n \log n)$ |
| Złożoność czasowa (średni) | $O(n \log n)$ |
| Złożoność czasowa (najlepszy) | $O(n \log n)$ |
| Złożoność pamięciowa | $O(n)$ (tablica pomocnicza) |
| In-place | Nie |
| Stabilny | Tak |

## Algorytm 3: Heapsort

### Idea algorytmu

Heapsort wykorzystuje strukturę kopca binarnego (binary max-heap):

1. **Buduj kopiec** — przekształć tablicę wejściową w kopiec typu max-heap
2. **Sortuj** — wielokrotnie usuwaj element maksymalny z korzenia kopca i umieszczaj go na końcu tablicy, zmniejszając rozmiar kopca

### Kopiec binarny (max-heap)

Kopiec binarny to drzewo binarne spełniające dwa warunki:
- **Własność kształtu** — drzewo jest prawie pełne (wszystkie poziomy wypełnione, ostatni od lewej)
- **Własność kopca** — wartość każdego węzła jest nie mniejsza niż wartości jego dzieci: $A[\text{parent}(i)] \geq A[i]$

Kopiec jest przechowywany w tablicy, gdzie dla węzła o indeksie $i$ (indeksowanie od 1):
- Rodzic: $\text{parent}(i) = \lfloor i/2 \rfloor$
- Lewe dziecko: $\text{left}(i) = 2i$
- Prawe dziecko: $\text{right}(i) = 2i + 1$

### Pseudokod

```
ALGORYTM HeapSort(A, n)
  Wejście: tablica A[1..n]
  Wyjście: tablica A posortowana rosnąco

  // Faza 1: Budowanie kopca max-heap
  BuildMaxHeap(A, n)

  // Faza 2: Sortowanie
  DLA i ← n W DÓŁ DO 2:
    ZAMIEŃ A[1] ↔ A[i]     // przenieś max na koniec
    heapSize ← i - 1
    MaxHeapify(A, 1, heapSize)
```

```
ALGORYTM BuildMaxHeap(A, n)
  // Buduje max-heap z tablicy A[1..n]
  // Węzły A[⌊n/2⌋+1..n] są liśćmi — już spełniają własność kopca
  DLA i ← ⌊n/2⌋ W DÓŁ DO 1:
    MaxHeapify(A, i, n)
```

```
ALGORYTM MaxHeapify(A, i, heapSize)
  // Przywraca własność kopca w poddrzewie o korzeniu i
  l ← 2i          // lewe dziecko
  r ← 2i + 1      // prawe dziecko
  largest ← i

  JEŚLI l ≤ heapSize ORAZ A[l] > A[largest]:
    largest ← l
  JEŚLI r ≤ heapSize ORAZ A[r] > A[largest]:
    largest ← r

  JEŚLI largest ≠ i:
    ZAMIEŃ A[i] ↔ A[largest]
    MaxHeapify(A, largest, heapSize)   // rekurencyjnie napraw
```

### Analiza złożoności heapsort

**BuildMaxHeap — $O(n)$:**

Choć wywołuje MaxHeapify $O(n)$ razy, a każde wywołanie kosztuje $O(\log n)$, dokładniejsza analiza daje $O(n)$. Na poziomie $h$ (licząc od dołu) jest co najwyżej $\lceil n / 2^{h+1} \rceil$ węzłów, a MaxHeapify na tym poziomie kosztuje $O(h)$:

$\sum_{h=0}^{\lfloor \log n \rfloor} \left\lceil \frac{n}{2^{h+1}} \right\rceil \cdot O(h) = O\left(n \sum_{h=0}^{\infty} \frac{h}{2^h}\right) = O(n \cdot 2) = O(n)$

**HeapSort — $O(n \log n)$:**

Faza sortowania wykonuje $n - 1$ wywołań MaxHeapify, każde o koszcie $O(\log n)$:

$T(n) = O(n) + (n - 1) \cdot O(\log n) = O(n \log n)$

**Złożoność jest taka sama we wszystkich przypadkach** — struktura kopca nie zależy od rozkładu danych wejściowych.

### Właściwości heapsort

| Cecha | Wartość |
|---|---|
| Złożoność czasowa (najgorszy) | $O(n \log n)$ |
| Złożoność czasowa (średni) | $O(n \log n)$ |
| Złożoność czasowa (najlepszy) | $O(n \log n)$ |
| Złożoność pamięciowa | $O(1)$ |
| In-place | Tak |
| Stabilny | Nie |

## Porównanie algorytmów sortowania

### Tabela porównawcza złożoności

| Algorytm | Najlepszy | Średni | Najgorszy | Pamięć | In-place | Stabilny |
|---|---|---|---|---|---|---|
| **Quicksort** | $O(n \log n)$ | $O(n \log n)$ | $O(n^2)$ | $O(\log n)$ | Tak | Nie |
| **Mergesort** | $O(n \log n)$ | $O(n \log n)$ | $O(n \log n)$ | $O(n)$ | Nie | Tak |
| **Heapsort** | $O(n \log n)$ | $O(n \log n)$ | $O(n \log n)$ | $O(1)$ | Tak | Nie |
| Insertion sort | $O(n)$ | $O(n^2)$ | $O(n^2)$ | $O(1)$ | Tak | Tak |
| Selection sort | $O(n^2)$ | $O(n^2)$ | $O(n^2)$ | $O(1)$ | Tak | Nie |
| Bubble sort | $O(n)$ | $O(n^2)$ | $O(n^2)$ | $O(1)$ | Tak | Tak |

### Kiedy który algorytm wybrać?

**Quicksort** — najczęściej stosowany w praktyce. Mimo najgorszego przypadku $O(n^2)$, z losowym pivotem lub medianą z trzech jest bardzo szybki dzięki:
- Małej stałej ukrytej w notacji $O$
- Dobrej lokalizacji pamięci podręcznej (cache-friendly — operuje na ciągłych fragmentach tablicy)
- Sortowaniu in-place

**Mergesort** — preferowany, gdy:
- Wymagana jest stabilność sortowania
- Dane są na liście łączonej (nie wymaga wtedy dodatkowej pamięci $O(n)$)
- Wymagana jest gwarancja $O(n \log n)$ w najgorszym przypadku
- Sortowanie zewnętrzne (dane na dysku)

**Heapsort** — preferowany, gdy:
- Wymagana jest gwarancja $O(n \log n)$ w najgorszym przypadku
- Pamięć jest ograniczona (jedyny algorytm $O(n \log n)$ z $O(1)$ dodatkowej pamięci)
- Nie jest wymagana stabilność

### Stabilność — dlaczego ma znaczenie?

Rozważmy sortowanie rekordów studentów:

```
Dane wejściowe (posortowane po imieniu):
  (Anna, 5.0), (Jan, 4.0), (Jan, 5.0), (Zofia, 3.0)

Sortowanie po ocenie (stabilne — mergesort):
  (Zofia, 3.0), (Jan, 4.0), (Anna, 5.0), (Jan, 5.0)
  ↑ Kolejność Janów zachowana: Jan(4.0) przed Jan(5.0)

Sortowanie po ocenie (niestabilne — quicksort/heapsort):
  (Zofia, 3.0), (Jan, 4.0), (Jan, 5.0), (Anna, 5.0)
  ↑ Kolejność Janów może być zmieniona
```

Stabilność pozwala na sortowanie wielokluczowe: najpierw sortujemy po kluczu drugorzędnym, potem po pierwszorzędnym — wynik jest poprawnie posortowany po obu kluczach.

## Przykłady

### Przykład 1: Quicksort krok po kroku

Sortujemy tablicę $A = [6, 3, 8, 1, 5, 2, 7, 4]$ algorytmem quicksort (schemat Lomuto, pivot = ostatni element).

**Wywołanie 1: QuickSort(A, 0, 7)**

Pivot = $A[7] = 4$. Partycjonowanie:

```
Tablica:  [6, 3, 8, 1, 5, 2, 7, 4]
                                  ↑ pivot = 4
i = -1

j=0: A[0]=6 > 4  → nic
j=1: A[1]=3 ≤ 4  → i=0, zamień A[0]↔A[1] → [3, 6, 8, 1, 5, 2, 7, 4]
j=2: A[2]=8 > 4  → nic
j=3: A[3]=1 ≤ 4  → i=1, zamień A[1]↔A[3] → [3, 1, 8, 6, 5, 2, 7, 4]
j=4: A[4]=5 > 4  → nic
j=5: A[5]=2 ≤ 4  → i=2, zamień A[2]↔A[5] → [3, 1, 2, 6, 5, 8, 7, 4]
j=6: A[6]=7 > 4  → nic

Zamień A[i+1]↔A[7]: A[3]↔A[7] → [3, 1, 2, 4, 5, 8, 7, 6]
                              ↑ pivot na pozycji 3

Wynik partycjonowania: p = 3
  Lewa część:  [3, 1, 2]     (indeksy 0-2, elementy < 4)
  Pivot:       [4]           (indeks 3)
  Prawa część: [5, 8, 7, 6]  (indeksy 4-7, elementy > 4)
```

**Wywołanie 2: QuickSort(A, 0, 2)** — sortowanie lewej części $[3, 1, 2]$

Pivot = $A[2] = 2$. Partycjonowanie:

```
[3, 1, 2]   pivot = 2, i = -1
j=0: A[0]=3 > 2  → nic
j=1: A[1]=1 ≤ 2  → i=0, zamień A[0]↔A[1] → [1, 3, 2]
Zamień A[1]↔A[2] → [1, 2, 3]   p = 1
```

Rekurencja: QuickSort(A, 0, 0) — jeden element, koniec. QuickSort(A, 2, 2) — jeden element, koniec.

**Wywołanie 3: QuickSort(A, 4, 7)** — sortowanie prawej części $[5, 8, 7, 6]$

Pivot = $A[7] = 6$. Partycjonowanie:

```
[5, 8, 7, 6]   pivot = 6, i = 3 (= lo - 1)
j=4: A[4]=5 ≤ 6  → i=4, zamień A[4]↔A[4] → [5, 8, 7, 6]
j=5: A[5]=8 > 6  → nic
j=6: A[6]=7 > 6  → nic
Zamień A[5]↔A[7] → [5, 6, 7, 8]   p = 5
```

Rekurencja: QuickSort(A, 4, 4) — jeden element. QuickSort(A, 6, 7):

```
[7, 8]   pivot = 8, i = 5
j=6: A[6]=7 ≤ 8  → i=6, zamień A[6]↔A[6] → [7, 8]
Zamień A[7]↔A[7] → [7, 8]   p = 7
```

**Wynik końcowy:** $A = [1, 2, 3, 4, 5, 6, 7, 8]$ ✓

### Przykład 2: Mergesort krok po kroku

Sortujemy tablicę $A = [5, 2, 8, 3, 1, 6, 4, 7]$.

```
Faza podziału (dziel):
[5, 2, 8, 3, 1, 6, 4, 7]
         ↓
[5, 2, 8, 3]    [1, 6, 4, 7]
    ↓                ↓
[5, 2]  [8, 3]  [1, 6]  [4, 7]
  ↓       ↓       ↓       ↓
[5] [2] [8] [3] [1] [6] [4] [7]

Faza scalania (zwyciężaj):
[5] [2] → scalanie → [2, 5]
[8] [3] → scalanie → [3, 8]
[1] [6] → scalanie → [1, 6]
[4] [7] → scalanie → [4, 7]

[2, 5] [3, 8] → scalanie → [2, 3, 5, 8]
[1, 6] [4, 7] → scalanie → [1, 4, 6, 7]

[2, 3, 5, 8] [1, 4, 6, 7] → scalanie → [1, 2, 3, 4, 5, 6, 7, 8]
```

Szczegóły ostatniego scalania $[2, 3, 5, 8]$ i $[1, 4, 6, 7]$:

```
L = [2, 3, 5, 8]    R = [1, 4, 6, 7]
i=0, j=0

Krok 1: L[0]=2 vs R[0]=1 → 1 < 2 → wynik: [1],           j=1
Krok 2: L[0]=2 vs R[1]=4 → 2 < 4 → wynik: [1, 2],        i=1
Krok 3: L[1]=3 vs R[1]=4 → 3 < 4 → wynik: [1, 2, 3],     i=2
Krok 4: L[2]=5 vs R[1]=4 → 4 < 5 → wynik: [1, 2, 3, 4],  j=2
Krok 5: L[2]=5 vs R[2]=6 → 5 < 6 → wynik: [1,2,3,4,5],   i=3
Krok 6: L[3]=8 vs R[2]=6 → 6 < 8 → wynik: [1,2,3,4,5,6], j=3
Krok 7: L[3]=8 vs R[3]=7 → 7 < 8 → wynik: [1,2,3,4,5,6,7], j=4
Krok 8: R wyczerpane → kopiuj L[3]=8 → wynik: [1,2,3,4,5,6,7,8]
```

**Wynik końcowy:** $A = [1, 2, 3, 4, 5, 6, 7, 8]$ ✓

### Przykład 3: Heapsort — budowa kopca i sortowanie

Sortujemy tablicę $A = [4, 1, 3, 2, 5]$ ($n = 5$).

**Faza 1: BuildMaxHeap**

Tablica jako drzewo binarne (przed budową kopca):

```
        4
       / \
      1   3
     / \
    2   5
```

Wywołania MaxHeapify od $\lfloor 5/2 \rfloor = 2$ w dół do 1:

```
MaxHeapify(A, 2, 5):  A[2]=1, dzieci: A[4]=2, A[5]=5
  largest = 5 (indeks 5), zamień A[2]↔A[5] → [4, 5, 3, 2, 1]
        4
       / \
      5   3
     / \
    2   1

MaxHeapify(A, 1, 5):  A[1]=4, dzieci: A[2]=5, A[3]=3
  largest = 5 (indeks 2), zamień A[1]↔A[2] → [5, 4, 3, 2, 1]
        5
       / \
      4   3
     / \
    2   1
```

Max-heap zbudowany: $A = [5, 4, 3, 2, 1]$

**Faza 2: Sortowanie**

```
Iteracja i=5: zamień A[1]↔A[5] → [1, 4, 3, 2, | 5]
  MaxHeapify(A, 1, 4): 1→4→2 → [4, 2, 3, 1, | 5]

Iteracja i=4: zamień A[1]↔A[4] → [1, 2, 3, | 4, 5]
  MaxHeapify(A, 1, 3): 1→3 → [3, 2, 1, | 4, 5]

Iteracja i=3: zamień A[1]↔A[3] → [1, 2, | 3, 4, 5]
  MaxHeapify(A, 1, 2): 1→2 → [2, 1, | 3, 4, 5]

Iteracja i=2: zamień A[1]↔A[2] → [1, | 2, 3, 4, 5]
```

**Wynik końcowy:** $A = [1, 2, 3, 4, 5]$ ✓

## Podsumowanie

1. **Quicksort** jest najszybszym algorytmem sortowania w praktyce dzięki małej stałej i dobrej lokalizacji pamięci podręcznej. Średnia złożoność wynosi $O(n \log n)$, ale najgorszy przypadek to $O(n^2)$. Losowy wybór pivota lub mediana z trzech skutecznie eliminują najgorszy przypadek. Sortuje in-place, ale nie jest stabilny.

2. **Mergesort** gwarantuje złożoność $O(n \log n)$ we wszystkich przypadkach i jest stabilny, co czyni go idealnym do sortowania list łączonych i sortowania zewnętrznego. Wadą jest dodatkowa pamięć $O(n)$ wymagana na scalanie.

3. **Heapsort** gwarantuje złożoność $O(n \log n)$ we wszystkich przypadkach i sortuje in-place z $O(1)$ dodatkowej pamięci — jest jedynym algorytmem łączącym obie te cechy. Nie jest stabilny i w praktyce jest wolniejszy od quicksort ze względu na gorsze wykorzystanie pamięci podręcznej.

4. Wszystkie trzy algorytmy osiągają dolne ograniczenie $\Omega(n \log n)$ dla sortowania porównawczego (mergesort i heapsort w najgorszym przypadku, quicksort w średnim). Żaden algorytm porównawczy nie może być asymptotycznie szybszy.

5. Wybór algorytmu zależy od kontekstu: quicksort dla ogólnego zastosowania, mergesort gdy wymagana jest stabilność lub gwarancja złożoności, heapsort gdy pamięć jest ograniczona.

## Powiązane pytania

- [Pytanie 27: Złożoność obliczeniowa](27-zlozonosc-obliczeniowa.md)
- [Pytanie 41: Dolne ograniczenie sortowania O(n log n)](41-dolne-ograniczenie-sortowania.md)
- [Pytanie 42: Listy, tablice, tablice haszujące](42-listy-tablice-hasze.md)