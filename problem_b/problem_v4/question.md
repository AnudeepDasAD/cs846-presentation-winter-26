<!-- # Problem B: Backend PR Review Simulation

**PR description:** `problem_b/problem_v4/PR.md`  
**Diff to review:** `problem_b/problem_v4/PR.diff`  
**Code to review:** `problem_b/problem_v4/backend/user_helpers.py`

Use the PR description and diff when prompting. They provide the goal, constraints, scope, and what is out of scope.

---

## Task 1: Structured Code Review

Perform a structured PR review.

You want feedback on:

- Correctness  
- Error handling  
- Security (what is exposed to the UI)  
- Performance (e.g., unnecessary API calls or latency risks)

For each issue:

- Tie it directly to a constraint in the PR.
- Assign severity: **Blocker / Major / Minor / Question**.
- End with a merge decision:
  - Approve
  - Request Changes
  - Reject

---

## Task 2: Security and Data-Exposure Concerns

Identify security concerns and data-exposure risks in the PR.

Be specific:

- Which data might be exposed where?
- Which inputs are not validated?
- Any injection or URL-construction risks?
- Are any backend-only helpers leaking frontend-visible data?

Respect the PR’s scope and out-of-scope notes.

---

## Task 3: Constraint Alignment Audit

Using the PR description and implementation:

1. Identify where the implementation violates the stated constraints.
2. Quote the specific constraint being violated.
3. Explain why the violation is risky in production.
4. Classify each violation as:
   - Blocker
   - Major
   - Minor

Focus especially on:
- Hot-path performance
- External API failure handling
- Data exposure boundaries

---

## Task 4: High-Risk Areas and Review Focus

Using the PR description and code:

- Identify the highest-risk or most complex areas.
- Explain why they deserve review attention.
- Suggest what the review should prioritize.
- Suggest what should be deprioritized per the PR’s “Out of scope” section. -->

# Problem B: Backend PR Review Simulation

**PR description:** `problem_b/problem_v4/PR.md`  
**Diff to review:** `problem_b/problem_v4/PR.diff`  
**Code to review:** `problem_b/problem_v4/backend/user_helpers.py`

Use the PR description and diff when prompting. They provide the goal of the change, the stated constraints, the system boundaries, and what is explicitly out of scope.

---

## Task 1: Structured Code Review

Provide a structured review of this pull request as if you were reviewing it before merge. Begin by briefly summarizing the intent of the PR and the components it affects. Then evaluate the changes with respect to correctness, error handling, security (particularly what data may be exposed to the frontend), and performance (including latency risks and unnecessary API calls). 

For each issue you identify, clearly tie your comment to a specific constraint or requirement stated in the PR description. Indicate the severity of each issue as **Blocker**, **Major**, **Minor**, or **Question**, and conclude your review with a merge decision (Approve, Request Changes, or Reject), explaining your reasoning.

---

## Task 2: Security and Data-Exposure Analysis

Analyze the security implications of the changes introduced in this PR. Specifically, consider whether any internal identifiers or sensitive data might be exposed beyond intended boundaries. Identify where inputs are accepted and whether they are validated or safely handled. Discuss any risks related to URL construction, external API calls, or trust boundaries between backend and frontend. 

Your analysis should remain within the scope defined in the PR and avoid suggesting architectural redesigns or changes that are explicitly listed as out of scope.

---

## Task 3: Constraint Alignment Audit

Examine the implementation in light of the constraints and requirements stated in the PR description. Identify any areas where the implementation appears to violate or inadequately satisfy those constraints. For each such case, quote or reference the relevant constraint and explain how the code diverges from it. 

Discuss why each divergence may pose a risk in production, particularly in the context of hot-path execution and external API dependency. Classify each violation as a **Blocker**, **Major**, or **Minor** issue and justify your classification.

---

## Task 4: High-Risk Areas and Review Focus

Based on the PR description and the implementation, identify the areas of highest technical or operational risk. Explain why these areas deserve the most review attention, especially given that the code affects a hot path and is intended for near-term production deployment. 

Finally, describe which aspects of the PR should be deprioritized during review because they fall outside the defined scope or do not materially impact correctness, security, or performance.