## Data Access Decision Simulator

### Context

You're a DevOps engineer at EduConnect Ghana, an ed-tech platform serving 50,000+ students.

The company has a data classification policy with three tiers:

- **PUBLIC:** Marketing materials, public course catalogs
- **INTERNAL:** Aggregated analytics, internal reports
- **CONFIDENTIAL:** Student PII, grades, payment information

### The Scenario

It's Monday morning, and you've received three data access requests in your ticketing system. For each request, you must make a decision following the data lifecycle and access control principles.

#### Request 1: Marketing Campaign

**From:** Sarah Owusu, Marketing Manager

**Request:** "I need the full student database (names, emails, phone numbers, course enrollments) to launch our new referral campaign. This is urgent—campaign starts Friday!"

**Data Classification:** Email addresses (CONFIDENTIAL), Names (INTERNAL), Enrollments (INTERNAL)

**Current Access Level:** Sarah has INTERNAL access only

**Your Decision Points:**

- Approve, Deny, or Conditionally Approve?
- What data lifecycle stage is this? (Create/Store/Use/Share/Archive/Destroy)
- What principle of least privilege considerations apply?
- If conditional approval, what safeguards must be in place?
- Who else should be consulted before final decision?

#### Request 2: Analytics Partnership

**From:** David Mensah, Head of Product

**Request:** "I've signed a partnership with DataInsights Inc. (US-based) to analyze our student learning patterns. They need access to our AWS database to run their algorithms. Login credentials attached."

**Data Classification:** Student activity logs (INTERNAL), Student profiles (CONFIDENTIAL)

**Compliance Note:** Company is subject to Ghana DPA

**Your Decision Points:**

- Approve, Deny, or Conditionally Approve?
- What data lifecycle stage is this?
- What are the legal/compliance red flags?
- If data must be shared, what controls are needed?
- What documentation must be in place?

#### Request 3: Archive & Deletion

**From:** Comfort Asante, Customer Support Lead

**Request:** "A student, James Boateng, has requested full deletion of his account and all associated data per his 'right to be forgotten.' He completed his last course 6 months ago and has no pending payments. How should I proceed?"

**Data Classification:** All student data (CONFIDENTIAL)

**Current Status:** Account inactive, no outstanding obligations

**Your Decision Points:**

- Can the request be fulfilled? Why or why not?
- What data lifecycle stage is this?
- What legal obligations exist under Ghana's DPA?
- What data, if any, can be retained and why?
- What process should Customer Support follow?

### Your Deliverable

For each request, complete a "Data Access Decision Form" with:

- **Decision:** Approve/Deny/Conditional
- **Lifecycle Stage:** Which stage this represents
- **Justification:** Citing classification policy, least privilege, or compliance requirements
- **Action Steps:** Specific next steps to implement your decision

### Success Criteria

- Correctly applied data classification rules
- Identified appropriate lifecycle stages
- Demonstrated understanding of least privilege
- Recognized compliance implications
