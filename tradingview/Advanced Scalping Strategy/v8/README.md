# Starter Guide – Fabio Strategy + order-flow-delta-tracker-by-W

Ten zestaw to DWIE rzeczy pracujące razem:
1. Strategia `fabio.pine` – silnik sygnałów (order blocks + impuls wolumenowy + follow‑up + delta + opcjonalnie filtr CVD / VP).
2. Wskaźnik `order-flow-delta-tracker-by-W.pine` – warstwa kontekstu (CVD, Rolling CumΔ, Immediate Δ, divergence, absorpcja, spike).

## 🔍 Co to w ogóle robi?
Łączy STRUCTURĘ (Order Blocks / VWAP / Volume Profile) z PRZEPŁYWEM ZLECEŃ (delta, CVD, momentum) żeby:
- odsiać przypadkowe wybicia bez potwierdzenia,
- nie wchodzić przeciw świeżo dominującemu przepływowi,
- szybciej zauważyć wyczerpanie ruchu (bearish divergence / absorpcja),
- mieć powtarzalną checklistę zanim klikniesz kupuj.

## 🎯 Dla kogo?
- Dzień / intra-day trader (giełda, indeksy, futures, płynne krypto).
- Osoba, która zna podstawy świec i wsparcia/oporu, ale gubi się w chaosie „tysiąca sygnałów”.
- Kto chce JEDEN proces, zamiast mieszać 5 losowych wskaźników.

## 🚫 Czego to NIE robi
- Nie przewiduje rynku „z wyprzedzeniem 100%”.
- Nie jest systemem full-auto / botem.
- Nie „drukuje” sygnałów w konsolidacji jeśli brak jakości (to celowe). 
- Nie zastąpi zarządzania ryzykiem (SL / wielkość pozycji MUSISZ ustawić sam).

## ✅ Co dostajesz w praktyce
| Problem początkującego | Jak tu jest rozwiązany |
|------------------------|------------------------|
| Za dużo wejść / FOMO | Wielowarstwowa filtracja (OB + Impuls + Follow‑up + Delta + CVD trend) |
| Nie wiesz kiedy odpuścić | Checklista + brak follow‑up = brak wejścia |
| Wchodzisz w końcówkę ruchu | Divergence + brak świeżego impulsu ostrzegają |
| Zbyt późne wejścia | Immediate Δ pomaga zobaczyć wczesną agresję |

## 🧱 Minimalne wymagania żeby zacząć
- Wiesz co to: świeca, korpus, high/low, VWAP (wizualnie).
- Akceptujesz SL jako koszt (nie przesuwasz w panice).
- Masz instrument z realnym wolumenem (mikro altcoiny odpadają).

## 🧬 Jak to się łączy (mentalna mapka)

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   STRUKTURA     │    │   ORDER FLOW    │    │     IMPULS      │
│                 │    │                 │    │                 │
│ • Order Blocks  │    │ • CVD trend     │    │ • Wolumen ⚡    │
│ • VWAP bias     │───▶│ • Rolling CumΔ  │◀───│ • Follow-up ✔  │
│ • Volume Profile│    │ • Immediate Δ   │    │ • Delta bubbles │
│ • POC/VAH/VAL   │    │ • Divergence    │    │                 │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         └───────────────────────┼───────────────────────┘
                                 ▼
                    ┌─────────────────────────┐
                    │   KANDYDAT NA WEJŚCIE   │
                    │                         │
                    │ ✓ Świeża strefa (OB)    │
                    │ ✓ Impulse + Follow-up   │
                    │ ✓ CVD nie przeciw       │
                    │ ✓ Brak divergence       │
                    │ ✓ Delta bubble ≥ próg   │
                    └─────────────────────────┘
                                 │
                    ┌────────────┴────────────┐
                    ▼                         ▼
              [ 🟢 TRADE ]              [ 🔴 SKIP ]
```

## 🏁 Najkrótsza ścieżka startowa (30–40 minut)
1. Przeczytaj tę stronę do sekcji „Kolejność czytania”.
2. Wgraj oba skrypty na wykres (M5 / M15) i NIE zmieniaj 90% ustawień.
3. Oglądaj 20–30 świec: zaznacz ręcznie gdzie pojawiłyby się wszystkie warstwy (OB → ⚡ → ✔ → bubble ≥ próg → brak divergence).
4. Zapisz 5 miejsc gdzie byś wszedł – i 5 gdzie NIE („brak follow‑up”, „przeciw CVD”).
5. Dopiero potem drobny tuning `delta_min`.

---
Poniżej: indeks plików i dalsza struktura.

## 📂 Zawartość katalogu
| Plik | Typ | Cel | Czy czytać od razu? |
|------|-----|-----|---------------------|
| `fabio.pine` | Strategia | Generuje realne sygnały LONG/SHORT | Po lekturze podstaw |
| `order-flow-delta-tracker-by-W.pine` | Wskaźnik | Analiza CVD / Rolling / Immediate Δ / divergence | Szybkie przejrzenie parametrów |
| `SYNC-GUIDE.md` | Przewodnik | Spina Fabio + wskaźnik w proces decyzyjny | TAK (1) |
| `README-fabio.md` | Dokumentacja | Szczegóły logiki strategii Fabio i parametry | TAK (2) |
| `README-ORDER-FLOW-DELTA-TRACKER-by-W.md` | Dokumentacja | Głębszy opis komponentów order-flow-delta-tracker-by-W | TAK (3) |
| `README.md` | Ten plik | Indeks / mapa / kolejność | Start |

## ✅ Kolejność czytania (minimalna ścieżka)
1. Ten plik (mapa) – 1 min.
2. `SYNC-GUIDE.md` – zrozumienie workflow i checklisty wejść.
3. `README-fabio.md` – jak powstaje sygnał strategii.
4. `README-ORDER-FLOW-DELTA-TRACKER-by-W.md` – interpretacja linii i sygnałów order flow.
5. Dopiero potem: fine‑tuning parametrów w obu skryptach.

## 🔗 Zależności & Powiązania
| Element | Korzysta z | Uwagi |
|---------|-----------|-------|
| Fabio (`fabio.pine`) | CVD smoothing (powinien = wskaźnik), Order Blocks, Delta prog | Delta / CVD logika musi być spójna z ustawieniami wskaźnika dla poprawnej filtracji |
| order-flow-delta-tracker-by-W | Surowa delta (kolor świecy + body/ticks), EMA (Immediate Δ) | Nie generuje wejść – kontekst / filtrowanie i divergence |
| Rolling CumΔ | Różnica kumulacji w oknie | Szybciej reaguje niż pełny CVD (przełączenia balansu) |
| Immediate Δ | EMA z delty * skala | Sygnał świeżego agresywnego napływu – opcjonalny |
| Divergence (bearish) | Cena HH vs brak HH w CVD/Rolling | Ostrzega o potencjalnym wyczerpaniu ruchu |

## 🧪 Minimalny Quick Start (praktyka)
1. Otwórz wykres instrumentu z przyzwoitym wolumenem (np. indeks / futures / główny krypto).
2. Dodaj wskaźnik `order-flow-delta-tracker-by-W` – zostaw domyślne (Rolling 35, Immediate Δ opcjonalnie ON jeśli chcesz dynamicznych impulsów).
3. Dodaj `fabio.pine` – ustaw `cvd_smooth` tak samo jak w wskaźniku.
4. Ustal próg `delta_min` patrząc na typowe słupki: wybierz wartość odcinającą ~70% mniejszych bąbli.
5. Czekaj na: świeży Order Block → impuls (⚡) → follow‑up (✔) → brak przeciwnej divergence → zgodność CVD trendu.
6. Wejście testowe ma mieć logiczny SL (pod OB / nad OB) i R:R ≥ 1.5.

## 🎯 Kiedy który plik?
| Pytanie | Gdzie szukać |
|---------|--------------|
| Jak wygląda pełna formuła wejścia? | `README-fabio.md` (sekcja Formuła Wejść) |
| Co oznacza żółta linia? | `README-ORDER-FLOW-DELTA-TRACKER-by-W.md` (Immediate Δ) |
| Jak łączyć oba w proces decyzyjny? | `SYNC-GUIDE.md` |
| Dlaczego Rolling rośnie mimo czerwonych świec? | `README-ORDER-FLOW-DELTA-TRACKER-by-W.md` (FAQ / Rolling vs CVD) |
| Gdzie zmieniam agresję filtrowania deltą? | `fabio.pine` (`delta_min`) |
| Jak ograniczyć szum divergence? | Parametr `div_window` w wskaźniku + wyższy TF |

## 🛡 Typowe pułapki
- Niespójny `cvd_smooth` między strategią a wskaźnikiem → fałszywe blokady wejść.
- Zbyt niski `delta_min` → spam bąbli / przeładowanie sygnałów.
- Ignorowanie braku follow‑up po impulsie → wejścia w jednorazowe wybicia.
- Próba gry przeciw aktywnemu CVD trendowi bez strukturalnej divergence.

## 🔧 Kolejność strojenia parametrów (po 10–20 testowych tradach)
1. `delta_min` – usuń szum.
2. `vol_mult` – dopasuj definicję impulsu.
3. `ob_strength` / `ob_max_age` – jakość stref reakcji.
4. `rolling_len` (jeśli potrzebujesz wolniejszej/faster reakcji) – ostrożnie.
5. Dopiero na końcu: włącz/skaluj Immediate Δ jeśli chcesz dodatkową selekcję.

## 🗺 Decyzyjny Checkpoint (mentalny skrót)
VWAP bias?  OB świeży?  ⚡ + ✔?  CVD zgodne?  Brak bearish divergence?  Bąbel ≥ próg?  R:R ≥1.5?  Plan wyjścia?  Jeśli NIE → OMIJASZ.

## 📌 Gdzie dalej?
- Dodanie bullish divergence (na razie tylko bearish) – TODO jeśli potrzebne.
- Automatyczny dobór `delta_min` (statystycznie) – potencjalna przyszła funkcja.
- Skrócony cheat‑sheet w jednym PNG / PDF (można dodać później).

---
Jeżeli ten indeks czegoś nie odpowiada – dopisz pytanie w issues / notatce i rozbudujemy.
