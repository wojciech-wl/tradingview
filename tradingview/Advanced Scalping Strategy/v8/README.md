# Fabio – strategia w Pine Script v8

**Wersja pliku:** README v1.0
**Script name:** `Fabio` (TradingView, Pine v8)

---

## 1) Opis ogólny

`Fabio` to zaawansowana strategia intraday/swing łącząca **bias VWAP**, **Order Blocki (OB)**, **impuls wolumenowy**, prosty **proxy delta (tick‑rule)**, **Bollinger Bands (BB)** oraz **Volume Profile (POC/VA)** jako kontekst. Wejścia filtruje bliskością do świeżych OB i potwierdza świecą „follow‑up". Zarządzanie pozycją oparte jest o **ATR stop** i **wolumenowy trailing**.

> **Cel**: selektywne wejścia w pobliżu stref popytu/podaży (OB), tylko gdy ruch jest pchany przez wyraźny impuls wolumenowy, potwierdzony kierunkowo i wspierany przez Volume Profile.

---

## 2) Funkcje i komponenty

* **VWAP Bias** – trend filter: long gdy `close > VWAP`, short gdy `close < VWAP`.
* **Order Block (OB)** – prosty detektor świec o zakresie > `ATR * ob_strength`. Zapamiętuje ostatni bull/bear OB przez `ob_max_age` barów.
* **Proximity buffer** – wejścia tylko, gdy cena dotyka strefy OB w buforze `ob_buffer` (w % ATR).
* **Impulse candle** – wolumen `> SMA(vol) * vol_mult` oraz korpus/zakres > `body_thresh` i kierunek świecy zgodny.
* **Follow‑up candle** – kolejna świeca zgodna z kierunkiem impulsu (potwierdzenie momentum).
* **Delta bubbles (proxy)** – **body‑priority + tick‑rule fallback**: znak delty najpierw z koloru korpusu, jeśli doji → z tick‑rule (zmiana close vs close[1]), jeśli nadal 0 → poprzedni znak. Bąble rysowane dla `|delta| ≥ bubble_min` (osobny próg **display**), kolor wg znaku, rozmiar skokowo wg siły (5 poziomów: 3-5k, 5-10k, 10-25k, 25-50k, 50k+).
* **BB (opcjonalnie)** – kontekst zmienności: `length = 50`, `mult = 2.0` (domyślnie). Nie generuje sygnałów, tylko tło.
* **Volume Profile (NOWOŚĆ!)** – lekkoniski VP z POC (Point of Control) i Value Area (70% wolumenu). Opcjonalny filtr wejść względem POC. Czerwona linia = POC, turkusowe = VAH/VAL.
* **Risk management** – SL wg ATR od OB; trailing aktywuje się przy skoku wolumenu (`trail_vol_mult`).

---

## 0) TradingView – instalacja i start

1. Wejdź na [tradingview.com](https://www.tradingview.com/) i załóż darmowe konto (Sign Up).
2. Potwierdź e‑mail i zaloguj się.
3. Zainstaluj aplikację TradingView:

   * **Desktop**: dostępna dla Windows/macOS z poziomu strony (Download App).
   * **Mobile**: pobierz z Google Play (Android) lub App Store (iOS).
4. Po zalogowaniu zsynchronizujesz wykresy i ustawienia między przeglądarką, desktopem i telefonem.
5. Na telefonie możesz otworzyć wykres, wyszukać spółkę/ETF, dodać własne wskaźniki/skrypty i mieć podgląd w ruchu.
6. **Jak wgrać skrypt z tego repo do TradingView:**

   * Skopiuj cały kod strategii (z pliku `.pine`).
   * W TradingView otwórz zakładkę **Pine Editor** (na dole ekranu, tylko w wersji web/desktop).
   * Wklej kod i kliknij **Add to Chart**.
   * Zapisz skrypt do swojego profilu (Save → nadaj nazwę, np. „Fabio v8").
   * Skrypt pojawi się na liście Twoich wskaźników i możesz go włączać także z telefonu (zakładka Indicators → My Scripts).

---

## 3) Instalacja i uruchomienie (TradingView)

1. Skopiuj kod `Fabio` do nowego skryptu Pine v6 w TradingView.
2. Kliknij **Add to chart**.
3. W panelu **Settings** skonfiguruj wejścia (patrz sekcja 4).
4. Aby testować, przełącz na **Strategy Tester** (Backtest).

> **Uwaga:** strategia używa `strategy.entry/exit`, więc można od razu oglądać wyniki backtestu. Do handlu na żywo potrzebny jest własny routing z TV → broker.

---

## 4) Wejścia (parametry)

| Grupa           | Parametr         | Domyślnie | Opis                                                              |
| --------------- | ---------------- | --------: | ----------------------------------------------------------------- |
| Bias            | `use_bias`       |    `true` | Włącza filtr VWAP (rekomendowane).                                |
| OB              | `ob_strength`    |     `1.5` | Minimalny zakres świecy OB: `range > ATR * ob_strength`.          |
|                 | `ob_max_age`     |      `30` | Maks. „wiek" zapamiętanego OB (w barach).                         |
|                 | `ob_buffer`      | `0.5` (%) | Dopuszczalna odległość ceny od poziomu OB (w % ATR).              |
| Volume/Momentum | `vol_period`     |      `20` | Okres SMA wolumenu.                                               |
|                 | `vol_mult`       |     `2.0` | Próg „spike" wolumenu: `vol > SMA * mult`.                        |
|                 | `body_thresh`    |     `0.6` | Minimalny stosunek korpusu do zakresu świecy (0–1).               |
| SL/Trailing     | `atr_period`     |      `14` | Okres ATR do SL i OB detekcji.                                    |
|                 | `sl_buffer`      |     `1.0` | Mnożnik ATR dla SL od OB (`1.0` = 1×ATR).                         |
|                 | `trail_vol_mult` |     `2.0` | Aktywacja trailing‑SL przy skoku wolumenu.                        |
| Bollinger       | `show_bb`        |    `true` | Pokazuje BB na wykresie (kontekst).                               |
|                 | `bb_length`      |      `50` | Długość BB (swing).                                               |
|                 | `bb_mult`        |     `2.0` | Odchylenie standardowe BB (krotność σ).                           |
| Delta bubbles   | `show_bubbles`   |    `true` | Włącza etykiety bąbli delta.                                      |
|                 | `delta_min`      |    `5000` | **Próg do FILTRA WEJŚĆ** – minimalna delta wymagana do sygnału.   |
|                 | `bubble_min`     |    `3000` | **Próg do WYŚWIETLANIA bąbli** – może być niższy niż `delta_min`. |
| Volume Profile  | `show_vp`        |    `true` | Pokazuje Volume Profile (POC/VA) na wykresie.                     |
|                 | `vp_use_filter`  |   `false` | **NOWOŚĆ!** Używa POC jako filtr wejść (long≥POC, short≤POC).     |
|                 | `vp_lookback`    |     `500` | Ilość barów do analizy VP (100-5000).                             |
|                 | `vp_bins`        |      `60` | Ilość przedziałów cenowych VP (20-200).                           |

---

## 5) Logika sygnałów (TL;DR)

**Long entry** gdy łącznie:

1. **Bias**: `close > VWAP` (lub `use_bias = false`).
2. **OB**: poziom bull OB świeży (`ob_max_age`) i cena blisko (`ob_buffer` w % ATR).
3. **Impulse**: świeca z wolumenowym spike'iem + duży korpus w górę.
4. **Follow‑up**: kolejna świeca rosnąca.
5. **Delta**: `delta > delta_min` (wolumen pchający w górę; **`delta_min` dotyczy wejścia**, nie rysowania bąbli).
6. **Volume Profile**: opcjonalnie `close >= POC` (gdy `vp_use_filter = true`).

**Short entry** simetrycznie, lecz pod VWAP, z bear OB i opcjonalnie `close <= POC`.

**Stop‑Loss**:

* Ustawiany od **krawędzi odpowiedniego OB** z buforem `ATR * sl_buffer`.
* **Trailing SL**: gdy `volume > SMA(vol) * trail_vol_mult`, SL podciągany o `ATR * sl_buffer` na korzyść pozycji.

---

## 6) Jak grać – playbook

> **Horyzont**: M1–M15 intraday lub M15–H1 swing/„day‑2".

### Setup A – Long z popytu (mean‑revert → impuls)

1. Cena cofa do **bull OB** (świeży, w limicie `ob_max_age`).
2. Pojawia się **impuls up** (żółty ⚡) i **follow‑up** (✔), bias >0.
3. **Delta bubble** zielona ≥ próg (opcjonalnie większy rozmiar = lepiej).
4. **VP kontekst**: POC wspiera kierunek (opcjonalnie włączony filtr).
5. Wejście **po follow‑up** (agresywnie market) lub na drobnym pullbacku (limit).
6. **SL**: poniżej bull OB o `ATR * sl_buffer`.
7. **TP**:

   * Konserwatywnie do **BB basis/upper**, **POC/VAH** lub do **lokalnego S/R**.
   * Albo **R:R ≥ 1.5–2.0**.
   * Trailing aktywuj przy skokach wolumenu.

### Setup B – Short z podaży (odbicie od resist)

Analogicznie do Long, ale pod VWAP, przy bear OB, czerwonym bąblu i POC jako resistance.

### Setup C – Volume Profile Enhanced (NOWOŚĆ!)

* **POC jako magnes**: obserwuj jak cena reaguje na POC (czerwona linia).
* **Value Area bounces**: odbicia od VAL/VAH (turkusowe linie) mogą dać dodatkowe konfirmacje.
* **Filter mode**: gdy `vp_use_filter = true`, tylko longi ≥ POC i shorty ≤ POC.

### Dodatkowe wskazówki

* **Nie gonić świecy impulsowej** – lepszy jest wejściowy pullback lub świeca follow‑up.
* **Kontekst BB + VP**: wybicia górnego pasma BB powyżej VAH + zielone bąble ≈ silniejsza kontynuacja.
* **POC confluence**: gdy POC zbieżne z BB basis lub OB level = silniejsze S/R.
* **Sesja/okno czasu**: ustandaryzuj porę handlu (np. pierwsza godzina sesji US).
* **Zarządzanie kapitałem**: 0.5–2% ryzyka na trade; pyramiding = `1` (domyślnie).

---

## 7) Strojenie parametrów

### Podstawowe (jak w v7)
* **`delta_min`**: dopasuj do płynności instrumentu. Dla spółek z dużym wolumenem próg 5k–20k akcji; dla mniejszych 500–3k.
* **`ob_strength`**: większa wartość = rzadsze, „mocniejsze" OB. 1.2–2.0 to typowy zakres.
* **`ob_buffer`**: 0.3–0.8% ATR – ciaśniej dla precyzyjnych wejść, szerzej dla zmiennych rynków.
* **`vol_mult` / `body_thresh`**: kontrolują jakość impulsu. Na noisy tickach rozważ `vol_mult` 2.0–3.0.
* **`sl_buffer`**: im wyższy interwał/zmienność, tym większy bufor (1.0–1.8× ATR).

### Volume Profile (NOWOŚĆ!)
* **`vp_lookback`**: 200-500 dla intraday, 500-2000 dla swing. Więcej barów = stabilniejszy POC, ale wolniejsza aktualizacja.
* **`vp_bins`**: 40-80 dla większości timeframów. Więcej binów = większa precyzja, ale może być noise.
* **`vp_use_filter`**: `false` dla kontekstu, `true` dla dodatkowego filtra (mniej sygnałów, ale potencjalnie lepszych).

---

## 8) Backtest – dobre praktyki

* **Sesja i strefa czasowa**: trzymaj stałe (np. NYSE 09:30–16:00).
* **Dane**: preferuj regular session bez extended hours dla akcji US.
* **Commission & slippage**: ustaw realistycznie (np. 0.01–0.03$/akcję + 1–3 ticki poślizgu).
* **Out‑of‑sample**: stroisz na 3–6 miesiącach, walidujesz na innych 3–6 mies.
* **Zmieniaj tylko 1–2 parametry na raz** i zapisuj wyniki (net profit, winrate, PF, max DD, avg trade).
* **VP testing**: testuj zarówno z `vp_use_filter = false` (tylko kontekst) jak i `true` (aktywny filtr).

---

## 9) Ograniczenia i uwagi

* **Delta bubbles to proxy**, nie realny order‑flow z tape. Znak bazuje na zmianie ceny bara (`tick‑rule`) i kolorze korpusu.
* **Detekcja OB** jest uproszczona (range > ATR*k), nie klasyczna SMC‑struktura.
* **BB** to kontekst, nie sygnał. Nie wszystkie wybicia są równe.
* **Volume Profile** jest uproszczony (bez Time & Sales), bazuje tylko na close price każdego bara.
* **Rynek o niskiej płynności**: rozważ większe progi delty i wolumenu, by ograniczyć fałszywe sygnały.

---

## 10) FAQ / Troubleshooting

**Q: Bąbel zielony na czerwonej świecy?**
A: Rzadziej niż wcześniej, bo teraz **priorytet ma kolor korpusu**. Jeśli świeca jest **doji**, wchodzi tick‑rule i taki przypadek może się pojawić. To nie błąd, tylko skutek proxy. Jeśli przeszkadza – zwiększ `bubble_min` i/lub `delta_min`.

**Q: Za dużo/mało sygnałów?**
A: Zmieniaj `ob_strength`, `ob_max_age`, `vol_mult`, `body_thresh`, **`delta_min`** (wejścia), **`bubble_min`** (wizualizacja) oraz **`vp_use_filter`** (włącz dla mniejszej ilości sygnałów).

**Q: SL „za ciasny"?**
A: Podnieś `sl_buffer` (np. 1.4–1.8) lub zwiększ TF.

**Q: Strategia łapie konsolidacje.**
A: Utrzymaj VWAP bias, podnieś `vol_mult`, wymagaj dotknięcia OB, filtruj sesję, rozważ włączenie `vp_use_filter`.

**Q: Volume Profile nie pokazuje się lub jest dziwny?**
A: Sprawdź `vp_lookback` (może za mało danych) i `vp_bins`. Dla krótkich sesji zmniejsz lookback, dla długoterminowych zwiększ.

**Q: POC filter daje za mało sygnałów?**
A: To normalny efekt dodatkowego filtra. Możesz wyłączyć `vp_use_filter` i używać VP tylko jako kontekst.

---

## 11) Checklist przed wejściem (kartka na monitor)

* [ ] Cena względem **VWAP**? (zgodna z kierunkiem biasu)
* [ ] Jest świeży **OB** blisko? (bull_near / bear_near)
* [ ] **⚡ + ✔** w Twoim kierunku?
* [ ] **Delta** i **CumDelta** nie zaprzeczają?
* [ ] **POC/VA** wspiera lub nie przeszkadza kierunkowi?
* [ ] **SL** i **TP** policzone (≥1R)?
* [ ] Spread, zmienność i newsy OK?
* [ ] Zerknij na **futures Nasdaq** – czy kierunek i momentum wspierają Twój trade?

---

## 12) Zmiany w kodzie v8 (vs v7)

### Główne nowości:
* **Volume Profile (POC/VA)**: implementacja lekkiej wersji VP z POC (czerwona linia) i Value Area 70% (turkusowe linie VAH/VAL).
* **VP jako filtr**: opcjonalny `vp_use_filter` – longi tylko gdy `close >= POC`, shorty gdy `close <= POC`.
* **Ulepszony legend**: dodano informacje o POC/VA w tabeli na wykresie.
* **Optymalizacja**: lepsza organizacja kodu VP z konfigurowalnymi parametrami `vp_lookback` i `vp_bins`.

### Zachowane z v7:
* Detekcja OB: świeca o dużym zakresie → zapisz poziom OB i indeks. Ważność `ob_max_age` barów.
* Wejście tylko **near OB** (`abs(price–OB) ≤ ATR*(ob_buffer/100)`).
* Impuls = `volume > SMA*mult` i `|body|/range > body_thresh` + zgodny kolor.
* Follow‑up = kolejna świeca w tym samym kierunku.
* **Delta (body priority):** znak wg sekwencji **(1) korpus ≠ 0 → znak korpusu; (2) doji → tick‑rule; (3) dalej 0 → poprzedni znak**.
* **Progi rozdzielone:** `delta_min` → **filtr wejść**, `bubble_min` → **display bąbli**.
* **Bąble 5 rozmiarów:** 3–5k, 5–10k, 10–25k, 25–50k, 50k+ (jak w kodzie).
* SL od OB ± `ATR*sl_buffer`, trailing przy spike'u wolumenu.

---

## 13) Licencja & zastrzeżenia

Ten skrypt i README mają charakter **edukacyjny**. To **nie** jest rekomendacja inwestycyjna. Handel na rynkach finansowych wiąże się z ryzykiem utraty kapitału. Używaj na własną odpowiedzialność, uwzględniając opłaty i poślizgi.
© 2025 – **licencja należy do Wojtasa** – na beke, ale serio, do prywatnego użytku, bez gwarancji.

---

## 14) Pomysły na rozwój

* Filtr sesji (`input.session`) i auto‑blokada poza godzinami.
* Zaawansowana detekcja OB (SMC, FVG, BOS/CHOCH).
* **Enhanced Volume Profile**: VWAP anchored VP, multi-timeframe POC.
* **Market Profile integration**: TPO charts, initial balance, value area migration.
* Własny menedżer take‑profit (poziomy BB/VPVR/ADR + POC confluence).
* Integracja z realnym order‑flow (feed L2/T&S) – jeśli dostępny.
* **VP-based position sizing**: większe pozycje przy wejściach blisko POC.
* Labeling sygnałów R/R na wykresie i statystyki trade‑by‑trade.
