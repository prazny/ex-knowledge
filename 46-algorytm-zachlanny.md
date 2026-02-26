# Pytanie 46: Proszę opisać dowolny algorytm „zachłanny" (greedy).

## Kluczowe pojęcia

- **Algorytm zachłanny (greedy algorithm)** — strategia projektowania algorytmów, w której w każdym kroku dokonuje się wyboru lokalnie optymalnego (najlepszego w danym momencie), bez cofania się ani rozważania przyszłych konsekwencji. Algorytm zachłanny nigdy nie zmienia raz podjętej decyzji. Strategia ta jest prosta i efektywna, ale nie zawsze prowadzi do rozwiązania globalnie optymalnego — działa poprawnie tylko dla problemów spełniających określone warunki strukturalne.
- **Własność zachłannego wyboru (greedy-choice property)** — kluczowy warunek poprawności algorytmu zachłannego. Mówi, że istnieje rozwiązanie optymalne zawierające wybór zachłanny, tzn. lokalnie optymalny wybór w danym kroku może być rozszerzony do rozwiązania globalnie optymalnego. Formalnie: jeśli $S^*$ jest rozwiązaniem optymalnym, to istnieje rozwiązanie optymalne $S'$ zawierające element wybrany zachłannie. Dowód tej własności zwykle polega na pokazaniu, że zamiana dowolnego elementu w rozwiązaniu optymalnym na element zachłanny nie pogarsza wyniku.
- **Optymalna podstruktura (optimal substructure)** — własność problemu polegająca na tym, że optymalne rozwiązanie problemu zawiera w sobie optymalne rozwiązania podproblemów. Jest to warunek wspólny dla algorytmów zachłannych i programowania dynamicznego. Formalnie: jeśli $S^*$ jest optymalnym rozwiązaniem problemu $P$, to po usunięciu elementu zachłannego $g$ z $S^*$, pozostałe elementy $S^* \setminus \{g\}$ tworzą optymalne rozwiązanie podproblemu $P'$.
- **Matroid** — struktura algebraiczna $(S, \mathcal{I})$, gdzie $S$ jest zbiorem skończonym, a $\mathcal{I}$ jest rodziną podzbiorów niezależnych spełniającą: (1) $\emptyset \in \mathcal{I}$, (2) dziedziczność — jeśli $B \in \mathcal{I}$ i $A \subseteq B$, to $A \in \mathcal{I}$, (3) własność wymiany — jeśli $A, B \in \mathcal{I}$ i $|A| < |B|$, to istnieje $x \in B \setminus A$ taki, że $A \cup \{x\} \in \mathcal{I}$. Twierdzenie Rado-Edmondsa gwarantuje, że algorytm zachłanny znajduje optymalne rozwiązanie dla każdego problemu optymalizacji na matroidzie z wagami.
- **Kontrprzykład** — przykład danych wejściowych, dla których algorytm zachłanny nie daje rozwiązania optymalnego. Kontrprzykłady są podstawowym narzędziem wykazywania, że strategia zachłanna jest niepoprawna dla danego problemu. Klasyczny kontrprzykład: problem plecakowy 0-1, w którym zachłanny wybór przedmiotów o najlepszym stosunku wartość/waga nie gwarantuje optymalnego wypełnienia plecaka.

## Zasada algorytmów zachłannych

### Idea ogólna

Algorytm zachłanny rozwiązuje problem optymalizacyjny poprzez sekwencję **nieodwracalnych decyzji lokalnych**. W każdym kroku wybiera opcję, która wydaje się najlepsza w danym momencie, bez analizy dalszych konsekwencji. Po dokonaniu wyboru algorytm nigdy go nie zmienia — nie cofa się, nie porównuje z alternatywami.

Ogólny schemat algorytmu zachłannego:

```
ALGORYTM Zachłanny(problem)
  rozwiązanie ← ∅
  kandydaci ← zbiór wszystkich możliwych elementów

  DOPÓKI rozwiązanie nie jest kompletne ORAZ kandydaci ≠ ∅:
    x ← WybierzNajlepszego(kandydaci)    // wybór zachłanny
    kandydaci ← kandydaci \ {x}
    JEŚLI rozwiązanie ∪ {x} jest dopuszczalne:
      rozwiązanie ← rozwiązanie ∪ {x}

  ZWRÓĆ rozwiązanie
```

Kluczowe cechy:
- **Jednokierunkowość** — decyzje są podejmowane raz i nie są zmieniane.
- **Lokalność** — wybór opiera się wyłącznie na bieżącym stanie, bez przewidywania przyszłości.
- **Prostota** — algorytmy zachłanne są zwykle proste w implementacji i efektywne obliczeniowo.
- **Brak gwarancji** — bez dowodu poprawności nie ma pewności, że wynik jest optymalny.

### Porównanie z innymi strategiami

| Cecha | Algorytm zachłanny | Programowanie dynamiczne | Przeszukiwanie wyczerpujące |
|---|---|---|---|
| Decyzje | Lokalne, nieodwracalne | Globalne, rozważa podproblemy | Wszystkie możliwości |
| Cofanie | Nigdy | Nie (ale rozważa warianty) | Tak (backtracking) |
| Złożoność | Zwykle niska | Zwykle wyższa (tablica DP) | Wykładnicza |
| Optymalność | Tylko z dowodem | Gwarantowana | Gwarantowana |
| Pamięć | Minimalna | Tablica podproblemów | Stos rekursji |

## Warunki poprawności algorytmu zachłannego

### Własność zachłannego wyboru

Aby algorytm zachłanny dawał rozwiązanie optymalne, problem musi posiadać **własność zachłannego wyboru**: zawsze istnieje rozwiązanie optymalne, które zawiera element wybrany zachłannie (tj. lokalnie najlepszy).

**Schemat dowodu** (metoda zamiany — exchange argument):
1. Załóż, że $S^*$ jest rozwiązaniem optymalnym, które **nie** zawiera elementu zachłannego $g$.
2. Pokaż, że można zamienić pewien element $s \in S^*$ na $g$, otrzymując rozwiązanie $S' = (S^* \setminus \{s\}) \cup \{g\}$.
3. Pokaż, że $S'$ jest co najmniej tak dobre jak $S^*$ (tzn. $\text{wartość}(S') \geq \text{wartość}(S^*)$).
4. Wniosek: istnieje rozwiązanie optymalne zawierające $g$ — własność zachłannego wyboru zachodzi.

### Optymalna podstruktura

Problem ma **optymalną podstrukturę**, jeśli po dokonaniu zachłannego wyboru $g$ pozostały podproblem ma rozwiązanie optymalne, które razem z $g$ tworzy rozwiązanie optymalne całego problemu.

Formalnie: jeśli $S^*$ jest optymalnym rozwiązaniem problemu $P$ i $g \in S^*$ jest elementem zachłannym, to $S^* \setminus \{g\}$ jest optymalnym rozwiązaniem podproblemu $P'$ powstałego po wyborze $g$.

### Kiedy zachłanny działa, a kiedy nie

**Zachłanny działa** (z dowodem poprawności):
- Problem wydawania reszty (dla systemów kanonicznych, np. nominały 1, 2, 5, 10, 20, 50)
- Minimalne drzewo rozpinające (Kruskal, Prim)
- Najkrótsze ścieżki z jednego źródła (Dijkstra, dla wag nieujemnych)
- Kodowanie Huffmana
- Problem plecakowy ułamkowy (fractional knapsack)
- Planowanie zadań (activity selection)
- Problemy optymalizacji na matroidach

**Zachłanny NIE działa** (kontrprzykłady istnieją):
- Problem plecakowy 0-1 (knapsack 0-1)
- Problem komiwojażera (TSP)
- Problem wydawania reszty dla niekanonicznych nominałów
- Najdłuższa ścieżka w grafie
- Problem kolorowania grafów (optymalnego)

## Przykłady

### Problem wydawania reszty (coin change)

**Problem:** Mając zbiór nominałów monet $\{c_1, c_2, \ldots, c_k\}$ i kwotę $W$, wydaj resztę używając minimalnej liczby monet.

**Strategia zachłanna:** Zawsze wybieraj monetę o największym nominale, który nie przekracza pozostałej kwoty.

```
ALGORYTM WydawanieResztyZachłannie(nominały, W)
  // nominały posortowane malejąco: c₁ > c₂ > ... > cₖ
  wynik ← []
  reszta ← W

  DLA KAŻDEGO nominału c w nominały (malejąco):
    DOPÓKI reszta ≥ c:
      wynik.dodaj(c)
      reszta ← reszta - c

  JEŚLI reszta = 0:
    ZWRÓĆ wynik
  W PRZECIWNYM RAZIE:
    ZWRÓĆ "Nie można wydać reszty"
```

**Przykład — nominały {50, 20, 10, 5, 2, 1}, kwota W = 93:**

```
Krok 1: 93 ≥ 50 → bierzemy 50, reszta = 43
Krok 2: 43 ≥ 20 → bierzemy 20, reszta = 23
Krok 3: 23 ≥ 20 → bierzemy 20, reszta = 3
Krok 4: 3 ≥ 2  → bierzemy 2,  reszta = 1
Krok 5: 1 ≥ 1  → bierzemy 1,  reszta = 0

Wynik: [50, 20, 20, 2, 1] — 5 monet ✓ (optymalnie)
```

**Kontrprzykład — nominały {1, 3, 4}, kwota W = 6:**

```
Zachłannie: 4 + 1 + 1 = 6 → 3 monety
Optymalnie: 3 + 3     = 6 → 2 monety ✗

Algorytm zachłanny NIE daje optymalnego wyniku!
```

Dla nominałów niekanonicznych (np. {1, 3, 4}) algorytm zachłanny nie jest poprawny. Poprawne rozwiązanie wymaga programowania dynamicznego.

### Algorytm Kruskala — minimalne drzewo rozpinające

**Problem:** Dany jest graf nieskierowany, ważony, spójny $G = (V, E, w)$. Znaleźć drzewo rozpinające o minimalnej sumie wag krawędzi.

**Strategia zachłanna:** Rozpatruj krawędzie w kolejności rosnących wag. Dodaj krawędź do drzewa, jeśli nie tworzy cyklu.

```
ALGORYTM Kruskal(G = (V, E, w))
  // Wejście: graf G z wierzchołkami V, krawędziami E, wagami w
  // Wyjście: zbiór krawędzi minimalnego drzewa rozpinającego T

  T ← ∅
  Posortuj krawędzie E rosnąco według wag w

  // Inicjalizacja struktury Union-Find
  DLA KAŻDEGO wierzchołka v ∈ V:
    MakeSet(v)

  DLA KAŻDEJ krawędzi (u, v) ∈ E (w kolejności rosnących wag):
    JEŚLI Find(u) ≠ Find(v):       // u i v w różnych składowych
      T ← T ∪ {(u, v)}
      Union(u, v)                   // połącz składowe
    JEŚLI |T| = |V| - 1:
      PRZERWIJ                      // drzewo kompletne

  ZWRÓĆ T
```

**Złożoność:** $O(|E| \log |E|)$ — zdominowana przez sortowanie krawędzi. Z Union-Find z kompresją ścieżek i łączeniem według rangi, operacje Union/Find działają w zamortyzowanym czasie $O(\alpha(|V|))$, gdzie $\alpha$ to odwrotność funkcji Ackermanna (praktycznie stała).

**Przykład:**

```
Graf:
    A ---2--- B
    |       / |
    4     3   1
    |   /     |
    C ---5--- D

Krawędzie posortowane: (B,D):1, (A,B):2, (B,C):3, (A,C):4, (C,D):5

Krok 1: (B,D):1 — Find(B)≠Find(D) → dodaj. T = {(B,D)}
Krok 2: (A,B):2 — Find(A)≠Find(B) → dodaj. T = {(B,D), (A,B)}
Krok 3: (B,C):3 — Find(B)≠Find(C) → dodaj. T = {(B,D), (A,B), (B,C)}
         |T| = 3 = |V|-1 → STOP

MST: {(B,D):1, (A,B):2, (B,C):3}, waga = 6
```

**Dowód poprawności (szkic):**

*Własność zachłannego wyboru:* Niech $e = (u,v)$ będzie krawędzią o najmniejszej wadze przecinającą pewien przekrój $(S, V \setminus S)$. Wtedy istnieje MST zawierające $e$ (twierdzenie o przekroju — cut property). Jeśli MST $T^*$ nie zawiera $e$, to dodanie $e$ do $T^*$ tworzy cykl, a usunięcie innej krawędzi tego cyklu przecinającej ten sam przekrój daje drzewo o wadze $\leq w(T^*)$.

*Optymalna podstruktura:* Po dodaniu krawędzi $e$ do MST, pozostały problem to znalezienie MST w grafie ze skontraktowaną krawędzią $e$.

### Algorytm Prima — minimalne drzewo rozpinające

**Problem:** Ten sam co dla Kruskala — minimalne drzewo rozpinające.

**Strategia zachłanna:** Zaczynając od dowolnego wierzchołka, w każdym kroku dodaj najtańszą krawędź łączącą drzewo z wierzchołkiem spoza drzewa.

```
ALGORYTM Prim(G = (V, E, w), s)
  // Wejście: graf G, wierzchołek startowy s
  // Wyjście: zbiór krawędzi MST

  T ← ∅
  klucz[v] ← ∞  DLA KAŻDEGO v ∈ V
  rodzic[v] ← NIL  DLA KAŻDEGO v ∈ V
  klucz[s] ← 0
  Q ← kolejka priorytetowa (min-heap) ze wszystkimi wierzchołkami

  DOPÓKI Q ≠ ∅:
    u ← Q.wyciągnijMin()            // wierzchołek o najmniejszym kluczu
    JEŚLI rodzic[u] ≠ NIL:
      T ← T ∪ {(rodzic[u], u)}

    DLA KAŻDEGO sąsiada v wierzchołka u:
      JEŚLI v ∈ Q ORAZ w(u, v) < klucz[v]:
        rodzic[v] ← u
        klucz[v] ← w(u, v)
        Q.zmniejszKlucz(v, klucz[v])

  ZWRÓĆ T
```

**Złożoność:**
- Z kopcem binarnym: $O((|V| + |E|) \log |V|)$
- Z kopcem Fibonacciego: $O(|E| + |V| \log |V|)$

**Porównanie Kruskal vs Prim:**

| Cecha | Kruskal | Prim |
|---|---|---|
| Podejście | Krawędziowe (sortuj i dodawaj) | Wierzchołkowe (rozrastaj drzewo) |
| Struktura danych | Union-Find | Kolejka priorytetowa |
| Lepszy dla | Grafów rzadkich ($|E| \approx |V|$) | Grafów gęstych ($|E| \approx |V|^2$) |
| Złożoność | $O(|E| \log |E|)$ | $O(|E| + |V| \log |V|)$ z kopcem Fib. |

### Problem plecakowy — ułamkowy vs 0-1

Problem plecakowy doskonale ilustruje granicę stosowalności algorytmów zachłannych.

**Problem:** Danych jest $n$ przedmiotów, każdy o wadze $w_i$ i wartości $v_i$. Plecak ma pojemność $W$. Zmaksymalizuj wartość przedmiotów w plecaku.

#### Plecak ułamkowy (fractional knapsack) — zachłanny działa

W wersji ułamkowej można brać dowolną część przedmiotu (np. 0,3 przedmiotu).

**Strategia zachłanna:** Sortuj przedmioty malejąco według stosunku $v_i / w_i$ (wartość na jednostkę wagi). Bierz kolejne przedmioty w całości, a ostatni — ułamkowo.

```
ALGORYTM PlecakUłamkowy(przedmioty, W)
  // Sortuj przedmioty malejąco wg vᵢ/wᵢ
  Posortuj przedmioty malejąco według v[i]/w[i]

  wartość ← 0
  pojemność ← W

  DLA KAŻDEGO przedmiotu i (w kolejności malejącego v[i]/w[i]):
    JEŚLI w[i] ≤ pojemność:
      // Bierz cały przedmiot
      wartość ← wartość + v[i]
      pojemność ← pojemność - w[i]
    W PRZECIWNYM RAZIE:
      // Bierz ułamek przedmiotu
      ułamek ← pojemność / w[i]
      wartość ← wartość + ułamek × v[i]
      pojemność ← 0
      PRZERWIJ

  ZWRÓĆ wartość
```

**Złożoność:** $O(n \log n)$ — zdominowana przez sortowanie.

**Dowód poprawności:** Własność zachłannego wyboru zachodzi, ponieważ zamiana dowolnego ułamka przedmiotu o gorszym stosunku $v/w$ na ułamek przedmiotu o lepszym stosunku nie zmniejsza wartości rozwiązania.

#### Plecak 0-1 (0-1 knapsack) — zachłanny NIE działa

W wersji 0-1 każdy przedmiot można wziąć w całości lub wcale.

**Kontrprzykład:**

```
Pojemność plecaka: W = 10

Przedmiot | Waga | Wartość | v/w
---------|------|---------|-----
    A    |   6  |   30    | 5,0
    B    |   5  |   20    | 4,0
    C    |   5  |   20    | 4,0

Zachłannie (wg v/w): bierz A (waga 6, wartość 30)
  Pozostała pojemność: 4 — żaden inny przedmiot nie mieści się
  Wartość zachłanna: 30

Optymalnie: bierz B + C (waga 10, wartość 40)
  Wartość optymalna: 40 > 30 ✗
```

Problem plecakowy 0-1 wymaga programowania dynamicznego (złożoność $O(nW)$) lub algorytmów przybliżonych.

### Planowanie zadań (activity selection)

**Problem:** Danych jest $n$ zadań, każde z czasem rozpoczęcia $s_i$ i zakończenia $f_i$. Wybierz maksymalną liczbę zadań nieprzekrywających się.

**Strategia zachłanna:** Sortuj zadania rosnąco według czasu zakończenia $f_i$. Wybieraj kolejne zadanie, które nie koliduje z ostatnio wybranym.

```
ALGORYTM WybórZadań(zadania)
  Posortuj zadania rosnąco według f[i]

  wybrane ← [zadania[0]]
  ostatnieZakończenie ← f[0]

  DLA i ← 1 DO n-1:
    JEŚLI s[i] ≥ ostatnieZakończenie:
      wybrane.dodaj(zadania[i])
      ostatnieZakończenie ← f[i]

  ZWRÓĆ wybrane
```

**Złożoność:** $O(n \log n)$ — sortowanie.

**Przykład:**

```
Zadania (posortowane wg f):
  A: [1, 3)    B: [2, 5)    C: [4, 7)    D: [6, 8)    E: [7, 9)

Krok 1: Wybierz A [1,3). ostatnieZakończenie = 3
Krok 2: B [2,5) — s=2 < 3 → pomiń
Krok 3: C [4,7) — s=4 ≥ 3 → wybierz. ostatnieZakończenie = 7
Krok 4: D [6,8) — s=6 < 7 → pomiń
Krok 5: E [7,9) — s=7 ≥ 7 → wybierz. ostatnieZakończenie = 9

Wynik: {A, C, E} — 3 zadania (optymalnie)
```

## Matroidy a algorytmy zachłanne

### Twierdzenie Rado-Edmondsa

Matroid to struktura algebraiczna, która formalizuje warunki, w których algorytm zachłanny jest optymalny.

**Definicja:** Para $(S, \mathcal{I})$ jest matroidem, jeśli:
1. $\emptyset \in \mathcal{I}$ (zbiór pusty jest niezależny)
2. **Dziedziczność:** $B \in \mathcal{I} \wedge A \subseteq B \Rightarrow A \in \mathcal{I}$
3. **Własność wymiany:** $A, B \in \mathcal{I} \wedge |A| < |B| \Rightarrow \exists x \in B \setminus A : A \cup \{x\} \in \mathcal{I}$

**Twierdzenie (Rado-Edmonds):** Algorytm zachłanny znajduje zbiór niezależny o maksymalnej wadze w matroidzie ważonym $(S, \mathcal{I}, w)$.

**Przykład — matroid grafowy:** Dla grafu $G = (V, E)$, zbiór $S = E$ (krawędzie), a $\mathcal{I}$ to rodzina zbiorów krawędzi niezawierających cyklu (lasy). Algorytm Kruskala to algorytm zachłanny na matroidzie grafowym — dlatego jest poprawny.

## Podsumowanie

- **Algorytm zachłanny** w każdym kroku dokonuje lokalnie optymalnego wyboru, nigdy go nie zmieniając. Jest prosty i efektywny, ale wymaga dowodu poprawności.
- Poprawność algorytmu zachłannego wymaga dwóch warunków: **własności zachłannego wyboru** (lokalnie optymalny wybór prowadzi do globalnego optimum) i **optymalnej podstruktury** (optymalne rozwiązanie zawiera optymalne rozwiązania podproblemów).
- **Algorytm Kruskala** buduje minimalne drzewo rozpinające, sortując krawędzie rosnąco i dodając te, które nie tworzą cyklu — złożoność $O(|E| \log |E|)$. **Algorytm Prima** rozrasta drzewo od jednego wierzchołka, dodając najtańszą krawędź do nowego wierzchołka — złożoność $O(|E| + |V| \log |V|)$ z kopcem Fibonacciego.
- **Problem plecakowy ułamkowy** jest rozwiązywalny zachłannie (sortowanie wg $v/w$), ale **problem plecakowy 0-1** wymaga programowania dynamicznego — zachłanny daje kontrprzykłady.
- **Matroidy** formalizują klasę problemów, dla których algorytm zachłanny jest zawsze optymalny (twierdzenie Rado-Edmondsa).
- Kontrprzykłady (np. wydawanie reszty dla nominałów {1, 3, 4}, plecak 0-1) są kluczowym narzędziem wykazywania, że strategia zachłanna jest niepoprawna dla danego problemu.

## Powiązane pytania

- [Pytanie 27: Złożoność obliczeniowa](27-zlozonosc-obliczeniowa.md)
- [Pytanie 40: Programowanie dynamiczne](40-programowanie-dynamiczne.md)
- [Pytanie 45: Algorytmy kompresji tekstu](45-kompresja-tekstu.md)
