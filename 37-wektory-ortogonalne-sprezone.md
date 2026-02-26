# Pytanie 37: Co oznaczają terminy: wektory ortogonalne oraz wektory sprzężone względem macierzy A?

## Kluczowe pojęcia

- **Ortogonalność wektorów** — dwa wektory $\mathbf{u}$ i $\mathbf{v}$ są ortogonalne (prostopadłe), gdy ich iloczyn skalarny wynosi zero: $\mathbf{u}^T \mathbf{v} = 0$. Ortogonalność oznacza, że wektory są „niezależne kierunkowo" — rzut jednego na drugi jest zerowy.
- **Sprzężoność wektorów względem macierzy A** — dwa wektory $\mathbf{u}$ i $\mathbf{v}$ są sprzężone (A-ortogonalne) względem macierzy $\mathbf{A}$, gdy $\mathbf{u}^T \mathbf{A} \mathbf{v} = 0$. Jest to uogólnienie ortogonalności — zamiast standardowego iloczynu skalarnego stosujemy iloczyn skalarny indukowany przez macierz $\mathbf{A}$.
- **Iloczyn skalarny** — operacja przypisująca parze wektorów liczbę rzeczywistą. Standardowy iloczyn skalarny: $\langle \mathbf{u}, \mathbf{v} \rangle = \mathbf{u}^T \mathbf{v} = \sum_{i=1}^n u_i v_i$. Iloczyn skalarny indukowany przez macierz $\mathbf{A}$: $\langle \mathbf{u}, \mathbf{v} \rangle_A = \mathbf{u}^T \mathbf{A} \mathbf{v}$.
- **Macierz symetryczna dodatnio określona (SPD)** — macierz $\mathbf{A}$ jest SPD, gdy $\mathbf{A} = \mathbf{A}^T$ (symetria) oraz $\mathbf{x}^T \mathbf{A} \mathbf{x} > 0$ dla każdego $\mathbf{x} \neq \mathbf{0}$ (dodatnia określoność). Macierze SPD gwarantują, że $\langle \cdot, \cdot \rangle_A$ jest poprawnym iloczynem skalarnym.
- **Baza ortogonalna** — zbiór wektorów wzajemnie ortogonalnych, które rozpinają daną przestrzeń. Jeśli dodatkowo każdy wektor ma normę 1, mówimy o bazie ortonormalnej.
- **Metoda gradientów sprzężonych (CG)** — iteracyjna metoda rozwiązywania układów $\mathbf{A}\mathbf{x} = \mathbf{b}$ z macierzą SPD, w której kolejne kierunki poszukiwań są wzajemnie sprzężone względem $\mathbf{A}$. Dzięki temu metoda zbiega w co najwyżej $n$ krokach (dla macierzy $n \times n$).

## Definicja ortogonalności wektorów

### Warunek ortogonalności

Dwa wektory $\mathbf{u}, \mathbf{v} \in \mathbb{R}^n$ są **ortogonalne**, jeśli ich standardowy iloczyn skalarny wynosi zero:

$$\mathbf{u}^T \mathbf{v} = \sum_{i=1}^n u_i v_i = 0$$

Geometrycznie oznacza to, że wektory są prostopadłe — kąt między nimi wynosi $90°$. Z definicji iloczynu skalarnego:

$$\mathbf{u}^T \mathbf{v} = \|\mathbf{u}\| \cdot \|\mathbf{v}\| \cdot \cos\theta$$

więc $\cos\theta = 0$, czyli $\theta = 90°$.

### Zbiór wektorów ortogonalnych

Zbiór wektorów $\{\mathbf{d}_0, \mathbf{d}_1, \ldots, \mathbf{d}_{k}\}$ jest **wzajemnie ortogonalny**, jeśli:

$$\mathbf{d}_i^T \mathbf{d}_j = 0 \quad \text{dla } i \neq j$$

Zbiór wektorów ortonormalnych spełnia dodatkowo $\|\mathbf{d}_i\| = 1$ dla każdego $i$.

### Właściwości ortogonalności

1. **Liniowa niezależność** — wektory wzajemnie ortogonalne (niezerowe) są liniowo niezależne.
2. **Baza ortogonalna** — w $\mathbb{R}^n$ można znaleźć co najwyżej $n$ wzajemnie ortogonalnych wektorów niezerowych, które tworzą bazę.
3. **Łatwość rozkładu** — współrzędne wektora $\mathbf{x}$ w bazie ortogonalnej $\{\mathbf{d}_i\}$ wyznaczamy prostym wzorem:

$$\alpha_i = \frac{\mathbf{d}_i^T \mathbf{x}}{\mathbf{d}_i^T \mathbf{d}_i}$$

4. **Twierdzenie Pitagorasa** — dla wektorów ortogonalnych: $\|\mathbf{u} + \mathbf{v}\|^2 = \|\mathbf{u}\|^2 + \|\mathbf{v}\|^2$.

## Definicja sprzężoności wektorów względem macierzy A

### Warunek sprzężoności (A-ortogonalności)

Dwa wektory $\mathbf{u}, \mathbf{v} \in \mathbb{R}^n$ są **sprzężone** (A-ortogonalne) względem macierzy $\mathbf{A}$, jeśli:

$$\mathbf{u}^T \mathbf{A} \mathbf{v} = 0$$

Równoważnie, stosując iloczyn skalarny indukowany przez $\mathbf{A}$:

$$\langle \mathbf{u}, \mathbf{v} \rangle_A = \mathbf{u}^T \mathbf{A} \mathbf{v} = 0$$

### Wymagania dotyczące macierzy A

Aby $\langle \cdot, \cdot \rangle_A$ był poprawnym iloczynem skalarnym, macierz $\mathbf{A}$ musi być **symetryczna i dodatnio określona** (SPD):

1. **Symetria**: $\mathbf{A} = \mathbf{A}^T$ — zapewnia symetrię iloczynu skalarnego: $\langle \mathbf{u}, \mathbf{v} \rangle_A = \langle \mathbf{v}, \mathbf{u} \rangle_A$
2. **Dodatnia określoność**: $\mathbf{x}^T \mathbf{A} \mathbf{x} > 0$ dla $\mathbf{x} \neq \mathbf{0}$ — zapewnia, że $\langle \mathbf{x}, \mathbf{x} \rangle_A > 0$ (norma jest dodatnia)

### Zbiór wektorów sprzężonych

Zbiór wektorów $\{\mathbf{d}_0, \mathbf{d}_1, \ldots, \mathbf{d}_{k}\}$ jest **wzajemnie sprzężony** względem $\mathbf{A}$, jeśli:

$$\mathbf{d}_i^T \mathbf{A} \mathbf{d}_j = 0 \quad \text{dla } i \neq j$$

### Właściwości sprzężoności

1. **Uogólnienie ortogonalności** — gdy $\mathbf{A} = \mathbf{I}$ (macierz jednostkowa), sprzężoność redukuje się do zwykłej ortogonalności.
2. **Liniowa niezależność** — wektory wzajemnie sprzężone (niezerowe) względem macierzy SPD są liniowo niezależne.
3. **Baza sprzężona** — w $\mathbb{R}^n$ istnieje co najwyżej $n$ wzajemnie sprzężonych wektorów niezerowych.
4. **Rozkład w bazie sprzężonej** — współrzędne wektora $\mathbf{x}$ w bazie sprzężonej $\{\mathbf{d}_i\}$:

$$\alpha_i = \frac{\mathbf{d}_i^T \mathbf{A} \mathbf{x}}{\mathbf{d}_i^T \mathbf{A} \mathbf{d}_i}$$

5. **Interpretacja geometryczna** — sprzężoność oznacza ortogonalność w metryce zdefiniowanej przez $\mathbf{A}$. Macierz $\mathbf{A}$ „deformuje" przestrzeń, a wektory sprzężone są prostopadłe w tej zdeformowanej przestrzeni.

## Związek z metodą gradientów sprzężonych (CG)

### Idea metody CG

Metoda gradientów sprzężonych rozwiązuje układ $\mathbf{A}\mathbf{x} = \mathbf{b}$, gdzie $\mathbf{A}$ jest macierzą SPD, minimalizując funkcjonał kwadratowy:

$$\phi(\mathbf{x}) = \frac{1}{2} \mathbf{x}^T \mathbf{A} \mathbf{x} - \mathbf{b}^T \mathbf{x}$$

Gradient tego funkcjonału: $\nabla \phi(\mathbf{x}) = \mathbf{A}\mathbf{x} - \mathbf{b} = -\mathbf{r}$, gdzie $\mathbf{r} = \mathbf{b} - \mathbf{A}\mathbf{x}$ to residuum.

### Dlaczego kierunki sprzężone?

W metodzie najszybszego spadku (steepest descent) kolejne kierunki poszukiwań to gradienty, które mogą być prawie równoległe — metoda „zygzakuje" i zbiega wolno.

Metoda CG generuje kierunki $\mathbf{d}_0, \mathbf{d}_1, \ldots$ wzajemnie sprzężone względem $\mathbf{A}$:

$$\mathbf{d}_i^T \mathbf{A} \mathbf{d}_j = 0 \quad \text{dla } i \neq j$$

Dzięki temu minimalizacja wzdłuż każdego kierunku jest „niezależna" od pozostałych — raz zminimalizowany składnik nie jest pogarszany w kolejnych krokach.

### Algorytm CG (pseudokod)

```
Wejście: A (macierz SPD n×n), b (wektor), x₀ (przybliżenie początkowe)
r₀ = b - A·x₀           // residuum początkowe
d₀ = r₀                  // pierwszy kierunek = gradient

Dla k = 0, 1, 2, ...:
    αₖ = (rₖᵀ·rₖ) / (dₖᵀ·A·dₖ)       // długość kroku
    xₖ₊₁ = xₖ + αₖ·dₖ                 // aktualizacja rozwiązania
    rₖ₊₁ = rₖ - αₖ·A·dₖ               // aktualizacja residuum
    
    Jeśli ‖rₖ₊₁‖ < ε: STOP            // warunek zbieżności
    
    βₖ = (rₖ₊₁ᵀ·rₖ₊₁) / (rₖᵀ·rₖ)     // współczynnik sprzężoności
    dₖ₊₁ = rₖ₊₁ + βₖ·dₖ               // nowy kierunek sprzężony

Wyjście: xₖ₊₁ (przybliżone rozwiązanie)
```

### Kluczowe właściwości CG

- **Zbieżność skończona**: dla macierzy $n \times n$ metoda CG zbiega w co najwyżej $n$ iteracjach (w arytmetyce dokładnej).
- **Ortogonalność residuów**: $\mathbf{r}_i^T \mathbf{r}_j = 0$ dla $i \neq j$.
- **Sprzężoność kierunków**: $\mathbf{d}_i^T \mathbf{A} \mathbf{d}_j = 0$ dla $i \neq j$.
- **Złożoność**: każda iteracja wymaga jednego mnożenia macierz-wektor ($\mathbf{A}\mathbf{d}_k$), co jest efektywne dla macierzy rzadkich.

## Proces ortogonalizacji Grama-Schmidta

### Idea

Proces Grama-Schmidta przekształca dowolny zbiór liniowo niezależnych wektorów $\{\mathbf{v}_1, \ldots, \mathbf{v}_n\}$ w zbiór ortogonalny (lub ortonormalny) $\{\mathbf{u}_1, \ldots, \mathbf{u}_n\}$.

### Algorytm klasyczny

Dla standardowego iloczynu skalarnego:

$$\mathbf{u}_1 = \mathbf{v}_1$$

$$\mathbf{u}_k = \mathbf{v}_k - \sum_{j=1}^{k-1} \frac{\mathbf{u}_j^T \mathbf{v}_k}{\mathbf{u}_j^T \mathbf{u}_j} \mathbf{u}_j, \quad k = 2, 3, \ldots, n$$

Każdy nowy wektor $\mathbf{u}_k$ powstaje przez odjęcie od $\mathbf{v}_k$ jego rzutów na wcześniej wyznaczone wektory ortogonalne.

### Uogólnienie na A-ortogonalizację

Aby uzyskać wektory wzajemnie sprzężone względem $\mathbf{A}$, wystarczy zastąpić standardowy iloczyn skalarny iloczynem $\langle \cdot, \cdot \rangle_A$:

$$\mathbf{u}_1 = \mathbf{v}_1$$

$$\mathbf{u}_k = \mathbf{v}_k - \sum_{j=1}^{k-1} \frac{\mathbf{u}_j^T \mathbf{A} \mathbf{v}_k}{\mathbf{u}_j^T \mathbf{A} \mathbf{u}_j} \mathbf{u}_j, \quad k = 2, 3, \ldots, n$$

Wynikowe wektory spełniają $\mathbf{u}_i^T \mathbf{A} \mathbf{u}_j = 0$ dla $i \neq j$.

### Związek z CG

W metodzie CG proces generowania kierunków sprzężonych jest uproszczoną wersją A-ortogonalizacji Grama-Schmidta. Dzięki szczególnym właściwościom residuów w CG, wystarczy pamiętać tylko jeden poprzedni kierunek (zamiast wszystkich), co daje rekurencję trójczłonową:

$$\mathbf{d}_{k+1} = \mathbf{r}_{k+1} + \beta_k \mathbf{d}_k$$

zamiast pełnej sumy Grama-Schmidta. To sprawia, że CG jest bardzo efektywna pamięciowo.

## Przykłady

### Przykład 1: Weryfikacja ortogonalności wektorów

Dane wektory:

$$\mathbf{u} = \begin{bmatrix} 1 \\ 2 \\ -1 \end{bmatrix}, \quad \mathbf{v} = \begin{bmatrix} 3 \\ -1 \\ 1 \end{bmatrix}$$

Sprawdzamy iloczyn skalarny:

$$\mathbf{u}^T \mathbf{v} = 1 \cdot 3 + 2 \cdot (-1) + (-1) \cdot 1 = 3 - 2 - 1 = 0$$

Wektory $\mathbf{u}$ i $\mathbf{v}$ **są ortogonalne**.

### Przykład 2: Weryfikacja sprzężoności wektorów względem macierzy A

Dane wektory i macierz:

$$\mathbf{d}_1 = \begin{bmatrix} 1 \\ 0 \end{bmatrix}, \quad \mathbf{d}_2 = \begin{bmatrix} -1 \\ 2 \end{bmatrix}, \quad \mathbf{A} = \begin{bmatrix} 2 & 1 \\ 1 & 2 \end{bmatrix}$$

Macierz $\mathbf{A}$ jest symetryczna. Sprawdzamy dodatnią określoność — wartości własne: $\lambda_1 = 1 > 0$, $\lambda_2 = 3 > 0$, więc $\mathbf{A}$ jest SPD.

Obliczamy $\mathbf{A}\mathbf{d}_2$:

$$\mathbf{A}\mathbf{d}_2 = \begin{bmatrix} 2 & 1 \\ 1 & 2 \end{bmatrix} \begin{bmatrix} -1 \\ 2 \end{bmatrix} = \begin{bmatrix} 2 \cdot (-1) + 1 \cdot 2 \\ 1 \cdot (-1) + 2 \cdot 2 \end{bmatrix} = \begin{bmatrix} 0 \\ 3 \end{bmatrix}$$

Sprawdzamy sprzężoność:

$$\mathbf{d}_1^T \mathbf{A} \mathbf{d}_2 = \begin{bmatrix} 1 & 0 \end{bmatrix} \begin{bmatrix} 0 \\ 3 \end{bmatrix} = 0$$

Wektory $\mathbf{d}_1$ i $\mathbf{d}_2$ **są sprzężone** względem macierzy $\mathbf{A}$.

Zauważmy, że te same wektory **nie są ortogonalne** w sensie standardowym:

$$\mathbf{d}_1^T \mathbf{d}_2 = 1 \cdot (-1) + 0 \cdot 2 = -1 \neq 0$$

To pokazuje, że sprzężoność i ortogonalność to różne pojęcia (chyba że $\mathbf{A} = \mathbf{I}$).

### Przykład 3: Konstrukcja wektora sprzężonego

Mamy wektor $\mathbf{d}_0 = \begin{bmatrix} 1 \\ 1 \end{bmatrix}$ i macierz $\mathbf{A} = \begin{bmatrix} 4 & 2 \\ 2 & 3 \end{bmatrix}$ (SPD, bo $\lambda_1 \approx 1.44 > 0$, $\lambda_2 \approx 5.56 > 0$).

Chcemy znaleźć wektor $\mathbf{d}_1$ sprzężony z $\mathbf{d}_0$ względem $\mathbf{A}$, startując od $\mathbf{v} = \begin{bmatrix} 1 \\ -1 \end{bmatrix}$.

Stosujemy A-ortogonalizację Grama-Schmidta:

$$\mathbf{d}_1 = \mathbf{v} - \frac{\mathbf{d}_0^T \mathbf{A} \mathbf{v}}{\mathbf{d}_0^T \mathbf{A} \mathbf{d}_0} \mathbf{d}_0$$

Obliczamy:

$$\mathbf{A}\mathbf{v} = \begin{bmatrix} 4 & 2 \\ 2 & 3 \end{bmatrix}\begin{bmatrix} 1 \\ -1 \end{bmatrix} = \begin{bmatrix} 2 \\ -1 \end{bmatrix}$$

$$\mathbf{d}_0^T \mathbf{A} \mathbf{v} = \begin{bmatrix} 1 & 1 \end{bmatrix}\begin{bmatrix} 2 \\ -1 \end{bmatrix} = 1$$

$$\mathbf{A}\mathbf{d}_0 = \begin{bmatrix} 4 & 2 \\ 2 & 3 \end{bmatrix}\begin{bmatrix} 1 \\ 1 \end{bmatrix} = \begin{bmatrix} 6 \\ 5 \end{bmatrix}$$

$$\mathbf{d}_0^T \mathbf{A} \mathbf{d}_0 = \begin{bmatrix} 1 & 1 \end{bmatrix}\begin{bmatrix} 6 \\ 5 \end{bmatrix} = 11$$

$$\mathbf{d}_1 = \begin{bmatrix} 1 \\ -1 \end{bmatrix} - \frac{1}{11}\begin{bmatrix} 1 \\ 1 \end{bmatrix} = \begin{bmatrix} 10/11 \\ -12/11 \end{bmatrix}$$

Weryfikacja: $\mathbf{d}_0^T \mathbf{A} \mathbf{d}_1 = \begin{bmatrix} 1 & 1 \end{bmatrix} \mathbf{A} \begin{bmatrix} 10/11 \\ -12/11 \end{bmatrix}$. Obliczamy $\mathbf{A}\mathbf{d}_1 = \frac{1}{11}\begin{bmatrix} 4 \cdot 10 + 2 \cdot (-12) \\ 2 \cdot 10 + 3 \cdot (-12) \end{bmatrix} = \frac{1}{11}\begin{bmatrix} 16 \\ -16 \end{bmatrix}$, więc $\mathbf{d}_0^T \mathbf{A} \mathbf{d}_1 = \frac{1}{11}(16 - 16) = 0$ ✓

## Podsumowanie

1. **Wektory ortogonalne** to wektory, których standardowy iloczyn skalarny wynosi zero: $\mathbf{u}^T \mathbf{v} = 0$. Geometrycznie są prostopadłe.

2. **Wektory sprzężone** (A-ortogonalne) to wektory, których iloczyn skalarny indukowany przez macierz $\mathbf{A}$ wynosi zero: $\mathbf{u}^T \mathbf{A} \mathbf{v} = 0$. Sprzężoność jest uogólnieniem ortogonalności — redukuje się do niej dla $\mathbf{A} = \mathbf{I}$.

3. Aby $\langle \cdot, \cdot \rangle_A$ był poprawnym iloczynem skalarnym, macierz $\mathbf{A}$ musi być **symetryczna i dodatnio określona** (SPD).

4. **Metoda gradientów sprzężonych (CG)** wykorzystuje kierunki wzajemnie sprzężone względem $\mathbf{A}$ do iteracyjnego rozwiązywania układów $\mathbf{A}\mathbf{x} = \mathbf{b}$. Sprzężoność kierunków zapewnia zbieżność w co najwyżej $n$ krokach.

5. **Proces Grama-Schmidta** pozwala konstruować zbiory wektorów ortogonalnych (lub sprzężonych, po zamianie iloczynu skalarnego na $\langle \cdot, \cdot \rangle_A$). W CG ten proces jest uproszczony do rekurencji trójczłonowej.

6. Sprzężoność ma kluczowe znaczenie w metodach numerycznych — macierze sztywności z MES/MRS są SPD, a metoda CG jest jedną z najefektywniejszych metod ich rozwiązywania.

## Powiązane pytania

- [Pytanie 35: Metoda elementów skończonych](35-metoda-elementow-skonczonych.md)
- [Pytanie 36: Metoda różnic skończonych](36-metoda-roznic-skonczonych.md)
- [Pytanie 38: Metody iteracyjne dla układów równań liniowych](38-metody-iteracyjne-uklady-rownan.md)
