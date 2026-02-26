# Pytanie 48: Czym różnią się metody jedno i wielokrokowe? Co to jest rząd metody całkowania?

## Kluczowe pojęcia

- **Metoda jednokrokowa** — metoda numerycznego rozwiązywania ODE, w której przybliżenie $y_{n+1}$ zależy wyłącznie od jednego poprzedniego punktu $(t_n, y_n)$. Przykłady: metoda Eulera, metody Rungego-Kutty. Metody te są samowystarczalne — nie wymagają dodatkowych wartości startowych poza warunkiem początkowym $y_0$.
- **Metoda wielokrokowa** — metoda, w której przybliżenie $y_{n+1}$ zależy od kilku poprzednich punktów $(t_n, y_n), (t_{n-1}, y_{n-1}), \ldots, (t_{n-k+1}, y_{n-k+1})$. Ogólna postać $k$-krokowej liniowej metody wielokrokowej (LMM): $\sum_{j=0}^{k} \alpha_j y_{n+j} = h \sum_{j=0}^{k} \beta_j f_{n+j}$, gdzie $f_{n+j} = f(t_{n+j}, y_{n+j})$. Przykłady: metody Adamsa, metody BDF.
- **Rząd metody** — miara dokładności metody numerycznej. Metoda ma rząd $p$, jeśli błąd lokalny (local truncation error) wynosi $O(h^{p+1})$, co oznacza, że błąd globalny jest rzędu $O(h^p)$. Formalnie: metoda ma rząd $p$, jeśli $p$ jest największą liczbą całkowitą taką, że metoda jest dokładna dla wielomianów stopnia $\leq p$.
- **Metody Adamsa-Bashfortha (AB)** — jawne metody wielokrokowe, w których $\alpha_k = 1$, $\alpha_{k-1} = -1$, $\alpha_j = 0$ dla $j < k-1$, a $\beta_k = 0$ (jawność). Wykorzystują interpolację wielomianową wartości $f$ w poprzednich punktach do ekstrapolacji całki. Nie wymagają rozwiązywania równań nieliniowych, ale mają ograniczoną stabilność.
- **Metody Adamsa-Moultona (AM)** — niejawne metody wielokrokowe, w których $\beta_k \neq 0$ (wartość $f_{n+k}$ pojawia się po prawej stronie). Mają wyższy rząd niż AB przy tej samej liczbie kroków i lepszą stabilność, ale wymagają rozwiązywania równania niejawnego w każdym kroku.
- **Metoda predyktor-korektor** — schemat łączący metodę jawną (predyktor) z niejawną (korektor). Predyktor (np. Adams-Bashforth) daje wstępne przybliżenie $y_{n+1}^{(0)}$, które jest następnie poprawiane przez korektor (np. Adams-Moulton). Pozwala to uniknąć kosztownego rozwiązywania równań nieliniowych przy zachowaniu zalet metod niejawnych.

## Metody jednokrokowe vs wielokrokowe — definicja i porównanie

### Metody jednokrokowe

Metoda jednokrokowa oblicza $y_{n+1}$ wyłącznie na podstawie $(t_n, y_n)$:

$$y_{n+1} = \Phi(t_n, y_n, h)$$

gdzie $\Phi$ jest pewną funkcją przyrostu. Przykłady:

- **Metoda Eulera jawna** (rząd 1): $y_{n+1} = y_n + h f(t_n, y_n)$
- **Metoda Eulera niejawna** (rząd 1): $y_{n+1} = y_n + h f(t_{n+1}, y_{n+1})$
- **Metoda RK4** (rząd 4): $y_{n+1} = y_n + \frac{h}{6}(k_1 + 2k_2 + 2k_3 + k_4)$

### Metody wielokrokowe

Metoda $k$-krokowa wykorzystuje $k$ poprzednich wartości:

$$\sum_{j=0}^{k} \alpha_j y_{n+j} = h \sum_{j=0}^{k} \beta_j f(t_{n+j}, y_{n+j})$$

z normalizacją $\alpha_k = 1$. Metoda jest:
- **jawna**, gdy $\beta_k = 0$
- **niejawna**, gdy $\beta_k \neq 0$

### Porównanie

| Cecha | Metody jednokrokowe | Metody wielokrokowe |
|---|---|---|
| Wartości startowe | Tylko $y_0$ | Wymaga $y_0, y_1, \ldots, y_{k-1}$ |
| Zmiana kroku $h$ | Łatwa | Trudna (wymaga restartu lub interpolacji) |
| Koszt na krok | Wysoki (wiele ewaluacji $f$, np. 4 dla RK4) | Niski (zwykle 1-2 ewaluacje $f$) |
| Pamięć | Mała (tylko bieżący punkt) | Większa ($k$ poprzednich wartości) |
| Implementacja | Prosta | Bardziej złożona (start, zmiana kroku) |
| Stabilność | Zależy od metody | Często lepsza (metody niejawne) |

Główna zaleta metod wielokrokowych: **niski koszt obliczeniowy na krok** — osiągają wysoki rząd przy minimalnej liczbie ewaluacji $f$, ponieważ „recyklingują" wcześniej obliczone wartości.

## Rząd metody — definicja formalna

### Definicja przez błąd lokalny

Metoda numeryczna ma **rząd $p$**, jeśli błąd lokalny obcięcia (LTE) spełnia:

$$\text{LTE} = y(t_{n+1}) - y_{n+1} = O(h^{p+1})$$

przy założeniu, że $y_n = y(t_n)$ (dane wejściowe są dokładne).

Równoważnie: metoda ma rząd $p$, jeśli jest dokładna (LTE = 0) dla rozwiązań będących wielomianami stopnia $\leq p$.

### Definicja dla liniowych metod wielokrokowych

Dla LMM definiujemy **operator błędu lokalnego**:

$$L[y(t); h] = \sum_{j=0}^{k} \left[\alpha_j y(t + jh) - h \beta_j y'(t + jh)\right]$$

Rozwijając $y(t + jh)$ i $y'(t + jh)$ w szereg Taylora wokół $t$:

$$L[y(t); h] = C_0 y(t) + C_1 h y'(t) + C_2 h^2 y''(t) + \cdots$$

gdzie:

$$C_0 = \sum_{j=0}^{k} \alpha_j, \quad C_1 = \sum_{j=0}^{k} \left(j \alpha_j - \beta_j\right)$$

$$C_q = \sum_{j=0}^{k} \left(\frac{j^q}{q!} \alpha_j - \frac{j^{q-1}}{(q-1)!} \beta_j\right), \quad q \geq 2$$

Metoda ma **rząd $p$**, jeśli:

$$C_0 = C_1 = \cdots = C_p = 0, \quad C_{p+1} \neq 0$$

Stała $C_{p+1}$ nazywana jest **stałą błędu** metody.

### Konsekwencje rzędu

- **Błąd lokalny**: $\text{LTE} = C_{p+1} h^{p+1} y^{(p+1)}(\xi) + O(h^{p+2})$
- **Błąd globalny**: $e_N = O(h^p)$ (po $N \sim 1/h$ krokach)
- Podwojenie kroku $h$ zwiększa błąd globalny $2^p$-krotnie
- Zmniejszenie $h$ o połowę zmniejsza błąd $2^p$-krotnie

## Metody Adamsa

### Idea — interpolacja wielomianowa

Metody Adamsa wychodzą od postaci całkowej ODE:

$$y(t_{n+1}) = y(t_n) + \int_{t_n}^{t_{n+1}} f(t, y(t)) \, dt$$

Przybliżamy $f(t, y(t))$ wielomianem interpolacyjnym przechodzącym przez znane punkty $(t_{n}, f_n), (t_{n-1}, f_{n-1}), \ldots$ i całkujemy ten wielomian analitycznie.

### Adams-Bashforth (jawne)

Interpolujemy $f$ w punktach $t_n, t_{n-1}, \ldots, t_{n-k+1}$ wielomianem stopnia $k-1$ i całkujemy na $[t_n, t_{n+1}]$ (ekstrapolacja).

**AB rzędu 1** (metoda Eulera jawna):

$$y_{n+1} = y_n + h f_n$$

**AB rzędu 2:**

$$y_{n+1} = y_n + \frac{h}{2}\left(3f_n - f_{n-1}\right)$$

**AB rzędu 3:**

$$y_{n+1} = y_n + \frac{h}{12}\left(23f_n - 16f_{n-1} + 5f_{n-2}\right)$$

**AB rzędu 4:**

$$y_{n+1} = y_n + \frac{h}{24}\left(55f_n - 59f_{n-1} + 37f_{n-2} - 9f_{n-3}\right)$$

Metoda AB rzędu $k$ jest $k$-krokowa i wymaga jednej ewaluacji $f$ na krok (plus $k-1$ zapamiętanych wartości).

### Adams-Moulton (niejawne)

Interpolujemy $f$ w punktach $t_{n+1}, t_n, \ldots, t_{n-k+1}$ (włączając nowy punkt) i całkujemy na $[t_n, t_{n+1}]$.

**AM rzędu 1** (metoda Eulera niejawna):

$$y_{n+1} = y_n + h f_{n+1}$$

**AM rzędu 2** (metoda trapezów):

$$y_{n+1} = y_n + \frac{h}{2}\left(f_{n+1} + f_n\right)$$

**AM rzędu 3:**

$$y_{n+1} = y_n + \frac{h}{12}\left(5f_{n+1} + 8f_n - f_{n-1}\right)$$

**AM rzędu 4:**

$$y_{n+1} = y_n + \frac{h}{24}\left(9f_{n+1} + 19f_n - 5f_{n-1} + f_{n-2}\right)$$

Metoda AM rzędu $k$ jest $(k-1)$-krokowa — osiąga o jeden rząd więcej niż AB przy tej samej liczbie kroków. Wadą jest niejawność: $y_{n+1}$ pojawia się po obu stronach równania.

### Porównanie AB i AM

| Metoda | Liczba kroków | Rząd | Ewaluacje $f$/krok | Typ |
|---|---|---|---|---|
| AB1 (Euler jawny) | 1 | 1 | 1 | jawna |
| AB2 | 2 | 2 | 1 | jawna |
| AB3 | 3 | 3 | 1 | jawna |
| AB4 | 4 | 4 | 1 | jawna |
| AM1 (Euler niejawny) | 1 | 1 | wymaga iteracji | niejawna |
| AM2 (trapezy) | 1 | 2 | wymaga iteracji | niejawna |
| AM3 | 2 | 3 | wymaga iteracji | niejawna |
| AM4 | 3 | 4 | wymaga iteracji | niejawna |

## Metody predyktor-korektor

### Idea

Schemat predyktor-korektor (P-C) rozwiązuje problem niejawności metod AM bez konieczności stosowania iteracji Newtona:

1. **Predyktor** (P) — metoda jawna (np. AB) daje wstępne przybliżenie $y_{n+1}^{(0)}$
2. **Ewaluacja** (E) — obliczamy $f_{n+1}^{(0)} = f(t_{n+1}, y_{n+1}^{(0)})$
3. **Korektor** (C) — metoda niejawna (np. AM) poprawia przybliżenie, używając $f_{n+1}^{(0)}$ zamiast iteracji
4. **Ewaluacja** (E) — obliczamy $f_{n+1} = f(t_{n+1}, y_{n+1})$ dla następnego kroku

Ten schemat nazywamy **PECE** (Predict-Evaluate-Correct-Evaluate).

### Przykład: AB4-AM4 (PECE)

**Predyktor (AB4):**

$$y_{n+1}^{(0)} = y_n + \frac{h}{24}\left(55f_n - 59f_{n-1} + 37f_{n-2} - 9f_{n-3}\right)$$

**Ewaluacja:**

$$f_{n+1}^{(0)} = f(t_{n+1}, y_{n+1}^{(0)})$$

**Korektor (AM4):**

$$y_{n+1} = y_n + \frac{h}{24}\left(9f_{n+1}^{(0)} + 19f_n - 5f_{n-1} + f_{n-2}\right)$$

**Ewaluacja:**

$$f_{n+1} = f(t_{n+1}, y_{n+1})$$

Koszt: 2 ewaluacje $f$ na krok, rząd 4. Dla porównania RK4 wymaga 4 ewaluacji $f$ na krok przy tym samym rzędzie.

### Estymacja błędu w schemacie P-C

Różnica między predyktorem a korektorem daje estymację błędu lokalnego:

$$\text{err} \approx \frac{y_{n+1} - y_{n+1}^{(0)}}{1 - (C_{p+1}^{\text{AM}} / C_{p+1}^{\text{AB}})}$$

Można to wykorzystać do adaptacyjnego doboru kroku $h$.

### Algorytm — schemat PECE (pseudokod)

```
Wejście: f, t₀, y₀, T, h, wartości startowe y₀, y₁, ..., y_{k-1}
Wyjście: tablice t[], y[]

Dla n = k-1, k, ..., N-1:
    // Predyktor (Adams-Bashforth)
    y_pred = y[n] + (h/24) · (55·f[n] - 59·f[n-1] + 37·f[n-2] - 9·f[n-3])
    
    // Ewaluacja
    f_pred = f(t[n+1], y_pred)
    
    // Korektor (Adams-Moulton)
    y[n+1] = y[n] + (h/24) · (9·f_pred + 19·f[n] - 5·f[n-1] + f[n-2])
    
    // Ewaluacja
    f[n+1] = f(t[n+1], y[n+1])
    t[n+1] = t[n] + h

Zwróć t[], y[]
```

## Stabilność metod wielokrokowych

### Warunki stabilności (warunek korzeni)

Liniowa metoda wielokrokowa jest **stabilna** (zero-stable), jeśli wielomian charakterystyczny:

$$\rho(\zeta) = \sum_{j=0}^{k} \alpha_j \zeta^j$$

spełnia **warunek korzeni**: wszystkie pierwiastki $\rho(\zeta)$ leżą wewnątrz lub na okręgu jednostkowym $|\zeta| \leq 1$, a pierwiastki na okręgu są jednokrotne.

### Twierdzenie Dahlquista o stabilności

Twierdzenie Dahlquista ogranicza maksymalny rząd stabilnych metod wielokrokowych:

1. Stabilna $k$-krokowa metoda jawna ma rząd co najwyżej $k$
2. Stabilna $k$-krokowa metoda niejawna ma rząd co najwyżej $k+1$ (dla $k$ parzystego) lub $k+2$ (dla $k$ nieparzystego)
3. Nie istnieje A-stabilna LMM rzędu wyższego niż 2

Punkt 3 oznacza, że metoda trapezów (AM2, rząd 2) jest jedyną A-stabilną metodą Adamsa — metody wyższego rzędu mają ograniczone obszary stabilności.

### Porównanie stabilności

- **Metody AB** — ograniczone obszary stabilności, malejące ze wzrostem rzędu
- **Metody AM** — lepsze obszary stabilności niż AB, ale tylko AM2 jest A-stabilna
- **Metody BDF** (Backward Differentiation Formulas) — alternatywa dla problemów sztywnych, A($\alpha$)-stabilne do rzędu 6

## Przykłady

### Przykład 1: Porównanie rzędów — Euler (rząd 1), RK4 (rząd 4), AB2 (rząd 2), AB4 (rząd 4)

Rozwiązujemy $y' = -y$, $y(0) = 1$ na przedziale $[0, 1]$. Rozwiązanie dokładne: $y(t) = e^{-t}$, $y(1) = e^{-1} \approx 0.367879$.

**Porównanie błędów globalnych dla różnych kroków $h$:**

| Metoda | Rząd $p$ | $h = 0.1$ | $h = 0.05$ | Stosunek błędów |
|---|---|---|---|---|
| Euler jawny (AB1) | 1 | $5.2 \times 10^{-2}$ | $2.6 \times 10^{-2}$ | $\approx 2 = 2^1$ |
| Adams-Bashforth 2 | 2 | $1.7 \times 10^{-3}$ | $4.3 \times 10^{-4}$ | $\approx 4 = 2^2$ |
| Adams-Bashforth 4 | 4 | $2.1 \times 10^{-6}$ | $1.3 \times 10^{-7}$ | $\approx 16 = 2^4$ |
| RK4 | 4 | $1.5 \times 10^{-6}$ | $9.4 \times 10^{-8}$ | $\approx 16 = 2^4$ |

Zmniejszenie kroku o połowę zmniejsza błąd $2^p$-krotnie, co potwierdza rząd metody. AB4 i RK4 mają ten sam rząd 4, ale AB4 wymaga tylko 1 ewaluacji $f$ na krok (vs 4 dla RK4).

### Przykład 2: Schemat predyktor-korektor AB2-AM2 dla $y' = -y$

Krok $h = 0.2$, $y_0 = 1$. Potrzebujemy $y_1$ — obliczamy metodą RK4 (rozruch): $y_1 \approx 0.81873$.

$f_0 = f(0, 1) = -1$, $f_1 = f(0.2, 0.81873) = -0.81873$.

**Krok $n = 1$ ($t_2 = 0.4$):**

Predyktor (AB2):

$$y_2^{(0)} = y_1 + \frac{0.2}{2}(3f_1 - f_0) = 0.81873 + 0.1(3 \cdot (-0.81873) - (-1))$$

$$y_2^{(0)} = 0.81873 + 0.1(-2.45619 + 1) = 0.81873 - 0.14562 = 0.67311$$

Ewaluacja: $f_2^{(0)} = -0.67311$

Korektor (AM2 — trapezy):

$$y_2 = y_1 + \frac{0.2}{2}(f_2^{(0)} + f_1) = 0.81873 + 0.1(-0.67311 - 0.81873)$$

$$y_2 = 0.81873 - 0.14918 = 0.66955$$

Wartość dokładna: $y(0.4) = e^{-0.4} \approx 0.67032$.

Błąd predyktora: $|0.67311 - 0.67032| = 0.00279$

Błąd korektora: $|0.66955 - 0.67032| = 0.00077$

Korektor zmniejszył błąd ponad 3-krotnie w stosunku do predyktora.

### Przykład 3: Problem rozruchu metod wielokrokowych

Metoda AB4 wymaga wartości $y_0, y_1, y_2, y_3$. Tylko $y_0$ jest dany z warunku początkowego. Wartości $y_1, y_2, y_3$ muszą być obliczone inną metodą (rozruch):

1. **Metoda jednokrokowa** — najczęściej RK4 (ten sam rząd co AB4) dla pierwszych $k-1$ kroków
2. **Metody niższego rzędu** — AB1 → AB2 → AB3 → AB4 (stopniowe zwiększanie rzędu)

Rozruch metodą RK4 jest preferowany, ponieważ zapewnia dokładność tego samego rzędu od pierwszego kroku.

## Podsumowanie

1. **Metody jednokrokowe** (Euler, RK) obliczają $y_{n+1}$ z jednego punktu $(t_n, y_n)$ — są proste w implementacji, łatwe w zmianie kroku, ale kosztowne obliczeniowo (wiele ewaluacji $f$ na krok). Metody wielokrokowe wykorzystują $k$ poprzednich punktów, osiągając wysoki rząd przy minimalnym koszcie (1-2 ewaluacje $f$), ale wymagają rozruchu i są trudniejsze w adaptacji kroku.

2. **Rząd metody $p$** oznacza, że błąd lokalny wynosi $O(h^{p+1})$, a globalny $O(h^p)$. Zmniejszenie kroku o połowę redukuje błąd $2^p$-krotnie. Formalnie rząd wyznacza się przez rozwinięcie Taylora i zerowanie współczynników $C_0, C_1, \ldots, C_p$.

3. **Metody Adamsa-Bashfortha** (jawne) i **Adamsa-Moultona** (niejawne) to najważniejsze rodziny metod wielokrokowych. AM osiąga o jeden rząd więcej niż AB przy tej samej liczbie kroków, ale wymaga rozwiązywania równań niejawnych.

4. **Schemat predyktor-korektor** (PECE) łączy zalety obu podejść: jawny predyktor (AB) daje przybliżenie, niejawny korektor (AM) je poprawia — bez iteracji, z estymacją błędu „za darmo".

5. **Twierdzenie Dahlquista** ogranicza rząd stabilnych metod wielokrokowych i wyklucza A-stabilność dla LMM rzędu > 2. Dla problemów sztywnych stosuje się metody BDF zamiast Adamsa.

## Powiązane pytania

- [Pytanie 47: Numeryczne rozwiązywanie równań różniczkowych zwyczajnych](47-rownania-rozniczkowe-numerycznie.md)
- [Pytanie 36: Metoda różnic skończonych](36-metoda-roznic-skonczonych.md)
