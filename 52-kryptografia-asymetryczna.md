# Pytanie 52: Przedstaw dwa wybrane problemy matematyczne stojące u podstaw kryptografii asymetrycznej.

## Kluczowe pojęcia

- **Kryptografia asymetryczna (kryptografia klucza publicznego)** — system kryptograficzny wykorzystujący parę kluczy: klucz publiczny (jawny, służący do szyfrowania lub weryfikacji podpisu) oraz klucz prywatny (tajny, służący do deszyfrowania lub składania podpisu). Bezpieczeństwo opiera się na trudności obliczeniowej pewnych problemów matematycznych — znając klucz publiczny, nie da się w rozsądnym czasie wyznaczyć klucza prywatnego.
- **RSA (Rivest–Shamir–Adleman)** — jeden z pierwszych i najszerzej stosowanych algorytmów kryptografii asymetrycznej (1977). Bezpieczeństwo RSA opiera się na trudności faktoryzacji dużych liczb złożonych — iloczynu dwóch dużych liczb pierwszych. RSA służy zarówno do szyfrowania, jak i do podpisów cyfrowych.
- **Faktoryzacja (rozkład na czynniki pierwsze)** — problem polegający na znalezieniu rozkładu danej liczby naturalnej $n$ na iloczyn liczb pierwszych. Dla dużych liczb (np. 2048-bitowych) nie jest znany algorytm wielomianowy rozwiązujący ten problem na klasycznym komputerze. Najlepszy znany algorytm (GNFS — General Number Field Sieve) ma złożoność subwykładniczą $O\!\left(\exp\!\left(c \cdot (\ln n)^{1/3} (\ln \ln n)^{2/3}\right)\right)$.
- **Logarytm dyskretny (DLP — Discrete Logarithm Problem)** — problem polegający na znalezieniu wykładnika $x$ w równaniu $g^x \equiv h \pmod{p}$, gdzie $g$ jest generatorem grupy cyklicznej, $h$ jest elementem grupy, a $p$ jest liczbą pierwszą. DLP stanowi podstawę bezpieczeństwa protokołów Diffie-Hellman i ElGamal.
- **Krzywe eliptyczne (ECC — Elliptic Curve Cryptography)** — kryptografia oparta na trudności problemu logarytmu dyskretnego w grupie punktów krzywej eliptycznej (ECDLP). ECC oferuje porównywalny poziom bezpieczeństwa do RSA przy znacznie krótszych kluczach (np. 256-bitowy klucz ECC ≈ 3072-bitowy klucz RSA).
- **Klucz publiczny** — klucz jawny, udostępniany publicznie. Służy do szyfrowania wiadomości (które może odszyfrować tylko posiadacz klucza prywatnego) lub do weryfikacji podpisu cyfrowego.
- **Klucz prywatny** — klucz tajny, znany wyłącznie właścicielowi. Służy do deszyfrowania wiadomości zaszyfrowanych kluczem publicznym lub do składania podpisu cyfrowego.

## Idea kryptografii asymetrycznej

Kryptografia asymetryczna rozwiązuje fundamentalny problem dystrybucji kluczy, który występuje w kryptografii symetrycznej. W systemie symetrycznym obie strony muszą wcześniej uzgodnić wspólny tajny klucz — w systemie asymetrycznym każdy użytkownik generuje parę kluczy i udostępnia klucz publiczny.

### Funkcje jednokierunkowe z furtką (trapdoor one-way functions)

Bezpieczeństwo kryptografii asymetrycznej opiera się na istnieniu **funkcji jednokierunkowych z furtką** — funkcji, które:
1. Są łatwe do obliczenia w jednym kierunku (wielomianowy czas)
2. Są trudne do odwrócenia bez znajomości dodatkowej informacji (furtki)
3. Stają się łatwe do odwrócenia, gdy znana jest furtka (klucz prywatny)

### Schemat ogólny

```
Generacja kluczy:
  (klucz_publiczny, klucz_prywatny) ← GenerujKlucze(parametry_bezpieczeństwa)

Szyfrowanie:
  szyfrogram ← Szyfruj(wiadomość, klucz_publiczny)

Deszyfrowanie:
  wiadomość ← Deszyfruj(szyfrogram, klucz_prywatny)

Podpis cyfrowy:
  podpis ← Podpisz(wiadomość, klucz_prywatny)
  wynik  ← Weryfikuj(wiadomość, podpis, klucz_publiczny)
```

## Problem faktoryzacji (RSA)

### Sformułowanie problemu

**Problem faktoryzacji liczb całkowitych (Integer Factorization Problem — IFP):**

Dany jest iloczyn $n = p \cdot q$, gdzie $p$ i $q$ są dużymi liczbami pierwszymi. Zadanie polega na znalezieniu $p$ i $q$ znając jedynie $n$.

Mnożenie dwóch dużych liczb pierwszych jest operacją łatwą (wielomianową), natomiast rozkład ich iloczynu na czynniki jest problemem trudnym obliczeniowo — nie jest znany algorytm wielomianowy dla klasycznych komputerów.

### Algorytm RSA

#### Generacja kluczy

1. Wybierz dwie duże liczby pierwsze $p$ i $q$ (typowo 1024-bitowe każda)
2. Oblicz $n = p \cdot q$ (moduł RSA)
3. Oblicz funkcję Eulera: $\varphi(n) = (p - 1)(q - 1)$
4. Wybierz wykładnik publiczny $e$ taki, że $1 < e < \varphi(n)$ i $\gcd(e, \varphi(n)) = 1$ (typowo $e = 65537$)
5. Oblicz wykładnik prywatny $d$ jako odwrotność modularną: $d \equiv e^{-1} \pmod{\varphi(n)}$, czyli $e \cdot d \equiv 1 \pmod{\varphi(n)}$

**Klucz publiczny:** $(n, e)$

**Klucz prywatny:** $(n, d)$

#### Szyfrowanie

Wiadomość $m$ (jako liczba, $0 \leq m < n$):

$$c \equiv m^e \pmod{n}$$

#### Deszyfrowanie

$$m \equiv c^d \pmod{n}$$

#### Poprawność RSA

Poprawność wynika z twierdzenia Eulera: jeśli $\gcd(m, n) = 1$, to:

$$m^{\varphi(n)} \equiv 1 \pmod{n}$$

Zatem:

$$c^d \equiv (m^e)^d \equiv m^{ed} \equiv m^{1 + k\varphi(n)} \equiv m \cdot (m^{\varphi(n)})^k \equiv m \cdot 1^k \equiv m \pmod{n}$$

### Bezpieczeństwo RSA

Złamanie RSA wymaga znalezienia $d$ z $(n, e)$. Wymaga to obliczenia $\varphi(n)$, co jest równoważne faktoryzacji $n$ (znając $p$ i $q$, łatwo obliczyć $\varphi(n) = (p-1)(q-1)$; znając $\varphi(n)$ i $n$, łatwo odzyskać $p$ i $q$).

**Najlepsze znane algorytmy faktoryzacji:**

| Algorytm | Złożoność | Uwagi |
|---|---|---|
| Dzielenie próbne | $O(\sqrt{n})$ | Praktyczny tylko dla małych $n$ |
| Metoda $\rho$ Pollarda | $O(n^{1/4})$ | Heurystyczna |
| Sito kwadratowe (QS) | $O\!\left(\exp\!\left(\sqrt{\ln n \cdot \ln \ln n}\right)\right)$ | Dla $n < 10^{100}$ |
| GNFS | $O\!\left(\exp\!\left(c \cdot (\ln n)^{1/3} (\ln \ln n)^{2/3}\right)\right)$ | Najlepszy ogólny |

**Rekomendowane długości kluczy RSA:**

| Poziom bezpieczeństwa (bity) | Długość klucza RSA | Odpowiednik AES |
|---|---|---|
| 80 | 1024 | — |
| 112 | 2048 | AES-128 |
| 128 | 3072 | AES-128 |
| 192 | 7680 | AES-192 |
| 256 | 15360 | AES-256 |

## Problem logarytmu dyskretnego (Diffie-Hellman, ElGamal)

### Sformułowanie problemu

**Problem logarytmu dyskretnego (Discrete Logarithm Problem — DLP):**

Dana jest grupa cykliczna $\mathbb{Z}_p^*$ rzędu $p - 1$ (gdzie $p$ jest liczbą pierwszą), generator $g$ tej grupy oraz element $h \in \mathbb{Z}_p^*$. Zadanie polega na znalezieniu liczby całkowitej $x$ takiej, że:

$$g^x \equiv h \pmod{p}$$

Potęgowanie modularne ($g^x \bmod p$) jest operacją łatwą (algorytm szybkiego potęgowania, złożoność $O(\log x)$ mnożeń modularnych), natomiast odwrócenie — znalezienie $x$ z $g$, $h$ i $p$ — jest problemem trudnym.

### Protokół wymiany kluczy Diffie-Hellman

Protokół Diffie-Hellman (1976) umożliwia dwóm stronom (Alicji i Bobowi) uzgodnienie wspólnego tajnego klucza przez niezabezpieczony kanał komunikacyjny.

**Parametry publiczne:** liczba pierwsza $p$ i generator $g$ grupy $\mathbb{Z}_p^*$.

```
Alicja:                              Bob:
  a ← losowa (1 < a < p-1)            b ← losowa (1 < b < p-1)
  A ← g^a mod p                       B ← g^b mod p
  
  ──── wysyła A ────────────────────►
  ◄──── wysyła B ───────────────────
  
  K_A ← B^a mod p                     K_B ← A^b mod p
      = (g^b)^a mod p                     = (g^a)^b mod p
      = g^(ab) mod p                      = g^(ab) mod p
  
  K_A = K_B = K (wspólny sekret)
```

Podsłuchujący zna $g$, $p$, $A = g^a \bmod p$ i $B = g^b \bmod p$, ale aby obliczyć $K = g^{ab} \bmod p$, musiałby rozwiązać **problem Diffie-Hellmana (CDH)**, który jest co najmniej tak trudny jak DLP.

### Kryptosystem ElGamal

Kryptosystem ElGamal (1985) wykorzystuje DLP do szyfrowania i podpisów cyfrowych.

#### Generacja kluczy

1. Wybierz dużą liczbę pierwszą $p$ i generator $g$ grupy $\mathbb{Z}_p^*$
2. Wybierz losowy klucz prywatny $x$, gdzie $1 < x < p - 1$
3. Oblicz klucz publiczny: $y \equiv g^x \pmod{p}$

**Klucz publiczny:** $(p, g, y)$

**Klucz prywatny:** $x$

#### Szyfrowanie

Dla wiadomości $m$ ($1 \leq m < p$):
1. Wybierz losowe $k$, gdzie $1 < k < p - 1$ i $\gcd(k, p-1) = 1$
2. Oblicz: $c_1 \equiv g^k \pmod{p}$
3. Oblicz: $c_2 \equiv m \cdot y^k \pmod{p}$

**Szyfrogram:** $(c_1, c_2)$

#### Deszyfrowanie

$$m \equiv c_2 \cdot (c_1^x)^{-1} \pmod{p}$$

Poprawność: $c_2 \cdot (c_1^x)^{-1} = m \cdot y^k \cdot (g^{kx})^{-1} = m \cdot g^{xk} \cdot g^{-xk} = m$

### Najlepsze znane algorytmy dla DLP

| Algorytm | Złożoność | Uwagi |
|---|---|---|
| Brute force | $O(p)$ | Przegląd zupełny |
| Baby-step giant-step (Shanks) | $O(\sqrt{p})$ czas i pamięć | Deterministyczny |
| Metoda $\rho$ Pollarda (DLP) | $O(\sqrt{p})$ czas, $O(1)$ pamięć | Probabilistyczny |
| Index calculus | $O\!\left(\exp\!\left(c \cdot \sqrt{\ln p \cdot \ln \ln p}\right)\right)$ | Subwykładniczy |
| GNFS (dla DLP) | $O\!\left(\exp\!\left(c \cdot (\ln p)^{1/3} (\ln \ln p)^{2/3}\right)\right)$ | Najlepszy ogólny |

## Kryptografia krzywych eliptycznych (ECC)

### Krzywa eliptyczna

Krzywa eliptyczna nad ciałem $\mathbb{F}_p$ (gdzie $p$ jest liczbą pierwszą, $p > 3$) to zbiór punktów $(x, y)$ spełniających równanie:

$$y^2 \equiv x^3 + ax + b \pmod{p}$$

gdzie $4a^3 + 27b^2 \not\equiv 0 \pmod{p}$ (warunek nieosobliwości), wraz z punktem w nieskończoności $\mathcal{O}$ (element neutralny).

### Grupa punktów krzywej eliptycznej

Punkty krzywej eliptycznej tworzą grupę abelową z operacją dodawania punktów:
- **Element neutralny:** punkt w nieskończoności $\mathcal{O}$
- **Dodawanie punktów** $P + Q$: geometrycznie — prosta przez $P$ i $Q$ przecina krzywą w trzecim punkcie $R'$; wynikiem jest odbicie $R'$ względem osi $x$
- **Podwajanie punktu** $P + P = 2P$: styczna do krzywej w punkcie $P$

**Wzory algebraiczne** dla $P = (x_1, y_1)$, $Q = (x_2, y_2)$, $P + Q = (x_3, y_3)$:

Jeśli $P \neq Q$:

$$\lambda = \frac{y_2 - y_1}{x_2 - x_1} \pmod{p}$$

Jeśli $P = Q$ (podwajanie):

$$\lambda = \frac{3x_1^2 + a}{2y_1} \pmod{p}$$

Następnie:

$$x_3 = \lambda^2 - x_1 - x_2 \pmod{p}$$

$$y_3 = \lambda(x_1 - x_3) - y_1 \pmod{p}$$

### Problem logarytmu dyskretnego na krzywych eliptycznych (ECDLP)

**ECDLP:** Dany jest punkt bazowy $G$ na krzywej eliptycznej oraz punkt $Q = kG$ (mnożenie skalarne — $k$-krotne dodanie $G$ do siebie). Zadanie polega na znalezieniu liczby $k$.

Kluczowa różnica: dla ECDLP **nie istnieje odpowiednik algorytmu index calculus**, więc najlepsze znane algorytmy mają złożoność $O(\sqrt{n})$ (metoda $\rho$ Pollarda), gdzie $n$ jest rzędem grupy. Dlatego ECC oferuje wyższy poziom bezpieczeństwa na bit klucza.

### ECDH (Elliptic Curve Diffie-Hellman)

Analogia protokołu Diffie-Hellman na krzywych eliptycznych:

```
Parametry publiczne: krzywa E, punkt bazowy G rzędu n

Alicja:                              Bob:
  a ← losowa (1 < a < n)              b ← losowa (1 < b < n)
  A ← aG (mnożenie skalarne)          B ← bG
  
  ──── wysyła A ────────────────────►
  ◄──── wysyła B ───────────────────
  
  K_A ← aB = a(bG) = (ab)G            K_B ← bA = b(aG) = (ab)G
  
  K_A = K_B = K (wspólny sekret)
```

## Porównanie bezpieczeństwa

### Porównanie długości kluczy

| Poziom bezpieczeństwa (bity) | Klucz symetryczny (AES) | RSA / DH | ECC |
|---|---|---|---|
| 80 | 80 | 1024 | 160 |
| 112 | 112 | 2048 | 224 |
| 128 | 128 | 3072 | 256 |
| 192 | 192 | 7680 | 384 |
| 256 | 256 | 15360 | 521 |

ECC wymaga kluczy **~10-15× krótszych** niż RSA dla tego samego poziomu bezpieczeństwa.

### Porównanie algorytmów asymetrycznych

| Cecha | RSA | DH / ElGamal | ECC |
|---|---|---|---|
| Problem matematyczny | Faktoryzacja (IFP) | Logarytm dyskretny (DLP) | ECDLP |
| Typowa długość klucza | 2048-4096 bitów | 2048-4096 bitów | 256-521 bitów |
| Wydajność generacji kluczy | Wolna (szukanie liczb pierwszych) | Średnia | Szybka |
| Wydajność szyfrowania | Szybka (mały $e$) | Średnia | Szybka |
| Wydajność deszyfrowania | Wolna (duży $d$) | Średnia | Szybka |
| Rozmiar szyfrogramu | $= |n|$ | $2 \times |p|$ | $2 \times |klucz|$ |
| Zagrożenie kwantowe | Tak (algorytm Shora) | Tak (algorytm Shora) | Tak (algorytm Shora) |
| Standardy | PKCS#1, FIPS 186 | RFC 3526, RFC 7919 | NIST P-256/384/521, Curve25519 |

### Zagrożenie ze strony komputerów kwantowych

Algorytm Shora (1994) pozwala na:
- Faktoryzację w czasie wielomianowym — łamie RSA
- Rozwiązanie DLP w czasie wielomianowym — łamie DH, ElGamal, ECC

Dlatego trwają prace nad **kryptografią postkwantową** (post-quantum cryptography) opartą na problemach odpornych na ataki kwantowe (np. kratowe — lattice-based, kodowe — code-based, wielomianowe — multivariate).

## Przykłady

### Przykład 1: Generacja kluczy RSA z małymi liczbami

```
Krok 1 — Wybór liczb pierwszych:
  p = 61,  q = 53

Krok 2 — Obliczenie modułu:
  n = p × q = 61 × 53 = 3233

Krok 3 — Funkcja Eulera:
  φ(n) = (p-1)(q-1) = 60 × 52 = 3120

Krok 4 — Wybór wykładnika publicznego:
  e = 17  (bo gcd(17, 3120) = 1)

Krok 5 — Obliczenie wykładnika prywatnego:
  d ≡ e⁻¹ (mod φ(n))
  d ≡ 17⁻¹ (mod 3120)
  
  Rozszerzony algorytm Euklidesa:
  17 × d ≡ 1 (mod 3120)
  17 × 2753 = 46801 = 15 × 3120 + 1
  d = 2753

Klucz publiczny:  (n, e) = (3233, 17)
Klucz prywatny:   (n, d) = (3233, 2753)
```

### Przykład 2: Szyfrowanie i deszyfrowanie RSA

```
Wiadomość: m = 65 (np. litera 'A' w ASCII)

=== SZYFROWANIE (klucz publiczny: n=3233, e=17) ===
  c = m^e mod n
  c = 65^17 mod 3233

  Szybkie potęgowanie modularne:
  65^1  mod 3233 = 65
  65^2  mod 3233 = 4225 mod 3233 = 992
  65^4  mod 3233 = 992^2 mod 3233 = 984064 mod 3233 = 2149
  65^8  mod 3233 = 2149^2 mod 3233 = 4618201 mod 3233 = 2452
  65^16 mod 3233 = 2452^2 mod 3233 = 6012304 mod 3233 = 2195

  65^17 = 65^16 × 65^1
  c = (2195 × 65) mod 3233 = 142675 mod 3233 = 2790

  Szyfrogram: c = 2790

=== DESZYFROWANIE (klucz prywatny: n=3233, d=2753) ===
  m = c^d mod n
  m = 2790^2753 mod 3233

  (obliczenie szybkim potęgowaniem modularnym)
  m = 65 ✓

  Odzyskana wiadomość: m = 65 (litera 'A')
```

### Przykład 3: Wymiana kluczy Diffie-Hellman z małymi liczbami

```
Parametry publiczne: p = 23, g = 5

=== ALICJA ===
  Klucz prywatny: a = 6
  Klucz publiczny: A = g^a mod p = 5^6 mod 23
    5^1 = 5
    5^2 = 25 mod 23 = 2
    5^4 = 2^2 = 4
    5^6 = 5^4 × 5^2 = 4 × 2 = 8
  A = 8

=== BOB ===
  Klucz prywatny: b = 15
  Klucz publiczny: B = g^b mod p = 5^15 mod 23
    5^8  = 5^4 × 5^4 = 4 × 4 = 16
    5^15 = 5^8 × 5^4 × 5^2 × 5^1 = 16 × 4 × 2 × 5 = 640 mod 23
    640 = 27 × 23 + 19
  B = 19

=== WYMIANA ===
  Alicja wysyła A = 8 do Boba
  Bob wysyła B = 19 do Alicji

=== OBLICZENIE WSPÓLNEGO SEKRETU ===
  Alicja: K = B^a mod p = 19^6 mod 23
    19^2 = 361 mod 23 = 16
    19^4 = 16^2 = 256 mod 23 = 3
    19^6 = 19^4 × 19^2 = 3 × 16 = 48 mod 23 = 2
  K_A = 2

  Bob: K = A^b mod p = 8^15 mod 23
    8^2  = 64 mod 23 = 18
    8^4  = 18^2 = 324 mod 23 = 2
    8^8  = 2^2 = 4
    8^15 = 8^8 × 8^4 × 8^2 × 8^1 = 4 × 2 × 18 × 8 = 1152 mod 23
    1152 = 50 × 23 + 2
  K_B = 2

  Wspólny sekret: K = K_A = K_B = 2 ✓

Podsłuchujący zna: p=23, g=5, A=8, B=19
Aby obliczyć K, musiałby rozwiązać DLP: 5^a ≡ 8 (mod 23) → a = ?
```

## Podsumowanie

1. **Kryptografia asymetryczna** opiera się na funkcjach jednokierunkowych z furtką — operacjach łatwych do wykonania w jednym kierunku, ale trudnych do odwrócenia bez znajomości klucza prywatnego.

2. **Problem faktoryzacji (IFP)** stanowi podstawę algorytmu RSA. Mnożenie dwóch dużych liczb pierwszych jest łatwe, ale rozkład ich iloczynu na czynniki jest trudny obliczeniowo. Najlepszy znany algorytm (GNFS) ma złożoność subwykładniczą. RSA wykorzystuje to do konstrukcji pary kluczy: publiczny $(n, e)$ i prywatny $(n, d)$, gdzie bezpieczeństwo wynika z trudności obliczenia $d$ bez znajomości $\varphi(n) = (p-1)(q-1)$.

3. **Problem logarytmu dyskretnego (DLP)** stanowi podstawę protokołu Diffie-Hellman i kryptosystemu ElGamal. Potęgowanie modularne $g^x \bmod p$ jest łatwe, ale znalezienie $x$ z $g^x \bmod p$ jest trudne. DLP ma podobną złożoność obliczeniową do faktoryzacji w grupach $\mathbb{Z}_p^*$.

4. **Kryptografia krzywych eliptycznych (ECC)** przenosi DLP do grupy punktów krzywej eliptycznej (ECDLP). Brak algorytmu index calculus dla ECDLP sprawia, że ECC oferuje wyższy poziom bezpieczeństwa na bit klucza — klucz 256-bitowy ECC odpowiada kluczowi 3072-bitowemu RSA.

5. **Porównanie:** RSA wymaga kluczy 2048-4096 bitów, DH/ElGamal podobnie, natomiast ECC osiąga porównywalny poziom bezpieczeństwa z kluczami 256-521 bitów. Wszystkie trzy podejścia są zagrożone przez algorytm Shora na komputerach kwantowych.

6. W praktyce RSA i ECC są najczęściej stosowanymi algorytmami asymetrycznymi — RSA w starszych systemach i podpisach, ECC w nowoczesnych protokołach (TLS 1.3, Signal, Bitcoin). Trwają prace nad kryptografią postkwantową odporną na ataki kwantowe.

## Powiązane pytania

- [Pytanie 51: Budowa rejestrów rozproszonych typu blockchain](51-blockchain-rejestry-rozproszone.md)
