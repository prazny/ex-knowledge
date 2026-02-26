# Pytanie 36: Omówić zalety i ograniczenia metody różnic skończonych.

## Kluczowe pojęcia

- **Metoda różnic skończonych (MRS/FDM)** — metoda numerycznego rozwiązywania równań różniczkowych polegająca na zastąpieniu pochodnych w równaniu ich przybliżeniami za pomocą ilorazów różnicowych na dyskretnej siatce punktów. MRS przekształca równanie różniczkowe w układ równań algebraicznych, w którym niewiadomymi są wartości rozwiązania w węzłach siatki.
- **Siatka regularna (uniform grid)** — zbiór równomiernie rozmieszczonych punktów (węzłów) pokrywających dziedzinę obliczeniową. W 1D siatka o kroku $h$ to zbiór punktów $x_i = x_0 + i \cdot h$, $i = 0, 1, \ldots, N$. W 2D siatka jest iloczynem kartezjańskim siatek 1D o krokach $\Delta x$ i $\Delta y$.
- **Schemat różnicowy** — konkretna formuła zastępująca pochodne w równaniu różniczkowym ilorazami różnicowymi. Schemat definiuje, które węzły siatki biorą udział w aproksymacji (tzw. szablon, stencil) oraz jakie są współczynniki przy wartościach w tych węzłach.
- **Stabilność** — właściwość schematu różnicowego gwarantująca, że błędy numeryczne (zaokrągleń, obcięcia) nie narastają w sposób nieograniczony w trakcie obliczeń. Schemat jest stabilny, jeśli małe zaburzenia danych początkowych prowadzą do małych zaburzeń rozwiązania.
- **Zbieżność** — właściwość schematu różnicowego polegająca na tym, że rozwiązanie numeryczne dąży do rozwiązania dokładnego, gdy krok siatki $h \to 0$ (i krok czasowy $\Delta t \to 0$). Twierdzenie Laxa: dla schematu konsystentnego zbieżność jest równoważna stabilności.
- **Rząd dokładności** — miara szybkości zbieżności schematu różnicowego. Schemat ma rząd dokładności $p$, jeśli błąd obcięcia (local truncation error) jest rzędu $O(h^p)$. Wyższy rząd oznacza szybszą zbieżność przy zagęszczaniu siatki.

## Idea metody różnic skończonych

### Ogólna koncepcja

Metoda różnic skończonych polega na:

1. **Dyskretyzacji dziedziny** — zastąpieniu ciągłej dziedziny skończonym zbiorem punktów (siatką).
2. **Aproksymacji pochodnych** — zastąpieniu pochodnych w równaniu różniczkowym ilorazami różnicowymi opartymi na wartościach funkcji w węzłach siatki.
3. **Rozwiązaniu układu równań algebraicznych** — otrzymany układ równań (liniowych lub nieliniowych) jest rozwiązywany metodami algebry numerycznej.

Rozważmy równanie różniczkowe zwyczajne:

$$-\frac{d^2u}{dx^2} = f(x), \quad x \in (0, L)$$

z warunkami brzegowymi $u(0) = \alpha$, $u(L) = \beta$.

Po wprowadzeniu siatki $x_i = i \cdot h$, $h = L/N$, i zastąpieniu drugiej pochodnej różnicą centralną otrzymujemy:

$$-\frac{u_{i-1} - 2u_i + u_{i+1}}{h^2} = f(x_i), \quad i = 1, 2, \ldots, N-1$$

co daje układ $(N-1)$ równań liniowych z $(N-1)$ niewiadomymi $u_1, u_2, \ldots, u_{N-1}$.

### Porównanie z MES

W odróżnieniu od metody elementów skończonych (MES), MRS:
- Nie wymaga sformułowania słabego (wariacyjnego) — operuje bezpośrednio na równaniu różniczkowym w postaci silnej.
- Stosuje regularne siatki prostokątne (choć istnieją rozszerzenia na siatki nieregularne).
- Jest prostsza koncepcyjnie i łatwiejsza w implementacji dla prostych geometrii.

## Aproksymacja pochodnych ilorazami różnicowymi

### Różnica w przód (forward difference)

Aproksymacja pierwszej pochodnej w punkcie $x_i$:

$$\frac{du}{dx}\bigg|_{x_i} \approx \frac{u_{i+1} - u_i}{h}$$

Rząd dokładności: $O(h)$ — schemat pierwszego rzędu. Błąd obcięcia wynika z rozwinięcia Taylora:

$$u(x_i + h) = u(x_i) + h u'(x_i) + \frac{h^2}{2} u''(x_i) + O(h^3)$$

$$\Rightarrow \frac{u_{i+1} - u_i}{h} = u'(x_i) + \frac{h}{2} u''(x_i) + O(h^2)$$

### Różnica wstecz (backward difference)

$$\frac{du}{dx}\bigg|_{x_i} \approx \frac{u_i - u_{i-1}}{h}$$

Rząd dokładności: $O(h)$ — schemat pierwszego rzędu.

### Różnica centralna (central difference)

$$\frac{du}{dx}\bigg|_{x_i} \approx \frac{u_{i+1} - u_{i-1}}{2h}$$

Rząd dokładności: $O(h^2)$ — schemat drugiego rzędu. Jest to średnia arytmetyczna różnicy w przód i wstecz.

### Druga pochodna — różnica centralna

$$\frac{d^2u}{dx^2}\bigg|_{x_i} \approx \frac{u_{i-1} - 2u_i + u_{i+1}}{h^2}$$

Rząd dokładności: $O(h^2)$. Jest to najczęściej stosowana aproksymacja drugiej pochodnej w MRS.

### Podsumowanie aproksymacji

| Pochodna | Formuła | Rząd | Szablon |
|---|---|---|---|
| $u'$ — w przód | $(u_{i+1} - u_i)/h$ | $O(h)$ | $[i, i+1]$ |
| $u'$ — wstecz | $(u_i - u_{i-1})/h$ | $O(h)$ | $[i-1, i]$ |
| $u'$ — centralna | $(u_{i+1} - u_{i-1})/(2h)$ | $O(h^2)$ | $[i-1, i+1]$ |
| $u''$ — centralna | $(u_{i-1} - 2u_i + u_{i+1})/h^2$ | $O(h^2)$ | $[i-1, i, i+1]$ |

## Schematy jawne i niejawne

Rozróżnienie schematów jawnych i niejawnych jest kluczowe dla problemów zależnych od czasu (równania paraboliczne, hiperboliczne).

### Schemat jawny (explicit)

W schemacie jawnym wartości w nowym kroku czasowym $n+1$ oblicza się bezpośrednio z wartości w kroku $n$ (i ewentualnie wcześniejszych).

Dla równania ciepła $\frac{\partial u}{\partial t} = \alpha \frac{\partial^2 u}{\partial x^2}$:

**Schemat Eulera w przód (FTCS — Forward Time, Central Space):**

$$u_i^{n+1} = u_i^n + r \left( u_{i-1}^n - 2u_i^n + u_{i+1}^n \right)$$

gdzie $r = \frac{\alpha \Delta t}{(\Delta x)^2}$ to liczba Couranta (parametr siatki).

**Zalety:** prostota implementacji, brak konieczności rozwiązywania układu równań w każdym kroku.

**Wady:** warunkowo stabilny — wymaga spełnienia warunku stabilności (patrz warunek CFL).

### Schemat niejawny (implicit)

W schemacie niejawnym wartości w kroku $n+1$ zależą od siebie nawzajem, co wymaga rozwiązania układu równań w każdym kroku czasowym.

**Schemat Eulera wstecz (BTCS — Backward Time, Central Space):**

$$u_i^{n+1} - r \left( u_{i-1}^{n+1} - 2u_i^{n+1} + u_{i+1}^{n+1} \right) = u_i^n$$

**Schemat Cranka-Nicolson** (średnia schematów jawnego i niejawnego):

$$u_i^{n+1} - \frac{r}{2}\left(u_{i-1}^{n+1} - 2u_i^{n+1} + u_{i+1}^{n+1}\right) = u_i^n + \frac{r}{2}\left(u_{i-1}^n - 2u_i^n + u_{i+1}^n\right)$$

**Zalety:** bezwarunkowo stabilny (Euler wstecz) lub stabilny przy łagodniejszych warunkach (Crank-Nicolson), pozwala na większe kroki czasowe.

**Wady:** wymaga rozwiązania układu równań (trójdiagonalnego) w każdym kroku czasowym.

### Porównanie schematów

| Cecha | Schemat jawny (FTCS) | Schemat niejawny (BTCS) | Crank-Nicolson |
|---|---|---|---|
| Stabilność | Warunkowa ($r \leq 1/2$) | Bezwarunkowa | Bezwarunkowa |
| Rząd w czasie | $O(\Delta t)$ | $O(\Delta t)$ | $O(\Delta t^2)$ |
| Rząd w przestrzeni | $O(\Delta x^2)$ | $O(\Delta x^2)$ | $O(\Delta x^2)$ |
| Układ równań | Nie | Tak (trójdiagonalny) | Tak (trójdiagonalny) |
| Koszt na krok | Niski | Wyższy | Wyższy |

## Stabilność i zbieżność

### Konsystencja (zgodność)

Schemat różnicowy jest **konsystentny** z równaniem różniczkowym, jeśli błąd obcięcia (local truncation error) dąży do zera, gdy $h \to 0$ i $\Delta t \to 0$. Innymi słowy, schemat różnicowy w granicy przechodzi w równanie różniczkowe.

### Stabilność

Schemat jest **stabilny**, jeśli błędy nie narastają w sposób nieograniczony. Stabilność bada się najczęściej metodą von Neumanna (analiza Fouriera): podstawiamy $u_i^n = \xi^n e^{ik x_i}$ do schematu i sprawdzamy, czy współczynnik wzmocnienia $|\xi| \leq 1$.

### Warunek CFL (Courant-Friedrichs-Lewy)

Warunek CFL jest koniecznym warunkiem stabilności schematów jawnych. Dla równania ciepła 1D ze schematem FTCS:

$$r = \frac{\alpha \Delta t}{(\Delta x)^2} \leq \frac{1}{2}$$

Dla równania falowego $\frac{\partial^2 u}{\partial t^2} = c^2 \frac{\partial^2 u}{\partial x^2}$ ze schematem jawnym:

$$C = \frac{c \Delta t}{\Delta x} \leq 1$$

gdzie $C$ to liczba Couranta. Warunek CFL oznacza, że **numeryczna dziedzina zależności musi zawierać fizyczną dziedzinę zależności** — informacja nie może „przeskakiwać" węzłów siatki w jednym kroku czasowym.

### Twierdzenie Laxa o równoważności

Dla liniowego, konsystentnego schematu różnicowego:

$$\text{Stabilność} \iff \text{Zbieżność}$$

Jest to fundamentalne twierdzenie MRS: jeśli schemat jest konsystentny z równaniem różniczkowym, to jedynym warunkiem zbieżności jest stabilność. Twierdzenie Laxa pozwala weryfikować zbieżność poprzez sprawdzenie stabilności, co jest zwykle prostsze.

### Rząd zbieżności

Jeśli schemat ma rząd dokładności $p$ w przestrzeni i $q$ w czasie, to globalny błąd jest rzędu:

$$\|u^h - u\| = O((\Delta x)^p + (\Delta t)^q)$$

Na przykład schemat Cranka-Nicolson ma rząd $O((\Delta x)^2 + (\Delta t)^2)$.

## Zalety i ograniczenia MRS vs MES

### Zalety MRS

1. **Prostota koncepcyjna i implementacyjna** — bezpośrednie zastąpienie pochodnych ilorazami różnicowymi jest intuicyjne i łatwe do zaprogramowania.
2. **Efektywność na siatkach regularnych** — dla prostokątnych dziedzin z regularną siatką MRS jest bardzo wydajna, a powstające macierze mają regularną strukturę (np. trójdiagonalną).
3. **Łatwość analizy stabilności** — metoda von Neumanna i warunek CFL dają jasne kryteria stabilności.
4. **Niski koszt obliczeniowy na węzeł** — brak konieczności obliczania całek (jak w MES), co upraszcza implementację.
5. **Naturalne zastosowanie do problemów na prostokątnych dziedzinach** — równania ciepła, fali, dyfuzji na prostokątach/prostopadłościanach.

### Ograniczenia MRS

1. **Trudność z nieregularnymi geometriami** — MRS wymaga siatki prostokątnej, co utrudnia modelowanie złożonych kształtów (w przeciwieństwie do MES, która obsługuje dowolne geometrie).
2. **Problemy z warunkami brzegowymi na krzywoliniowych brzegach** — nałożenie warunków brzegowych na brzegach niepokrywających się z liniami siatki wymaga specjalnych technik (np. metoda ghost points).
3. **Ograniczona elastyczność zagęszczania siatki** — lokalne zagęszczanie siatki jest trudniejsze niż w MES (wymaga siatek zagnieżdżonych lub nieregularnych).
4. **Niższy rząd dokładności przy tym samym nakładzie** — dla osiągnięcia wysokiej dokładności na nieregularnych siatkach MES z elementami wyższego rzędu jest zwykle lepsza.
5. **Brak solidnych podstaw wariacyjnych** — MRS nie opiera się na sformułowaniu słabym, co utrudnia analizę zbieżności dla problemów nieliniowych i złożonych.

### Porównanie tabelaryczne MRS vs MES

| Cecha | MRS (FDM) | MES (FEM) |
|---|---|---|
| Podstawa matematyczna | Rozwinięcie Taylora | Sformułowanie wariacyjne |
| Geometria dziedziny | Prostokątna (regularna) | Dowolna |
| Siatka | Regularna, strukturalna | Niestrukturalna, adaptacyjna |
| Implementacja | Prosta | Złożona |
| Warunki brzegowe | Proste na prostych brzegach | Naturalne w sformułowaniu słabym |
| Zagęszczanie lokalne | Trudne | Łatwe (adaptive mesh refinement) |
| Analiza stabilności | Dobrze rozwinięta (von Neumann) | Oparta na właściwościach form bilinearnych |
| Typowe zastosowania | CFD, meteorologia, akustyka | Mechanika konstrukcji, elektromagnetyzm |

## Przykłady

### Przykład: Rozwiązanie równania ciepła 1D metodą różnic skończonych

Rozważmy równanie ciepła na pręcie o długości $L = 1$:

$$\frac{\partial u}{\partial t} = \alpha \frac{\partial^2 u}{\partial x^2}, \quad x \in (0, 1), \quad t > 0$$

z warunkami:
- Początkowy: $u(x, 0) = \sin(\pi x)$
- Brzegowe: $u(0, t) = 0$, $u(1, t) = 0$

Współczynnik dyfuzji: $\alpha = 1$.

Rozwiązanie analityczne: $u(x, t) = e^{-\pi^2 t} \sin(\pi x)$.

**Krok 1: Dyskretyzacja**

Siatka przestrzenna: $N = 4$ przedziały, $\Delta x = 0.25$, węzły $x_0 = 0, x_1 = 0.25, x_2 = 0.5, x_3 = 0.75, x_4 = 1$.

Krok czasowy: $\Delta t = 0.01$ (sprawdzamy warunek CFL: $r = \frac{1 \cdot 0.01}{0.25^2} = 0.16 \leq 0.5$ ✓).

**Krok 2: Warunki początkowe**

| $i$ | $x_i$ | $u_i^0 = \sin(\pi x_i)$ |
|---|---|---|
| 0 | 0 | 0 |
| 1 | 0.25 | 0.7071 |
| 2 | 0.50 | 1.0000 |
| 3 | 0.75 | 0.7071 |
| 4 | 1.00 | 0 |

**Krok 3: Schemat jawny FTCS**

Stosujemy schemat:

$$u_i^{n+1} = u_i^n + r(u_{i-1}^n - 2u_i^n + u_{i+1}^n)$$

z $r = 0.16$.

**Iteracja 1** ($n = 0 \to n = 1$, $t = 0.01$):

Dla $i = 1$:

$$u_1^1 = 0.7071 + 0.16(0 - 2 \cdot 0.7071 + 1.0000) = 0.7071 + 0.16 \cdot (-0.4142) = 0.7071 - 0.0663 = 0.6408$$

Dla $i = 2$:

$$u_2^1 = 1.0000 + 0.16(0.7071 - 2 \cdot 1.0000 + 0.7071) = 1.0000 + 0.16 \cdot (-0.5858) = 1.0000 - 0.0937 = 0.9063$$

Dla $i = 3$ (z symetrii): $u_3^1 = 0.6408$

**Krok 4: Porównanie z rozwiązaniem analitycznym** (dla $t = 0.01$):

$u(x, 0.01) = e^{-\pi^2 \cdot 0.01} \sin(\pi x) = e^{-0.0987} \sin(\pi x) \approx 0.9060 \cdot \sin(\pi x)$

| $i$ | $x_i$ | MRS ($u_i^1$) | Analityczne | Błąd bezwzględny |
|---|---|---|---|---|
| 1 | 0.25 | 0.6408 | 0.6407 | 0.0001 |
| 2 | 0.50 | 0.9063 | 0.9060 | 0.0003 |
| 3 | 0.75 | 0.6408 | 0.6407 | 0.0001 |

Błąd jest bardzo mały, co potwierdza poprawność schematu. Przy zagęszczaniu siatki ($\Delta x \to 0$, $\Delta t \to 0$ z zachowaniem warunku CFL) błąd maleje jak $O((\Delta x)^2 + \Delta t)$.

## Podsumowanie

1. **MRS (FDM)** to metoda numeryczna polegająca na zastąpieniu pochodnych w równaniu różniczkowym ilorazami różnicowymi na dyskretnej siatce. Jest koncepcyjnie prosta i łatwa w implementacji.

2. **Aproksymacje różnicowe** — różnice w przód i wstecz mają rząd $O(h)$, różnice centralne mają rząd $O(h^2)$. Wybór aproksymacji wpływa na dokładność i stabilność schematu.

3. **Schematy jawne** (np. FTCS) są proste, ale warunkowo stabilne — wymagają spełnienia warunku CFL ($r \leq 1/2$ dla równania ciepła). **Schematy niejawne** (np. BTCS, Crank-Nicolson) są bezwarunkowo stabilne, ale wymagają rozwiązywania układu równań w każdym kroku.

4. **Twierdzenie Laxa**: dla konsystentnego schematu liniowego zbieżność $\iff$ stabilność. Warunek CFL jest koniecznym warunkiem stabilności schematów jawnych.

5. **Zalety MRS**: prostota, efektywność na siatkach regularnych, łatwość analizy stabilności. **Ograniczenia**: trudność z nieregularnymi geometriami, ograniczona elastyczność zagęszczania siatki, brak podstaw wariacyjnych.

6. **MRS vs MES**: MRS jest lepsza dla prostych geometrii i regularnych siatek; MES jest lepsza dla złożonych geometrii, nieregularnych siatek i problemów wymagających lokalnego zagęszczania.

## Powiązane pytania

- [Pytanie 35: Metoda elementów skończonych](35-metoda-elementow-skonczonych.md)
- [Pytanie 37: Wektory ortogonalne i sprzężone](37-wektory-ortogonalne-sprezone.md)
- [Pytanie 38: Metody iteracyjne dla układów równań liniowych](38-metody-iteracyjne-uklady-rownan.md)
- [Pytanie 47: Numeryczne rozwiązywanie równań różniczkowych](47-rownania-rozniczkowe-numerycznie.md)
