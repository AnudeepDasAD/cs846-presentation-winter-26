# Evaluation — B3: Edge Cases & Error Conditions

## Evaluation Description

The answer should:
- Identify edge cases per function (not just general categories)
- Consider timeout and responsiveness constraints
- Consider data-exposure constraints (internal IDs)
- Suggest actionable, testable scenarios
- Be specific about inputs, failures, and API behavior

---

### Bad Example

**Characteristics of Output:**
- Mentions general API/network failures
- Mentions invalid input formats
- Mentions malformed JSON
- Mentions large datasets
- Mentions authentication concerns
- Does not prioritize dashboard responsiveness
- Does not emphasize internal ID exposure
- Mostly generic categories of failures

**Why This Is Weaker**
- The answer is broad and high-level.
- Edge cases are not strongly tied to specific functions.
- It does not reflect the system constraints.
- It lacks prioritization based on business impact.

---

### Good Example

**Characteristics of Output:**
- Breaks tests down by function (get_user_display, get_user_list, etc.)
<!-- - Includes timeout scenarios tied to responsiveness -->
- Explicitly includes tests ensuring internal IDs are not exposed
- Identifies fallback behavior when APIs fail
- Includes malformed/missing field scenarios
- Suggests mocking and concrete testing approaches

**Why This Is Stronger**
- Edge cases are structured per function.
- Tests reflect real system constraints.
<!-- - Responsiveness and timeout risks are explicitly covered. -->
- Data-exposure concerns are turned into concrete test cases.
- Suggestions are practical and implementable.
