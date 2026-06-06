# Bladder Health Score (BHS) — Calculation Rules

## Day Definition

- A **logical day** runs from **4:00am ET to 3:59am ET** (next calendar day)
- Voids between **12:00am–3:59am ET** are assigned to the **previous** calendar date
- Timezone: `America/New_York` (handles DST automatically)
- Today (current incomplete day) is always **excluded** from calculations

## Flare Detection (per day)

1. Sort all voids in the day by timestamp
2. Count **short intervals**: consecutive void pairs < 30 minutes apart
3. **Compression** = short_intervals / total_intervals
4. A day is a **flare day** if compression ≥ 0.20 (20%) **AND** short_intervals ≥ 3

> If a day has fewer than 3 short intervals, it is never a flare regardless of compression.

## BHS Formula (30-day rolling)

```
freqScore  = clamp((16 − rolling_mean_voids) / (16 − 7) × 100,  0, 100)
flareScore = clamp((1 − rolling_flare_rate / 40) × 100,          0, 100)
BHS        = 0.40 × freqScore + 0.60 × flareScore
```

### Anchors

| Component | Score = 100 | Score = 0 | Weight |
|-----------|-------------|-----------|--------|
| Frequency | ≤7 voids/day | ≥16 voids/day | 40% |
| Flare rate | 0% flare days | ≥40% flare days | 60% |

- Population normal = 7 voids/day, 0% flare days → **BHS 100**
- `rolling_flare_rate` is expressed as a percentage (0–100)

## Rolling Window Rules

- Window size: **30 days**
- Minimum clean days required: **30** (BHS returns `null` if fewer available)
- **Travel/excluded days are skipped** and do not count toward the window
- Window walks backward from the current date collecting only clean (non-excluded) days

## BHS Classification

| BHS Range | Label |
|-----------|-------|
| 90–100 | Good |
| 70–89 | Acceptable |
| 50–69 | Warning |
| 0–49 | Bad |

## Other Day-Level Thresholds

| Threshold | Value |
|-----------|-------|
| Bad day | ≥13 voids |
| Good day | ≤9 voids |
| Nocturia window | 12:30am–7:00am ET |
| Nocturia (strict) | 12:30am–6:00am ET |
| Early morning window | 6:00am–7:30am ET |
| Presleep window | 9:00pm–12:30am ET |

## Excluded (Travel) Days

- Specific date ranges can be marked as excluded (e.g. travel)
- Excluded days are **invisible to all rolling windows** — they are skipped, not zeroed
- They do not count toward the 30-day minimum either
