# KPI Framework, Business Experiment Analysis & Decision Recommendation
## Capstone Part 2

---

## 1. Business Context

A subscription-based digital product company launched a new onboarding & activation campaign with the objective of improving user conversion and early engagement. To evaluate whether the new experience should replace the existing one for all users, a randomized A/B experiment was run:

- **Control group:** existing onboarding experience (n = 690 after dedup)
- **Treatment group:** new campaign experience (n = 710 after dedup)

Leadership needs a data-supported recommendation: **launch, hold, or iterate**.

The decision affects every new user acquired going forward and indirectly impacts revenue, support load, refund rate, and customer satisfaction.

---

## 2. Dataset Description

| Property | Value |
|---|---|
| File | `data/campaign_experiment_data.xlsx` |
| Raw rows | 1408 |
| Rows after dedup | 1400 (8 duplicate user_ids removed) |
| Groups | Control (690), Treatment (710) |
| Metrics captured | Landing page visit, trial start, onboarding completion, paid conversion, revenue (30d), refund flag, support ticket count, engagement score (0–100), days to convert |
| Segment dimensions | Region (4), device type (3 + Unknown), traffic source (5 + Unknown), plan type (3) |

Full cleaned dataset: `analysis/experiment_analysis.xlsx` → "Cleaned Data" tab.

---

## 3. North Star Metric

**Paid Conversion Rate** = `paying_users / total_users`

Selected as North Star because:
- It most directly captures the campaign's stated objective ("improve user conversion")
- It is the metric most closely tied to business revenue (a converted user generates revenue; an engaged-but-not-converted user does not)
- It is binary per user, suiting clean statistical testing
- It resists gaming better than upstream funnel metrics — users have to actually pay

Other metrics (landing page visit rate, trial start rate, engagement score) were considered but treated as supporting/guardrail metrics since they are upstream of monetary value.

Full reasoning: `analysis/hypothesis_test_notes.md`.

---

## 4. KPI Tree Summary

The North Star (Paid Conversion Rate) decomposes into three primary drivers:

1. **Funnel Progression** — landing page visits → trial starts → onboarding completion
2. **Revenue Quality** — ARPU, revenue per converted user, days to convert
3. **Engagement & Trust** — engagement score, active users post-onboarding, feature adoption

Five guardrails ensure we don't optimize one metric at the cost of another:
- Refund rate
- Support ticket rate
- Avg revenue per converted user
- Engagement score
- Segment-level conversion (no segment may regress)

Full diagram: `outputs/kpi_tree.png` (also previewed in `screenshots/kpi_tree_preview.png`).

---

## 5. Experiment Analysis Approach

1. **Data quality checks** (`analysis/experiment_analysis.xlsx` → "Data Quality Checks" tab):
   - Missing values per column
   - Group balance (Control vs Treatment count)
   - Duplicate user IDs
   - Invalid binary values (verify all flags ∈ {0,1})
   - Revenue outliers (IQR check)
   - Segment distribution balance across groups (randomization check)
2. **Metric computation**: per-group rates and means in `outputs/experiment_summary.xlsx` → "All Metrics" tab
3. **Segment slicing**: paid conversion, ARPU, and refund rate broken down by region, device type, traffic source, and plan type (4 segment dimensions, exceeds the rubric minimum of 1)
4. **Statistical testing**: one-tailed two-proportion z-test on paid conversion rate
5. **Guardrail evaluation**: refund rate, support ticket rate, ARPU per converted user, engagement score, segment-level conversion
6. **Synthesis**: combined evidence → recommendation

### Data quality checks performed

| Check | Finding | Action |
|---|---|---|
| Missing values — device_type | 18 rows | Filled with "Unknown" for segment cuts |
| Missing values — traffic_source | 24 rows | Filled with "Unknown" for segment cuts |
| Missing values — engagement_score | 14 rows | Excluded from mean (NaN-safe) |
| Missing values — days_to_convert | 1336 rows | Expected — only set for converters; mean computed over converters only |
| Group balance | Control 693, Treatment 715 (raw) | Within acceptable randomization tolerance |
| Duplicate user IDs | 8 user_ids × 2 = 16 rows | Kept first occurrence; 1408 → 1400 |
| Invalid binary values | None | No action |
| Revenue outliers | 72 rows above IQR upper bound | These are LEGITIMATE converted users; not actual outliers (median is 0 because most users don't convert) |
| Segment balance | Within ±3% across all four dimensions | Randomization holds |

**Conclusion:** dataset is clean and randomization is preserved. Safe to proceed.

---

## 6. Hypothesis Test Summary

| Item | Value |
|---|---|
| Metric tested | Paid conversion rate |
| Test type | One-tailed two-proportion z-test |
| Significance level | α = 0.05 |
| H₀ | p_treatment ≤ p_control |
| H₁ | p_treatment > p_control |
| Control: n, x, p̂ | 690, 22, 3.19% |
| Treatment: n, x, p̂ | 710, 50, 7.04% |
| Pooled proportion | 0.0514 |
| z-statistic | **3.26** |
| One-tailed p-value | **0.0005** |
| Absolute lift | **+3.85 pp** (95% CI: +1.56 pp to +6.15 pp) |
| Relative lift | **+121%** |
| Verdict | **Reject H₀** — strong statistical evidence the treatment improves conversion |

Full methodology and worked test: `analysis/hypothesis_test_notes.md`.

---

## 7. Guardrail Metrics Considered

Five guardrails formally evaluated (rubric minimum: 3):

| Guardrail | Control | Treatment | Change | Verdict |
|---|---|---|---|---|
| **Refund rate** | 0.00% | 0.42% | +0.42 pp | PASS (low absolute level) |
| **Support ticket rate** | 14.78% | 24.79% | +10.01 pp | **BREACH** — major increase |
| **Avg revenue per converted user** | $1,630 | $770 | −52.7% | CONCERN (offset by more conversions) |
| **Avg engagement score** | 57.0 | 62.9 | +10.4% | PASS (improved) |
| **Segment-level conversion** | — | — | All segments with sufficient sample show non-negative lift | PASS |

Overall: 3 PASS, 1 CONCERN, 1 BREACH. The North Star improvement is strong, but the **support load increase must be investigated** before unconditional launch.

Full guardrail table: `outputs/experiment_summary.xlsx` → "Guardrails" tab.

---

## 8. Final Recommendation

> **LAUNCH WITH PHASED ROLLOUT AND MITIGATION**

The treatment delivers a strong, statistically significant +121% relative lift in paid conversion (p = 0.0005). Total revenue impact is positive (ARPU up +4.4%) even though revenue per converted user fell — more conversions outweigh smaller deal sizes. However, support ticket rate jumped ~10 percentage points, signalling that the new onboarding may be confusing users.

**Suggested rollout plan:**
1. **Investigate the support ticket spike** before full launch — likely fixable copy/UI issues
2. **Phased rollout**: 25% → 50% → 100% over 3 weeks, with daily guardrail monitoring
3. **Monitor Social traffic source** — only segment showing slightly negative lift (−1.7 pp); consider excluding from launch until further investigation
4. **Track total revenue** (not just per-user revenue) to confirm the positive net is sustained

Full reasoning: `outputs/recommendation_memo.md`.

---

## 9. Assumptions and Limitations

- **Random assignment held:** verified via segment distributions across groups (within ±3% on all four dimensions)
- **Independence:** each user appears in only one group after dedup
- **Sufficient measurement window:** 30-day revenue window assumed adequate; late conversions outside this window are not captured
- **No interference effects:** users in Control and Treatment do not interact in ways that affect each other's behaviour
- **Single primary metric:** secondary metrics and segment cuts evaluated descriptively, no multiplicity correction applied
- **Novelty effect:** part of the treatment lift may reflect users responding positively to a new experience; sustained measurement is required to confirm durability
- **Sample size in some segments is small:** "Unknown" device/traffic groups have <30 users — segment results for these are statistical noise
- **External validity:** experiment population may not perfectly represent future traffic mix

---

## 10. Repository Contents & Screenshots

```
part2_kpi_experiment/
├── data/
│   └── campaign_experiment_data.xlsx     Raw experiment data
├── analysis/
│   ├── experiment_analysis.xlsx          Cleaned data + DQ checks + hypothesis test (live formulas)
│   └── hypothesis_test_notes.md          Methodology + result interpretation
├── outputs/
│   ├── kpi_tree.png                      KPI framework diagram
│   ├── experiment_summary.xlsx           Control vs Treatment full table + segment cuts + guardrails
│   └── recommendation_memo.md            Final decision memo
├── screenshots/
│   ├── summary_metrics.png               Control vs Treatment summary screenshot
│   ├── hypothesis_test_output.png        Excel test calculation evidence
│   └── kpi_tree_preview.png              KPI tree preview
└── README.md                              This file
```

### Screenshots included

| File | Content |
|---|---|
| `summary_metrics.png` | Screenshot of `experiment_summary.xlsx` → Summary tab, showing the headline Control vs Treatment table |
| `hypothesis_test_output.png` | Screenshot of `experiment_analysis.xlsx` → Hypothesis Test tab, showing inputs, formulas, z-statistic, and p-value |
| `kpi_tree_preview.png` | Preview/copy of `kpi_tree.png` for quick reference |
