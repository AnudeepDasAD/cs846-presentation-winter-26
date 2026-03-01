# Evaluation - B1: Code Review

## Evaluation Description

The review should:

- Identify correctness and error-handling issues
- Consider performance impact (dashboard responsiveness)
- Consider security concerns (data exposed to UI)
<!-- - Address API timeout requirement (2 seconds) -->
- Provide actionable, context-aware feedback

---

### Bad Example

**Characteristics of Output:**
- Mentions imports inside functions
- Mentions hardcoded URLs
- Mentions general error handling
- Mentions inefficient API usage
- Provides mostly style and maintainability suggestions
- Does not connect issues to responsiveness or timeout constraints
- Does not prioritize risks based on dashboard usage

**Why This Is Weak**
- Feedback is generic and context-agnostic.
- It evaluates the code as standalone logic.
- It does not assess impact on responsiveness or UI constraints.
- It lacks prioritization based on system requirements.

---

### Good Example

**Prompt included:**
<!-- - 2-second API timeout requirement -->
- Dashboard must stay responsive
- Do not expose internal user IDs
- Explicit review focus (correctness, performance, security, edge cases)

**Characteristics of Output:**
- Flags missing timeout handling
- Connects redundant API calls to dashboard latency
- Mentions exposure of internal user IDs
- Evaluates impact on UI responsiveness
- Provides targeted, constraint-aware suggestions

**Why This Is Strong**
- Feedback is aligned with real system constraints.
- Risks are prioritized based on impact.
- Output resembles a production-level PR review.
