## Fabio + order-flow-delta-tracker-by-W Sync Guide (dla początkujących)

Ten przewodnik pokazuje jak łączyć strategię **Fabio** (Order Blocks + Impuls + Delta + VP) ze wskaźnikiem **order-flow-delta-tracker-by-W** (CVD + Rolling/Immediate Δ + divergence), aby filtrować słabe setupy i grać tylko konfluencje.

### 📚 Kolejność czytania
1. Poniższy blok pojęć – 1 minuta.
2. "Quick Start" – uruchom i zobacz.
3. "Najlepsze Combinowane Setupy" – trzy bazowe schematy.
4. Recommended Settings – dopasuj progi.
5. Trading Workflow – codzienna checklista.
6. Pro Tips + Common Mistakes.

### 🧠 Pojęcia w 1 zdaniu
- **CVD (fiolet)**: całkowita przewaga kupujących/sprzedających od początku sesji.
- **Rolling CumΔ (niebieska)**: lokalny bilans ostatniego okna – szybciej reaguje na zmianę.
- **Immediate Δ (żółta)**: świeży impuls (EMA z delty) – opcjonalne turbo‑potwierdzenie.
- **Impulse (⚡)**: świeca z wolumenem > SMA * mult i odpowiednio dużym korpusem.
- **Follow‑up (✔)**: kolejna świeca w tym samym kierunku – potwierdza, że impuls nie był jednorazowy.
- **OB (Order Block)**: świeca o dużym zakresie – potencjalna strefa reakcji (popyt/podaż).
- **POC / VAH / VAL**: najaktywniejsza cena (magnes) i granice strefy wartości z Volume Profile.
- **Δ Divergence**: cena robi HH, przepływ (rolling/CVD) nie – potencjalne wyczerpanie.

### ⚡ Quick Start
1. Dodaj Fabio i order-flow-delta-tracker-by-W na tym samym interwale (np. M5).
2. `cvd_smooth` ustaw tak samo w obu (np. 3). W order-flow-delta-tracker-by-W: Delta sign = Body+Tick.
3. Nic nie stroisz prócz ewentualnego `delta_min` (dopasuj do wolumenu instrumentu).
4. Czekasz: świeży OB + impuls ⚡ + follow‑up ✔ + brak spadku CVD.
5. Wejście testowe tylko jeśli bąbel delty ≥ `delta_min` i CVD filter nie blokuje.

---

## 🔥 Najlepsze Combinowane Setupy

### **SETUP A: Trend Continuation** 
**order-flow-delta-tracker-by-W pokazuje:**
- CVD w trendzie wzrostowym (fioletowa linia ↗️)
- Serie zielonych słupków delty 🟢

**Fabio potwierdza:**
- Cena nad VWAP (bias bullish) 
- Impulse volume przy Bull Order Block
- CVD filter pozwala na LONG (w tle)

**Akcja:** LONG na następnej świecy

---

### **SETUP B: Reversal na Divergence**
**order-flow-delta-tracker-by-W pokazuje:**
- "Δ Divergence" signal 🔴
- Absorption triangle 🔻
- CVD nie potwierdza nowych szczytów

**Fabio potwierdza:**  
- Cena przy Bear Order Block
- Impulse down + follow-up
- CVD filter pozwala na SHORT (w tle)

**Akcja:** SHORT na breakout

---

### **SETUP C: Range Break**
**order-flow-delta-tracker-by-W pokazuje:**
- Delta spike (fioletowy krzyżyk) ❌
- CVD breakout z konsolidacji
- Volume spike

**Fabio potwierdza:**
- Break ponad/poniżej POC (czerwona linia)
- Bollinger Bands expansion
- Order Block jako punkt odbicia

**Akcja:** Momentum play w kierunku breakout

---

## ⚙️ Recommended Settings

### **Fabio ustawienia:**
```
CVD smoothing: 3 (match order-flow-delta-tracker-by-W)
Use CVD filter: true  
Delta threshold: 5000
OB Range Multiplier: 1.5
Volume Spike Mult: 2.0
```

### **order-flow-delta-tracker-by-W ustawienia (aktualne):**
```
Delta sign mode: "Body+Tick"
Rolling CumDelta length: 35 (lokalny bilans)
Immediate Δ (opcjonalnie): EMA len 2, scale 5 – szybki impuls
CVD smoothing: 3 (match Fabio)
Divergence lookback: 10
```

---

## 🎮 Trading Workflow

### **Pre-Market (5 min przed otwarciem):**
1. Sprawdź overnight CVD na order-flow-delta-tracker-by-W
2. Zidentyfikuj kluczowe Order Blocks na Fabio  
3. Ustaw alerty na POC levels

### **Podczas sesji:**
1. **Primary**: Trend CVD (fiolet) stabilny / brak świeżej divergence
2. **Secondary**: Fabio: impuls ⚡ + follow‑up ✔ w świeżym OB
3. **Entry**: Zgodność VWAP bias + CVD filter + (opcjonalnie) rosnąca Immediate Δ
4. **Exit**: Przeciwna divergence order-flow-delta-tracker-by-W / odwrót CVD / trailing SL Fabio / brak follow‑up kontynuacji

### **Risk Management:**
- Max 2-3 pozycje dziennie
- Stop Loss wg Fabio (Order Blocks + ATR)
- Take Profit przy przeciwnej divergence order-flow-delta-tracker-by-W

---

## 💡 Pro Tips

### **Timeframe Strategy:**
- **order-flow-delta-tracker-by-W**: M15 dla głównego trendu CVD
- **Fabio**: M5 dla precyzyjnych wejść
- **Sync**: Oba na tym samym TF dla scalping

### **Market Context:**
- **Trending market**: CVD trend + sekwencja zielonych delt + rosnąca Immediate Δ
- **Range market**: Mean reversion do POC, OB reaktywne + brak silnej Immediate Δ
- **High volatility**: Szukaj absorption + divergence przed kontrą

### **Common Mistakes:**
❌ Trading przeciw trendowi CVD  
❌ Ignorowanie Order Blocks w Fabio
❌ Zbyt małe `delta_min` (szum)
❌ Brak synchronizacji timeframes

✅ **Winning Combo**: CVD trend + OB confirmation + Volume spike + Follow‑up + Delta bubble

---

**Rezultat:** Znacznie wyższa precyzja sygnałów dzięki wielowarstwowej analizie! 🎯📈

---

## 🛠 Troubleshooting (gdy coś nie działa)
| Problem | Sprawdź | Korekta |
|---------|---------|---------|
| Brak strzałek (wejść) | Czy cena near OB? delta > prog? | Zmniejsz `delta_min`, zwiększ `ob_max_age` |
| Za dużo sygnałów | VP filtr OFF? słabe impulsy? | Włącz `vp_use_filter`, podnieś `vol_mult` |
| Impuls bez follow‑up | Druga świeca nie potwierdziła | Poczekaj na ✔ albo zrezygnuj |
| Divergence zbyt częsta | Zbyt niski TF / niski wolumen | Przejdź na wyższy TF lub zwiększ `div_window` w order-flow-delta-tracker-by-W |
| CVD kontra sygnał Fabio | Inny smoothing / TF | Ujednolić `cvd_smooth` i TF |
| Trailing SL brak ruchu | Brak volume spike | Obniż `trail_vol_mult` lub poczekaj |

**Mental quick check:** VWAP? OB? ⚡ + ✔? CVD zgodne? Bąbel ≥ próg? R:R ≥1.5? Plan wyjścia? Jeśli nie – skip.
