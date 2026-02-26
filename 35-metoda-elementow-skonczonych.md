# Pytanie 35: Przedstawić ideę metody elementów skończonych.

## Kluczowe pojęcia

- **Metoda elementów skończonych (MES/FEM)** — metoda numerycznego rozwiązywania równań różniczkowych cząstkowych (i zwyczajnych), polegająca na podziale dziedziny na skończoną liczbę poddziedzin (elementów) i przybliżeniu rozwiązania na każdym elemencie za pomocą prostych funkcji (funkcji kształtu). MES przekształca problem ciągły w skończony układ równań algebraicznych.
- **Element skończony** — poddziedzina (np. odcinek w 1D, trójkąt lub czworokąt w 2D, czworościan lub sześcian w 3D), na której rozwiązanie jest przybliżane za pomocą funkcji kształtu. Element jest zdefiniowany przez swoje węzły, geometrię i funkcje bazowe.
- **Siatka (mesh)** — zbiór elementów skończonych pokrywających całą dziedzinę obliczeniową. Jakość siatki (rozmiar, kształt elementów, zagęszczenie w obszarach dużych gradientów) ma kluczowy wpływ na dokładność rozwiązania.
- **Funkcje kształtu (shape functions)** — funkcje bazowe $N_i(x)$ zdefiniowane na elemencie, które służą do interpolacji rozwiązania wewnątrz elementu. Każda funkcja kształtu przyjmuje wartość 1 w jednym węźle i 0 w pozostałych węzłach elementu: $N_i(x_j) = \delta_{ij}$.
- **Macierz sztywności (stiffness matrix)** — macierz $\mathbf{K}$ powstająca z dyskretyzacji MES, wiążąca wektor niewiadomych węzłowych $\mathbf{u}$ z wektorem obciążeń $\mathbf{f}$ w układzie $\mathbf{K}\mathbf{u} = \mathbf{f}$. Elementy macierzy sztywności zależą od właściwości materiału, geometrii elementu i funkcji kształtu.
- **Warunki brzegowe** — ograniczenia nałożone na rozwiązanie na brzegu dziedziny. Wyróżniamy warunki Dirichleta (wartość funkcji na brzegu), Neumanna (wartość pochodnej normalnej na brzegu) i Robina (kombinacja liniowa funkcji i pochodnej).

## Idea metody elementów skończonych

### Ogólna koncepcja

Metoda elementów skończonych polega na przybliżonym rozwiązywaniu równań różniczkowych poprzez:

1. **Zamianę problemu ciągłego na dyskretny** — zamiast szukać funkcji $u(x)$ spełniającej równanie różniczkowe w każdym punkcie dziedziny, szukamy przybliżenia $u^h(x)$ wyrażonego jako kombinacja liniowa skończonej liczby funkcji bazowych.
2. **Podział dziedziny na elementy** — dziedzina jest dzielona na małe poddziedziny (elementy), na których rozwiązanie jest przybliżane prostymi funkcjami.
3. **Złożenie globalnego układu równań** — lokalne macierze sztywności poszczególnych elementów są składane w globalny układ równań algebraicznych.

Przybliżone rozwiązanie ma postać:

$$u^h(x) = \sum_{i=1}^{n} u_i N_i(x)$$

gdzie $u_i$ to niewiadome wartości węzłowe, a $N_i(x)$ to globalne funkcje kształtu.

### Sformułowanie słabe (wariacyjne)

MES opiera się na **sformułowaniu słabym** (weak form) równania różniczkowego, które jest równoważne sformułowaniu silnemu, ale wymaga mniejszej regularności rozwiązania.

Rozważmy równanie różniczkowe (problem modelowy):

$$-\frac{d}{dx}\left(k(x)\frac{du}{dx}\right) = f(x), \quad x \in (0, L)$$

z warunkami brzegowymi $u(0) = 0$ (Dirichlet) i $k(L)\frac{du}{dx}(L) = q$ (Neumann).

**Sformułowanie silne** wymaga, aby równanie było spełnione w każdym punkcie dziedziny.

**Sformułowanie słabe** otrzymujemy przez:
1. Pomnożenie równania przez funkcję testową (wagową) $v(x)$, spełniającą $v(0) = 0$
2. Scałkowanie po dziedzinie
3. Zastosowanie całkowania przez części (obniżenie rzędu pochodnej)

Otrzymujemy:

$$\int_0^L k(x) \frac{du}{dx} \frac{dv}{dx} \, dx = \int_0^L f(x) v(x) \, dx + q \cdot v(L)$$

W zapisie skróconym: **Znaleźć $u \in V$ takie, że $a(u, v) = L(v)$ dla każdego $v \in V$**, gdzie:

$$a(u, v) = \int_0^L k \, u' v' \, dx, \qquad L(v) = \int_0^L f \, v \, dx + q \, v(L)$$

$a(u,v)$ to forma dwuliniowa (energia), a $L(v)$ to funkcjonał liniowy (obciążenie).

## Etapy metody elementów skończonych

### Etap 1: Dyskretyzacja dziedziny (generacja siatki)

Dziedzina $\Omega$ jest dzielona na $n_e$ elementów skończonych $\Omega^e$:

$$\Omega \approx \bigcup_{e=1}^{n_e} \Omega^e$$

Elementy nie mogą się nakładać, a ich suma powinna pokrywać całą dziedzinę. W 1D elementami są odcinki, w 2D — trójkąty lub czworokąty, w 3D — czworościany lub sześciany.

### Etap 2: Wybór funkcji kształtu

Na każdym elemencie rozwiązanie jest przybliżane za pomocą funkcji kształtu. Dla elementu liniowego 1D o węzłach $x_1$ i $x_2$:

$$N_1(\xi) = \frac{1 - \xi}{2}, \qquad N_2(\xi) = \frac{1 + \xi}{2}$$

gdzie $\xi \in [-1, 1]$ to współrzędna lokalna (naturalna) elementu.

Przybliżenie na elemencie:

$$u^e(x) = N_1(\xi) \, u_1 + N_2(\xi) \, u_2 = \mathbf{N}^T \mathbf{u}^e$$

Funkcje kształtu spełniają warunek **partycji jedności**: $\sum_i N_i(x) = 1$.

### Etap 3: Wyznaczenie macierzy sztywności elementu

Macierz sztywności elementu $\mathbf{K}^e$ wynika z podstawienia przybliżenia MES do sformułowania słabego. Dla elementu liniowego 1D:

$$K_{ij}^e = \int_{\Omega^e} k(x) \frac{dN_i}{dx} \frac{dN_j}{dx} \, dx$$

Dla elementu o stałym $k$ i długości $h_e$:

$$\mathbf{K}^e = \frac{k}{h_e} \begin{bmatrix} 1 & -1 \\ -1 & 1 \end{bmatrix}$$

Wektor obciążeń elementu:

$$f_i^e = \int_{\Omega^e} f(x) N_i(x) \, dx$$

### Etap 4: Złożenie globalnego układu równań (assembly)

Lokalne macierze sztywności i wektory obciążeń są składane w globalny układ:

$$\mathbf{K} \mathbf{u} = \mathbf{f}$$

Złożenie polega na dodaniu wkładów poszczególnych elementów do odpowiednich pozycji macierzy globalnej, zgodnie z numeracją globalną węzłów. Jeśli element $e$ ma węzły globalne $i$ i $j$, to:

$$K_{ii} \mathrel{+}= K_{11}^e, \quad K_{ij} \mathrel{+}= K_{12}^e, \quad K_{ji} \mathrel{+}= K_{21}^e, \quad K_{jj} \mathrel{+}= K_{22}^e$$

### Etap 5: Nałożenie warunków brzegowych

- **Warunki Dirichleta** (istotne) — wymuszają wartość rozwiązania w węźle. Realizowane przez modyfikację globalnego układu równań (np. wyzerowanie wiersza i kolumny, ustawienie 1 na diagonali i wartości warunku w wektorze prawej strony).
- **Warunki Neumanna** (naturalne) — wymuszają wartość pochodnej na brzegu. Wchodzą naturalnie do sformułowania słabego jako dodatkowy składnik wektora obciążeń.

### Etap 6: Rozwiązanie układu równań

Po nałożeniu warunków brzegowych rozwiązujemy układ $\mathbf{K}\mathbf{u} = \mathbf{f}$ metodami:
- **Bezpośrednimi** — eliminacja Gaussa, dekompozycja LU/Cholesky'ego (dla małych układów)
- **Iteracyjnymi** — metoda gradientów sprzężonych (CG), GMRES (dla dużych, rzadkich układów)

Wynikiem są wartości węzłowe $u_i$, z których odtwarzamy rozwiązanie w dowolnym punkcie dziedziny za pomocą funkcji kształtu.

## Typy elementów skończonych

| Wymiar | Element | Węzły (liniowy) | Węzły (kwadratowy) | Zastosowanie |
|---|---|---|---|---|
| 1D | Odcinek | 2 | 3 | Belki, pręty |
| 2D | Trójkąt | 3 | 6 | Geometrie nieregularne |
| 2D | Czworokąt | 4 | 8/9 | Geometrie regularne |
| 3D | Czworościan | 4 | 10 | Bryły nieregularne |
| 3D | Sześcian (hex) | 8 | 20/27 | Bryły regularne |

**Elementy liniowe** — funkcje kształtu są wielomianami stopnia 1 (proste krawędzie).

**Elementy kwadratowe** — funkcje kształtu są wielomianami stopnia 2 (zakrzywione krawędzie, lepsza dokładność przy mniejszej liczbie elementów).

## Przykłady

### Przykład: Rozwiązanie problemu 1D metodą MES — belka pod obciążeniem

Rozważmy pręt o długości $L = 4$ m, stałej sztywności $EA = 1$ (dla uproszczenia), obciążony siłą rozłożoną $f(x) = 1$ N/m. Warunki brzegowe: $u(0) = 0$ (utwierdzenie), $EA \cdot u'(4) = 0$ (wolny koniec).

Równanie różniczkowe:

$$-EA \frac{d^2u}{dx^2} = f(x) \quad \Rightarrow \quad -\frac{d^2u}{dx^2} = 1$$

Rozwiązanie analityczne: $u(x) = -\frac{x^2}{2} + 4x = x(4 - \frac{x}{2})$.

**Krok 1: Dyskretyzacja** — dzielimy pręt na 2 elementy liniowe o długości $h = 2$:
- Element 1: węzły 1 ($x=0$) i 2 ($x=2$)
- Element 2: węzły 2 ($x=2$) i 3 ($x=4$)

**Krok 2: Macierze sztywności elementów** (dla $k = EA = 1$, $h = 2$):

$$\mathbf{K}^1 = \mathbf{K}^2 = \frac{1}{2} \begin{bmatrix} 1 & -1 \\ -1 & 1 \end{bmatrix}$$

**Krok 3: Wektory obciążeń elementów** (dla $f = 1$, $h = 2$):

$$\mathbf{f}^1 = \mathbf{f}^2 = \frac{f \cdot h}{2} \begin{bmatrix} 1 \\ 1 \end{bmatrix} = \begin{bmatrix} 1 \\ 1 \end{bmatrix}$$

**Krok 4: Złożenie globalnego układu** (3 węzły):

$$\mathbf{K} = \frac{1}{2}\begin{bmatrix} 1 & -1 & 0 \\ -1 & 2 & -1 \\ 0 & -1 & 1 \end{bmatrix}, \qquad \mathbf{f} = \begin{bmatrix} 1 \\ 2 \\ 1 \end{bmatrix}$$

**Krok 5: Warunki brzegowe** — $u_1 = 0$ (Dirichlet w $x = 0$). Usuwamy pierwszy wiersz i kolumnę:

$$\frac{1}{2}\begin{bmatrix} 2 & -1 \\ -1 & 1 \end{bmatrix} \begin{bmatrix} u_2 \\ u_3 \end{bmatrix} = \begin{bmatrix} 2 \\ 1 \end{bmatrix}$$

**Krok 6: Rozwiązanie:**

$$\begin{bmatrix} 2 & -1 \\ -1 & 1 \end{bmatrix} \begin{bmatrix} u_2 \\ u_3 \end{bmatrix} = \begin{bmatrix} 4 \\ 2 \end{bmatrix}$$

Z drugiego równania: $-u_2 + u_3 = 2$, z pierwszego: $2u_2 - u_3 = 4$.

Dodając: $u_2 = 6$, $u_3 = 8$.

**Porównanie z rozwiązaniem analitycznym:**

| Węzeł | $x$ | MES ($u^h$) | Analityczne ($u$) | Błąd |
|---|---|---|---|---|
| 1 | 0 | 0 | 0 | 0 |
| 2 | 2 | 6 | 6 | 0 |
| 3 | 4 | 8 | 8 | 0 |

W tym przypadku rozwiązanie MES jest dokładne w węzłach, ponieważ rozwiązanie analityczne jest wielomianem stopnia 2, a elementy liniowe z 2 elementami dają dokładne wartości węzłowe (choć przybliżenie między węzłami jest liniowe, a nie paraboliczne).

## Podsumowanie

1. **MES** to metoda numeryczna polegająca na podziale dziedziny na elementy skończone i przybliżeniu rozwiązania za pomocą funkcji kształtu. Przekształca równanie różniczkowe w układ równań algebraicznych $\mathbf{K}\mathbf{u} = \mathbf{f}$.

2. **Sformułowanie słabe** (wariacyjne) jest fundamentem MES — obniża wymagania regularności rozwiązania i pozwala na naturalne uwzględnienie warunków brzegowych Neumanna.

3. **Etapy MES**: dyskretyzacja → wybór funkcji kształtu → macierze sztywności elementów → złożenie globalnego układu → warunki brzegowe → rozwiązanie układu.

4. **Macierz sztywności** elementu wynika z całkowania iloczynu pochodnych funkcji kształtu: $K_{ij}^e = \int k \, N_i' N_j' \, dx$. Globalna macierz powstaje przez złożenie (assembly) macierzy elementowych.

5. **Typy elementów** (1D, 2D, 3D) i rząd aproksymacji (liniowy, kwadratowy) dobiera się w zależności od geometrii i wymaganej dokładności. Elementy wyższego rzędu dają lepszą dokładność przy mniejszej liczbie elementów.

6. **Zalety MES**: uniwersalność (dowolne geometrie i warunki brzegowe), solidne podstawy matematyczne, możliwość lokalnego zagęszczania siatki. **Wady**: złożoność implementacji, koszt obliczeniowy dla dużych problemów 3D.

## Powiązane pytania

- [Pytanie 36: Metoda różnic skończonych](36-metoda-roznic-skonczonych.md)
- [Pytanie 37: Wektory ortogonalne i sprzężone](37-wektory-ortogonalne-sprezone.md)
- [Pytanie 38: Metody iteracyjne dla układów równań liniowych](38-metody-iteracyjne-uklady-rownan.md)
