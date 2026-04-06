# EMA Trading Bot

An automated MetaTrader 5 trading bot using EMA crossover, RSI, and SuperTrend indicators for forex and gold trading.

## Features

- **EMA Crossover Signals**: 9 and 21 period EMA analysis
- **RSI Filter**: 14-period RSI for momentum confirmation
- **SuperTrend Confirmation**: ATR-based trend direction filter
- **ATR-Based Risk Management**: Dynamic stop loss using Average True Range
- **Trailing Stop + Breakeven**: Automatic position management after profit threshold
- **Multi-Timeframe Mode**: Optional unanimous consensus across 6 timeframes (1M, 5M, 15M, 30M, 1H, 1D)

## How the Bot Works

The bot runs in a continuous loop (every 1 second) and performs the following on each cycle:

1. Fetches the latest OHLCV candle data from MT5
2. Calculates RSI(14), EMA(9), EMA(21), and ATR(14)
3. Evaluates entry conditions against the latest closed candle
4. If a signal is found and no position is open, executes a market order
5. Monitors open positions and applies breakeven + trailing stop logic

---

## Entry Conditions

### BUY Signal
All 4 conditions must be true on the current candle:
- RSI(14) > 50
- EMA(9) > EMA(21)
- Current candle is **Green** (close > open)
- Candle **Low** is above EMA(9)

### SELL Signal
All 4 conditions must be true on the current candle:
- RSI(14) < 50
- EMA(9) < EMA(21)
- Current candle is **Red** (close < open)
- Candle **High** is below EMA(9)

> Only one position per symbol is allowed at a time.

---

## Exit Conditions

### Stop Loss
- Placed at **2.0x ATR** distance from entry price
- BUY: `entry - (2.0 × ATR)`
- SELL: `entry + (2.0 × ATR)`

### Take Profit
- Fixed at **$10 USD** profit (converted to price distance based on volume and tick value)

### Breakeven
- Once the trade reaches **+$5 USD** profit, stop loss is moved to entry price (breakeven)

### Trailing Stop
- After breakeven is set, a trailing stop trails **$2 USD** behind the current price
- BUY: `current bid - trailing distance` (only moves up)
- SELL: `current ask + trailing distance` (only moves down)

---

## Multi-Timeframe Mode (`triple_strategy.py`)

An alternative stricter mode that requires **unanimous agreement across all 6 timeframes** (1M, 5M, 15M, 30M, 1H, 1D):

### BUY (all 6 TFs must agree):
- RSI(14) > 50
- SuperTrend direction = Bullish
- EMA(9) > EMA(21)

### SELL (all 6 TFs must agree):
- RSI(14) < 40
- SuperTrend direction = Bearish
- EMA(9) < EMA(21)

Stop loss uses **1.5x ATR(14)** from the 15M timeframe. Take profit is set at **2:1 risk-reward ratio**.

---

## Setup Instructions

### 1. Prerequisites
- MetaTrader 5 installed and running
- Python 3.8 or higher
- Active MT5 account

### 2. Installation
```bash
pip install -r requirements.txt
```

### 3. Configuration
Create a `.env` file with your MT5 credentials:
```
MT5_LOGIN=your_login
MT5_PASSWORD=your_password
MT5_SERVER=your_server
MT5_PATH=C:\Program Files\MetaTrader 5\terminal64.exe
```

### 4. Run the Bot
```bash
python enhanced_strategy.py
```

---

## File Structure

```
Trading_forex/
├── enhanced_strategy.py        # Main bot (EMA + RSI + ATR strategy)
├── trade_backend/
│   ├── triple_strategy.py      # Multi-timeframe consensus strategy
│   ├── mt5_api_bridge.py       # MT5 API bridge
│   └── run_bot.py              # Bot runner
├── requirements.txt
├── .env                        # MT5 credentials (not committed)
└── README.md
```

## Trading Symbols

Configured for:
- **XAUUSD** (Gold)
- **EURUSD** (Euro/Dollar)

## Disclaimer

This bot is for educational and research purposes. Trading forex and CFDs involves significant risk. Always test on a demo account before live trading.
