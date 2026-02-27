# Testing the Frontier AI Lab Economics Simulator

## Overview
Single-file HTML/JS financial model simulator. No build step, no backend, no auth required.

## How to Run Locally
```bash
cd ~/repos/frontier-lab-economics
python3 -m http.server 8080
```
Then open http://localhost:8080 in a browser.

## What to Test

### 1. Page Load (Base Case)
- Verify stat cards at top: 2024 Profit, Break-Even Year, 10-Year NPV, Industry Capex
- Check that Capex Growth slider matches BASE.capexGrowth value
- Check that TAM Growth Rate slider (under Advanced Parameters) matches BASE.gCapBase value

### 2. Preset Switching
- Click Bear/Base/Bull/AGI Shock buttons at the bottom of the sidebar
- Verify stat cards update (especially NPV sign and break-even year)
- Verify sliders update to match the selected preset's parameters
- Verify charts re-render without errors

### 3. Scenario Comparison Tab
- Click "Scenario Comparison" tab at the top
- Verify all 4 scenario charts render
- Scroll down to verify the comparison table shows correct values for each scenario

### 4. What Drives Profit Tab
- Click "What Drives Profit?" tab
- Verify tornado sensitivity chart and break-even heatmap render

## Key Architecture Notes
- All code is in `index.html` (single file: HTML + CSS + JS)
- BASE preset defined around line 667
- PRESETS object around line 685 (bear/base/bull/agi)
- `_extras` object around line 720 holds non-slider parameter defaults
- `runSim(p)` function around line 470 is the core simulation engine
- Init code at bottom calls `loadPreset('base')` then `updateLabels()` and `updateSimCharts()`
- Plotly.js loaded via CDN for charting

## Verifying Model Output
To verify simulation output without a browser, extract the `runSim()` function and presets into a Node.js script and run it:
```bash
node /tmp/verify.js
```
Check 2030 industry cost (~$491B for base), revenue (should exceed cost), and break-even year.
