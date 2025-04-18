# Advanced Scalping Strategy v2

## Overview

This enhanced version of the original High-Low Cloud Trend strategy introduces several significant improvements designed to:

1. Improve signal accuracy
2. Reduce false entries
3. Better adapt to higher timeframes (1H, 4H)
4. Add advanced entry/exit mechanics
5. Handle both trending and ranging markets

## Key Enhancements

### 1. Volatility-Adaptive Lookback Period

**Problem:** Fixed lookback periods don't adapt to changing market conditions. What works on low volatility days causes excessive noise on high volatility days.

**Solution:**
- Dynamically adjusts the lookback period based on current market volatility
- Uses normalized ATR to scale lookback between user-defined min and max values
- Shorter lookback in calm markets for faster signals, longer lookback in volatile conditions to filter noise
- Includes volatility smoothing to prevent rapid changes
- Self-calibrating min/max volatility observations

### 2. Multi-Timeframe Confirmation

**Problem:** Lower timeframe signals often get stopped out when trading against higher timeframe trends.

**Solution:**
- Integrates higher timeframe trend analysis (60m, 240m, Daily options)
- "Strict" mode option that only allows trades in the direction of higher timeframe trend
- Percentage-based trend determination for more reliable signals
- Visualization of higher timeframe trend status in the info panel

### 3. Order Block Detection

**Problem:** Entry timing lacks precision, missing key supply/demand zones.

**Solution:**
- Identifies significant price rejection areas as potential order blocks
- Advanced detection criteria including:
  - Minimum candle body percentage
  - Strength factor based on price movement
  - Age requirements (minimum/maximum bars)
- Visual highlighting of active order blocks on the chart
- Integration with entry logic to prefer entries near order blocks

### 4. Dynamic RSI Thresholds

**Problem:** Fixed RSI thresholds (70/30) work poorly in strong trends.

**Solution:**
- Intelligently adjusts RSI thresholds based on trend direction and strength
- In uptrends: Lowers oversold threshold to enter earlier
- In downtrends: Raises oversold threshold to avoid "catching falling knives"
- Customizable sensitivity to trend strength
- Shows adaptive threshold values in the info panel

### 5. Advanced Trailing Exit System

**Problem:** Fixed take-profit levels often leave profit on the table in trending moves.

**Solution:**
- Implements ATR-based trailing stop mechanism
- Configurable trigger level before trailing begins
- Progressive trailing step size to follow the trend
- Properly persisted trailing levels across bars
- Clear visualization of current trailing stop level
- Optional usage alongside traditional fixed TP/SL

## Enhanced Visualization

- Improved diagnostic information panels showing:
  - Current filter status with details on why entries are allowed/blocked
  - Multi-timeframe trend information
  - Order block detection status
  - Current dynamic lookback period and volatility levels
  - Trailing stop status and values

## Usage Tips

### For Scalping (1-5m charts)
- Enable "Strict MTF" mode to filter out counter-trend signals
- Use volatility-adaptive lookback with min=10, max=30
- Consider using trailing exits with small trigger values (0.5-1.0 ATR)
- Primarily use cloud retest signals

### For Swing Trading (1H, 4H charts)
- Increase lookback ranges (min=20, max=60)
- Use longer MTF confirmation (Daily)
- Prioritize order blocks by setting higher minimum age
- Use more aggressive trailing with higher trigger values (1.5-2.0 ATR)
- Consider enabling mean reversion and cloud cross signals

## Parameters Comparison

| Feature | Original Strategy | Enhanced v2 |
|---------|------------------|-------------|
| Lookback Period | Fixed (35) | Adaptive (15-50 based on volatility) |
| MTF Analysis | None | 60m/240m/Daily trend confirmation |
| Entry Filters | RSI, MACD, Volume | + Order blocks, MTF trend |
| RSI Thresholds | Fixed (30/70) | Dynamic based on trend |
| Stop Loss/TP | Fixed ATR-based | Fixed + ATR-based trailing option |
| Debug Info | Basic | Comprehensive with detailed status |