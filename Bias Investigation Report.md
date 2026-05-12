# Bias Investigation Report

**Project:** HireScore AI Bias Analysis

**Role:** QA Engineer – TalentMatch AI

## 1. Executive Summary

**Bias Severity:** High

**Primary Affected Groups:**

- Female candidates
- Candidates from non-top universities
- Candidates from regions outside Greater Accra & Ashanti

**Top 3 Recommended Actions:**

- Remove or reduce weight of proxy features (e.g., university, location, LinkedIn connections)
- Retrain model with balanced and representative dataset
- Implement fairness monitoring metrics (weekly bias audits)

## 2. Detailed Findings

### 2.1 Bias Type Analysis

#### 1. Historical Bias

**Present?** Yes

**Evidence:**

- Training data shows 72% male hires vs 28% female
- Overrepresentation of specific universities and regions

**Disadvantaged Groups:**

- Female candidates
- Candidates from less represented universities
- Candidates from non-major regions

#### 2. Sampling Bias

**Present?** Yes

**Evidence:**

- Training dataset heavily skewed:
  - Software Engineering dominates (60%)
  - Other job categories underrepresented
  - Regional imbalance: majority from Greater Accra

**Underrepresented Groups:**

- Non-tech job applicants
- Candidates from rural/less represented regions

#### 3. Measurement Bias

**Present?** Yes

**Evidence:**

- Metrics like LinkedIn connections and references vary by access/opportunity
- These metrics do not equally reflect candidate ability

**Unfair Measurement:**

- Professional exposure indicators favor privileged groups

#### 4. Proxy Bias

**Present?** Yes

**Proxy Features:**

- University attended
- Location of previous employment
- LinkedIn connections
- Previous company names

**Impact:**

- Indirect discrimination based on socioeconomic background
- Favors urban, elite, and well-connected candidates

### 2.2 Bias Pipeline Mapping

```
[Historical Hiring Decisions]
        ↓
[Training Data Collection] → Bias Point #1: Skewed hiring data (gender, region, schools)
        ↓
[Feature Selection]        → Bias Point #2: Inclusion of biased proxy features
        ↓
[Model Training]           → Bias Point #3: Learning and amplifying historical bias
        ↓
[Deployment & Scoring]     → Bias Point #4: Discriminatory ranking outcomes
        ↓
[Biased Outcomes]
```

### 2.3 Feature Risk Analysis

**High-Risk Features:**

- University attended
- Location
- LinkedIn connections
- Previous companies

**Moderate Risk:**

- References
- Extracurricular activities

**Low Risk:**

- Skills
- Years of experience

## 3. Mitigation Plan

### 3.1 Immediate Actions (This Week)

**Feature Removal:**

- Remove or reduce weight of:
  - University
  - Location
  - LinkedIn connections

**Threshold Adjustments:**

- Introduce fairness-aware scoring thresholds
- Ensure balanced selection rates across groups

**Output Monitoring:**

Track weekly:

- Gender selection ratio
- Regional distribution
- Average scores by demographic

### 3.2 Short-Term Actions (1–3 Months)

**Data Collection:**

- Gather more diverse training data
- Include underrepresented groups and job categories

**Model Retraining:**

- Use balanced datasets
- Apply fairness-aware algorithms

**Human Oversight:**

- Add manual review for top-ranked candidates
- Audit decisions regularly

### 3.3 Long-Term Actions (6–12 Months)

**Fairness Metrics to Adopt:**

- Equal Opportunity (recommended)
- Demographic Parity (optional comparison)

**Process Changes:**

- Limit over-reliance on AI rankings
- Introduce hybrid decision-making (AI + human)

**Transparency Measures:**

- Inform candidates about AI usage
- Provide explanation of scoring criteria
- Allow appeals or review requests

## 4. Success Metrics

- Reduced score gap between demographics
- Balanced representation in top-ranked candidates
- Improved diversity across hires
- Weekly fairness reports show consistent improvement

## 5. Timeline

| Phase | Timeline | Key Actions |
|-------|----------|-------------|
| Immediate | Week 1 | Feature removal, monitoring setup |
| Short-Term | 1–3 Months | Data balancing, retraining |
| Long-Term | 6–12 Months | Fairness metrics, transparency |

## 6. Conclusion

HireScore demonstrates **significant bias risks** driven by historical data, proxy variables, and imbalanced sampling. Without intervention, the system may reinforce inequality. Implementing fairness controls, improving data quality, and increasing transparency will be critical to building a trustworthy AI system.
