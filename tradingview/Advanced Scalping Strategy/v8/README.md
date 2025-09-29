# Fabio – s> **Cel**: Chcemy wchodzić w transakcje w "ważnych" miejscach na wykresie, gdzie wcześniej było dużo walki między kupującymi a sprzedającymi. Szukamy potwierdzenia w postaci nagłego wzrostu zainteresowania (duży wolumen) i dodatkowych wskazówek z "mapy wolumenu" (Volume Profile).

---

> **WAŻNE OSTRZEŻENIE DLA POCZĄTKUJĄCYCH**
> Ta strategia **NIE HANDLUJE SAMA** na Twoim koncie u brokera (np. w banku, na Binance itp.). Jest to **narzędzie analityczne** do TradingView, które:
> 1.  **Pokazuje na wykresie sygnały**, gdzie MOŻNA by rozważyć transakcję.
> 2.  **Symuluje wyniki** w module "Strategy Tester", żebyś mógł sprawdzić jej historyczną skuteczność.
> 3.  **Oblicza sugerowane poziomy Stop Loss**, które musisz ustawić **RĘCZNIE** u swojego brokera.
>
> Traktuj ten skrypt jak zaawansowany kalkulator i system alarmowy, a nie jak autopilota. Każda decyzja o transakcji na prawdziwe pieniądze należy do Ciebie.

---

## 💡 Giełda i ta strategia w Pine Script v8

**Wersja pliku:** README v1.0
**Script name:** `Fabio` (TradingView, Pine v8)

---

## 1) Opis ogólny

`Fabio` to zestaw wskaźników, który pomaga podejmować decyzje w handlu krótkoterminowym (w ciągu jednego dnia lub na kilka dni). Działa jak zaawansowana deska rozdzielcza w samochodzie, łącząc kilka kluczowych informacji, aby znaleźć potencjalnie dobre momenty do wejścia w transakcję.

Skrypt analizuje rynek pod kątem:
*   **Głównego trendu (VWAP)**,
*   **Ważnych stref cenowych z przeszłości (Order Blocks)**,
*   **Nagłych wzrostów zainteresowania (impulsów wolumenu)**,
*   **Przewagi kupujących lub sprzedających (delta proxy)**.

Sygnały pojawiają się tylko wtedy, gdy cena znajdzie się w pobliżu tych ważnych stref i dostanie dodatkowe potwierdzenie. Poziomy zabezpieczające (Stop Loss) są sugerowane na podstawie aktualnej zmienności rynku.

> **Cel (po ludzku)**: Chcemy wchodzić w transakcje w "ważnych" miejscach na wykresie, gdzie wcześniej było dużo walki między kupującymi a sprzedającymi. Szukamy potwierdzenia w postaci nagłego wzrostu zainteresowania (duży wolumen) i dodatkowych wskazówek z "mapy wolumenu" (Volume Profile).

---

## 💡 Giełda i ta strategia - wyjaśnienie dla początkujących

Zanim zaczniesz, przeczytaj to. Serio. To najważniejsza część.

### Czym są te "wskaźniki"?

Wyobraź sobie, że jedziesz samochodem w nieznanym terenie.
*   **Wykres ceny** to droga, którą widzisz przed sobą.
*   **Wskaźniki** to Twoja deska rozdzielcza i GPS. Mówią Ci, jaka jest pogoda (trend), czy silnik ma moc (wolumen) i gdzie są potencjalne korki lub stacje benzynowe (strefy wsparcia/oporu).

### Komponenty strategii "Fabio" - Twoja deska rozdzielcza:

*   **VWAP (pomarańczowa linia)**
    *   **Co to jest?** Średnia cena, po której handlowano danego dnia, uwzględniająca wolumen.
    *   **Po co to?** To nasz **kompas trendu**.
    *   **Jak używać?** Działa jak kompas trendu. Generalnie, gdy cena jest **nad** pomarańczową linią, sytuacja jest korzystniejsza dla pozycji **długich (kupna)**. Traktuj to jako ważną wskazówkę, a nie żelazną regułę.

*   **Order Block (OB) (zielone i czerwone kółka)**
    *   **Co to jest?** To "pamięć" rynku. Duża świeca z przeszłości, gdzie było mnóstwo zleceń. Zielone kółko to strefa, gdzie kiedyś mocno kupowano (popyt). Czerwone - gdzie mocno sprzedawano (podaż).
    *   **Po co to?** To nasze **potencjalne strefy wsparcia (podłoga) i oporu (sufit)**. Cena często reaguje w tych miejscach.
    *   **Jak używać?** Czekamy, aż cena wróci w pobliże takiej strefy. Chcemy kupić tanio przy "podłodze" (zielone kółko) lub sprzedać drogo przy "suficie" (czerwone kółko).

*   **Impuls Wolumenowy (żółta błyskawica ⚡)**
    *   **Co to jest?** Świeca, na której wolumen (ilość transakcji) nagle wystrzelił w górę.
    *   **Po co to?** To sygnał, że "grube ryby" (instytucje) weszły do gry. To **potwierdzenie siły**.
    *   **Jak używać?** Sam impuls to za mało. To zaproszenie do obserwacji. Czekamy na kolejną świecę, która potwierdzi kierunek.

*   **Follow-up Candle (niebieski ptaszek ✔)**
    *   **Co to jest?** Świeca, która pojawia się **po** impulsie i idzie w tym samym kierunku.
    *   **Po co to?** To **potwierdzenie, że impuls nie był przypadkiem**.
    *   **Jak używać?** Dopiero gdy zobaczysz parę **⚡ + ✔**, zaczynasz myśleć o wejściu w transakcję.

*   **Delta Bubbles (kolorowe bąbelki)**
    *   **Co to jest?** Uproszczony wskaźnik pokazujący, czy na danej świecy przeważała agresja kupujących (zielony bąbel) czy sprzedających (czerwony bąbel).
    *   **Po co to?** To dodatkowe **potwierdzenie siły kierunku**.
    *   **Jak używać?** Jeśli myślisz o kupnie, chcesz widzieć zielone bąble. Jeśli o sprzedaży - czerwone. Im większy bąbel, tym większa agresja.

*   **Bollinger Bands (BB) (niebieskie wstęgi)**
    *   **Co to jest?** Dwie linie, które pokazują, jak bardzo "rozciągnięta" jest cena w stosunku do swojej średniej.
    *   **Po co to?** Dają **kontekst zmienności**.
    *   **Jak używać?** Gdy wstęgi są wąskie, rynek "śpi". Gdy się rozszerzają, zaczyna się akcja. Cena przy górnej wstędze jest "droga", przy dolnej "tania". To tylko informacja, nie sygnał!

*   **Volume Profile (VP) (histogram z boku i 3 linie)**
    *   **Co to jest?** To **mapa wolumenu**. Pokazuje, na jakich **poziomach cenowych** (a nie w jakim czasie) było najwięcej handlu.
    *   **Po co to?** Pokazuje "uczciwą wartość" i strefy, które rynek uważa za ważne.
    *   **Jak używać?**
        *   **🔴 POC (Point of Control) - czerwona linia**: **Król wolumenu**. Poziom z największą ilością transakcji. Działa jak **magnes** na cenę.
        *   **🔵 VAH (Value Area High) - górna turkusowa linia**: Górna granica "strefy komfortu" rynku. Często działa jak **opór (sufit)**.
        *   **🔵 VAL (Value Area Low) - dolna turkusowa linia**: Dolna granica "strefy komfortu". Często działa jak **wsparcie (podłoga)**.

*   **Risk Management (Zarządzanie Ryzykiem)**
    *   **Co to jest?** Najważniejszy element! To Twój plan, ile możesz stracić na jednej transakcji.
    *   **Po co to?** Żeby nie wyzerować konta.
    *   **Jak używać?** Strategia **sugeruje** poziom **Stop Loss (SL)** - zlecenia, które zamyka transakcję z małą stratą. Musisz je ustawić **RĘCZNIE** u swojego brokera.

---

## 0) TradingView – pełny setup dla laika (krok po kroku)

### 🚀 Rejestracja i podstawowa konfiguracja

1. **Rejestracja konta**:
   * Wejdź na [tradingview.com](https://www.tradingview.com/)
   * Kliknij **"Sign Up"** (prawy górny róg)
   * Wpisz e-mail, hasło i username (lub zaloguj przez Google/Apple)
   * **WAŻNE**: Potwierdź adres e-mail (sprawdź spam!)

2. **Pierwsze logowanie**:
   * Po potwierdzeniu maila zaloguj się na [tradingview.com](https://www.tradingview.com/)
   * Wybierz plan: **darmowy wystarczy na start** (Basic = 0$/miesiąc)
   * Możesz pominąć ankietę o doświadczeniu

3. **Instalacja aplikacji** (opcjonalnie, ale warto):
   * **Desktop**: Na stronie głównej → "Download App" → wybierz Windows/Mac
   * **Mobile**: Google Play (Android) lub App Store (iOS) → szukaj "TradingView"
   * **Synchronizacja**: Po zalogowaniu wszystko się zsynchronizuje między urządzeniami

### 📊 Podstawy obsługi TradingView

4. **Pierwszy wykres**:
   * Na stronie głównej kliknij **"Chart"** (górny pasek menu)
   * W polu wyszukiwania wpisz symbol (np. "AAPL", "SPY", "TSLA")
   * Wybierz timeframe (M1, M5, M15, H1, D1) na górnym pasku
   * **Tip**: do strategii Fabio polecam M5-M15 na start

5. **Orientacja w interfejsie**:
   * **Lewy pasek**: narzędzia do rysowania linii, poziomów
   * **Górny pasek**: symbol, timeframe, ustawienia wykresu
   * **Prawy pasek**: lista obserwowanych, newsy, pomysły
   * **Dolny pasek**: tu będzie **Pine Editor** (kluczowy dla nas!)

### 🛠️ Wgrywanie strategii Fabio v8 (szczegółowo)

6. **Pobranie kodu strategii**:
   * Otwórz plik `fabio&bb&bubbles.pine` z folderu **v8** tego repozytorium
   * **Zaznacz CAŁY kod** (Ctrl+A) i **skopiuj** (Ctrl+C)
   * **Sprawdź**: kod powinien zaczynać się od `// @version=6`

7. **Otwarcie Pine Editor**:
   * W TradingView (wersja web/desktop) kliknij **"Pine Editor"** na dolnym pasku
   * Jeśli nie widzisz, kliknij trzy kropki "..." na dole → "Pine Editor"
   * **Mobile**: Pine Editor nie jest dostępny w aplikacji mobilnej!

8. **Wklejenie i dodanie strategii**:
   * W Pine Editor **usuń** domyślny kod (zazwyczaj jakiś przykład)
   * **Wklej** skopiowany kod strategii Fabio v8 (Ctrl+V)
   * Kliknij **"Add to Chart"** (niebieski przycisk, prawy górny róg editora)
   * **Sukces**: na wykresie pojawią się sygnały, wskaźniki i **Volume Profile**!

9. **Zapisywanie strategii**:
   * Kliknij **"Save"** w Pine Editor
   * Nadaj nazwę: **"Fabio v8 + VP"** (żeby odróżnić od innych wersji)
   * Teraz strategia jest zapisana w Twoim profilu

### 📱 Dostęp z telefonu

10. **Używanie na mobile**:
    * Otwórz aplikację TradingView na telefonie
    * Wybierz wykres i symbol
    * Kliknij **"Indicators"** (ikona wykresu na dole)
    * Przejdź do zakładki **"My Scripts"**
    * Znajdź **"Fabio v8 + VP"** i kliknij, żeby dodać do wykresu

### ⚙️ Pierwsze ustawienia

11. **Konfiguracja strategii**:
    * Na wykresie kliknij na nazwę strategii **"Fabio"** (góra wykresu)
    * Wybierz **"Settings"** (ikona koła zębatego)
    * **Zostawiaj domyślne wartości na start** - są dobrze dobrane!
    * **NOWOŚĆ v8**: możesz włączyć `vp_use_filter` dla dodatkowego filtra POC

12. **Ustawienia wykresu** (opcjonalnie):
    * Kliknij prawym na wykres → **"Settings"**
    * **Symbol**: ustaw "Extended Hours" na OFF (sesja regularna)
    * **Appearance**: wybierz motyw (ciemny polecam)
    * **Trading**: możesz włączyć "Show positions on chart"

### 🎯 Pierwsze kroki z strategią

13. **Testowanie na papierze**:
    * **NIE HANDLUJ NA PRAWDZIWYCH PIENIĄDZACH od razu!**
    * Używaj **Strategy Tester** (zakładka na dole) do sprawdzenia wyników
    * Obserwuj sygnały na żywo przez kilka dni
    * **NOWOŚĆ**: obserwuj jak cena reaguje na POC (czerwona linia) i VA (turkusowe)

14. **Najczęstsze problemy dla laików**:
    * **"Nie widzę sygnałów"**: sprawdź czy strategia jest włączona na wykresie
    * **"Za dużo/mało sygnałów"**: zmień `delta_min`, `vol_mult` lub włącz `vp_use_filter`
    * **"Nie widzę Volume Profile"**: sprawdź czy `show_vp = true` w ustawieniach
    * **"Nie działa na telefonie"**: strategia musi być najpierw wgrana przez przeglądarkę
    * **"Błąd w kodzie"**: sprawdź czy skopiowałeś CAŁY kod ze WSZYSTKIMI znaczkami

### 💡 Dodatkowe tipy dla początkujących (v8)

15. **Edukacja przed tradingiem**:
    * Przeczytaj CAŁY ten README (sekcje 5-11 szczególnie!)
    * Przetestuj strategię na danych historycznych
    * Zacznij od papierowego tradingu (symulacja)
    * **Poznaj Volume Profile**: obserwuj POC jako magnes cenowy

16. **Bezpieczeństwo**:
    * **Nigdy nie inwestuj więcej niż możesz stracić**
    * Zacznij od małych kwot (1-2% kapitału na trade)
    * Używaj stop-lossów (strategia ma je wbudowane)
    * Nie używaj dźwigni finansowej na początku
    * **Volume Profile to dodatkowy kontekst**, nie zastępuje analizy fundamentalnej

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

## 5) Logika sygnałów (Jak to wszystko działa razem?)

Myślenie strategii jest proste i logiczne. Szuka ona **zbiegu kilku sygnałów naraz**.

**Kiedy strategia myśli o KUPNIE (Long)?**

1.  **Kompas (VWAP)**: Cena musi być **powyżej** pomarańczowej linii (trend wzrostowy).
2.  **Lokalizacja (OB)**: Cena musi być **blisko** zielonego kółka (strefa popytu). Kupujemy "tanio".
3.  **Siła (Impuls ⚡)**: Musi pojawić się żółta błyskawica (wzrost zainteresowania).
4.  **Potwierdzenie (Follow-up ✔)**: Musi pojawić się niebieski ptaszek (potwierdzenie kierunku).
5.  **Agresja (Delta)**: Musi pojawić się zielony bąbel (przewaga kupujących).
6.  **Mapa (VP)**: (Opcjonalnie) Cena jest powyżej czerwonej linii POC (handlujemy po "silnej" stronie rynku).

**Dopiero gdy WSZYSTKIE te warunki są spełnione, na wykresie pojawia się zielona strzałka "KUP"**.

**Kiedy strategia myśli o SPRZEDAŻY (Short)?**

Analogicznie, ale na odwrót:
1.  **Kompas (VWAP)**: Cena **poniżej** pomarańczowej linii.
2.  **Lokalizacja (OB)**: Cena **blisko** czerwonego kółka (strefa podaży). Sprzedajemy "drogo".
3.  **Siła (Impuls ⚡)**: Jest żółta błyskawica.
4.  **Potwierdzenie (Follow-up ✔)**: Jest niebieski ptaszek w dół.
5.  **Agresja (Delta)**: Jest czerwony bąbel.
6.  **Mapa (VP)**: (Opcjonalnie) Cena jest poniżej czerwonej linii POC.

**A co ze Stop-Lossem (Twoje bezpieczeństwo)?**

*   **Początkowy SL**: Poziom SL jest **sugerowany i rysowany** na wykresie. Jeśli kupujesz przy zielonym kółku, sugerowany SL jest pod nim. Jeśli sprzedajesz przy czerwonym, SL jest nad nim. **Musisz go ustawić ręcznie u brokera!**
*   **Przesuwanie SL (Trailing)**: Jeśli transakcja idzie w dobrym kierunku i pojawi się kolejny duży wolumen, strategia **zaktualizuje sugerowany poziom SL** na wykresie, żeby pokazać, gdzie można by zabezpieczyć część zysku. **Tę zmianę również musisz wprowadzić ręcznie u brokera.**

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
* [ ] Zerknij na indeksy przed sesją na **futures Nasdaq** w trakcie na ineks nasdaq oraz QQQ – czy kierunek i momentum wspierają Twój trade?

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