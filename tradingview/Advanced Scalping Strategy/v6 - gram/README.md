# Fabio Scalping v3 + BB50 + Order Flow Delta — README

Prosty przewodnik **krok po kroku**, jak korzystać z Twojego skryptu strategii (Pine v6) razem z panelem **Order Flow Delta Tracker** (Pine v5) do grania **intraday**.

---

## 1) Co to jest i dla kogo?

* **Fabio Scalping v3 (z BB50)** – strategia wejść/wyjść oparta o: **VWAP (kierunek/bias)**, **Order Blocki** (lokalne strefy popytu/podaży), **impulse candles** (świece z wolumenem), **ATR** (SL/trailling) oraz **Bollinger Bands 50** (kontekst zmienności).
* **Order Flow Delta Tracker** – osobny panel pod wykresem do czytania przepływu zleceń: **Delta**, **Cumulative Delta**, **absorpcja**, **nierównowagi (imbalance)**, **dywergencje** i **VWAP** w panelu.
* Dla osób grających **intra**, 2–3 transakcje dziennie, preferujących jasne reguły i szybki **risk management**.

---

## 2) Ustawienia wykresu (TradingView)

1. **Interwał**: 1–5 min (dla akcji i ETF), 1–3 min (dla bardzo płynnych tickerów). Zacznij od **3 min**.
2. **Sesja**: graj głównie w **godzinach regularnych** (np. NASDAQ/NYSE 15:30–22:00 CET). Unikaj pierwszych 1–2 minut po starcie, jeśli spread jest duży.
3. **Wskaźniki na wykresie**:

   * Dodaj strategię: **Fabio Scalping Strategy v3 (with Legend + BB50)** – overlay na cenie.
   * W osobnym panelu dodaj **Order Flow Delta Tracker** (nasz drugi skrypt).
4. **Skalowanie**: w strategii włącz widoczność **VWAP** i **BB50** (basis + bandy). W panelu OF zostaw **Delta, CumDelta, VWAP, Session CumDelta**.

---

## 3) Szybkie zrozumienie elementów

* **VWAP (pomarańczowa)**: kierunek rynku w danym dniu. Powyżej – przewaga long; poniżej – przewaga short.
* **Order Block (OB)**:

  * 🟢 **Bull OB** – świeca impulsowa w górę; strefa **wsparcia** (ostatnie *low* tej świecy).
  * 🔴 **Bear OB** – świeca impulsowa w dół; strefa **oporu** (ostatnie *high* tej świecy).
  * **Ważność** OB ograniczona jest parametrem **Max OB Age (bars)**.
* **Impulse candle (⚡)**: świeca z **wolumenem > volSMA * mult** i dużym korpusem (body_ratio > próg).
* **Follow‑up (✔)**: następna świeca w tym samym kierunku – **potwierdzenie** momentum.
* **BB50 (niebieskie)**: tło zmienności – wybicia powyżej/pod dolny pas często oznaczają **ekstrema**/przeciągnięcie ruchu.
* **ATR**: wyznacza **Stop Loss** i trailing SL w oparciu o zmienność.

**W panelu Delta:**

* **Delta** (słupki): ile było „kupna minus sprzedaż” w świecy (proxy).
* **Cumulative Delta (CumDelta, niebieska)**: narastająca suma delty z ostatnich N świec → **trend w przepływie zleceń**.
* **Imbalance (czerwone tło)**: przewaga podaży w górnej części świecy (sygnał presji spadkowej).
* **Absorpcja (pomarańczowy trójkąt)**: duży wolumen, mały korpus → ktoś „przyjmuje” zlecenia.
* **Dywergencja**: cena robi **HH**, a CumDelta nie potwierdza → słabnący popyt.
* **VWAP (żółta w panelu)**: odniesienie siły vs. cena.

---

## 4) Reguły wejść (system)

> **Cel**: grać tylko, gdy *kierunek, strefa i wolumen* są zgodne.

### Long (Kupno)

1. **Bias**: cena **powyżej** VWAP (lub `use_bias=false` – patrz warianty poniżej).
2. **Strefa**: jesteś **blisko Bull OB** (sygnał `bull_near` – cena wraca/testuje wsparcie z ostatniej świecy impulsowej).
3. **Momentum**: pojawił się **⚡ Impulse up** oraz kolejna świeca **✔ follow‑up up**.
4. **OF potwierdzenie** (opcjonalne, ale zalecane):

   * **Delta ≥ 0** na wejściowej świecy,
   * **CumDelta** rośnie lub brak dywergencji bessy,
   * brak czerwonego tła **imbalance** nad świecą wejścia.
5. **Wejście**: po zamknięciu świecy z ✔ (lub na retest strefy, jeśli jest).

### Short (Sprzedaż)

1. **Bias**: cena **poniżej** VWAP.
2. **Strefa**: **blisko Bear OB** (test oporu).
3. **Momentum**: **⚡ Impulse down** + **✔ follow‑up down**.
4. **OF potwierdzenie**:

   * **Delta ≤ 0**,
   * **CumDelta** spada lub brak dywergencji byczej,
   * pojawia się **imbalance** (czerwone tło) – dodatkowy plus dla shortów.
5. **Wejście**: po ✔ (lub na retest strefy).

> **Nie bierz sygnałów** przeciw VWAP, **bez** OB w pobliżu, **bez** ⚡+✔, **w środku pasma BB** gdy rynek jest płaski i wolumen niski.

---

## 5) Zarządzanie pozycją

**Stop‑Loss (SL)** – ustawiany automatycznie przez strategię:

* **Long**: `SL = Bull OB – ATR * SL Multiplier`.
* **Short**: `SL = Bear OB + ATR * SL Multiplier`.

**Trailing SL** (automatyczny):

* Kiedy **wolumen > volSMA * trail_vol_mult**, SL przesuwa się agresywnie w kierunku ceny (na bazie ATR). To pozwala **blokować zysk** przy przyspieszeniu ruchu.

**Take‑Profit (TP) – propozycje praktyczne:**

* **Konserwatywnie**: 1R–1.5R (R = wielkość ryzyka do SL).
* **Dynamicznie**: częściowe TP przy **BB basis**/**VWAP**/**lokalnych HH/LL**, resztę prowadź trailingiem.
* **Ekstrema BB50**: gdy cena wybija mocno poza BB Upper/Lower – rozważ **realizację**, bo to często skraj.

**Wyjście manualne – OF sygnały ostrzegawcze:**

* **Dywergencja** (cena HH, CumDelta nie rośnie) – bywa szczyt zmęczenia.
* **Absorpcja** przeciw Twojej pozycji blisko TP – ryzyko odwrócenia.
* Seria **przeciwnych Δ spike’ów**.

---

## 6) Parametry – szybki przewodnik

* **VWAP Bias**: włączony = grasz *z trendem dnia*. Dla początkujących **zostaw ON**.
* **OB Range Multiplier (ATR)**: 1.5 domyślnie. Więcej = rzadsze, „mocniejsze” OB.
* **Max OB Age**: 20–50 barów – starsze OB mniej warte.
* **OB Price Buffer (%)**: 0.3–0.8% – tolerancja wejścia „blisko” strefy.
* **Volume SMA Period**: 20; **Volume Spike Multiplier**: 1.8–2.5 – dopasuj do instrumentu.
* **Body/Range Threshold**: 0.5–0.7 – im wyżej, tym „bardziej prawdziwe” impulsy.
* **ATR Period**: 14; **SL ATR Multiplier**: 0.8–1.2 dla szybkich rynków, 1.2–1.6 dla spokojniejszych.
* **Trail Volume Spike Multiplier**: 1.8–2.5 – częstotliwość podciągania SL.
* **BB Length**: 50; **BB Multiplier**: 2.0 – klasyka. Przy bardzo wysokiej zmienności możesz zejść do 1.8.

**W panelu Delta:**

* **CumDelta okno**: 50 (w kodzie). Dla wolniejszych walorów 100–200; dla skalpu 30–60.

---

## 7) Playbook – 3 przykładowe setupy

**A. Trend‑pullback do Bull OB (long)**

1. Cena powyżej VWAP → **bias long**. 2) Mamy świeżo wykryty **Bull OB**. 3) Pullback w okolice OB (sygnał *bull_near*). 4) Pojawia się **⚡ + ✔**. 5) **Delta ≥ 0**, CumDelta nie spada. 6) **Wejście long**; SL pod OB, TP 1R/BB basis/VWAP.

**B. Od rejection BB Upper z Bear OB (short)**

1. Cena dochodzi nad **BB Upper**, pojawia się czerwone **imbalance** i świeca spadkowa. 2) Niedaleko mamy **Bear OB**. 3) **⚡ down + ✔ down**. 4) **CumDelta spada**, czasem dywergencja wcześniejsza. 5) **Wejście short**, SL nad OB; TP 1–1.5R lub BB basis.

**C. Revers na absorpcji + dywergencja (kontrarian, ostrożnie)**

1. Długi ruch up, **CumDelta przestaje rosnąć** (dywergencja). 2) Pojawia się **absorpcja** na szczycie. 3) **⚡ down + ✔ down** na teście **Bear OB**/BB Upper. 4) **Short**; szybki TP 1R, reszta trailing.

---

## 8) Risk & Money Management

* **Ryzyko na trade**: 0.5–1.0% kapitału.
* **Wejścia partiami**: 1/2 lub 1/3 pozycji na sygnał, reszta na retest.
* **Nie dokładaj do stratnych**. Wyjątek: planowany **pyramid** tylko po zabezpieczeniu SL > BE.
* **Maks. 2–3 transakcje dziennie** – jakość > ilość.
* **Stop handlu** po 2 stratach z rzędu; wróć jutro.

---

## 9) Kiedy nie grać

* Bardzo **niski wolumen**, brak ⚡ oraz brak OB w pobliżu.
* **Środek pasma BB** i konsola bez kierunku (VWAP płaski, cena go „przecina” w tę i z powrotem).
* **Publikacje makro** za 1–2 min (nagłe skoki Δ bez kontekstu).
* **Pierwsze 1–2 min sesji**, jeśli spread szeroki.

---

## 10) Checklista przed kliknięciem

* [ ] Cena względem **VWAP**? (z kierunkiem)
* [ ] Jest **OB** blisko? (bull_near/bear_near)
* [ ] **⚡ + ✔** w Twoim kierunku?
* [ ] **Delta** i **CumDelta** nie zaprzeczają?
* [ ] **SL** i **TP** policzone (≥1R)?
* [ ] Spread, zmienność, newsy OK?

---

## 11) Najczęstsze pytania (FAQ)

**Czy BB50 służy do sygnału wejścia?**  Nie wprost. To **kontekst**: skrajne wybicia poza pasma → myśl o realizacji / mean‑reversion.

**Co jeśli sygnał ⚡ + ✔ jest, ale nie ma OB blisko?**  Pomijaj. Rdzeń systemu to **strefa + momentum**.

**Czy muszę patrzeć na panel Delta?**  Strategia działa i bez tego, ale OF znacznie **podnosi jakość** selekcji setupów.

**Bias z VWAP wyłączony?**  To tryb zaawansowany – więcej sygnałów, ale też więcej fałszywek. Dla startu: **ON**.

---

## 12) Presety startowe

* **Akcje płynne (3m)**: vol_mult **2.0**, trail_vol_mult **2.0**, sl_buffer **1.0**, body_thresh **0.6**.
* **Mega‑płynne (NVDA, QQQ, 1–2m)**: vol_mult **2.2–2.5**, body_thresh **0.65**, sl_buffer **0.9**.
* **Spokojniejsze**: vol_mult **1.8–2.0**, sl_buffer **1.2–1.4**.

---

## 13) Podsumowanie

**Wejście = VWAP bias + test OB + ⚡ + ✔ (+ OF potwierdzenie).**
**Wyjście = 1R/BB/VWAP + trailing na wolumenie.**
Discyplina, niska ekspozycja na pojedynczy trade i brak pościgu za rynkiem to klucz do stabilnego intraday.

> Ten README opisuje logikę Twoich skryptów i praktyczne zasady gry. Dostosuj parametry do instrumentu i własnego tempa, a statystyki (win‑rate, R:R) szybko pokażą, gdzie dokręcić śruby.

