# Frontier AI Lab Economics Simulator

Interactive browser-based simulator that models whether frontier AI labs will be profitable. Built on a **two-stage capacity investment game** (Dixit & Pindyck 1994; Grenadier 2002): firms commit irreversible compute spend years before demand is known, then compete on realized demand via capacity-constrained pricing.

**[Live demo](https://tedfoley.github.io/frontier-lab-economics/)** &mdash; open `index.html` in any browser (no server required).

## How It Works

The model simulates a representative frontier AI firm over 10 years (2024–2033). Each year:

1. **Invest** &mdash; commit capex with a 2-year lead time before capacity comes online
2. **Demand realizes** &mdash; TAM grows via power-law scaling of cumulative training compute; adoption follows a Bass S-curve with novelty dilution
3. **Compete** &mdash; capacity-constrained pricing determines revenue; overcapacity crushes prices, scarcity enables premiums (Kreps & Scheinkman 1983)

### Key Model Features

| Feature | Description |
|---------|-------------|
| **Power-law TAM scaling** | Capability growth uses `(cumTraining/historical)^α` (Kaplan et al. 2020), referenced to ~$40B historical base |
| **Novelty dilution** | Rapid TAM growth slows effective adoption rate &mdash; newly addressable markets haven't started the S-curve yet |
| **Hardware depreciation** | 15%/yr net depreciation; with τ=2 lead time, hardware loses ~28% by arrival |
| **Kreps & Scheinkman scarcity** | Capacity constraints eliminate undercutting incentives &mdash; competition does NOT erode scarcity rents |
| **OS salvage revenue** | Overcapacity firms earn low-margin revenue from demand lost to open source (cheap API tiers, fine-tuning) |
| **Legacy capacity** | Exited firms' hardware enters a decay pool rather than vanishing |
| **Entry/exit dynamics** | Optional: firms enter/exit based on cumulative profitability |

### Core Equations

```
TAM growth:    g_cap = g_base + λ·((cumTraining_total / historical)^α − 1)
Diffusion:     D(t) = TAM × D_max / (1 + ((D_max−D₀)/D₀)·e^(−r_d_eff·t))
               r_d_eff = r_d / (1 + ψ·max(TAM_growth_rate, 0))
Capacity:      K_i(t) = θ · I(t−τ) · η(t) · (1−δ_hw)^age
S/D ratio:     u = K_total / D_frontier
Overcapacity:  Rev = (D_f/N)·(1/u)^σ_eff + salvage_revenue
Scarcity:      Rev = (D_f/N)·min((1/u)^ρ, S_max)
```

## Presets

| Scenario | Story | 2024 Rev/Firm | 10yr NPV |
|----------|-------|---------------|----------|
| **Base** | Moderate competition, 4 firms, breaks even by 2030 | ~$1.0B | +$287B |
| **Bear** | 5 aggressive investors, commodity market, OS eats margins | ~$0.1B | -$280B |
| **Bull** | 3 differentiated firms, strong moats, fast adoption | ~$2.1B | +$2,155B |
| **AGI Shock** | Transformative demand explosion ($150B+ TAM) | ~$2.1B | +$9,004B |

Base case calibrated to ~$1B revenue / ~$2.4B cost per firm in 2024.

## Project Structure

```
frontier_lab_sim/
  index.html    # Entire app: HTML + CSS + JS (single file, no build step)
  README.md     # This file
```

**Dependencies:** [Plotly.js](https://plotly.com/javascript/) via CDN. No other external dependencies. All computation runs client-side.

## Usage

Open `index.html` in a browser. Use the sidebar sliders to adjust parameters and see real-time chart updates. Three tabs:

- **Simulator** &mdash; interactive parameter tuning with revenue/cost/profit charts
- **Scenario Comparison** &mdash; side-by-side Bear/Base/Bull/AGI analysis
- **What Drives Profit?** &mdash; tornado sensitivity chart + break-even heatmap

## References

- Dixit, A. K., & Pindyck, R. S. (1994). *Investment under Uncertainty*
- Grenadier, S. R. (2002). Option exercise games
- Kreps, D. M., & Scheinkman, J. A. (1983). Quantity precommitment and Bertrand competition yield Cournot outcomes
- Kaplan, J., et al. (2020). Scaling laws for neural language models
- Hoffmann, J., et al. (2022). Training compute-optimal large language models
