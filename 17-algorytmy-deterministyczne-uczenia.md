# Pytanie 17: Algorytmy deterministyczne uczenia sieci neuronowych.

## Kluczowe pojęcia

- **Gradient prosty (SGD — Stochastic Gradient Descent)** — podstawowy algorytm optymalizacji, w którym wagi sieci aktualizowane są w kierunku przeciwnym do gradientu funkcji kosztu: $\mathbf{w}_{t+1} = \mathbf{w}_t - \eta \cdot \nabla L(\mathbf{w}_t)$. „Stochastic" oznacza, że gradient jest estymowany na podstawie pojedynczego przykładu lub mini-batcha, a nie całego zbioru treningowego. SGD jest prosty w implementacji, ale wrażliwy na dobór learning rate i może oscylować w wąskich dolinach funkcji kosztu.
- **Momentum** — rozszerzenie SGD o „bezwładność" aktualizacji wag. Algorytm akumuluje wykładniczo ważoną średnią poprzednich gradientów (wektor prędkości $\mathbf{v}$), co przyspiesza zbieżność w kierunkach o spójnym gradiencie i tłumi oscylacje w kierunkach o zmiennym znaku gradientu. Parametr $\beta$ (typowo 0.9) kontroluje siłę bezwładności. Momentum pozwala „prześlizgnąć się" przez płaskie regiony i płytkie minima lokalne.
- **RMSProp (Root Mean Square Propagation)** — adaptacyjny algorytm optymalizacji zaproponowany przez Geoffreya Hintona. RMSProp utrzymuje wykładniczo ważoną średnią kwadratów gradientów $\mathbf{s}_t$ i normalizuje bieżący gradient przez $\sqrt{\mathbf{s}_t + \epsilon}$. Dzięki temu parametry o dużych gradientach otrzymują mniejsze aktualizacje, a parametry o małych gradientach — większe. Rozwiązuje problem malejącego learning rate w Adagrad.
- **Adam (Adaptive Moment Estimation)** — algorytm łączący idee Momentum (estymacja pierwszego momentu — średniej gradientów) i RMSProp (estymacja drugiego momentu — średniej kwadratów gradientów). Adam dodatkowo stosuje korekcję biasu (bias correction) dla obu momentów, co stabilizuje początkowe iteracje. Jest najczęściej stosowanym optymalizatorem w praktyce deep learning ze względu na dobrą zbieżność przy domyślnych hiperparametrach ($\beta_1 = 0.9$, $\beta_2 = 0.999$, $\eta = 10^{-3}$).
- **Learning rate schedule** — strategia zmiany współczynnika uczenia $\eta$ w trakcie trenowania. Popularne schematy: step decay (redukcja $\eta$ co $k$ epok), exponential decay ($\eta_t = \eta_0 \cdot \gamma^t$), cosine annealing ($\eta_t = \frac{\eta_0}{2}(1 + \cos(\frac{t\pi}{T}))$), warm-up (stopniowe zwiększanie $\eta$ na początku trenowania). Odpowiedni schedule pozwala na szybką zbieżność na początku i precyzyjne dostrojenie wag pod koniec uczenia.

## Przegląd metod optymalizacji

### Kontekst — problem optymalizacji w sieciach neuronowych

Uczenie sieci neuronowej sprowadza się do minimalizacji funkcji kosztu $L(\mathbf{w})$ w przestrzeni wag $\mathbf{w} \in \mathbb{R}^d$, gdzie $d$ to łączna liczba parametrów sieci (wagi + biasy). Funkcja kosztu jest zazwyczaj nieliniowa, niewypukła i wielowymiarowa (współczesne sieci mają miliony do miliardów parametrów).

Algorytmy deterministyczne uczenia to metody optymalizacji oparte na gradiencie, które w sposób systematyczny (deterministyczny) aktualizują wagi sieci na podstawie obliczonego gradientu funkcji kosztu. W odróżnieniu od metod stochastycznych (np. algorytmy genetyczne, symulowane wyżarzanie), metody gradientowe wykorzystują informację o kierunku najszybszego spadku funkcji kosztu.

### Ewolucja algorytmów optymalizacji

```
SGD (1951)
 │
 ├── Momentum (1964, Polyak)
 │    │
 │    └── Nesterov Accelerated Gradient (1983)
 │
 ├── Adagrad (2011, Duchi et al.)
 │    │
 │    ├── RMSProp (2012, Hinton)
 │    │    │
 │    │    └── Adam (2014, Kingma & Ba)
 │    │         │
 │    │         ├── AdaMax (2014)
 │    │         ├── Nadam (2016)
 │    │         └── AdamW (2017, Loshchilov & Hutter)
 │    │
 │    └── Adadelta (2012, Zeiler)
 │
 └── Learning rate schedules
      ├── Step decay
      ├── Exponential decay
      ├── Cosine annealing (2016, Loshchilov & Hutter)
      └── Warm-up + decay
```

### Klasyfikacja metod

| Kategoria | Metody | Cecha charakterystyczna |
|---|---|---|
| **Stały learning rate** | SGD | Jeden globalny $\eta$ dla wszystkich parametrów |
| **Momentum** | SGD + Momentum, NAG | Akumulacja historii gradientów (pierwszy moment) |
| **Adaptacyjne** | Adagrad, RMSProp, Adadelta | Indywidualny $\eta$ dla każdego parametru (drugi moment) |
| **Kombinowane** | Adam, AdaMax, Nadam, AdamW | Pierwszy + drugi moment z korekcją biasu |


## Wzory aktualizacji wag — szczegółowy opis każdej metody

### 1. SGD (Stochastic Gradient Descent)

Najprostsza metoda gradientowa. Wagi aktualizowane są proporcjonalnie do gradientu funkcji kosztu:

$$\mathbf{w}_{t+1} = \mathbf{w}_t - \eta \cdot \nabla L(\mathbf{w}_t)$$

gdzie:
- $\mathbf{w}_t$ — wektor wag w kroku $t$
- $\eta$ — współczynnik uczenia (learning rate)
- $\nabla L(\mathbf{w}_t)$ — gradient funkcji kosztu w punkcie $\mathbf{w}_t$

**Właściwości:**
- Prosty w implementacji — wymaga tylko obliczenia gradientu
- Wrażliwy na dobór $\eta$ — zbyt duży powoduje oscylacje, zbyt mały spowalnia zbieżność
- Oscyluje w wąskich dolinach funkcji kosztu (różne krzywizny w różnych kierunkach)
- Nie posiada mechanizmu „pamięci" — każdy krok zależy wyłącznie od bieżącego gradientu

```
Pseudokod: SGD
─────────────────────────────
Wejście: η (learning rate), w₀ (wagi początkowe)

DLA t = 1, 2, 3, ...:
    g_t ← ∇L(w_t)           // oblicz gradient
    w_{t+1} ← w_t - η · g_t  // aktualizuj wagi
```

### 2. SGD z Momentum

Momentum dodaje „bezwładność" do aktualizacji wag, akumulując wykładniczo ważoną średnią poprzednich gradientów:

$$\mathbf{v}_{t+1} = \beta \cdot \mathbf{v}_t + \nabla L(\mathbf{w}_t)$$

$$\mathbf{w}_{t+1} = \mathbf{w}_t - \eta \cdot \mathbf{v}_{t+1}$$

gdzie:
- $\mathbf{v}_t$ — wektor prędkości (akumulowany gradient), $\mathbf{v}_0 = \mathbf{0}$
- $\beta$ — współczynnik momentum (typowo $\beta = 0.9$)

**Alternatywna forma** (stosowana w PyTorch):

$$\mathbf{v}_{t+1} = \beta \cdot \mathbf{v}_t + \eta \cdot \nabla L(\mathbf{w}_t)$$

$$\mathbf{w}_{t+1} = \mathbf{w}_t - \mathbf{v}_{t+1}$$

**Intuicja fizyczna:** Momentum działa jak kulka tocząca się po powierzchni funkcji kosztu — nabiera prędkości na stokach i prześlizguje się przez płaskie regiony dzięki zgromadzonej bezwładności.

**Właściwości:**
- Przyspiesza zbieżność w kierunkach o spójnym gradiencie (kulka nabiera prędkości)
- Tłumi oscylacje w kierunkach o zmiennym znaku gradientu (oscylacje się znoszą)
- Pomaga uciec z płytkich minimów lokalnych i punktów siodłowych
- Wymaga doboru dodatkowego hiperparametru $\beta$

```
Pseudokod: SGD z Momentum
─────────────────────────────
Wejście: η, β, w₀
v₀ ← 0

DLA t = 1, 2, 3, ...:
    g_t ← ∇L(w_t)                  // oblicz gradient
    v_t ← β · v_{t-1} + g_t        // akumuluj prędkość
    w_{t+1} ← w_t - η · v_t        // aktualizuj wagi
```

### 3. Nesterov Accelerated Gradient (NAG)

Wariant momentum, w którym gradient obliczany jest w „przesuniętym" punkcie $\mathbf{w}_t - \eta \beta \mathbf{v}_t$ (look-ahead):

$$\mathbf{v}_{t+1} = \beta \cdot \mathbf{v}_t + \nabla L(\mathbf{w}_t - \eta \beta \cdot \mathbf{v}_t)$$

$$\mathbf{w}_{t+1} = \mathbf{w}_t - \eta \cdot \mathbf{v}_{t+1}$$

**Intuicja:** Zamiast obliczać gradient w bieżącym punkcie, NAG „patrzy w przód" — oblicza gradient w punkcie, do którego momentum by nas zaprowadziło. Pozwala to na wcześniejsze korygowanie kierunku, gdy momentum prowadzi w złą stronę.

**Właściwości:**
- Lepsza zbieżność niż klasyczne momentum dla funkcji wypukłych
- „Hamuje" przed minimami — gradient w punkcie look-ahead sygnalizuje zbliżanie się do minimum
- Szeroko stosowany w praktyce jako ulepszenie momentum

### 4. Adagrad (Adaptive Gradient)

Adagrad adaptuje learning rate indywidualnie dla każdego parametru na podstawie historii gradientów:

$$\mathbf{s}_{t+1} = \mathbf{s}_t + \nabla L(\mathbf{w}_t) \odot \nabla L(\mathbf{w}_t)$$

$$\mathbf{w}_{t+1} = \mathbf{w}_t - \frac{\eta}{\sqrt{\mathbf{s}_{t+1} + \epsilon}} \odot \nabla L(\mathbf{w}_t)$$

gdzie:
- $\mathbf{s}_t$ — suma kwadratów gradientów (akumulator), $\mathbf{s}_0 = \mathbf{0}$
- $\epsilon$ — mała stała zapobiegająca dzieleniu przez zero (typowo $10^{-8}$)
- $\odot$ — iloczyn Hadamarda (element-wise)

**Właściwości:**
- Parametry o dużych gradientach otrzymują mniejszy efektywny $\eta$ → stabilizacja
- Parametry o małych gradientach otrzymują większy efektywny $\eta$ → przyspieszenie
- Dobrze działa dla rzadkich gradientów (NLP, systemy rekomendacji)
- **Wada:** $\mathbf{s}_t$ rośnie monotonicznie → efektywny $\eta$ maleje do zera → uczenie się zatrzymuje

### 5. RMSProp (Root Mean Square Propagation)

RMSProp rozwiązuje problem malejącego learning rate w Adagrad, stosując wykładniczo ważoną średnią ruchomą kwadratów gradientów:

$$\mathbf{s}_{t+1} = \rho \cdot \mathbf{s}_t + (1 - \rho) \cdot \nabla L(\mathbf{w}_t) \odot \nabla L(\mathbf{w}_t)$$

$$\mathbf{w}_{t+1} = \mathbf{w}_t - \frac{\eta}{\sqrt{\mathbf{s}_{t+1} + \epsilon}} \odot \nabla L(\mathbf{w}_t)$$

gdzie:
- $\rho$ — współczynnik zapominania (typowo $\rho = 0.9$)
- Dzięki $\rho < 1$ stare gradienty są „zapominane" → $\mathbf{s}_t$ nie rośnie bez ograniczeń

**Właściwości:**
- Adaptacyjny learning rate bez problemu malejącego $\eta$
- Dobrze radzi sobie z nieliniowymi i niestacjonarnymi funkcjami kosztu
- Skuteczny dla sieci rekurencyjnych (RNN)
- Zaproponowany przez G. Hintona w wykładach Coursera (2012), nigdy formalnie opublikowany

```
Pseudokod: RMSProp
─────────────────────────────
Wejście: η, ρ = 0.9, ε = 1e-8, w₀
s₀ ← 0

DLA t = 1, 2, 3, ...:
    g_t ← ∇L(w_t)                          // oblicz gradient
    s_t ← ρ · s_{t-1} + (1-ρ) · g_t²       // średnia ruchoma kwadratów
    w_{t+1} ← w_t - η · g_t / (√s_t + ε)   // aktualizuj wagi
```

### 6. Adam (Adaptive Moment Estimation)

Adam łączy Momentum (pierwszy moment — średnia gradientów) z RMSProp (drugi moment — średnia kwadratów gradientów) i dodaje korekcję biasu:

**Estymacja momentów:**

$$\mathbf{m}_{t+1} = \beta_1 \cdot \mathbf{m}_t + (1 - \beta_1) \cdot \nabla L(\mathbf{w}_t) \quad \text{(pierwszy moment — średnia)}$$

$$\mathbf{v}_{t+1} = \beta_2 \cdot \mathbf{v}_t + (1 - \beta_2) \cdot \nabla L(\mathbf{w}_t) \odot \nabla L(\mathbf{w}_t) \quad \text{(drugi moment — wariancja)}$$

**Korekcja biasu:**

$$\hat{\mathbf{m}}_{t+1} = \frac{\mathbf{m}_{t+1}}{1 - \beta_1^{t+1}}$$

$$\hat{\mathbf{v}}_{t+1} = \frac{\mathbf{v}_{t+1}}{1 - \beta_2^{t+1}}$$

**Aktualizacja wag:**

$$\mathbf{w}_{t+1} = \mathbf{w}_t - \frac{\eta}{\sqrt{\hat{\mathbf{v}}_{t+1}} + \epsilon} \odot \hat{\mathbf{m}}_{t+1}$$

gdzie:
- $\beta_1 = 0.9$ — współczynnik pierwszego momentu (jak momentum)
- $\beta_2 = 0.999$ — współczynnik drugiego momentu (jak RMSProp)
- $\eta = 0.001$ — learning rate
- $\epsilon = 10^{-8}$ — stała stabilizująca

**Dlaczego korekcja biasu?** Ponieważ $\mathbf{m}_0 = \mathbf{v}_0 = \mathbf{0}$, estymaty momentów są obciążone w kierunku zera w pierwszych iteracjach. Korekcja $\frac{1}{1 - \beta^t}$ kompensuje to obciążenie (dla $t \to \infty$ korekcja zanika, bo $\beta^t \to 0$).

**Właściwości:**
- Łączy zalety Momentum (przyspieszenie zbieżności) i RMSProp (adaptacyjny $\eta$)
- Korekcja biasu stabilizuje początkowe iteracje
- Domyślne hiperparametry ($\beta_1 = 0.9$, $\beta_2 = 0.999$, $\eta = 10^{-3}$) działają dobrze w większości przypadków
- Najczęściej stosowany optymalizator w deep learning

### 7. AdamW (Adam z Weight Decay)

AdamW rozdziela regularyzację L2 (weight decay) od adaptacyjnego learning rate. W klasycznym Adam regularyzacja L2 jest dodawana do gradientu, co powoduje, że jest skalowana przez adaptacyjny $\eta$ — to niepożądane zachowanie. AdamW stosuje weight decay bezpośrednio na wagach:

$$\mathbf{w}_{t+1} = (1 - \lambda) \cdot \mathbf{w}_t - \frac{\eta}{\sqrt{\hat{\mathbf{v}}_{t+1}} + \epsilon} \odot \hat{\mathbf{m}}_{t+1}$$

gdzie $\lambda$ to współczynnik weight decay (typowo $\lambda = 0.01$).

**Właściwości:**
- Poprawna dekompozycja regularyzacji i optymalizacji
- Lepsza generalizacja niż Adam z L2
- Standard w trenowaniu Transformerów (BERT, GPT)

## Porównanie zbieżności algorytmów

### Tabela porównawcza

| Algorytm | Adaptacyjny $\eta$ | Momentum | Korekcja biasu | Hiperparametry | Zbieżność |
|---|---|---|---|---|---|
| **SGD** | ✗ | ✗ | — | $\eta$ | Wolna, oscylacje |
| **SGD + Momentum** | ✗ | ✓ | — | $\eta$, $\beta$ | Szybsza, mniej oscylacji |
| **NAG** | ✗ | ✓ (look-ahead) | — | $\eta$, $\beta$ | Lepsza niż Momentum |
| **Adagrad** | ✓ | ✗ | — | $\eta$ | Dobra, ale maleje $\eta$ |
| **RMSProp** | ✓ | ✗ | — | $\eta$, $\rho$ | Dobra, stabilna |
| **Adam** | ✓ | ✓ | ✓ | $\eta$, $\beta_1$, $\beta_2$ | Bardzo dobra |
| **AdamW** | ✓ | ✓ | ✓ | $\eta$, $\beta_1$, $\beta_2$, $\lambda$ | Bardzo dobra + lepsza generalizacja |

### Zachowanie na różnych typach powierzchni kosztu

```
1. Wąska dolina (różne krzywizny w różnych kierunkach):

   SGD:                    Momentum:                Adam:
   ┌─────────────┐        ┌─────────────┐          ┌─────────────┐
   │    ╱╲╱╲     │        │    ╱─╲      │          │             │
   │   ╱    ╲    │        │   ╱   ╲     │          │    ╲        │
   │  ╱  ★   ╲   │        │  ╱  ★  ─    │          │     ╲ ★     │
   │ ╱        ╲  │        │ ╱       ─   │          │      ──     │
   │╱    oscylacje│       │╱   szybciej  │          │   najszybciej│
   └─────────────┘        └─────────────┘          └─────────────┘

2. Płaski region (plateau):

   SGD:                    Momentum:                Adam:
   ┌─────────────┐        ┌─────────────┐          ┌─────────────┐
   │─ ─ ─ ─ ─ ─ │        │─ ─ ── ───── │          │─ ── ─────── │
   │  utknięcie  │        │  przejście   │          │  szybkie     │
   │             │        │  (bezwładność)│         │  przejście   │
   └─────────────┘        └─────────────┘          └─────────────┘

3. Punkt siodłowy:

   SGD:                    Momentum:                Adam:
   ┌─────────────┐        ┌─────────────┐          ┌─────────────┐
   │    ╱╲       │        │    ╱╲       │          │    ╱╲       │
   │   ╱  ╲      │        │   ╱  ╲──    │          │   ╱  ╲──    │
   │  ★    ╲     │        │  ★    ╲  ╲  │          │  ★    ╲  ╲  │
   │  utknięcie  │        │  ucieczka    │          │  szybka      │
   │             │        │              │          │  ucieczka    │
   └─────────────┘        └─────────────┘          └─────────────┘
```

### Kiedy stosować który algorytm?

| Scenariusz | Rekomendowany algorytm | Uzasadnienie |
|---|---|---|
| **Domyślny wybór** | Adam / AdamW | Dobre domyślne hiperparametry, szybka zbieżność |
| **Trenowanie Transformerów** | AdamW + warm-up + cosine decay | Standard w NLP (BERT, GPT) |
| **Sieci konwolucyjne (CNN)** | SGD + Momentum + step decay | Lepsza generalizacja niż Adam |
| **Sieci rekurencyjne (RNN)** | RMSProp lub Adam | Stabilność przy eksplodujących gradientach |
| **Rzadkie gradienty (NLP, embeddingi)** | Adam lub Adagrad | Adaptacyjny $\eta$ dla rzadkich cech |
| **Wymagana najlepsza generalizacja** | SGD + Momentum + schedule | SGD z dobrym schedule generalizuje lepiej |
| **Szybkie prototypowanie** | Adam | Minimalne strojenie hiperparametrów |

## Dobór hiperparametrów

### Learning rate — najważniejszy hiperparametr

Dobór learning rate ma największy wpływ na zbieżność:

| Algorytm | Typowy zakres $\eta$ | Domyślna wartość |
|---|---|---|
| SGD | $10^{-3}$ – $10^{-1}$ | $0.01$ |
| SGD + Momentum | $10^{-3}$ – $10^{-1}$ | $0.01$ |
| RMSProp | $10^{-4}$ – $10^{-2}$ | $0.001$ |
| Adam | $10^{-4}$ – $10^{-2}$ | $0.001$ |
| AdamW | $10^{-5}$ – $10^{-3}$ | $0.0001$ – $0.001$ |

### Learning rate schedule — strategie zmiany $\eta$

#### Step decay

$$\eta_t = \eta_0 \cdot \gamma^{\lfloor t / k \rfloor}$$

Redukcja $\eta$ o czynnik $\gamma$ (np. 0.1) co $k$ epok. Prosty i skuteczny.

#### Exponential decay

$$\eta_t = \eta_0 \cdot \gamma^t$$

Ciągła redukcja $\eta$ w każdej epoce. Parametr $\gamma$ typowo 0.95–0.99.

#### Cosine annealing

$$\eta_t = \eta_{min} + \frac{\eta_0 - \eta_{min}}{2}\left(1 + \cos\left(\frac{t \cdot \pi}{T}\right)\right)$$

Gładka redukcja $\eta$ od $\eta_0$ do $\eta_{min}$ w kształcie cosinusa. Popularna w trenowaniu Transformerów.

#### Warm-up + decay

$$\eta_t = \begin{cases} \eta_0 \cdot \frac{t}{T_{warmup}} & \text{dla } t < T_{warmup} \\ \eta_0 \cdot \text{decay}(t - T_{warmup}) & \text{dla } t \geq T_{warmup} \end{cases}$$

Stopniowe zwiększanie $\eta$ przez $T_{warmup}$ kroków, potem decay. Stabilizuje początkowe iteracje, szczególnie ważne dla Adam/AdamW z dużymi modelami.

```
  η
  │
  │     ╱╲
  │    ╱  ╲
  │   ╱    ╲
  │  ╱      ╲                    ← cosine annealing
  │ ╱        ╲
  │╱          ╲───────
  │                              ← step decay
  │ ╱──────┐
  │╱       └──────┐
  │               └──────
  └──────────────────────── epoki
  ↑ warm-up
```

### Praktyczne wskazówki doboru hiperparametrów

1. **Learning rate finder** — technika polegająca na trenowaniu sieci z rosnącym $\eta$ (od $10^{-7}$ do $10$) przez jedną epokę i wybraniu $\eta$ z najszybszym spadkiem kosztu (przed oscylacjami).

2. **Momentum $\beta$** — prawie zawsze $\beta = 0.9$. Wartości $> 0.99$ mogą powodować niestabilność.

3. **Adam $\beta_1$, $\beta_2$** — domyślne wartości ($0.9$, $0.999$) działają dobrze w większości przypadków. Dla niestabilnego trenowania można zmniejszyć $\beta_2$ do $0.99$.

4. **Weight decay $\lambda$** — typowo $10^{-4}$ – $10^{-2}$. Większy $\lambda$ → silniejsza regularyzacja → lepsza generalizacja, ale ryzyko underfittingu.

5. **Batch size** — wpływa na szum gradientu. Mniejszy batch → więcej szumu → lepsza generalizacja (ale wolniejsze trenowanie). Typowe wartości: 32–256.

## Przykłady

### Porównanie trajektorii optymalizacji — funkcja Rosenbrocka

Rozważmy minimalizację funkcji Rosenbrocka $f(x, y) = (1 - x)^2 + 100(y - x^2)^2$, która ma wąską, zakrzywioną dolinę — klasyczny test dla algorytmów optymalizacji. Minimum globalne: $(x^*, y^*) = (1, 1)$.

Punkt startowy: $(x_0, y_0) = (-1, 1)$, learning rate: $\eta = 0.001$.

```
  y
  2 │                                    Trajektorie optymalizacji
    │                                    na funkcji Rosenbrocka
    │  SGD: ·····→·····→·····→           f(x,y) = (1-x)² + 100(y-x²)²
    │       (oscyluje, wolna zbieżność)
  1 │  ★ start                    ● minimum (1,1)
    │   ╲
    │    ╲  Momentum: ──→──→──→
    │     ╲  (szybsza, mniej oscylacji)
  0 │      ╲
    │       ╲  Adam: ═══►═══►═══►
    │        ╲  (najszybsza, adaptacyjna)
    │         ╲
 -1 │          ╲───────────────────● (1,1)
    └──────────────────────────────────
   -2   -1    0    1    2    x
```

**Porównanie liczby iteracji do zbieżności** ($\|f(x,y)\| < 10^{-4}$):

| Algorytm | Iteracje | Uwagi |
|---|---|---|
| SGD ($\eta = 0.001$) | ~50 000 | Oscylacje w wąskiej dolinie |
| SGD + Momentum ($\beta = 0.9$) | ~10 000 | Momentum przyspiesza przejście doliny |
| RMSProp ($\rho = 0.9$) | ~5 000 | Adaptacyjny $\eta$ kompensuje różne krzywizny |
| Adam ($\beta_1 = 0.9$, $\beta_2 = 0.999$) | ~3 000 | Najszybsza zbieżność |

### Pseudokod algorytmu Adam — kompletna implementacja

```
Algorytm: Adam (Adaptive Moment Estimation)
──────────────────────────────────────────────────────────────────────────
Wejście:
    L(w)    — funkcja kosztu (różniczkowalna)
    w₀      — początkowe wagi
    η       — learning rate (domyślnie: 0.001)
    β₁      — współczynnik pierwszego momentu (domyślnie: 0.9)
    β₂      — współczynnik drugiego momentu (domyślnie: 0.999)
    ε       — stała stabilizująca (domyślnie: 1e-8)

Inicjalizacja:
    m₀ ← 0    // pierwszy moment (średnia gradientów)
    v₀ ← 0    // drugi moment (średnia kwadratów gradientów)
    t  ← 0    // licznik kroków

Pętla główna:
    DLA t = 1, 2, 3, ... (dopóki nie spełniony warunek stopu):

        1. Oblicz gradient:
           g_t ← ∇L(w_{t-1})

        2. Aktualizuj estymaty momentów:
           m_t ← β₁ · m_{t-1} + (1 - β₁) · g_t        // pierwszy moment
           v_t ← β₂ · v_{t-1} + (1 - β₂) · g_t ⊙ g_t  // drugi moment

        3. Korekcja biasu:
           m̂_t ← m_t / (1 - β₁ᵗ)    // skorygowany pierwszy moment
           v̂_t ← v_t / (1 - β₂ᵗ)    // skorygowany drugi moment

        4. Aktualizuj wagi:
           w_t ← w_{t-1} - η · m̂_t / (√v̂_t + ε)

    ZWRÓĆ w_t

Warunek stopu:
    - Osiągnięto maksymalną liczbę iteracji
    - ‖g_t‖ < tolerancja (gradient bliski zeru)
    - Koszt na zbiorze walidacyjnym zaczyna rosnąć (early stopping)
```

**Analiza korekcji biasu krok po kroku:**

Dla $\beta_1 = 0.9$, $\beta_2 = 0.999$:

| Krok $t$ | $1 - \beta_1^t$ | $1 - \beta_2^t$ | Mnożnik $\hat{m}$ | Mnożnik $\hat{v}$ |
|---|---|---|---|---|
| 1 | 0.100 | 0.001 | ×10.0 | ×1000.0 |
| 5 | 0.410 | 0.005 | ×2.44 | ×200.0 |
| 10 | 0.651 | 0.010 | ×1.54 | ×100.0 |
| 50 | 0.995 | 0.049 | ×1.01 | ×20.4 |
| 100 | 1.000 | 0.095 | ×1.00 | ×10.5 |
| 1000 | 1.000 | 0.632 | ×1.00 | ×1.58 |

W pierwszych iteracjach korekcja biasu znacząco zwiększa estymaty momentów, kompensując inicjalizację zerową. Dla dużych $t$ korekcja zanika ($\beta^t \to 0$).

### Przykład numeryczny — jeden krok Adam

Załóżmy jednowymiarowy przypadek: $w_0 = 2.0$, $\nabla L(w_0) = 0.5$, $\eta = 0.001$, $\beta_1 = 0.9$, $\beta_2 = 0.999$.

**Krok $t = 1$:**

$m_1 = 0.9 \cdot 0 + 0.1 \cdot 0.5 = 0.05$

$v_1 = 0.999 \cdot 0 + 0.001 \cdot 0.5^2 = 0.00025$

$\hat{m}_1 = \frac{0.05}{1 - 0.9^1} = \frac{0.05}{0.1} = 0.5$

$\hat{v}_1 = \frac{0.00025}{1 - 0.999^1} = \frac{0.00025}{0.001} = 0.25$

$w_1 = 2.0 - 0.001 \cdot \frac{0.5}{\sqrt{0.25} + 10^{-8}} = 2.0 - 0.001 \cdot \frac{0.5}{0.5} = 2.0 - 0.001 = 1.999$

Obserwacja: po korekcji biasu efektywny krok wynosi dokładnie $\eta = 0.001$, niezależnie od wielkości gradientu — to cecha Adam w pierwszej iteracji.

## Podsumowanie

1. **Algorytmy deterministyczne uczenia** sieci neuronowych to metody optymalizacji oparte na gradiencie, które systematycznie aktualizują wagi w kierunku minimalizacji funkcji kosztu. Ewoluowały od prostego SGD do zaawansowanych metod adaptacyjnych (Adam, AdamW).

2. **SGD** jest najprostszą metodą ($\mathbf{w} \leftarrow \mathbf{w} - \eta \nabla L$), ale jest wrażliwy na dobór $\eta$ i oscyluje w wąskich dolinach. **Momentum** dodaje bezwładność ($\mathbf{v} \leftarrow \beta \mathbf{v} + \nabla L$), przyspieszając zbieżność i tłumiąc oscylacje.

3. **Metody adaptacyjne** (Adagrad, RMSProp) dostosowują learning rate indywidualnie dla każdego parametru na podstawie historii gradientów. RMSProp rozwiązuje problem malejącego $\eta$ w Adagrad przez wykładniczo ważoną średnią ruchomą.

4. **Adam** łączy Momentum (pierwszy moment) z RMSProp (drugi moment) i dodaje korekcję biasu. Domyślne hiperparametry ($\beta_1 = 0.9$, $\beta_2 = 0.999$, $\eta = 10^{-3}$) działają dobrze w większości zastosowań. **AdamW** poprawia regularyzację przez rozdzielenie weight decay od adaptacyjnego $\eta$.

5. **Learning rate schedule** (step decay, cosine annealing, warm-up) pozwala na dynamiczną zmianę $\eta$ w trakcie trenowania — szybka zbieżność na początku, precyzyjne dostrojenie pod koniec.

6. **Wybór algorytmu** zależy od zastosowania: Adam/AdamW jako domyślny wybór, SGD + Momentum + schedule dla najlepszej generalizacji (CNN), AdamW + warm-up dla Transformerów.

7. **Kluczowe hiperparametry** to: learning rate $\eta$ (najważniejszy), momentum $\beta$, współczynniki Adam ($\beta_1$, $\beta_2$), weight decay $\lambda$ i batch size. Technika learning rate finder pomaga w doborze optymalnego $\eta$.

## Powiązane pytania

- [Pytanie 16: Na czym polega idea i zasada algorytmu propagacji wstecznej w sieciach neuronowych?](16-backpropagation.md)
- [Pytanie 18: Omówić zdolność generalizacji sieci neuronowych i metody poprawy tych zdolności.](18-generalizacja-sieci.md)
- [Pytanie 22: Algorytmy uczenia sieci głębokich. Na czym polega problem zanikającego gradientu i jak jest rozwiązywany?](22-zanikajacy-gradient.md)
