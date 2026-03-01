# Evaluation — B2: Security & Data-Exposure Risks

## Evaluation Description

The review should:
- Identify specific data being exposed (IDs, emails, roles)
- Identify where input is not validated
- Identify URL/injection risks
<!-- - Consider the 2-second timeout constraint -->
- Provide actionable, security-focused feedback
- Prioritize risks related to frontend data exposure

---

### Bad Example

**Characteristics of Output:**
- Mentions URL concatenation risks
- Mentions lack of authentication
- Mentions exposure of PII (emails)
- Mentions hardcoded endpoints
- Mentions general SSL verification concerns
- Does not prioritize internal user ID exposure
- Does not reference timeout requirement
- Includes some speculative or environment-dependent risks

**Why This Is Weaker**
- The answer is broad and somewhat generic.
- Some concerns (e.g., SSL verification settings) are speculative.
- It does not prioritize risks based on stated system constraints.
- It lacks focus on frontend exposure requirements.

---

### Good Example

**Characteristics of Output:**
- Explicitly flags exposure of internal user IDs
- Clearly identifies exposure of emails as PII
<!-- - Connects timeout omission to availability risk -->
- Identifies bulk user enumeration risk
- Provides specific, actionable mitigations
- Focuses on real data-exposure paths in this code

**Why This Is Stronger**
- Risks are tied directly to system constraints.
- Prioritization matches real security impact.
- Feedback is specific to this codebase.
- Suggestions are actionable and relevant.
