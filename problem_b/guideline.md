# Code Review Guideline

## Guideline: Provide full context when asking for code review

**Description:**  
Design your prompt so the LLM receives a clear “job description” before reviewing code. Include sufficient and relevant context—goal of the change, constraints the code must satisfy, and what aspects you want reviewed—so the problem space is constrained and the feedback is targeted.

**Reasoning:**  
This serves several purposes.

- **Clarifies intent.** The process of writing goal, constraints, and review focus forces you to articulate what the change is for, what must be preserved, and what “good” feedback looks like.
- **Reduces generic feedback.** Without context, models often default to generic comments (naming, style, add type hints). With goal and constraints, the model can tie feedback to your actual PR (e.g. “don’t expose internal `id` here,” “add timeout to this call”).
- **Improves actionability.** Explicit review criteria (correctness, error handling, security, performance, edge cases) yield concrete, actionable comments rather than vague suggestions.
- **Structured prompts perform better.** Prior work on prompt patterns suggests that structured, high-information-density prompts lead to more correct and evaluable outputs than free-text “review this” prompts.

**Good Example:**

```
You are reviewing a code / pull request.

Constraints: Must not expose internal user IDs to the front end; API calls should timeout after 2 seconds; dashboard must stay responsive.

Review the following code for: correctness, error handling, security (what we expose to the UI), performance (e.g., unnecessary API calls), and edge cases. Give actionable comments as if posting on GitHub.
```

**Bad Example:**

```
Please review the code above and give feedback on correctness, security, and edge cases.
```

<!-- 
Guideline: Request AI-Led Contextual Summaries Before Issue Detection

Description:
When asking an LLM to review a pull request, first request a structured summary of the PR’s intent, affected components, and major changes before asking for issue detection. Use the summary to guide deeper inspection.

Reasoning:
The study found that developers struggle with context switching and insufficient contextual information when reviewing pull requests, especially large or unfamiliar ones. Participants preferred AI-led summaries that provided an overview before detailed analysis. This reduced cognitive load, improved understanding, and increased perceived usefulness of the AI assistant. Requesting a contextual summary before issue detection aligns with the empirically preferred “AI-led” review mode identified in the paper.

Good Example:
This is a pull request in a service I’m not familiar with.
First:
- Summarize what the PR is trying to achieve.
- Identify which components are affected.
- Highlight complex or high-risk areas.
Then provide specific review comments.

Bad Example:
Review this pull request and list problems.
 -->