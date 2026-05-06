# QuickLoan Mobile Ethical Data Review

**Role: Data Governance Consultant**

---

## Deliverable 1: Governance Review Card

### Governance Risk Analysis Table

| Section | Issue / Definition | Impact | Suggested Fix / Mitigation |
|---------|-------------------|--------|---------------------------|
| **1. Data Quality Risk** | Incomplete and inconsistently formatted customer data (e.g., missing fields, inconsistent phone/email formats) | Poor model accuracy leading to incorrect loan approvals or rejections | Implement data validation rules at input stage, enforce standardized formats, and use automated data cleaning pipelines |
| **2. Legal & Compliance Risk** | No explicit user consent before collecting and processing personal data (violates Ghana Data Protection Act, Act 843) | Legal penalties, loss of customer trust, and potential regulatory shutdown | Introduce explicit consent capture (opt-in), clear privacy policy, and consent logging system |
| **Data Classification** | Sensitive (PII such as phone numbers, financial behavior, contacts) | — | Apply strict access controls, encryption, and role-based access to sensitive data |
| **3. Bias & Fairness Risk** | ML model trained on biased or incomplete datasets (e.g., excluding certain demographic groups) | Discriminatory loan decisions, reputational damage, and ethical violations | Conduct bias audits, use diverse datasets, and implement fairness testing during model evaluation |

### Storytelling / Reporting Recommendation

- **Metric to Monitor:** Loan Approval Rate by Demographic Group
- **Definition:** Percentage of approved vs rejected loan applications segmented by demographic factors (e.g., age group, location, income bracket)
- **Visualization Type:** Grouped Bar Chart
- **Why It Matters:** Helps detect and correct unfair bias in automated decision-making, ensuring transparency and ethical compliance

---

## Deliverable 2: Corrected Data Flow Diagram (Annotations)

**1. Reduce Data Collection (Step 1 – Mobile App)**
- *Change:* Limit data collection to only necessary fields (exclude contact list access)
- *Why:* Enforces data minimization and reduces privacy risk

**2. Implement Consent Management (Step 2 → Step 3)**
- *Change:* Add user consent capture and logging before storing data
- *Why:* Ensures compliance with Ghana's Data Protection Act (Act 843)

**3. Apply Data Classification & Retention Policies (Step 3 – Raw Data DB)**
- *Change:* Tag data as Sensitive and define retention limits
- *Why:* Prevents misuse and ensures regulatory compliance

**4. Define Preprocessing Standards (Step 4 – Preprocessing Service)**
- *Change:* Add data validation, normalization, and cleaning rules
- *Why:* Improves data quality and model reliability

**5. Enable Logging & Transparency (Step 7 – Decision Service)**
- *Change:* Log all decisions with reasoning (model outputs, scores)
- *Why:* Supports auditability and accountability

**6. Apply Data Masking/Anonymization (Steps 9 & 10 – Analytics & 3rd Party)**
- *Change:* Mask or anonymize PII before analytics or sharing
- *Why:* Protects user privacy and reduces exposure risk

---

## Deliverable 3: Summary of Review Process

The review process was conducted using core data governance principles, particularly the data lifecycle and data classification frameworks. Each stage of the QuickLoan data pipeline — from data collection to processing, storage, and sharing — was examined to identify risks related to quality, compliance, and ethics. By mapping how data flows through the system, it became clear where excessive collection, lack of validation, and absence of controls introduced vulnerabilities.

Data classification was critical in identifying that most of the information handled by QuickLoan qualifies as sensitive personal data under Ghana's Data Protection Act (Act 843). This informed recommendations such as encryption, restricted access, and strict retention policies. Applying data minimization principles revealed unnecessary data collection (such as contact lists), which increases both compliance risk and ethical concerns.

The proposed metric, loan approval rate by demographic group, ensures ethical and transparent governance by providing visibility into how automated decisions impact different user segments. By tracking this metric over time, the organization can detect patterns of bias and take corrective action. This aligns with responsible AI practices and promotes fairness in decision-making.

Overall, the recommended improvements enhance data quality, ensure regulatory compliance, and strengthen trust in the platform. By embedding governance controls across the data lifecycle, QuickLoan can scale responsibly while maintaining ethical standards.
