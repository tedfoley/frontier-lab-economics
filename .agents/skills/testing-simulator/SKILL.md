# Testing the Frontier AI Lab Economics Simulator

## Overview
This is a single-page HTML/JS simulator with no backend. All logic runs client-side via embedded JavaScript in `index.html`.

## How to Run Locally
```bash
cd ~/repos/frontier-lab-economics && python3 -m http.server 8080
```
Then visit `http://localhost:8080` in the browser. Alternatively, open `file:///home/ubuntu/repos/frontier-lab-economics/index.html` directly.

## Key UI Elements
- **Preset buttons**: Bear, Base, Bull, AGI Shock (bottom of left sidebar)
- **Simulator tab**: Shows Revenue vs Costs chart, Capacity vs Demand chart, and summary KPIs (2024 Profit, Break-Even Year, 10-Year NPV, Industry Capex)
- **Scenario Comparison tab**: Shows all 4 scenarios side-by-side with Annual Net Profit, Supply/Demand Ratio, Cumulative NPV, and Capacity Gap charts, plus a summary table
- **What Drives Profit? tab**: Sensitivity analysis

## How to Verify Model Parameters via Console
The simulator exposes `runSim()` and `PRESETS` globally. You can verify exact values without relying on chart hover:

```javascript
// Check a specific preset
var r = runSim(PRESETS.bear);
console.log('Revenue:', r.revenue.map(v => v.toFixed(1)));
console.log('Costs:', r.totalCost.map(v => v.toFixed(1)));
console.log('Monotonic:', r.revenue.every((v,i) => i===0 || v >= r.revenue[i-1]));
console.log('Below cost:', r.revenue.every((v,i) => v < r.totalCost[i]));
```

## Common Verification Checks
1. **Base case cost at year 2030**: `runSim(PRESETS.base).totalCost[6]` (index 6 = 2030 when starting from 2024)
2. **Bear case revenue never declines**: Check `r.revenue.every((v,i) => i===0 || v >= r.revenue[i-1])`
3. **Revenue/cost ratio**: `r.revenue.map((v,i) => (v/r.totalCost[i]).toFixed(2))`

## Important: Slider Step Alignment
When setting preset parameter values, they must align with the corresponding slider's `step` attribute. If a value doesn't match a valid slider step, the browser will snap it to the nearest valid value, causing a mismatch between the Simulator tab (which reads from sliders) and the Scenario Comparison tab (which uses raw preset values).

Common slider steps to watch:
- `phi`: step=5 (after multiplying by 100), so valid values are 0.50, 0.55, 0.60, etc.
- `switchGrowth`: step=0.5 (after multiplying by 100), so valid values are 0.005, 0.010, 0.015, etc.
- `Smax`: step=10 (after multiplying by 100), so valid values are 1.00, 1.10, 1.20, etc.
- `gamma`: step=1 (after multiplying by 100)
- `capexGrowth`: step=10 (after multiplying by 100)

## Devin Secrets Needed
None — this is a fully client-side app with no authentication.
