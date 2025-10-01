# Order Flow Delta Tracker WOJU++ 📊 (aktualizacja v8 + "v9 preview" zmian)

## 🎯 Co to jest?

**Order Flow Delta Tracker WOJU++** to zaawansowany wskaźnik do analizy przepływu zleceń (order flow) na rynku. Pomaga zidentyfikować przewagę kupujących lub sprzedających w czasie rzeczywistym i wykryć potencjalne punkty zwrotne na rynku.

---

## 🏆 Kluczowe Funkcje WOJU++

- **Pine Version v6** - Najnowsza technologia TradingView
- **2 metody obliczania delty** - CandleColor + Body+Tick (heurystyka agresji)
- **CVD z resetem dziennym** (fiolet) - Świeży start każdej sesji handlowej  
- **Rolling CumDelta (lokalna)** (niebieska) - Bilans ostatniego okna (domyślnie 35 barów)
- **Immediate Δ (impuls)** (żółta) - Bieżąca agresja (EMA z delty * skala + opcjonalny auto‑scale)
- **Pełna personalizacja** - Parametry długości, skali, smoothing
- **Inteligentne wygładzanie** - SMA dla CVD + EMA dla impulsu
- **Zaawansowane divergencje (bearish)** - Price HH vs (Rolling lub CVD) LH

---

## 📋 Główne Komponenty

### 1. **Delta (Δ)** - Słupki zielone/czerwone
- **Zielone słupki**: Przewaga kupujących (volume kupna > volume sprzedaży)
- **Czerwone słupki**: Przewaga sprzedających
- **Wysokość słupka**: Siła przewagi

### 2. **CVD (Cumulative Volume Delta)** - Fioletowa linia
- **Trendy wzrostowe**: Więcej kupujących w długim okresie
- **Trendy spadkowe**: Więcej sprzedających w długim okresie
- **Reset dzienny**: Nowy start każdego dnia sesyjnego

### 3. **Rolling CumDelta (Local CumΔ)** - Niebieska linia
- Skumulowana delta z określonego okna: `rolling = cum(delta) - cum(delta)[length]`
- Domyślne okno: **35** (wcześniej 50). Krótsze = szybsza reakcja, więcej szumu.
- Interpretacja: jeśli rośnie mimo czerwonych świec, bieżąca presja sprzedaży jest słabsza niż ta, która właśnie „wypadła” z okna.

### 4. **Immediate Δ (Impuls)** - Żółta linia
- Definicja: `EMA(delta, immediate_len)` * `scale` (domyślnie len=2, scale=5)
- Cel: wyróżnić świeże krótkoterminowe uderzenia agresji, które mogą poprzedzać zmianę kierunku lub kontynuację.
- Opcja: Auto‑scale – normalizuje przez średnią z |EMA(delta)| z wybranego lookbacku, utrzymując porównywalną amplitudę w różnych sesjach.
- Użycie: gwałtowny skok żółtej przy płaskiej niebieskiej = nowy impuls; żółta wygasa przy nadal wysokiej niebieskiej = wyczerpywanie.

### 5. **Sygnały Specjalne**
- 🔻 **Absorption (Abs)**: Pomarańczowy trójkąt - duży volume przy małym ruchu ceny
- ❌ **Delta Spike**: Fioletowy krzyżyk - nietypowo duża delta
- 🔴 **Δ Divergence**: Bearish divergence między ceną a deltą

---

## ⚙️ Konfiguracja Wskaźnika

### **Podstawowe Ustawienia (aktualne):**
```
Delta sign mode: "CandleColor" (dla początkujących)
Rolling CumDelta length: 35   # lokalny bilans (zmień 20–60 w zależności od TF)
Immediate Δ EMA length: 2     # impulsy
Immediate Δ scale factor: 5   # wizualne powiększenie; 1 = surowa wartość EMA
Immediate Δ auto scale: OFF   # włącz gdy instrumenty o różnych wolumenach
CVD smoothing: 0–3 (opcjonalnie)
Divergence lookback: 10
```

### **Zalecane Ustawienia dla Różnych Stylów:**

#### 🚀 **Scalping (M1-M5)**
- Rolling length: `25-40`
- Immediate scale: `3-6`
- CVD smoothing: `2-4`
- Divergence window: `5-8`

#### 📈 **Day Trading (M15-H1)**  
- Rolling length: `35-80`
- Immediate scale: `4-8`
- CVD smoothing: `0-3`
- Divergence window: `10-15`

#### 📊 **Swing Trading (H4-D1)**
- Rolling length: `100-200`
- Immediate len: `2-3`, scale mniejszy (1-3) aby uniknąć dominacji
- CVD smoothing: `5-8`
- Divergence window: `15-20`

---

## 🎮 Jak Grać - Praktyczny Przewodnik

### **SETUP 1: Trend Following z CVD** ⬆️

**Sygnał BUY:**
1. CVD (fioletowa linia) w trendzie wzrostowym ↗️
2. Pojawia się seria zielonych słupków delty 🟢
3. Rolling CumDelta (niebieska) > 0 i rośnie; Immediate Δ (żółta) potwierdza świeży impuls (opcjonalnie)

**Wejście:** Na kolejnej zielonej świecy po sygnale
**Stop Loss:** Pod ostatnim lokalnym minimum
**Take Profit:** 1:2 lub 1:3 risk/reward

---

### **SETUP 2: Reversal na Divergence** 🔄

**Sygnał SELL:**
1. Cena robi nowy szczyt (Higher High) 📈
2. Pojawia się czerwony znacznik "Δ Divergence" ⚠️
3. CVD lub Rolling Delta nie potwierdza nowego szczytu; brak świeżego żółtego impulsu (żółta płaska / opada)

**Wejście:** Na następnej czerwonej świecy po divergence
**Stop Loss:** Nad nowym szczytem ceny
**Take Profit:** Do najbliższego wsparcia

---

### **SETUP 3: Absorption Play** 🛡️

**Sygnał REVERSAL:**
1. Pojawia się pomarańczowy trójkąt "Abs" 🔻
2. Duży volume ale mały ruch ceny (świeca z długimi cieniami)
3. Delta spike (fioletowy krzyżyk) w tym samym obszarze

**Strategia:** Graj w kierunku przeciwnym do obecnego trendu
**Stop:** Tuż za absorption zone
**Target:** 1:1 lub 1:2 RR

---

## 💡 Pro Tips & Zaawansowane Techniki

### **1. Multi-Timeframe Analysis**
- Używaj wyższego timeframe dla głównego trendu CVD
- Wejścia na niższym TF przy potwierdzeniu delty

### **2. Kombinuj z Price Action**
- Szukaj absorption przy kluczowych poziomach S/R
- Divergence działa najlepiej przy strukturalnych ekstremach

### **3. Volume Context**
- Delta spike podczas niskiego volume = słaby sygnał
- Absorption przy wysokim volume = silniejszy sygnał

### **4. Sesje handlowe**
- CVD resetuje się codziennie - obserwuj pierwsze godziny sesji
- Najlepsze sygnały podczas wysokiej aktywności (London/NY overlap)

---

## ⚠️ Ważne Zasady Bezpieczeństwa

### **NIE HANDLUJ gdy:**
- ❌ Volume bardzo niski (poza sesją)
- ❌ Wysokie spready/commission
- ❌ Ważne newsy za 15-30 min
- ❌ Brak wyraźnego sygnału

### **ZAWSZE:**
- ✅ Używaj Stop Loss
- ✅ Testuj na demo przed live
- ✅ Maksymalnie 1-3% kapitału na trade
- ✅ Prowadź trading journal

---

## 🔧 Częste Problemy i Rozwiązania

**Problem:** "Za dużo fałszywych sygnałów"
**Rozwiązanie:** Zwiększ CVD smoothing (2-4), wydłuż rolling_len, włącz auto scale dla żółtej, filtruj tylko gdy Immediate Δ i rolling mają ten sam kierunek.

**Problem:** "Divergence nie działa"
**Rozwiązanie:** Sprawdź czy jesteś przy ważnym S/R, zwiększ divergence window

**Problem:** "Nie widzę wyraźnych trendów CVD"
**Rozwiązanie:** Przełącz na "Body+Tick" mode; jeśli nadal płasko – instrument o niskiej zmienności, skróć rolling_len lub obniż immediate_scale.

---

## 📚 Dodatkowe Materiały

### **Polecane Kombinacje z innymi wskaźnikami:**
- VWAP (dla kontekstu trendu)
- Volume Profile (dla poziomów S/R)
- RSI (dla overbought/oversold)
- Bollinger Bands (dla volatility context)

### **Najlepsze pary/instrumenty:**
- Forex majors (EUR/USD, GBP/USD)
- Indeksy (SPY, QQQ, /ES)
- Crypto (BTC/USD, ETH/USD)
- Akcje o wysokiej płynności

---

## 🎓 Przykładowy Trading Plan

### **Przygotowanie (przed sesją):**
1. Sprawdź kluczowe poziomy S/R na wyższym TF
2. Ustaw alerty na główne divergence
3. Przygotuj poziomy SL/TP

### **Podczas sesji:**
1. Monitoruj CVD trend na głównym TF
2. Szukaj setupów na niższym TF
3. Wykonuj max 3-5 transakcji dziennie

### **Po sesji:**
1. Zapisz screenshoty dobrych/złych tradów
2. Analizuj skuteczność sygnałów
3. Dostosuj ustawienia jeśli potrzeba

---

*Pamiętaj: Ten wskaźnik to narzędzie, nie magiczna różdżka. Sukces zależy od dyscypliny, zarządzania ryzykiem i praktyki!* 🎯

**Autor:** WOJU++
**Wersja:** 1.1 (Rolling 35 + Immediate Δ)
**Data:** 2025

---
### 🔄 Log zmian (v1.1)
- Rolling CumDelta domyślnie 50 → 35.
- Dodano linię Immediate Δ (EMA z delty) + skala i auto‑scale.
- Zaktualizowano rekomendacje parametrów i setupy.
- Doprecyzowano interpretację wzrostu rolling przy czerwonych słupkach.
