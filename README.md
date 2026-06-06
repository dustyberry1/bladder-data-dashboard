# Bladder Health Dashboard

Bladder Health Dashboard is a static web app for visualizing void-tracking data exported from P App.  
Upload a CSV and the app generates 30-month trend charts and summary metrics (daily counts, nocturia, inter-void interval statistics, urinary symptom trends, and protocol timeline overlays).

No backend is required: everything runs in the browser. Your data stays local unless you choose to push files to another location yourself.

## What It Does

After upload, the dashboard computes:

- Daily void counts
- Nocturia score and trend
- Urinary Symptom Drift (USD)-style trend charting
- Inter-void interval heatmaps and distribution views
- Nocturnal profile metrics (early night/late-night patterns)
- A protocol/intervention timeline overlaid on core charts
- Day boundarying using a 4:00 AM ET logical day definition

The app reads only the timestamp column it needs, stores the CSV in `localStorage` for quick reload, and renders all visuals client-side.

## Scoring Method

Core scoring and trend views are driven by the Bladder Health Score methodology in this repo:

- [Bladder Health Score rules (BHS)](BHS_RULES.md)

BHS is the symptom methodology this dashboard uses to define and quantify bladder issues. It combines:

- **Frequency burden** (how often voids happen across a day)
- **Flare behavior** (how often short intervals cluster in a day)
- A **30-day rolling view** with travel/excluded days removed from calculations

This gives a consistent way to compare symptom severity over time and identify meaningful changes during protocol adjustments, not just raw event counts.

## CSV Input Format

The parser expects a header row with one of:

- `Pee Date and Time` (P App export default)
- `startDate` (legacy export fallback)

Any additional columns are ignored.

Only one timestamp column is required for the dashboard to run.

### Accepted timestamp formats

Either of these timestamp styles are supported:

- UTC ISO 8601: `2026-06-01T14:23:00Z`
- Offset-aware local format: `2026-06-01 10:23:00-04:00`

The app supports both the old local-offset format and newer UTC export format in the same field.

### Minimum CSV example

```csv
Pee Date and Time
2026-06-01T14:23:00Z
2026-06-01 10:23:00-04:00
```

If you use `startDate` instead, use the same value structure:

```csv
startDate
2026-06-01 09:15:00-04:00
```

## Quick Start

- Open `bladder_dashboard.html` in a modern browser.
- Upload your CSV on the landing screen.
- You can test immediately with the included sample file:
  - `sample-export.csv`
- Use **Load demo data** to see a sample 90-day dataset when no file is available.

## Notes

- Timezone handling is applied via ET conversion and a 4:00 AM day boundary to keep overnight events grouped with the prior sleep cycle.
- A few configured travel/date exclusion windows are applied in the shipped dashboard logic for cleaner symptom scoring windows.
