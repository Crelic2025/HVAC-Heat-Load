# HVAC Heat Load Calculator

A single-file, offline-capable heat load estimator built for field use. Calculates heating and cooling loads using Manual-J–style factor tables and recommends mini-split head/condenser sizing.

Built for [Northern One Hour](https://northernonehour.com/), Ramsey MN.

<p align="center">
  <em>~36KB · zero dependencies · works offline on iPhone</em>
</p>

## Quick Start

1. Download [`hvac_heatload_calculator.html`](hvac_heatload_calculator.html)
2. Open it in any browser — done

**iPhone tip:** Open in Safari → Share → *Add to Home Screen*. It runs fullscreen like a native app.

## What It Does

### Inputs (6 cards, ~2 minutes to fill)

| # | Section | Fields |
|---|---------|--------|
| 1 | **Room Dimensions** | Length × Width × Ceiling height, exterior wall exposure (1–4 walls) |
| 2 | **Windows** | Primary compass exposure (N → S), glazing type, optional glass sq ft override (auto-defaults to 15% of gross wall) |
| 3 | **Doors** | Count (defaults 21 sq ft each), solid wood or metal |
| 4 | **Insulation** | Wall R-value (R11–R19), ceiling R-value (R11–R38) |
| 5 | **Foundation** | Basement / slab-no-ducts / slab-with-ducts / none; auto-derives footprint or accepts manual sq ft / linear ft |
| 6 | **Occupancy & Air** | People count, mechanical vent CFM, kitchen toggle |

### Outputs

1. **Sizing headline** — Heat Gain and Heat Loss shown side-by-side; the controlling load is flagged in green
2. **Equipment recommendation** — Indoor head and condenser size in large numbers (e.g. "18k"), with nameplate BTUH
3. **Cold-climate warning** — Triggers when heat loss exceeds ~72% of unit nameplate capacity (Minnesota -15°F derate). Recommends Mitsubishi Hyper Heat, Lennox MLA, or upsizing
4. **Large load warning** — At 30k+ BTUH, suggests splitting into two heads on a multi-zone condenser
5. **Collapsible breakdown** — Every line item (windows, doors, walls, ceiling, infiltration sensible/latent, mechanical, people, kitchen) so you can justify the number to a customer
6. **Filled worksheet** — Printable two-column layout mimicking the original Excel worksheet (gain left / loss right), with your specific rows highlighted in yellow. Hit the Print button to print-to-PDF and email the customer

### Field Features

- **Auto-save** — every keystroke saved to `localStorage`; swipe away and come back, everything's still there
- **Copy summary** — clipboard-ready text block for ServiceTitan notes
- **← Edit inputs** — go back and tweak without losing your results
- **Clear** — with confirm dialog
- **iPhone-safe** — 16px inputs (no zoom), safe-area insets for the notch, 44×44 tap targets, no hover-state-dependent UI

## Sizing Logic

### Heat Gain (cooling) — 20°F temperature difference

| Component | Factor | Notes |
|-----------|--------|-------|
| Windows | 19–48 BTU/sf | Direction-dependent: N=19, NE=34, E=48, SE=39, S=24 |
| Doors | 4.6–8.8 BTU/sf | Metal=4.6, Wood=8.8 |
| Walls | 1.6–2.6 BTU/sf | R19=1.6, R13=2.3, R11=2.6 |
| Ceiling | 1.3–4.1 BTU/sf | R38=1.3, R30=1.6, R19=2.6, R11=4.1 |
| Infiltration (sensible) | 27 BTU/CFM | 0.4 ACH × volume / 60 |
| Infiltration (latent) | 24 BTU/CFM | Same CFM base |
| Mechanical vent (sensible) | 27 BTU/CFM | |
| Mechanical vent (latent) | 24 BTU/CFM | |
| People | 530 BTU/person | |
| Kitchen allowance | 1,200 BTU | Zone toggle |

### Heat Loss (heating) — 85°F temperature difference

| Component | Factor | Notes |
|-----------|--------|-------|
| Windows | 82–113 BTU/sf | Single=113, Double=82, Sliding=85 |
| Doors | 39–52 BTU/sf | Metal=39, Wood=52 |
| Walls | 4.7–7.6 BTU/sf | R19=4.7, R13=5.9, R11=7.6 |
| Ceiling | 2.2–7.5 BTU/sf | R38=2.2, R30=2.8, R19=4.5, R11=7.5 |
| Infiltration | 94 BTU/CFM | Same 0.4 ACH CFM base |
| Ventilation | 94 BTU/CFM | Mechanical vent CFM |
| Basement floor | 2.4 BTU/sf | |
| Slab edge | 17.8–162 BTU/lf | Slab-no-duct: 17.8–69; slab-with-ducts: 79–162 |

### Equipment Sizing

The calculator walks a standard mini-split ladder (6k → 9k → 12k → 15k → 18k → 24k → 30k → 36k) and picks the smallest unit that covers the **controlling load** (whichever is higher between heat gain and heat loss).

Cold-climate check: at -15°F, standard mini-splits deliver ~72% of nameplate capacity. If heat loss exceeds this derated capacity, a warning is shown recommending a cold-climate rated unit or upsizing.

## Example

**20×30×8 ft room**, 2 exterior walls (corner), E/W double-pane windows, R19 walls, R30 ceiling, basement:

- Heat Gain: **8,995** BTUH
- Heat Loss: **16,458** BTUH (controls sizing)
- Recommendation: **18k** head + **18k** condenser
- ⚠ At -15°F this 18k unit delivers ~13,000 BTUH — below the 16,458 BTUH needed. Upgrade to Hyper Heat/MLA or bump size.

## Known Limitations

- **Masonry walls** are not modeled separately from framed walls. For finished basements with masonry exterior or brick ranches, the gain/loss will be under/overestimated. If you need masonry: add `B24*10.9` / `G24*12.5` (brick) etc. manually to the worksheet print-out, or flag for a future update.
- Factor tables are tuned for Minnesota 20°F TD (cooling) / 85°F TD (heating). Other climate zones may need adjusted ΔT values.
- Single-zone only. Multi-zone setups are suggested above 30k BTUH but not calculated in detail.

## Tech

- Single HTML file, zero external dependencies
- Dark theme, Northern branding
- Safari PWA-ready (`apple-mobile-web-app-capable`)
- Print stylesheet included for clean worksheet printouts

## License

Built for internal use at Northern One Hour.
