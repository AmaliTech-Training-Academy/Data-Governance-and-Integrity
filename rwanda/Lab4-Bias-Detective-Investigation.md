# Lab 4: Bias Detective Investigation

**Project:** TalentMatch Rwanda AI Bias Analysis

**Role:** QA Engineer, TalentMatch Rwanda

**Status:** Optional Lab

---

## 1. Executive Summary

**Bias Severity:** High

**Primary Affected Groups:**

- Female candidates
- Candidates who studied at public/TVET institutions outside the main private-university track (e.g. IPRC campuses, public teacher-training colleges) rather than at University of Rwanda's flagship campuses or well-known private universities
- Candidates from Northern, Western, and Southern Province addresses, relative to Kigali City
- Candidates whose CVs were submitted primarily in Kinyarwanda rather than English or French

**Top 3 Recommended Actions:**

- Remove or sharply down-weight proxy features (university brand, home province, CV language) that correlate with historical hiring rather than with candidate ability
- Retrain the scoring model on a rebalanced, representative dataset that does not simply relearn the employer base's past hiring pattern
- Stand up weekly fairness monitoring across gender, province, and CV-language groups, not only gender

## 2. Detailed Findings

### 2.1 Bias Type Analysis

#### 1. Historical Bias

**Present?** Yes

**Evidence:**

- The training data is drawn from five years of actual hiring outcomes across TalentMatch Rwanda's employer clients, which were themselves concentrated in Kigali-based tech and BPO firms — **69% male hires vs 31% female hires** across the training set.
- Candidates whose most recent employer or internship was in Kigali City are overrepresented relative to Rwanda's actual working-age population distribution across provinces.

**Disadvantaged Groups:**

- Female candidates
- Candidates whose prior work experience was outside Kigali City

#### 2. Sampling Bias

**Present?** Yes

**Evidence:**

- The training dataset is heavily skewed toward **software/IT roles (58%)**, with customer-service, sales, and non-tech roles underrepresented even though TalentMatch Rwanda's client base has since diversified into those sectors.
- **74%** of training records have a Kigali City address, even though Kigali holds roughly 15% of Rwanda's population — the model has, in effect, barely seen a rural applicant.

**Underrepresented Groups:**

- Applicants for non-tech roles
- Applicants from Northern, Western, Southern, and Eastern Province addresses

#### 3. Measurement Bias

**Present?** Yes

**Evidence:**

- The model uses "professional network size" (LinkedIn-style connection count) and "reference count" as scoring inputs. Both track access to a professional network built through Kigali-based internships and employer contacts far more than they track a candidate's actual competence — a highly capable graduate from Musanze or Huye with no Kigali internship simply has fewer connections to show, for reasons that have nothing to do with skill.
- CV completeness score penalizes CVs written primarily in Kinyarwanda with English section headers only, treating them as "incomplete" relative to fully bilingual/English CVs — this measures a candidate's exposure to English-medium CV-writing coaching, not their job competence.

**Unfair Measurement:**

- Professional-network and reference-count signals structurally favor candidates who already had access to Kigali's formal job market before they even applied.

#### 4. Proxy Bias

**Present?** Yes

**Proxy Features:**

- University name/brand (flagship private universities and University of Rwanda's Kigali-based colleges score higher than IPRC/TVET or other public campuses, independent of the specific program studied)
- Home province / address on file
- Professional network size
- CV language mix (English/French-forward CVs score higher than Kinyarwanda-forward CVs)

**Impact:**

- Indirect discrimination against candidates from lower-income households and rural provinces, who are statistically more likely to have attended TVET/public institutions, to have Kinyarwanda-forward CVs, and to lack a Kigali-built professional network — none of which reflects their actual ability to do the job.

### 2.2 Bias Pipeline Mapping

```
[Historical Hiring Decisions by Kigali-concentrated employer clients]
        ↓
[Training Data Collection]   → Bias Point #1: skewed hiring data (gender, province, role type)
        ↓
[Feature Selection]          → Bias Point #2: inclusion of proxy features (university brand,
                                province, network size, CV language)
        ↓
[Model Training]             → Bias Point #3: model learns and amplifies the historical
                                Kigali/male/private-university pattern
        ↓
[Deployment & Scoring]       → Bias Point #4: discriminatory ranking of otherwise-qualified
                                rural, female, and TVET-educated candidates
        ↓
[Biased Hiring Outcomes at Client Companies]
```

### 2.3 Feature Risk Analysis

**High-Risk Features:**

- University brand/name
- Home province / address
- Professional network size
- CV language mix

**Moderate Risk:**

- Reference count
- Extracurricular/volunteer activity descriptions

**Low Risk:**

- Verified technical skills assessment score
- Years of relevant work experience

## 3. Mitigation Plan

### 3.1 Immediate Actions (This Week)

**Feature Removal:**

- Remove or reduce the weight of:
  - University brand/name (retain only verified degree level and field of study)
  - Home province/address
  - Professional network size
  - CV language mix / "completeness" scoring tied to English fluency

**Threshold Adjustments:**

- Introduce fairness-aware scoring thresholds so no single demographic, province, or language group is systematically pushed below the interview-invite line at a materially different rate than others.

**Output Monitoring:**

Track weekly:

- Gender selection ratio
- Province-of-residence distribution among shortlisted candidates
- CV-language distribution among shortlisted candidates vs. applicant pool

### 3.2 Short-Term Actions (1–3 Months)

**Data Collection:**

- Actively recruit training examples from employer clients outside Kigali City and from non-tech roles, so the model stops learning "Kigali tech hire" as its implicit default profile.

**Model Retraining:**

- Retrain on the rebalanced dataset with fairness constraints applied during training, not only checked afterward.

**Human Oversight:**

- Require a human recruiter to review any batch of shortlisted candidates before it is sent to a client, specifically checking for province/gender/language skew.

### 3.3 Long-Term Actions (6–12 Months)

**Fairness Metrics to Adopt:**

- Equal Opportunity (recommended) — are equally-qualified candidates from different groups shortlisted at similar rates?
- Demographic Parity (secondary, monitoring only) — raw shortlist-rate comparison across groups, useful as an early warning even though it does not account for underlying qualification differences.

**Process Changes:**

- Cap how much weight any single automated score can carry in a final hiring recommendation; require a documented reason whenever the model's top-ranked candidate is not the one referred to the client.

**Transparency Measures:**

- Disclose to candidates that an AI ranking tool is used in the process.
- Provide candidates a plain-language explanation of the factors the score is based on.
- Offer a channel for candidates to request human review of their ranking.

## 4. Success Metrics

- Shortlist rate gap between gender groups narrows over successive quarters.
- Shortlist rate for non-Kigali provinces moves toward parity with Kigali City, adjusted for applicant volume.
- CV-language mix of shortlisted candidates approaches the CV-language mix of the overall applicant pool.
- Weekly fairness reports show a consistent, not one-off, improvement trend.

## 5. Timeline

| Phase | Timeline | Key Actions |
|---|---|---|
| Immediate | Week 1 | Remove/down-weight proxy features, stand up weekly monitoring |
| Short-Term | 1–3 Months | Rebalance training data, retrain with fairness constraints, add human review gate |
| Long-Term | 6–12 Months | Adopt Equal Opportunity metric formally, add candidate-facing transparency measures |

## 6. Conclusion

TalentMatch Rwanda's scoring model demonstrates significant bias risk, and the mechanism is a familiar one: a model trained on a Kigali-concentrated, male-skewed hiring history will reproduce that pattern unless something in the pipeline actively corrects for it. What makes this case distinct from a generic bias audit is the specific shape the proxies take in the Rwandan labor market — home province standing in for urban/rural income differences, and CV language standing in for access to English-medium schooling — both of which are easy to miss if a fairness review only checks for gender bias and stops there. Removing the highest-risk proxy features, rebalancing the training data to include the non-Kigali, non-tech, and Kinyarwanda-forward candidates the model has barely seen, and adopting Equal Opportunity as the standing fairness metric are the three changes most likely to close the gap this investigation found — and, just as importantly, to keep it from reopening the next time the model is retrained on another year of the same skewed hiring history.
