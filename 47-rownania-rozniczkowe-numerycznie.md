# Pytanie 47: Omówić sposoby numerycznego rozwiązywania układów równań różniczkowych zwyczajnych.

## Kluczowe pojęcia

- **Równanie różniczkowe zwyczajne (ODE)** — równanie wiążące nieznaną funkcję $y(t)$ z jej pochodnymi względem jednej zmiennej niezależnej $t$. Ogólna postać: $y'(t) = f(t, y(t))$. Termin „zwyczajne" odróżnia je od równań różniczkowych cząstkowych (PDE), w których występują pochodne po wielu zmiennych.
- **Problem początkowy (IVP)** — zagadnienie polegające na znalezieniu funkcji $y(t)$ spełniającej ODE $y' = f(t, y)$ z zadanym warunkiem początkowym $y(t_0) = y_0$. Twierdzenie Picarda-Lindelöfa gwarantuje istnienie i jednoznaczność rozwiązania, gdy $f$ jest ciągła i spełnia warunek Lipschitza względem $y$.
- **Metoda Eulera** — najprostsza metoda jednokrokowa rozwiązywania ODE. Wersja jawna (explicit): $y_{n+1} = y_n + h f(t_n, y_n)$. Wersja niejawna (implicit): $y_{n+1} = y_n + h f(t_{n+1}, y_{n+1})$. Metoda pierwszego rzędu — błąd lokalny jest proporcjonalny do $h^2$.
- **Metoda Rungego-Kutty (RK4)** — klasyczna metoda czwartego rzędu, wykorzystująca cztery ewaluacje funkcji $f$ na każdym kroku do uzyskania wysokiej dokładności. Błąd lokalny jest proporcjonalny do $h^5$, a błąd globalny do $h^4$. Najczęściej stosowana metoda jednokrokowa w praktyce.
- **Krok czasowy ($h$)** — odległość między kolejnymi punktami siatki czasowej: $h = t_{n+1} - t_n$. Mniejszy krok daje większą dokładność, ale wymaga więcej obliczeń. Metody adaptacyjne automatycznie dobierają $h$ na podstawie estymacji błędu lokalnego.
- **Błąd lokalny i globalny** — błąd lokalny (local truncation error, LTE) to błąd popełniany w jednym kroku przy założeniu dokładnych danych wejściowych. Błąd globalny to skumulowany błąd po wielu krokach. Dla metody rzędu $p$: LTE $= O(h^{p+1})$, błąd globalny $= O(h^p)$.
- **Stabilność metody numerycznej** — zdolność metody do kontrolowania wzrostu błędów w kolejnych krokach. Metoda jest stabilna dla danego ODE i kroku $h$, jeśli błędy nie narastają nieograniczenie. Analizę stabilności przeprowadza się na równaniu testowym $y' = \lambda y$ ($\lambda \in \mathbb{C}$, $\text{Re}(\lambda) < 0$).

## Problem początkowy (IVP)

### Sformułowanie

Szukamy funkcji $y: [t_0, T] \to \mathbb{R}$ (lub $\mathbb{R}^m$ dla układu) spełniającej:

$$y'(t) = f(t, y(t)), \quad y(t_0) = y_0$$

gdzie $f: \mathbb{R} \times \mathbb{R} \to \mathbb{R}$ jest daną funkcją, a $y_0$ — warunkiem początkowym.

### Dyskretyzacja

Dzielimy przedział $[t_0, T]$ na $N$ podprzedziałów o kroku $h = (T - t_0)/N$:

$$t_n = t_0 + n \cdot h, \quad n = 0, 1, \ldots, N$$

Szukamy przybliżeń $y_n \approx y(t_n)$ w węzłach siatki.

### Twierdzenie Picarda-Lindelöfa

Jeśli $f(t, y)$ jest ciągła w otoczeniu $(t_0, y_0)$ i spełnia warunek Lipschitza:

$$|f(t, y_1) - f(t, y_2)| \leq L |y_1 - y_2|$$

dla pewnej stałej $L > 0$, to problem początkowy ma dokładnie jedno rozwiązanie w pewnym otoczeniu $t_0$.

## Metoda Eulera

### Metoda Eulera jawna (explicit)

Najprostsza metoda numeryczna — przybliżamy pochodną ilorazem różnicowym w przód:

$$y_{n+1} = y_n + h \cdot f(t_n, y_n)$$

Geometrycznie: w każdym kroku poruszamy się po stycznej do krzywej rozwiązania.

### Metoda Eulera niejawna (implicit)

$$y_{n+1} = y_n + h \cdot f(t_{n+1}, y_{n+1})$$

Wartość $y_{n+1}$ pojawia się po obu stronach równania — wymaga rozwiązania równania (nieliniowego w ogólności) w każdym kroku, np. metodą Newtona. Jest jednak znacznie bardziej stabilna niż metoda jawna.

### Wyprowadzenie z rozwinięcia Taylora

Rozwijamy $y(t_{n+1})$ w szereg Taylora wokół $t_n$:

$$y(t_{n+1}) = y(t_n) + h \cdot y'(t_n) + \frac{h^2}{2} y''(t_n) + O(h^3)$$

Ponieważ $y'(t_n) = f(t_n, y(t_n))$, obcinając wyrazy od $h^2$ wzwyż otrzymujemy metodę Eulera jawną. Stąd **błąd lokalny** metody Eulera wynosi:

$$\text{LTE} = \frac{h^2}{2} y''(\xi) = O(h^2)$$

a **błąd globalny** (po $N = (T - t_0)/h$ krokach):

$$e_N = O(h)$$

Metoda Eulera jest więc **metodą pierwszego rzędu**.

### Algorytm — metoda Eulera jawna (pseudokod)

```
Wejście: f (funkcja ODE), t₀, y₀, T (koniec przedziału), h (krok)
Wyjście: tablice t[], y[] z przybliżonym rozwiązaniem

N = ⌊(T - t₀) / h⌋
t[0] = t₀
y[0] = y₀

Dla n = 0, 1, ..., N-1:
    y[n+1] = y[n] + h · f(t[n], y[n])
    t[n+1] = t[n] + h

Zwróć t[], y[]
```

## Metody Rungego-Kutty

### Idea ogólna

Metody Rungego-Kutty poprawiają dokładność metody Eulera przez ewaluację funkcji $f$ w kilku punktach pośrednich wewnątrz kroku $[t_n, t_{n+1}]$ i obliczenie średniej ważonej tych nachyleń.

Ogólna postać $s$-etapowej metody RK:

$$y_{n+1} = y_n + h \sum_{i=1}^{s} b_i k_i$$

gdzie:

$$k_i = f\!\left(t_n + c_i h, \; y_n + h \sum_{j=1}^{s} a_{ij} k_j\right)$$

Współczynniki $a_{ij}$, $b_i$, $c_i$ definiują konkretną metodę i są zapisywane w **tablicy Butchera**:

$$\begin{array}{c|cccc}
c_1 & a_{11} & a_{12} & \cdots & a_{1s} \\
c_2 & a_{21} & a_{22} & \cdots & a_{2s} \\
\vdots & \vdots & & \ddots & \vdots \\
c_s & a_{s1} & a_{s2} & \cdots & a_{ss} \\
\hline
& b_1 & b_2 & \cdots & b_s
\end{array}$$

Dla metod jawnych $a_{ij} = 0$ dla $j \geq i$ (macierz ściśle dolnotrójkątna).

### Klasyczna metoda RK4

Najczęściej stosowana metoda Rungego-Kutty czwartego rzędu:

$$k_1 = f(t_n, \; y_n)$$

$$k_2 = f\!\left(t_n + \frac{h}{2}, \; y_n + \frac{h}{2} k_1\right)$$

$$k_3 = f\!\left(t_n + \frac{h}{2}, \; y_n + \frac{h}{2} k_2\right)$$

$$k_4 = f(t_n + h, \; y_n + h \cdot k_3)$$

$$y_{n+1} = y_n + \frac{h}{6}\bigl(k_1 + 2k_2 + 2k_3 + k_4\bigr)$$

Tablica Butchera dla RK4:

$$\begin{array}{c|cccc}
0 & & & & \\
1/2 & 1/2 & & & \\
1/2 & 0 & 1/2 & & \\
1 & 0 & 0 & 1 & \\
\hline
& 1/6 & 1/3 & 1/3 & 1/6
\end{array}$$

### Algorytm — metoda RK4 (pseudokod)

```
Wejście: f (funkcja ODE), t₀, y₀, T (koniec przedziału), h (krok)
Wyjście: tablice t[], y[] z przybliżonym rozwiązaniem

N = ⌊(T - t₀) / h⌋
t[0] = t₀
y[0] = y₀

Dla n = 0, 1, ..., N-1:
    k₁ = f(t[n], y[n])
    k₂ = f(t[n] + h/2, y[n] + h/2 · k₁)
    k₃ = f(t[n] + h/2, y[n] + h/2 · k₂)
    k₄ = f(t[n] + h, y[n] + h · k₃)
    
    y[n+1] = y[n] + (h/6) · (k₁ + 2·k₂ + 2·k₃ + k₄)
    t[n+1] = t[n] + h

Zwróć t[], y[]
```

### Analiza błędu RK4

- **Błąd lokalny**: $\text{LTE} = O(h^5)$
- **Błąd globalny**: $O(h^4)$
- **Koszt**: 4 ewaluacje funkcji $f$ na krok (vs 1 dla Eulera)
- **Efektywność**: znacznie lepsza niż Euler — podwojenie $h$ zmniejsza błąd 16-krotnie (vs 2-krotnie dla Eulera)

## Dobór kroku czasowego

### Krok stały vs adaptacyjny

- **Krok stały**: $h = \text{const}$ — prosty, ale nieefektywny (zbyt mały krok w gładkich regionach, zbyt duży w regionach szybkich zmian)
- **Krok adaptacyjny**: $h_n$ dobierany automatycznie w każdym kroku na podstawie estymacji błędu lokalnego

### Metoda zagnieżdżona (embedded RK)

Najpopularniejsze podejście do adaptacji kroku — metoda Dormand-Prince (RK45). Oblicza dwa przybliżenia różnych rzędów ($p$ i $p+1$) z tych samych ewaluacji $k_i$:

$$\hat{y}_{n+1} = y_n + h \sum_{i=1}^{s} \hat{b}_i k_i \quad \text{(rząd } p+1\text{)}$$

$$y_{n+1} = y_n + h \sum_{i=1}^{s} b_i k_i \quad \text{(rząd } p\text{)}$$

Estymacja błędu:

$$\text{err} = \|y_{n+1} - \hat{y}_{n+1}\|$$

Nowy krok:

$$h_{\text{nowy}} = h \cdot \left(\frac{\text{tol}}{\text{err}}\right)^{1/(p+1)}$$

gdzie $\text{tol}$ to zadana tolerancja. Jeśli $\text{err} > \text{tol}$, krok jest odrzucany i powtarzany z mniejszym $h$.

## Stabilność metod numerycznych

### Równanie testowe

Stabilność analizujemy na równaniu testowym Dahlquista:

$$y' = \lambda y, \quad \lambda \in \mathbb{C}, \quad \text{Re}(\lambda) < 0$$

Rozwiązanie dokładne: $y(t) = y_0 e^{\lambda t} \to 0$ gdy $t \to \infty$.

Metoda numeryczna powinna odtwarzać to zachowanie: $y_n \to 0$ gdy $n \to \infty$.

### Funkcja stabilności

Po zastosowaniu metody do równania testowego otrzymujemy:

$$y_{n+1} = R(z) \cdot y_n, \quad z = h\lambda$$

gdzie $R(z)$ to **funkcja stabilności** metody. Warunek stabilności: $|R(z)| < 1$.

**Obszar stabilności** to zbiór $\{z \in \mathbb{C} : |R(z)| < 1\}$.

### Stabilność metody Eulera jawnej

$$R(z) = 1 + z$$

Warunek $|1 + z| < 1$ definiuje koło o środku $(-1, 0)$ i promieniu $1$ na płaszczyźnie zespolonej. Dla $\lambda$ rzeczywistego ujemnego: stabilność wymaga $|h\lambda| < 2$, czyli:

$$h < \frac{2}{|\lambda|}$$

### Stabilność metody Eulera niejawnej

$$R(z) = \frac{1}{1 - z}$$

Warunek $|1/(1-z)| < 1$ jest spełniony dla całej lewej półpłaszczyzny $\text{Re}(z) < 0$. Metoda jest **A-stabilna** — stabilna dla dowolnego kroku $h > 0$ i dowolnego $\lambda$ z $\text{Re}(\lambda) < 0$.

### Stabilność RK4

Funkcja stabilności RK4:

$$R(z) = 1 + z + \frac{z^2}{2} + \frac{z^3}{6} + \frac{z^4}{24}$$

Obszar stabilności jest większy niż dla Eulera jawnego, ale metoda nie jest A-stabilna. Dla $\lambda$ rzeczywistego ujemnego: stabilność wymaga w przybliżeniu $|h\lambda| < 2.785$.

### Problemy sztywne (stiff)

Układ ODE jest **sztywny**, gdy zawiera składowe o bardzo różnych skalach czasowych (wartości własne $\lambda_i$ macierzy Jacobiego różnią się o wiele rzędów wielkości). Dla problemów sztywnych:
- Metody jawne wymagają ekstremalnie małego kroku $h$ (ograniczenie stabilności, nie dokładności)
- Metody niejawne (A-stabilne) pozwalają na duże kroki — są preferowane

## Układy równań różniczkowych zwyczajnych

### Sformułowanie

Układ $m$ równań ODE pierwszego rzędu:

$$\mathbf{y}'(t) = \mathbf{f}(t, \mathbf{y}(t)), \quad \mathbf{y}(t_0) = \mathbf{y}_0$$

gdzie $\mathbf{y} = [y_1, y_2, \ldots, y_m]^T$ i $\mathbf{f} = [f_1, f_2, \ldots, f_m]^T$.

### Redukcja ODE wyższego rzędu do układu pierwszego rzędu

Równanie $n$-tego rzędu $y^{(n)} = g(t, y, y', \ldots, y^{(n-1)})$ można sprowadzić do układu $n$ równań pierwszego rzędu przez podstawienie:

$$z_1 = y, \quad z_2 = y', \quad \ldots, \quad z_n = y^{(n-1)}$$

Otrzymujemy układ:

$$z_1' = z_2, \quad z_2' = z_3, \quad \ldots, \quad z_n' = g(t, z_1, z_2, \ldots, z_n)$$

### Stosowanie metod numerycznych do układów

Metody Eulera i RK4 stosuje się do układów identycznie jak do pojedynczego ODE — wystarczy zastąpić skalary wektorami. Np. metoda Eulera jawna dla układu:

$$\mathbf{y}_{n+1} = \mathbf{y}_n + h \cdot \mathbf{f}(t_n, \mathbf{y}_n)$$

## Przykłady

### Przykład 1: Rozwiązanie $y' = -y$, $y(0) = 1$ metodą Eulera i RK4

Rozwiązanie dokładne: $y(t) = e^{-t}$.

Rozwiązujemy na przedziale $[0, 1]$ z krokiem $h = 0.2$ (5 kroków).

**Metoda Eulera jawna:**

$f(t, y) = -y$

- $y_0 = 1.0000$
- $y_1 = y_0 + 0.2 \cdot (-y_0) = 1 - 0.2 = 0.8000$
- $y_2 = 0.8 + 0.2 \cdot (-0.8) = 0.6400$
- $y_3 = 0.64 + 0.2 \cdot (-0.64) = 0.5120$
- $y_4 = 0.512 + 0.2 \cdot (-0.512) = 0.4096$
- $y_5 = 0.4096 + 0.2 \cdot (-0.4096) = 0.3277$

Wzór ogólny: $y_n = (1 - h)^n = 0.8^n$.

**Metoda RK4:**

Krok $n = 0$ ($t_0 = 0$, $y_0 = 1$):

- $k_1 = f(0, 1) = -1$
- $k_2 = f(0.1, 1 + 0.1 \cdot (-1)) = f(0.1, 0.9) = -0.9$
- $k_3 = f(0.1, 1 + 0.1 \cdot (-0.9)) = f(0.1, 0.91) = -0.91$
- $k_4 = f(0.2, 1 + 0.2 \cdot (-0.91)) = f(0.2, 0.818) = -0.818$

$y_1 = 1 + \frac{0.2}{6}(-1 + 2(-0.9) + 2(-0.91) + (-0.818))$

$y_1 = 1 + \frac{0.2}{6}(-5.428) = 1 - 0.18093 = 0.81873$

**Porównanie wyników w $t = 1$:**

| Metoda | $y(1)$ | Rozwiązanie dokładne | Błąd bezwzględny | Błąd względny |
|---|---|---|---|---|
| Euler ($h = 0.2$) | 0.32768 | 0.36788 | 0.04020 | 10.93% |
| RK4 ($h = 0.2$) | 0.36788 | 0.36788 | 0.00001 | 0.003% |

RK4 daje wynik praktycznie dokładny nawet przy dużym kroku $h = 0.2$, podczas gdy Euler ma błąd ponad 10%.

### Przykład 2: Układ ODE — oscylator harmoniczny

Równanie oscylatora: $y'' + y = 0$, $y(0) = 1$, $y'(0) = 0$.

Rozwiązanie dokładne: $y(t) = \cos(t)$.

Redukcja do układu pierwszego rzędu:

$$z_1 = y, \quad z_2 = y'$$

$$z_1' = z_2, \quad z_2' = -z_1$$

$$\mathbf{f}(t, \mathbf{z}) = \begin{bmatrix} z_2 \\ -z_1 \end{bmatrix}, \quad \mathbf{z}(0) = \begin{bmatrix} 1 \\ 0 \end{bmatrix}$$

Stosując metodę Eulera jawną z $h = 0.1$:

- $\mathbf{z}_0 = [1, 0]^T$
- $\mathbf{z}_1 = [1, 0]^T + 0.1 \cdot [0, -1]^T = [1, -0.1]^T$
- $\mathbf{z}_2 = [1, -0.1]^T + 0.1 \cdot [-0.1, -1]^T = [0.99, -0.2]^T$
- $\mathbf{z}_3 = [0.99, -0.2]^T + 0.1 \cdot [-0.2, -0.99]^T = [0.97, -0.299]^T$

| $t$ | $z_1$ (Euler) | $\cos(t)$ (dokładne) | Błąd |
|---|---|---|---|
| 0.0 | 1.0000 | 1.0000 | 0.0000 |
| 0.1 | 1.0000 | 0.9950 | 0.0050 |
| 0.2 | 0.9900 | 0.9801 | 0.0099 |
| 0.3 | 0.9700 | 0.9553 | 0.0147 |

Metoda Eulera dla oscylatora harmonicznego wykazuje charakterystyczny problem — energia numeryczna rośnie w czasie (spirala zamiast okręgu na płaszczyźnie fazowej), co jest konsekwencją braku symplektyczności metody.

## Podsumowanie

1. **Problem początkowy (IVP)** to podstawowe zagadnienie numerycznego rozwiązywania ODE: szukamy $y(t)$ spełniającego $y' = f(t, y)$ z warunkiem $y(t_0) = y_0$. Dyskretyzujemy przedział czasowy i obliczamy przybliżenia w węzłach siatki.

2. **Metoda Eulera jawna** ($y_{n+1} = y_n + hf(t_n, y_n)$) jest najprostsza, ale ma niską dokładność (rząd 1) i ograniczoną stabilność. Metoda Eulera niejawna jest A-stabilna, ale wymaga rozwiązywania równań w każdym kroku.

3. **Metoda RK4** jest standardem w praktyce — oferuje rząd 4 (błąd globalny $O(h^4)$) przy 4 ewaluacjach $f$ na krok. Daje doskonały stosunek dokładności do kosztu obliczeniowego.

4. **Dobór kroku** ma kluczowe znaczenie — metody adaptacyjne (np. Dormand-Prince RK45) automatycznie dostosowują $h$ na podstawie estymacji błędu, zapewniając efektywność i kontrolę dokładności.

5. **Stabilność** ogranicza maksymalny krok metod jawnych ($h < 2/|\lambda|$ dla Eulera). Dla problemów sztywnych (stiff) konieczne są metody niejawne (A-stabilne), które nie mają ograniczeń stabilności.

6. **Układy ODE** i równania wyższego rzędu rozwiązuje się tymi samymi metodami po sprowadzeniu do układu pierwszego rzędu — wystarczy zastąpić skalary wektorami.

## Powiązane pytania

- [Pytanie 36: Metoda różnic skończonych](36-metoda-roznic-skonczonych.md)
- [Pytanie 48: Metody jedno- i wielokrokowe, rząd metody](48-metody-jedno-wielokrokowe.md)
