# Pytanie 38: Dla jakich macierzy mają zastosowanie metody iteracyjne rozwiązywania układów równań liniowych? Podać przykłady takich macierzy w modelowaniu pól elektromagnetycznych.

## Kluczowe pojęcia

- **Metoda Jacobiego** — iteracyjna metoda rozwiązywania układu $\mathbf{A}\mathbf{x} = \mathbf{b}$, w której w każdej iteracji nowe przybliżenie $x_i^{(k+1)}$ oblicza się wyłącznie na podstawie wartości z poprzedniej iteracji $\mathbf{x}^{(k)}$. Macierz $\mathbf{A}$ rozkłada się na $\mathbf{A} = \mathbf{D} + \mathbf{L} + \mathbf{U}$, gdzie $\mathbf{D}$ to część diagonalna, $\mathbf{L}$ — dolnotrójkątna, $\mathbf{U}$ — górnotrójkątna (bez diagonali).
- **Metoda Gaussa-Seidela** — modyfikacja metody Jacobiego, w której przy obliczaniu $x_i^{(k+1)}$ wykorzystuje się już obliczone wartości z bieżącej iteracji (dla $j < i$). Dzięki temu metoda zazwyczaj zbiega szybciej niż Jacobi.
- **Metoda gradientów sprzężonych (CG)** — iteracyjna metoda rozwiązywania układów z macierzą symetryczną dodatnio określoną (SPD). Generuje kierunki poszukiwań wzajemnie sprzężone względem $\mathbf{A}$, co gwarantuje zbieżność w co najwyżej $n$ krokach (w arytmetyce dokładnej).
- **Zbieżność metody iteracyjnej** — metoda iteracyjna $\mathbf{x}^{(k+1)} = \mathbf{B}\mathbf{x}^{(k)} + \mathbf{c}$ jest zbieżna wtedy i tylko wtedy, gdy promień spektralny macierzy iteracji $\rho(\mathbf{B}) < 1$. Warunki wystarczające: dominacja diagonalna lub symetria i dodatnia określoność macierzy $\mathbf{A}$.
- **Macierz rzadka** — macierz, w której większość elementów jest zerowa. Typowo: liczba niezerowych elementów jest proporcjonalna do $n$ (a nie $n^2$). Macierze z dyskretyzacji MES/MRS są rzadkie — każdy węzeł siatki jest powiązany tylko z kilkoma sąsiadami.
- **Dominacja diagonalna** — macierz $\mathbf{A}$ jest silnie diagonalnie dominująca, gdy $|a_{ii}| > \sum_{j \neq i} |a_{ij}|$ dla każdego wiersza $i$. Dominacja diagonalna jest warunkiem wystarczającym zbieżności metod Jacobiego i Gaussa-Seidela.

## Warunki zbieżności metod iteracyjnych

### Ogólna postać metody iteracyjnej

Rozwiązujemy układ $\mathbf{A}\mathbf{x} = \mathbf{b}$, gdzie $\mathbf{A} \in \mathbb{R}^{n \times n}$ jest macierzą nieosobliwą. Rozkładamy:

$$\mathbf{A} = \mathbf{M} - \mathbf{N}$$

gdzie $\mathbf{M}$ jest łatwo odwracalna. Iteracja ma postać:

$$\mathbf{x}^{(k+1)} = \mathbf{M}^{-1}\mathbf{N}\mathbf{x}^{(k)} + \mathbf{M}^{-1}\mathbf{b} = \mathbf{B}\mathbf{x}^{(k)} + \mathbf{c}$$

gdzie $\mathbf{B} = \mathbf{M}^{-1}\mathbf{N}$ to **macierz iteracji**.

### Twierdzenie o zbieżności

Metoda iteracyjna jest zbieżna dla dowolnego przybliżenia początkowego $\mathbf{x}^{(0)}$ wtedy i tylko wtedy, gdy:

$$\rho(\mathbf{B}) < 1$$

gdzie $\rho(\mathbf{B}) = \max_i |\lambda_i(\mathbf{B})|$ to **promień spektralny** macierzy iteracji (największa wartość bezwzględna spośród wartości własnych).

### Warunki wystarczające zbieżności

1. **Silna dominacja diagonalna** macierzy $\mathbf{A}$:

$$|a_{ii}| > \sum_{j=1, j \neq i}^{n} |a_{ij}|, \quad i = 1, 2, \ldots, n$$

Gwarantuje zbieżność zarówno metody Jacobiego, jak i Gaussa-Seidela.

2. **Macierz symetryczna dodatnio określona (SPD)**: $\mathbf{A} = \mathbf{A}^T$ i $\mathbf{x}^T\mathbf{A}\mathbf{x} > 0$ dla $\mathbf{x} \neq \mathbf{0}$. Gwarantuje zbieżność metody Gaussa-Seidela i metody CG.

3. **Norma macierzy iteracji**: jeśli $\|\mathbf{B}\| < 1$ dla dowolnej normy macierzowej zgodnej, to metoda jest zbieżna.

### Dla jakich macierzy stosujemy metody iteracyjne?

Metody iteracyjne są preferowane nad metodami bezpośrednimi (np. eliminacja Gaussa) gdy:
- Macierz $\mathbf{A}$ jest **duża** ($n \gg 10^3$)
- Macierz $\mathbf{A}$ jest **rzadka** — metody iteracyjne wymagają głównie mnożenia macierz-wektor, które jest efektywne dla macierzy rzadkich ($O(\text{nnz})$ zamiast $O(n^2)$)
- Macierz spełnia warunki zbieżności (dominacja diagonalna lub SPD)
- Wystarczy przybliżone rozwiązanie z zadaną dokładnością $\varepsilon$

## Metoda Jacobiego

### Idea

Rozkładamy macierz $\mathbf{A} = \mathbf{D} + \mathbf{L} + \mathbf{U}$, gdzie:
- $\mathbf{D}$ — macierz diagonalna (elementy diagonalne $\mathbf{A}$)
- $\mathbf{L}$ — macierz ściśle dolnotrójkątna
- $\mathbf{U}$ — macierz ściśle górnotrójkątna

Wzór iteracyjny wynika z przekształcenia $\mathbf{D}\mathbf{x} = \mathbf{b} - (\mathbf{L} + \mathbf{U})\mathbf{x}$:

$$x_i^{(k+1)} = \frac{1}{a_{ii}} \left( b_i - \sum_{j=1, j \neq i}^{n} a_{ij} x_j^{(k)} \right), \quad i = 1, 2, \ldots, n$$

Macierz iteracji Jacobiego: $\mathbf{B}_J = -\mathbf{D}^{-1}(\mathbf{L} + \mathbf{U})$.

### Algorytm (pseudokod)

```
Wejście: A (macierz n×n), b (wektor), x⁰ (przybliżenie początkowe), ε (tolerancja), max_iter
Wyjście: x (przybliżone rozwiązanie)

x = x⁰
Dla k = 1, 2, ..., max_iter:
    x_nowe = wektor zerowy rozmiaru n
    
    Dla i = 1, 2, ..., n:
        suma = 0
        Dla j = 1, 2, ..., n:
            Jeśli j ≠ i:
                suma = suma + a[i][j] * x[j]
        x_nowe[i] = (b[i] - suma) / a[i][i]
    
    Jeśli ‖x_nowe - x‖ < ε:
        Zwróć x_nowe    // zbieżność osiągnięta
    
    x = x_nowe

Zwróć x    // osiągnięto max_iter bez zbieżności
```

### Właściwości metody Jacobiego

- **Złożoność jednej iteracji**: $O(n \cdot \text{nnz}_\text{wiersz})$, gdzie $\text{nnz}_\text{wiersz}$ to średnia liczba niezerowych elementów w wierszu
- **Łatwa równoległość**: obliczenia dla różnych $i$ są niezależne — idealna do obliczeń równoległych
- **Zbieżność**: gwarantowana dla macierzy silnie diagonalnie dominujących
- **Szybkość zbieżności**: zależy od $\rho(\mathbf{B}_J)$ — im mniejszy promień spektralny, tym szybsza zbieżność

## Metoda Gaussa-Seidela

### Idea

Modyfikacja metody Jacobiego — przy obliczaniu $x_i^{(k+1)}$ wykorzystujemy już obliczone wartości z bieżącej iteracji:

$$x_i^{(k+1)} = \frac{1}{a_{ii}} \left( b_i - \sum_{j=1}^{i-1} a_{ij} x_j^{(k+1)} - \sum_{j=i+1}^{n} a_{ij} x_j^{(k)} \right)$$

Macierz iteracji Gaussa-Seidela: $\mathbf{B}_{GS} = -(\mathbf{D} + \mathbf{L})^{-1}\mathbf{U}$.

### Algorytm (pseudokod)

```
Wejście: A (macierz n×n), b (wektor), x⁰ (przybliżenie początkowe), ε (tolerancja), max_iter
Wyjście: x (przybliżone rozwiązanie)

x = x⁰
Dla k = 1, 2, ..., max_iter:
    x_stare = kopia(x)
    
    Dla i = 1, 2, ..., n:
        suma = 0
        Dla j = 1, 2, ..., i-1:
            suma = suma + a[i][j] * x[j]        // już zaktualizowane wartości
        Dla j = i+1, i+2, ..., n:
            suma = suma + a[i][j] * x[j]        // wartości z poprzedniej iteracji
        x[i] = (b[i] - suma) / a[i][i]
    
    Jeśli ‖x - x_stare‖ < ε:
        Zwróć x    // zbieżność osiągnięta

Zwróć x    // osiągnięto max_iter bez zbieżności
```

### Właściwości metody Gaussa-Seidela

- **Szybsza zbieżność** niż Jacobi (zazwyczaj ~2× mniej iteracji)
- **Mniejsze zużycie pamięci** — nie potrzeba wektora $\mathbf{x}_\text{nowe}$, aktualizacja in-place
- **Brak naturalnej równoległości** — obliczenia są sekwencyjne (zależność od wcześniejszych $x_j^{(k+1)}$)
- **Zbieżność**: gwarantowana dla macierzy silnie diagonalnie dominujących oraz macierzy SPD

## Metoda gradientów sprzężonych (CG)

### Idea

Metoda CG rozwiązuje układ $\mathbf{A}\mathbf{x} = \mathbf{b}$, gdzie $\mathbf{A}$ jest macierzą **symetryczną dodatnio określoną** (SPD). Zamiast bezpośredniego rozwiązywania układu, minimalizuje funkcjonał kwadratowy:

$$\phi(\mathbf{x}) = \frac{1}{2}\mathbf{x}^T\mathbf{A}\mathbf{x} - \mathbf{b}^T\mathbf{x}$$

Minimum $\phi$ jest osiągane dokładnie w punkcie $\mathbf{x}^* = \mathbf{A}^{-1}\mathbf{b}$.

Kluczowa idea: kolejne kierunki poszukiwań $\mathbf{d}_0, \mathbf{d}_1, \ldots$ są wzajemnie **sprzężone** (A-ortogonalne) względem macierzy $\mathbf{A}$:

$$\mathbf{d}_i^T \mathbf{A} \mathbf{d}_j = 0 \quad \text{dla } i \neq j$$

### Algorytm (pseudokod)

```
Wejście: A (macierz SPD n×n), b (wektor), x₀ (przybliżenie początkowe), ε (tolerancja)
Wyjście: x (przybliżone rozwiązanie)

r₀ = b - A·x₀           // residuum początkowe
d₀ = r₀                  // pierwszy kierunek = residuum

Dla k = 0, 1, 2, ...:
    αₖ = (rₖᵀ·rₖ) / (dₖᵀ·A·dₖ)       // optymalny krok
    xₖ₊₁ = xₖ + αₖ·dₖ                 // aktualizacja rozwiązania
    rₖ₊₁ = rₖ - αₖ·A·dₖ               // aktualizacja residuum
    
    Jeśli ‖rₖ₊₁‖ < ε: STOP            // warunek zbieżności
    
    βₖ = (rₖ₊₁ᵀ·rₖ₊₁) / (rₖᵀ·rₖ)     // współczynnik sprzężoności
    dₖ₊₁ = rₖ₊₁ + βₖ·dₖ               // nowy kierunek sprzężony

Zwróć xₖ₊₁
```

### Właściwości metody CG

- **Zbieżność skończona**: w arytmetyce dokładnej zbiega w co najwyżej $n$ iteracjach
- **Złożoność iteracji**: jedno mnożenie macierz-wektor ($\mathbf{A}\mathbf{d}_k$) — efektywne dla macierzy rzadkich
- **Nie wymaga przechowywania macierzy iteracji** — wystarczy umieć obliczać iloczyn $\mathbf{A}\mathbf{v}$
- **Prekondycjonowanie**: w praktyce stosuje się prekondycjoner $\mathbf{M} \approx \mathbf{A}$, aby przyspieszyć zbieżność (metoda PCG)
- **Szybkość zbieżności** zależy od wskaźnika uwarunkowania $\kappa(\mathbf{A}) = \lambda_\max / \lambda_\min$:

$$\|\mathbf{e}^{(k)}\|_A \leq 2 \left(\frac{\sqrt{\kappa} - 1}{\sqrt{\kappa} + 1}\right)^k \|\mathbf{e}^{(0)}\|_A$$

Im mniejsze $\kappa(\mathbf{A})$, tym szybsza zbieżność.

## Zastosowanie w modelowaniu pól elektromagnetycznych

### Macierze z metody elementów skończonych (MES)

W modelowaniu pól elektromagnetycznych rozwiązujemy równania Maxwella w postaci różniczkowej. Po dyskretyzacji metodą elementów skończonych (MES) otrzymujemy układ:

$$\mathbf{K}\mathbf{u} = \mathbf{f}$$

gdzie:
- $\mathbf{K}$ — **macierz sztywności** (stiffness matrix)
- $\mathbf{u}$ — wektor niewiadomych (potencjały w węzłach siatki)
- $\mathbf{f}$ — wektor obciążeń (źródła pola)

Macierz $\mathbf{K}$ z MES ma następujące właściwości:
- **Rzadka** — element $K_{ij} \neq 0$ tylko gdy węzły $i$ i $j$ należą do tego samego elementu skończonego
- **Symetryczna** — $\mathbf{K} = \mathbf{K}^T$ (dla problemów samosprzężonych)
- **Dodatnio określona** (po nałożeniu warunków brzegowych)
- **Pasmowa** — niezerowe elementy skupione wokół diagonali

Te właściwości czynią macierze z MES idealnymi kandydatami dla metod iteracyjnych, szczególnie CG.

### Macierze z metody różnic skończonych (MRS)

Dyskretyzacja równania Laplace'a $\nabla^2 \phi = 0$ na siatce regularnej 2D schematem pięciopunktowym daje macierz o strukturze:

$$\mathbf{A} = \begin{bmatrix} \mathbf{T} & -\mathbf{I} & & \\ -\mathbf{I} & \mathbf{T} & -\mathbf{I} & \\ & \ddots & \ddots & \ddots \\ & & -\mathbf{I} & \mathbf{T} \end{bmatrix}$$

gdzie $\mathbf{T}$ jest macierzą trójdiagonalną $\text{tridiag}(-1, 4, -1)$.

Macierz ta jest:
- **Rzadka** — co najwyżej 5 niezerowych elementów w wierszu (schemat pięciopunktowy)
- **Diagonalnie dominująca** — $|4| \geq |-1|+|-1|+|-1|+|-1| = 4$ (słaba dominacja dla węzłów wewnętrznych; silna po uwzględnieniu warunków brzegowych)
- **Symetryczna dodatnio określona**
- **Pasmowa** — szerokość pasma zależy od numeracji węzłów

### Przykłady konkretnych zastosowań

| Problem EM | Równanie | Metoda dyskretyzacji | Typ macierzy | Zalecana metoda iteracyjna |
|---|---|---|---|---|
| Pole elektrostatyczne | $\nabla^2 \phi = -\rho/\varepsilon$ | MES/MRS | SPD, rzadka | CG, PCG |
| Pole magnetostatyczne | $\nabla \times (\nu \nabla \times \mathbf{A}) = \mathbf{J}$ | MES | SPD, rzadka | PCG |
| Pole termiczne (stacjonarne) | $\nabla \cdot (k \nabla T) = Q$ | MES/MRS | SPD, rzadka | CG, Gauss-Seidel |
| Obwody rezystancyjne | $\mathbf{G}\mathbf{v} = \mathbf{i}$ | MNA | Diag. dom., rzadka | Jacobi, Gauss-Seidel |

## Porównanie metod iteracyjnych

| Cecha | Jacobi | Gauss-Seidel | CG |
|---|---|---|---|
| Wymagania dot. macierzy | diag. dom. | diag. dom. lub SPD | SPD |
| Zbieżność | wolna | ~2× szybsza niż Jacobi | szybka (zależy od $\kappa$) |
| Pamięć | $O(n)$ dodatkowa | $O(1)$ dodatkowa | $O(n)$ (kilka wektorów) |
| Równoległość | naturalna | trudna | częściowa |
| Koszt iteracji | $O(\text{nnz})$ | $O(\text{nnz})$ | $O(\text{nnz})$ |
| Prekondycjonowanie | rzadko | rzadko | często (PCG) |
| Zbieżność skończona | nie | nie | tak (w $\leq n$ krokach) |

## Przykłady

### Przykład 1: Rozwiązanie układu 3×3 metodą Jacobiego

Rozwiązujemy układ $\mathbf{A}\mathbf{x} = \mathbf{b}$:

$$\begin{bmatrix} 4 & -1 & 1 \\ -1 & 4 & -2 \\ 1 & -2 & 4 \end{bmatrix} \begin{bmatrix} x_1 \\ x_2 \\ x_3 \end{bmatrix} = \begin{bmatrix} 12 \\ -1 \\ 5 \end{bmatrix}$$

Macierz jest silnie diagonalnie dominująca ($|4| > |-1|+|1| = 2$, $|4| > |-1|+|-2| = 3$, $|4| > |1|+|-2| = 3$), więc metoda Jacobiego jest zbieżna.

Rozwiązanie dokładne: $\mathbf{x}^* = [3, 1, 1]^T$.

Weryfikacja: $4(3) - 1 + 1 = 12$ ✓, $-3 + 4(1) - 2(1) = -1$ ✓, $3 - 2(1) + 4(1) = 5$ ✓.

Wzory iteracyjne:

$$x_1^{(k+1)} = \frac{1}{4}\bigl(12 + x_2^{(k)} - x_3^{(k)}\bigr)$$

$$x_2^{(k+1)} = \frac{1}{4}\bigl(-1 + x_1^{(k)} + 2x_3^{(k)}\bigr)$$

$$x_3^{(k+1)} = \frac{1}{4}\bigl(5 - x_1^{(k)} + 2x_2^{(k)}\bigr)$$

Startujemy od $\mathbf{x}^{(0)} = [0, 0, 0]^T$:

**Iteracja 1:**
- $x_1^{(1)} = \frac{1}{4}(12 + 0 - 0) = 3.0000$
- $x_2^{(1)} = \frac{1}{4}(-1 + 0 + 0) = -0.2500$
- $x_3^{(1)} = \frac{1}{4}(5 - 0 + 0) = 1.2500$

**Iteracja 2:**
- $x_1^{(2)} = \frac{1}{4}(12 + (-0.25) - 1.25) = 2.6250$
- $x_2^{(2)} = \frac{1}{4}(-1 + 3.0 + 2 \cdot 1.25) = 1.1250$
- $x_3^{(2)} = \frac{1}{4}(5 - 3.0 + 2 \cdot (-0.25)) = 0.3750$

**Iteracja 3:**
- $x_1^{(3)} = \frac{1}{4}(12 + 1.125 - 0.375) = 3.1875$
- $x_2^{(3)} = \frac{1}{4}(-1 + 2.625 + 2 \cdot 0.375) = 0.5938$
- $x_3^{(3)} = \frac{1}{4}(5 - 2.625 + 2 \cdot 1.125) = 1.1563$

| Iteracja $k$ | $x_1^{(k)}$ | $x_2^{(k)}$ | $x_3^{(k)}$ | $\|\mathbf{x}^{(k)} - \mathbf{x}^*\|_\infty$ |
|---|---|---|---|---|
| 0 | 0.0000 | 0.0000 | 0.0000 | 3.0000 |
| 1 | 3.0000 | −0.2500 | 1.2500 | 1.2500 |
| 2 | 2.6250 | 1.1250 | 0.3750 | 0.6250 |
| 3 | 3.1875 | 0.5938 | 1.1563 | 0.4063 |
| 4 | 2.8594 | 1.0781 | 0.9063 | 0.1406 |
| 5 | 3.0430 | 0.9180 | 1.0742 | 0.0820 |

Metoda zbiega ku rozwiązaniu dokładnemu $[3, 1, 1]^T$, redukując błąd w kolejnych iteracjach.

### Przykład 2: Rozwiązanie tego samego układu metodą Gaussa-Seidela

Ten sam układ, $\mathbf{x}^{(0)} = [0, 0, 0]^T$:

**Iteracja 1:**

$x_1^{(1)} = \frac{1}{4}(12 + 0 - 0) = 3.0000$

$x_2^{(1)} = \frac{1}{4}(-1 + 3.0 + 2 \cdot 0) = 0.5000$ — używamy już obliczonego $x_1^{(1)} = 3$

$x_3^{(1)} = \frac{1}{4}(5 - 3.0 + 2 \cdot 0.5) = 0.7500$ — używamy $x_1^{(1)}$ i $x_2^{(1)}$

**Iteracja 2:**

$x_1^{(2)} = \frac{1}{4}(12 + 0.5 - 0.75) = 2.9375$

$x_2^{(2)} = \frac{1}{4}(-1 + 2.9375 + 2 \cdot 0.75) = 0.8594$

$x_3^{(2)} = \frac{1}{4}(5 - 2.9375 + 2 \cdot 0.8594) = 0.9453$

**Iteracja 3:**

$x_1^{(3)} = \frac{1}{4}(12 + 0.8594 - 0.9453) = 2.9785$

$x_2^{(3)} = \frac{1}{4}(-1 + 2.9785 + 2 \cdot 0.9453) = 0.9673$

$x_3^{(3)} = \frac{1}{4}(5 - 2.9785 + 2 \cdot 0.9673) = 0.9890$

| Iteracja $k$ | $x_1^{(k)}$ | $x_2^{(k)}$ | $x_3^{(k)}$ | $\|\mathbf{x}^{(k)} - \mathbf{x}^*\|_\infty$ |
|---|---|---|---|---|
| 0 | 0.0000 | 0.0000 | 0.0000 | 3.0000 |
| 1 | 3.0000 | 0.5000 | 0.7500 | 0.5000 |
| 2 | 2.9375 | 0.8594 | 0.9453 | 0.1406 |
| 3 | 2.9785 | 0.9673 | 0.9890 | 0.0327 |

Gauss-Seidel zbiega znacznie szybciej niż Jacobi — po 3 iteracjach błąd wynosi 0.0327, podczas gdy Jacobi po 3 iteracjach ma błąd 0.4063.

### Przykład 3: Macierz z dyskretyzacji MRS — schemat pięciopunktowy

Rozważmy równanie Laplace'a $\nabla^2 \phi = 0$ na kwadracie $[0,1] \times [0,1]$. Dla siatki z 4 węzłami wewnętrznymi ($h = 1/3$) dyskretyzacja schematem pięciopunktowym:

$$\phi_{i-1,j} + \phi_{i+1,j} + \phi_{i,j-1} + \phi_{i,j+1} - 4\phi_{i,j} = 0$$

daje macierz:

$$\mathbf{A} = \begin{bmatrix} 4 & -1 & -1 & 0 \\ -1 & 4 & 0 & -1 \\ -1 & 0 & 4 & -1 \\ 0 & -1 & -1 & 4 \end{bmatrix}$$

Ta macierz jest:
- **Rzadka** — 12 niezerowych elementów z 16 (75%), ale dla dużych siatek stosunek maleje drastycznie
- **Symetryczna**: $\mathbf{A} = \mathbf{A}^T$ ✓
- **Diagonalnie dominująca**: $|4| \geq |-1|+|-1|+|-1| = 3$ ✓
- **Dodatnio określona** ✓

Dla siatki $100 \times 100$ macierz ma wymiar $10000 \times 10000$, ale tylko ~50000 niezerowych elementów (zamiast $10^8$). Metoda CG rozwiąże taki układ w kilkaset iteracji, podczas gdy eliminacja Gaussa wymagałaby $O(n^3) \approx 10^{12}$ operacji.

## Podsumowanie

1. **Metody iteracyjne** są preferowane dla dużych, rzadkich układów równań liniowych, typowych w modelowaniu pól elektromagnetycznych (MES, MRS). Metody bezpośrednie (eliminacja Gaussa, rozkład LU) są nieefektywne dla takich macierzy ze względu na zjawisko fill-in (wypełnianie zerowych pozycji).

2. **Warunki zbieżności**: metoda iteracyjna zbiega, gdy promień spektralny macierzy iteracji $\rho(\mathbf{B}) < 1$. Warunki wystarczające to silna dominacja diagonalna lub symetria i dodatnia określoność macierzy $\mathbf{A}$.

3. **Metoda Jacobiego** jest najprostsza i łatwo równoległa, ale zbiega wolno. Każda iteracja używa wyłącznie wartości z poprzedniego kroku.

4. **Metoda Gaussa-Seidela** zbiega szybciej niż Jacobi (wykorzystuje bieżące wartości), ale jest trudniejsza do zrównoleglenia.

5. **Metoda gradientów sprzężonych (CG)** jest najefektywniejsza dla macierzy SPD — zbiega w co najwyżej $n$ krokach, a z prekondycjonowaniem (PCG) znacznie szybciej. Jest standardową metodą w komercyjnych solverach MES.

6. **Macierze z modelowania pól EM** (macierze sztywności z MES, macierze z MRS) są duże, rzadkie, symetryczne i dodatnio określone — spełniają warunki zbieżności wszystkich trzech metod i są idealnymi kandydatami do rozwiązywania metodami iteracyjnymi.

## Powiązane pytania

- [Pytanie 35: Metoda elementów skończonych](35-metoda-elementow-skonczonych.md)
- [Pytanie 36: Metoda różnic skończonych](36-metoda-roznic-skonczonych.md)
- [Pytanie 37: Wektory ortogonalne i sprzężone](37-wektory-ortogonalne-sprezone.md)
- [Pytanie 44: Przechowywanie macierzy rzadkich](44-macierze-rzadkie.md)
