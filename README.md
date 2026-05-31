# Williams %R Alert — MQL4 Script

A MetaTrader 4 script that monitors the **Williams Percent Range (%R)** oscillator via `iWPR()` each minute cycle, tracks the indicator's value against a persistent `PrevWR` global to detect zone entry and exit transitions, and fires four distinct labeled alerts — **Entered Overbought**, **Exited Overbought**, **Entered Oversold**, **Exited Oversold** — using strict prior-value comparison logic that fires only on the cycle a transition occurs, not on every cycle the indicator remains within a zone.

---

## Overview

Williams %R, developed by Larry Williams and described in his 1979 book *How I Made One Million Dollars Last Year Trading Commodities*, is a momentum oscillator that measures the current closing price as a percentage of the highest high over the lookback period — scaled inverted from 0 to −100. A reading near 0 indicates price is closing near the top of its recent range (overbought); a reading near −100 indicates price is closing near the bottom of its recent range (oversold). Unlike RSI — which uses an exponentially weighted ratio of gains to losses — Williams %R is a pure price-position measure that responds immediately and linearly to each new bar, making it highly reactive to short-term reversals. The overbought zone (above `OverboughtLevel`, default `−20`) and oversold zone (below `OversoldLevel`, default `−80`) are the critical thresholds where reversals are statistically most probable. This script monitors both zone entries — when price enters an extreme zone for the first time — and zone exits — when price recovers back through the threshold — providing a complete picture of the oscillator's zone behavior.

---

## Features

- **Four-state zone transition detection** using `PrevWR` comparison:
  - `PrevWR > OverboughtLevel && currentWR <= OverboughtLevel` → **Entered Overbought Zone**
  - `PrevWR < OverboughtLevel && currentWR >= OverboughtLevel` → **Exited Overbought Zone**
  - `PrevWR > OversoldLevel && currentWR <= OversoldLevel` → **Entered Oversold Zone**
  - `PrevWR < OversoldLevel && currentWR >= OversoldLevel` → **Exited Oversold Zone**
- **`iWPR()` native computation** — `iWPR(TradeSymbol, Timeframe, WRPeriod, 0)` fetches the current Williams %R value directly from MT4's built-in WPR indicator
- **Strict transition-only alerting** — `PrevWR` comparison ensures alerts fire only on the bar a zone boundary is crossed, not on every bar within the zone — preventing alert flooding during sustained overbought/oversold readings
- **Rich alert message** — `AlertWPR()` formats with `EnumToString(timeframe)` and `"Williams %%R Value: %.2f"` for complete context; note the double-percent `%%R` escape for correct `%R` display in the formatted string
- **Persistent `PrevWR` state** — updated unconditionally at cycle end regardless of alert state
- **Three notification channels:** sound alert via `Alert()`, email via `SendMail()`, and mobile push via `SendNotification()`
- **Lightweight loop** — polls once per minute (`Sleep(60000)`)

---

## How It Works

1. Every minute, `iWPR(TradeSymbol, Timeframe, WRPeriod, 0)` fetches current `%R`
2. Four zone transition conditions evaluated using `PrevWR` vs `currentWR`:
   - Overbought entry/exit (`OverboughtLevel` default `−20.0`)
   - Oversold entry/exit (`OversoldLevel` default `−80.0`)
3. `PrevWR = currentWR` updated unconditionally at cycle end

---

## Input Parameters

| Parameter           | Type            | Default     | Description                                                        |
|---------------------|-----------------|-------------|--------------------------------------------------------------------|
| `TradeSymbol`       | string          | `EURUSD`    | Symbol for analysis                                                |
| `Timeframe`         | ENUM_TIMEFRAMES | `PERIOD_H1` | Timeframe for analysis                                             |
| `WRPeriod`          | int             | `14`        | Williams %R lookback period                                        |
| `OverboughtLevel`   | double          | `-20.0`     | %R level above which the market is considered overbought           |
| `OversoldLevel`     | double          | `-80.0`     | %R level below which the market is considered oversold             |
| `EnableAlerts`      | bool            | `true`      | Fire an on-screen/sound alert                                      |
| `EnableEmail`       | bool            | `false`     | Send an email notification                                         |
| `EnablePush`        | bool            | `false`     | Send a mobile push notification                                    |

---

## Alert Message Format

```
Entered Oversold Zone detected on EURUSD (Timeframe: PERIOD_H1)
Williams %R Value: -83.45
```

---

## Installation

1. Copy `Williams__R_001.mq4` to `MQL4/Scripts/` in your MT4 data folder
2. Compile in MetaEditor (F7)
3. Drag onto any chart from Navigator → Scripts
4. Configure inputs and click **OK**

---

## Requirements

- MetaTrader 4 (`#property strict` compatible build)
- MQL4 compiler (MetaEditor)

---

## License

MIT License

Copyright (c) 2026

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
