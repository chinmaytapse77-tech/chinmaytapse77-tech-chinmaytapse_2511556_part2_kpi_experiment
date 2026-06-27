# Recommendation Memo — Onboarding & Activation Campaign

**To:** Leadership Team
**From:** Business Analyst
**Re:** Whether to roll out the new onboarding & activation campaign to all users

---

## Executive Summary

The new onboarding & activation campaign was tested against the existing experience in a randomized A/B experiment (n=1400 users post-cleaning). Treatment delivered a **+121% relative lift in paid conversion rate** (3.19% → 7.04%), statistically significant at α = 0.05 (p = 0.0005, z = 3.26). Engagement improved (+10.4%) and time-to-conversion shortened (−27.8%). However, **support ticket rate rose materially** (+10 percentage points), and revenue per converted user dropped 52.7%, although total ARPU still improved (+4.4%) because the conversion lift more than offsets the smaller deal sizes.

> **Recommendation: LAUNCH WITH PHASED ROLLOUT AND MITIGATION**
>
> Specifically: (1) investigate and address the support-ticket spike before full launch; (2) ramp gradually (25% → 50% → 100%) over 3 weeks; (3) keep Social traffic source under separate monitoring (only segment showing slight regression); (4) track total revenue (not per-user revenue) to confirm net-positive impact holds.

---

## 1. Business Problem

A subscription-based digital product company launched a new onboarding & activation campaign with the objective of improving user conversion and early engagement. Users were randomly split into:
- **Control:** existing onboarding experience
- **Treatment:** new campaign experience

**Decision required:** whether to roll out treatment to 100% of users.
**Decision impact:** every new user acquired going forward; influences revenue, support load, and product quality perception.
**Primary success metric:** paid conversion rate.
**Risks to monitor:** refund rate, support ticket volume, revenue quality (per converted user), engagement, segment-level regressions.
**Evidence required:** statistically significant lift in paid conversion AND no critical guardrail breach AND no major segment regression.

---

## 2. North Star Metric

**Paid Conversion Rate** = `users who became paying customers / total users in group`

Chosen because:
- Directly measures the campaign's stated objective
- Captures monetary value (unlike upstream funnel metrics)
- Binary per user → clean statistical testing
- Hard to game — users have to actually pay

**Risk if optimized blindly:** maximizing conversion alone could come at the cost of refund rate (low-quality conversions), support load (confused users), or revenue per converted user (heavily-discounted conversions). The guardrail framework below mitigates this.

---

## 3. KPI Tree Explanation

The North Star breaks down into three primary drivers, each with three sub-drivers:

**Driver 1 — Funnel Progression** (do users get through the journey?)
- Landing page visit rate
- Trial start rate
- Onboarding completion rate

**Driver 2 — Revenue Quality** (are converters valuable?)
- ARPU (average revenue per user)
- Avg revenue per converted user
- Avg days to convert

**Driver 3 — Engagement & Trust** (do they stick and trust the product?)
- Avg engagement score
- Active users post-onboarding
- Feature adoption depth

**Guardrails (must not regress):**
- Refund rate
- Support ticket rate
- Avg revenue per converted user
- Engagement score
- Segment-level conversion

Full diagram: `outputs/kpi_tree.png`

---

## 4. Experiment Result Summary

| Metric | Control | Treatment | Lift |
|---|---|---|---|
| User count | 690 | 710 | — |
| Landing page visit rate | 63.62% | 72.39% | +13.8% |
| Trial start rate | 25.07% | 29.01% | +15.7% |
| Onboarding completion rate | 15.65% | 21.13% | +35.0% |
| **Paid conversion rate (North Star)** | **3.19%** | **7.04%** | **+121%** |
| ARPU | $51.97 | $54.25 | +4.4% |
| Avg revenue per converted user | $1,630 | $770 | −52.7% |
| Refund rate | 0.00% | 0.42% | +0.42 pp |
| Support ticket rate | 14.78% | 24.79% | **+10.01 pp** |
| Avg engagement score | 57.0 | 62.9 | +10.4% |
| Avg days to convert | 8.9 | 6.4 | −27.8% |

The funnel improved at every stage, with the largest gain at the final stage (paid conversion). Engagement is up. Time-to-convert is down. These are the positive headline numbers.

The two concerns are (a) support ticket rate, and (b) revenue per converted user — discussed in §6.

---

## 5. Hypothesis Test Interpretation

- **Test:** one-tailed two-proportion z-test
- **H₀:** p_treatment ≤ p_control · **H₁:** p_treatment > p_control · **α = 0.05**
- **z-statistic:** 3.26 · **p-value:** 0.0005
- **Verdict:** **Reject H₀**
- **95% CI on absolute lift:** +1.56 pp to +6.15 pp

**Plain language:** the probability of seeing a conversion improvement this large by random chance alone, if the campaign had no real effect, is about **5 in 10,000**. The lift is real, not noise. Even at the lower bound of the confidence interval (+1.56pp absolute), we'd be looking at a ~50% relative lift over the 3.19% baseline — still business-meaningful.

---

## 6. Guardrail Analysis

| Guardrail | Control | Treatment | Change | Verdict |
|---|---|---|---|---|
| Refund rate | 0.00% | 0.42% | +0.42 pp | **PASS** — increase from a zero baseline, absolute level still very low |
| Support ticket rate | 14.78% | 24.79% | +10.01 pp | **BREACH** — material increase |
| ARPU per converted user | $1,630 | $770 | −52.7% | **CONCERN** — but total ARPU still up +4.4% because more users convert |
| Engagement score | 57.0 | 62.9 | +10.4% | **PASS** — improved |
| Segment-level conversion | — | — | No segment with sufficient sample regresses meaningfully | **PASS** |

### Interpreting the breach

The support ticket rate increase from 14.8% to 24.8% is the single biggest red flag in the results. Several possible explanations:

1. **New experience is confusing.** New copy, new UI elements, or new flows may not be self-explanatory. This is fixable via iteration.
2. **More users get further down the funnel** (visit, trial, onboarding rates all up), so they have more opportunities to encounter issues. Some of the absolute increase may simply reflect more activity per user.
3. **Treatment is acquiring lower-quality users** who would not have converted under Control and who need more help. This is consistent with the ARPU-per-converter drop.

Action: rate measurement alone doesn't tell us which. Before full launch, the operations team should sample support tickets from both groups and classify them — to determine whether treatment-group tickets are about *the new experience itself* (fixable) vs. *general product questions* (load increase but expected).

### Interpreting the ARPU paradox

- Treatment converts 50 users at avg $770 → $38,500 total revenue
- Control converts 22 users at avg $1,630 → $35,860 total revenue
- Treatment generates **more total revenue** despite each conversion being smaller

This is the typical pattern of a funnel improvement: you reach more marginal users who would not previously have converted. They're smaller deals, but the net is positive. The math works out (ARPU up 4.4%) — but the per-converter drop should be communicated transparently to stakeholders so it isn't seen as a surprise later.

---

## 7. Segment-Level Insight

### By Region
| Region | Control | Treatment | Lift |
|---|---|---|---|
| North | 3.48% | 8.89% | +5.41 pp |
| South | 3.26% | 7.65% | +4.39 pp |
| East | 2.55% | 6.47% | +3.92 pp |
| West | 3.38% | 5.08% | +1.71 pp |

**All four regions benefit.** West is the weakest beneficiary but still positive. No region requires exclusion.

### By Traffic Source
| Source | Control | Treatment | Lift |
|---|---|---|---|
| Referral | 2.47% | 10.99% | **+8.52 pp** |
| Paid Search | 1.29% | 6.25% | +4.96 pp |
| Email | 2.74% | 7.14% | +4.40 pp |
| Organic | 2.03% | 6.33% | +4.30 pp |
| **Social** | **7.75%** | **6.06%** | **−1.69 pp** |

**Concern:** Social traffic was already the best-converting source in Control. With Treatment it drops to mid-pack. The decline is small (~1.7 pp) and may not be statistically significant given segment sample sizes — but it's the only segment showing a negative directional signal. Worth monitoring closely, or excluding from initial launch until investigation confirms it's noise rather than a real regression.

### By Device
All three device types (Desktop, Mobile, Tablet) improve. Mobile and Tablet see the largest gains (+4.8 pp and +5.3 pp respectively).

### By Plan Type
- Basic: barely moves (+0.26 pp)
- Free: large improvement (+6.23 pp)
- Premium: moderate improvement (+3.50 pp)

Treatment is most effective at converting Free-plan users — the largest segment of new signups. This is the most commercially valuable segment to improve, so this finding is favourable.

---

## 8. Final Recommendation

> **LAUNCH WITH PHASED ROLLOUT AND MITIGATION**

**Reasoning:**
- North Star delivered: statistically significant +121% relative lift in paid conversion (p = 0.0005), with a confidence interval far from zero
- Total revenue impact is positive (ARPU +4.4%) — the conversion lift exceeds the per-converter revenue drop
- Engagement (+10.4%) and time-to-convert (−27.8%) are both positive — early product satisfaction appears to be improving, not deteriorating
- The one BREACH (support tickets) is operationally addressable — likely a fixable issue with new onboarding copy/UI, not a fundamental product problem
- Segment results are encouraging — every region and device type benefits; only Social traffic source shows a small negative directional signal

**Why not "Launch Immediately":** the support ticket spike is real and substantial; ramping unilaterally could overload support operations.

**Why not "Do Not Launch":** the North Star evidence is strong and the total economic impact is positive. Walking away from a +121% conversion lift over a fixable support issue would be the wrong call.

**Why not "Continue Testing":** the experiment has already produced a clearly significant result. Continuing to test only delays capturing the value.

---

## 9. Risks and Limitations

- **Novelty effect:** part of the lift may reflect users responding positively because the experience is new; durability requires post-launch measurement over a longer window
- **30-day revenue window:** late conversions outside this window are not captured; the per-converter revenue gap may narrow with a longer measurement period
- **Support ticket interpretation:** rate alone doesn't separate "more confused users" from "more users period." Qualitative sampling is needed before launch
- **Social traffic source:** the directional negative may be statistical noise (small sample) or a real signal; treat as monitored, not excluded, until larger-sample data confirms
- **Sample composition:** experiment traffic may not perfectly represent future user mix; segment distributions should be monitored
- **Multiple comparisons:** secondary metrics and segment cuts are descriptive, not formally tested with multiplicity correction
- **Single experiment:** results are from one experiment in one time period; external macro factors could differ post-launch

---

## 10. Next Steps

### Pre-launch (week 1)
1. Support team samples 30+ tickets from Treatment group, classifies root cause (new onboarding confusion vs. general product question)
2. If primarily new-onboarding issues → ship copy/UI fixes for the identified pain points
3. Confirm tracking infrastructure to monitor all KPIs daily post-launch

### Phased rollout (weeks 2–4)
4. Week 2: enable Treatment for 25% of new users; monitor daily
5. Week 3: ramp to 50% if guardrails hold (support tickets within tolerance, conversion lift persists)
6. Week 4: ramp to 100% if metrics remain healthy

### Post-launch monitoring (ongoing)
7. Daily dashboard for North Star, support ticket rate, and revenue per converted user
8. Weekly segment review — flag any segment showing sustained regression
9. 30-day post-launch retrospective comparing live performance to experiment estimates

### Rollback criteria
- If support ticket rate exceeds 30% (treatment was 24.8%; ~5pp buffer for monitoring noise)
- If paid conversion rate drops below 5% (treatment was 7.04%; ~2pp buffer)
- If any segment shows >5pp conversion decline vs. its experiment-measured baseline

---

*Appendices:*
- `outputs/experiment_summary.xlsx` — full metric comparison + segment cuts + guardrails
- `analysis/experiment_analysis.xlsx` — cleaned data, DQ checks, hypothesis test calculation
- `analysis/hypothesis_test_notes.md` — detailed methodology
- `outputs/kpi_tree.png` — KPI framework visualization
