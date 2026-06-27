# Hypothesis Test Notes

## 1. Business Decision Being Made

Should the new onboarding & activation campaign (treatment) be rolled out to all users, or should the existing experience (control) be retained?

The decision hinges on whether the new experience drives a **statistically significant and practically meaningful** improvement in paid conversion — without breaking any guardrail metric.

---

## 2. Metric Being Tested

**Primary metric: Paid Conversion Rate**

Definition: `users_who_paid / total_users`, computed per group.

### Why this metric?

1. **Direct alignment with business objective.** The brief states the campaign's objective is to "improve user conversion." Paid conversion is the most concrete operationalization — it captures whether users actually exchange money for the product, not just whether they engage with the funnel.
2. **Resists gaming.** Upstream metrics (landing page visits, trial starts) can be inflated by friction reduction without producing revenue. Paid conversion forces the funnel to deliver actual business value.
3. **Statistical tractability.** It's a binary outcome per user (paid / not paid), which suits a two-proportion z-test cleanly.
4. **Affects revenue directly.** A 1pp improvement in paid conversion compounds across every user in the funnel.

### Why not the others?

| Candidate metric | Why not the primary test |
|---|---|
| Landing page visit rate | Upstream — improvement here doesn't guarantee downstream value |
| Trial start rate | Same — intent, not action |
| Onboarding completion rate | Activation, but not monetization |
| ARPU | Includes both conversion and revenue depth — multivariate; harder to attribute change cleanly to the campaign |
| Engagement score | Important guardrail, but not the primary success criterion |
| Days to convert | Speed matters, but is a refinement on conversion, not a substitute |

These all appear in the experiment summary and KPI tree as supporting/guardrail metrics.

---

## 3. Hypothesis Statement

**Null hypothesis (H₀):**
> The paid conversion rate in the treatment group is equal to (or worse than) the paid conversion rate in the control group.
> `p_treatment ≤ p_control`

**Alternate hypothesis (H₁):**
> The paid conversion rate in the treatment group is greater than the paid conversion rate in the control group.
> `p_treatment > p_control`

---

## 4. Test Direction

**One-tailed (right-tailed) test.**

Reason: the business is only interested in launching the new experience if it is *better* than the existing one. A treatment that is no different or worse should not be launched, so we don't need to test for a two-sided difference. A one-tailed test concentrates statistical power on the relevant direction.

---

## 5. Significance Level

**α = 0.05** (95% confidence level)

This is the standard threshold for product experiments at this scale. Given that:
- The decision is reversible (we can roll back the campaign if real-world performance differs)
- The cost of a Type I error (launching an ineffective change) is moderate but not catastrophic
- The cost of a Type II error (missing a real improvement) is comparable

…α = 0.05 strikes the right balance. We are not making an irreversible safety-critical decision that would warrant α = 0.01.

---

## 6. Statistical Test

**Two-proportion z-test (one-tailed)**

Test statistic:
```
z = (p̂_T − p̂_C) / √( p̂ × (1 − p̂) × (1/n_T + 1/n_C) )
```
where `p̂ = (x_T + x_C) / (n_T + n_C)` is the pooled proportion.

In Excel:

```
= (p_T - p_C) / SQRT( p_pooled * (1 - p_pooled) * (1/n_T + 1/n_C) )
```

Then `p_value = 1 - NORM.S.DIST(z, TRUE)` for a right-tailed test.

The full calculation block — with live, inspectable formulas — is in `experiment_analysis.xlsx` → "Hypothesis Test" tab.

---

## 7. Decision Rule

| Condition | Action |
|---|---|
| `p < 0.05` AND no guardrail breach | **Launch** |
| `p < 0.05` BUT a guardrail breaches | **Investigate before launching** — consider segment-specific rollout or mitigation |
| `p ≥ 0.05` | **Do not launch.** Continue testing or redesign. |

A statistically significant lift in conversion is necessary but not sufficient. The guardrails (refund rate, support tickets, ARPU, segment-level performance) must also pass.

---

## 8. Test Inputs

| Group | Users (n) | Converted (x) | Conversion Rate (p̂) |
|---|---|---|---|
| Control | 690 | 22 | 3.19% |
| Treatment | 710 | 50 | 7.04% |

- Pooled proportion `p̂` = (22 + 50) / (690 + 710) = **0.0514**
- Standard error = √(0.0514 × 0.9486 × (1/690 + 1/710)) = **0.01180**

---

## 9. Test Output

| Quantity | Value |
|---|---|
| z-statistic | **3.26** |
| One-tailed p-value | **0.0005** |
| Observed absolute lift | **+3.85 pp** |
| 95% CI on absolute lift | **+1.56 pp to +6.15 pp** |
| Observed relative lift | **+121%** |

---

## 10. Business Interpretation

### Statistical verdict
**Reject H₀.** The probability of seeing a conversion lift this large (or larger) by random chance alone, under the null hypothesis that the treatment is no better than control, is about **5 in 10,000** (p = 0.0005). This is well below our α = 0.05 threshold.

### Practical magnitude
Even at the **lower bound** of the 95% confidence interval (+1.56 pp absolute lift), the treatment would deliver a ~50% relative improvement over the 3.19% baseline. The point estimate is +121% relative lift. Both are business-meaningful: a 1-percentage-point shift in paid conversion at this scale represents a substantial revenue increment.

### Guardrail check
- **Refund rate**: increase from 0% to 0.42% — small absolute level, PASS
- **Support ticket rate**: increase from 14.78% to 24.79% — **BREACH** (+10pp)
- **Avg revenue per converted user**: dropped 52.7% — CONCERN (offset by conversion lift; total ARPU still up 4.4%)
- **Engagement score**: improved 10.4% — PASS
- **Segment-level conversion**: every segment with sufficient sample shows non-negative lift — PASS

### Segment-level check
All four regions improve. All three device types improve. Four of five named traffic sources improve significantly; **Social shows a small directional regression** (−1.7pp from 7.75% to 6.06%) which may be statistical noise but warrants monitoring. All three plan types improve, with Free-plan users (the largest segment) seeing the biggest gain.

### Final recommendation
**Launch with phased rollout and mitigation.** The statistical evidence for the North Star is strong, and the total revenue impact is positive. The support ticket spike is a real concern but operationally addressable. Full reasoning in `outputs/recommendation_memo.md`.

---

## Notes on Methodology

- We use a **frequentist two-proportion z-test** rather than a Bayesian or chi-square approach because (a) the metric is a pure proportion, (b) sample sizes are large enough for the normal approximation (np > 10 in both groups), (c) results are easily communicated to non-technical stakeholders as a p-value.
- Multiple testing correction (e.g. Bonferroni) is **not** applied to the primary test because there is only one primary hypothesis. Guardrail metrics are evaluated descriptively rather than via additional formal tests, which is standard practice in product experimentation.
- We assume **independent users** across groups — users were randomly assigned and only appear in one group. This was verified in the data cleaning step (after deduplication, no user_id appears in both groups).
- The normal approximation is reliable here: under the pooled proportion (~5.1%), expected counts in both groups exceed 10 (n × p > 35), satisfying the rule of thumb for the z-test's validity.
