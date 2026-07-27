# Project: Governance Review for QuickLoan Mobile

**Company:** QuickLoan Mobile — a Rwandan digital microloan app disbursing and collecting entirely via mobile money

**Regulators in scope:** National Bank of Rwanda (BNR), which supervises payment service providers and digital lenders; National Cyber Security Authority (NCSA), supervisory authority for Rwanda's Law N° 058/2021 relating to the Protection of Personal Data and Privacy

**Status:** Required Project

---

## 1. Background

QuickLoan Mobile lets users in any of Rwanda's five provinces apply for a small, short-term loan from their phone. The application form collects income, national ID, phone number, and a few behavioral signals (mobile money transaction history, on-time repayment history if returning), then an ML model scores the application and either approves, rejects, or refers it for manual review. Approved loans are disbursed to the applicant's mobile money wallet within minutes.

This review was triggered by two things arriving in the same month: a spike in loan-approval complaints from applicants in the Northern and Western provinces relative to Kigali, and a routine internal audit that could not fully explain, from the logs alone, *why* a specific application had been rejected. Both point to the same underlying gap — the pipeline was built for speed, not governance.

## 2. Governance Review Table

| Section | Issue / Definition | Impact | Suggested Fix / Mitigation |
|---|---|---|---|
| **1. Data Quality Risk** | Incomplete and inconsistent applicant data — missing income fields for informal-sector workers, inconsistent phone number formats (`0788...`, `+250788...`, `788...`) | Inaccurate ML predictions, inconsistent applicant matching across repeat applications, and unreliable downstream reporting | Enforce required fields and format validation at the input stage; normalize phone numbers to a single `+250XXXXXXXXX` format via a preprocessing pipeline before the data reaches scoring |
| **2. Legal & Compliance Risk** | No explicit, informed consent captured before collecting sensitive personal and financial data | Violates Rwanda's Law N° 058/2021 (protection of personal data and privacy), risking regulatory sanction from the NCSA and loss of applicant trust | Implement an explicit consent-management flow (opt-in checkbox with a plain-language explanation of what is collected and why) with an audit log of every consent event; apply data minimization so only fields the scoring model actually uses are collected |
| **Data Classification** | Applicant income, national ID, and mobile money transaction history are **Sensitive** | Exposure of this data could enable fraud, identity theft, or financial harm to applicants | Encrypt sensitive fields at rest and in transit; restrict access via role-based access control (RBAC) so, e.g., customer-support staff cannot view raw national ID numbers |
| **3. Bias & Fairness Risk** | The scoring model was trained on QuickLoan's own historical loan book, which itself reflects who was approved in the past, not who was actually creditworthy | Unfair approvals/rejections correlated with province and gender rather than repayment ability, disproportionately affecting applicants outside Kigali | Introduce fairness checks before deployment; monitor model outputs by demographic group in production; retrain periodically with a rebalanced dataset that corrects for historical approval skew |
| **Source of Bias** | Historical loan data reflects pre-existing disparities in formal financial access between Kigali and other provinces | Reinforces the exact urban/rural access gap the product was meant to help close | Use bias-detection tooling on the training set before each retrain; add fairness constraints (e.g. equalized approval-rate bounds across provinces) to the training objective |
| **4. Storytelling / Reporting Recommendation** | Metric: **Approval Rate by Province and by Gender** (percentage of approved applications per group) | Makes disparate treatment visible to risk and compliance teams instead of only showing up as a support-ticket spike months later | Grouped bar chart comparing approval rate across the five provinces, and a second chart across gender, refreshed with every model release |
| **Visualization Type** | Grouped bar chart | Makes disparities immediately legible to a non-technical risk committee | Supports routine fairness reporting rather than one-off audits |
| **Why It Matters** | Ensures transparency and fairness in an automated lending decision that materially affects people's access to credit | Builds regulator and customer trust, and supports compliance with both financial-sector conduct expectations and data protection law | Underpins ethical AI governance for the product as a whole |

## 3. Corrected Data Flow (Annotated Fixes)

The original pipeline collected everything the form could ask for, stored it indefinitely, and fed it straight into scoring with no logging of *why* a decision was made. The corrected flow below inserts a governance control at each stage where one was missing.

```
[1. Data Collection]        → Corrected: collect only income, national ID, phone, and
                               mobile money transaction history — nothing else, per the
                               data minimization principle.
        ↓
[2. Consent Verification]   → NEW STAGE: applicant must explicitly opt in before any
                               data leaves the form, satisfying Law N° 058/2021.
        ↓
[3. Data Classification &   → NEW STAGE: national ID, income, and transaction history
    Retention Tagging]        are tagged Sensitive; a retention period is attached
                               (see Section 4) rather than storing indefinitely.
        ↓
[4. Preprocessing &          → Corrected: phone-number normalization, required-field
    Validation]                 enforcement, and outlier/format checks (Lab 1's rules
                               apply directly here) run before the record reaches the model.
        ↓
[5. ML Scoring]              → Unchanged mechanically, but now trained on a
                               fairness-checked, rebalanced dataset (Section 2, row 3).
        ↓
[6. Decision]                 → Approve / Reject / Manual Review
        ↓
[7. Decision Logging]        → NEW STAGE: every decision is logged with the score and
                               the top contributing factors ("explanation metadata"),
                               so a rejected applicant's case can be reconstructed on
                               request — this is what the original audit couldn't do.
        ↓
[8. Disbursement / Rejection → Unchanged: mobile money payout or rejection notice sent
    Notice]                     to the applicant.
        ↓
[9. Analytics & Reporting]   → NEW STAGE: before reaching the fairness dashboard or any
                               internal analytics tool, sensitive fields (national ID,
                               raw phone number) are masked/anonymized.
        ↓
[10. Third-Party Sharing]    → NEW STAGE (if it occurs at all, e.g. credit bureau
                               reporting): PII is masked or aggregated first, and every
                               such transfer is logged for the same audit trail as stage 7.
```

**Corrections applied, and why:**

- **Limit Data Collection (Stage 1)** — collecting only what the model actually consumes is the data-minimization principle in practice, not just in policy. Every extra field collected "just in case" is extra breach exposure with no corresponding scoring benefit.
- **Add Consent Verification (Stage 2)** — a consent checkbox buried in a terms-of-service wall of text does not meet the spirit of informed consent under Rwanda's data protection law; the fix is a specific, plain-language opt-in tied to an audit log entry.
- **Data Classification & Retention (Stage 3)** — classifying income/national ID/transaction history as Sensitive at ingestion, with a retention period attached at the same moment, prevents the common failure mode of "sensitive data that nobody ever decided how long to keep."
- **Preprocessing Standards (Stage 4)** — the same validation and normalization discipline documented in Lab 1 (data cleansing) applies here directly; a lending model is only as fair as the data quality feeding it.
- **Decision Logging (Stage 7)** — this is the single highest-leverage fix in the whole pipeline: without it, neither an internal auditor nor a rejected applicant can ever get an answer to "why," which is both a fairness problem and, increasingly, a regulatory expectation for automated financial decisions.
- **Data Masking (Stages 9–10)** — anonymizing before analytics or any third-party sharing (e.g., credit bureau reporting) protects applicant privacy without blocking the fairness monitoring that Section 2's reporting recommendation depends on — the fairness dashboard needs the *group label* (province, gender), not the *identity* of the applicant.

## 4. Data Retention Guidance

| Data Category | Suggested Retention | Basis |
|---|---|---|
| Application form data (rejected applications) | 12 months, then anonymized for model-training use only | Balances applicant privacy against the model's need for a representative training set that includes rejections, not only approvals |
| Approved loan records (income, ID, repayment history) | Duration of any financial-sector record-keeping requirement set by BNR regulation, then reviewed for deletion | Confirm the current BNR-mandated period before finalizing a retention schedule |
| Consent logs | Retained for the life of the account plus a defined grace period | Needed to demonstrate compliance under Law N° 058/2021's accountability expectations |
| Decision-logging metadata (Stage 7) | Retained at least as long as the underlying loan record | Needed to answer any future audit or applicant inquiry about a specific decision |

## 5. Summary of Review Process

This review examined QuickLoan Mobile's lending pipeline using the same core data governance lenses applied throughout this lab series — the data lifecycle and data classification frameworks — walking each stage from collection through scoring, disbursement, and reporting to find where risk was introduced. Excessive data collection at intake violated the data-minimization principle before the model ever saw a single record; the complete absence of a consent-verification step was a direct compliance gap under Rwanda's Law N° 058/2021; and classifying income, national ID, and transaction history as Sensitive immediately justified the encryption and RBAC controls recommended in Section 2.

Underneath the compliance findings sat a data-quality problem: inconsistent phone formats and missing income fields for informal-sector applicants degraded the ML model's inputs in exactly the way Lab 1 documented for a different dataset — the same discipline (validation at entry, standardized formats) applies here. And underneath *that* sat the fairness finding that matters most for this specific product: a model trained on QuickLoan's own approval history will, by construction, reproduce whatever access gap already existed between Kigali and the other four provinces, unless that skew is explicitly corrected for during training.

The proposed "Approval Rate by Province and Gender" metric, rendered as a grouped bar chart and reviewed at every model release, turns that fairness risk from something a support-ticket spike eventually reveals into something the risk committee can see before it deploys. Combined with decision logging (Stage 7) — the fix that lets any single rejection actually be explained after the fact — these changes move QuickLoan Mobile from a pipeline that was fast and opaque to one that is fast *and* accountable, which is the standard a financial product making automated credit decisions about people's livelihoods should be held to.
