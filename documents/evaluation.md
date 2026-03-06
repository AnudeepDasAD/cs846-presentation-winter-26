# Week 10 Evaluation: CodeReview / PR

**Authors:** [Neel Sanjaybhai Faganiya, Ibrahim Mohammed Sayem, Felix Wang]


## 1. Evaluation Criteria

This section defines how students can determine whether they solved the example problems correctly.

Criteria should be applicable to any problem in this topic.

* Criteria 1
* Criteria 2
* Criteria n

---

## 2. Evalation specifically for Example Problems

### Problem A_1: [Title]

**Evaluation Description:**  
Describe the evaluation criteria clearly and precisely.

**Code:**  
// Include all necessary code here that is the correct answer.

---

### Problem A_2: [Title]

**Evaluation Description:**  
Describe the evaluation criteria clearly and precisely.

**Code:**  
// Include all necessary code here that is the correct answer.

---

### Problem A_n: [Title]

**Evaluation Description:**  
Describe the evaluation criteria clearly and precisely.

**Code:**  
// Include all necessary code here that is the correct answer.

---

### Problem C: Pull Request Supply Chain Review

**Good Example:**
Fork the current repository, then go to:

Settings -> Security -> Advanced Security -> Dependabot -> Enable Dependabot Alerts -> Enable.

Trigger a push on main branch, then go to:

Security -> Vulnerability Alerts -> Dependabot.

**Bad Prompt Example:**

`You are an experienced coding agent, please verify the dependency versions for me: [path-to-file].`

**Good Example Output:**

In Security -> Vulnerability Alerts -> Dependabot, this vulnerabile version of package will show up:

|             | CVE-2026-3304           |
|-------------|-------------------------------------|
| Severity   | High |
| CVSS Score | 8.7/10 |
|  Description  | Multer is a node.js middleware for handling `multipart/form-data`. A vulnerability in Multer prior to version 2.1.0 allows an attacker to trigger a Denial of Service (DoS) by sending malformed requests, potentially causing resource exhaustion. Users should upgrade to version 2.1.0 to receive a patch. No known workarounds are available.                                    |
| NVD Published Date | 02/27/2026 |
| Attack Vector | Network |
| Attack Complexity | Low |
| Attack Requirement | None |
| Privileges Required | None | 
| User Interaction | None |
| Related CWE | CWE-459: Incomplete Cleanup |

**Bad Example Output:**

GPT 4.1: 
```
......

Conclusion & recommendations
* The “after” lock file is consistent with the updated package.json and accurately captures all newly introduced dev dependencies.
* npm ci runs without errors, so the lock file is valid and usable.
* There’s nothing broken in the dependency update itself; the only “issue” is that running npm test returns an error because there are no tests. If you plan to use Jest, add test files or tweak the test script.
```

**Why it's bad:**

This problem will not likely be resolved by simply letting LLMs inspect the dependency files before and after PR. LLMs are not designed to reliably reason over large, highly-structured lockfile. In this example, multer package versions < 2.1.0 are known to be severely vulnerable to Denial of Service attacks (CVE-2026-3304). But the Copilot failed to capture it.

More details can be found on the National Vulnerability Database (NVD)'s official website [https://nvd.nist.gov/vuln/detail/CVE-2026-3304](https://nvd.nist.gov/vuln/detail/CVE-2026-3304).

---

### Problem D: Northwind Signal Project PR Review

#### Problem D.1: Usage Audit Feature PR Review

**Good Prompt Example:**

In `.github/instructions/typescript.instructions.md`, clearly identify testing and security expectations.

```
---
applyTo: "**/*.ts"
---
# TypeScript Coding Standards
This file defines our TypeScript coding conventions for Copilot code review.

## Error Handling

- Always handle promise rejections with `try/catch` or `.catch()`.
- Use custom error classes for application-specific errors.

## Security

- Be extremely cautious with binary executables. Any binary executable should be avoided by default. 

## Testing

- Write unit tests for all exported functions.
- Use [Jest](https://jestjs.io/) for all testing.
- Name test files as `<filename>.test.ts`.

## Example

```typescript
// Good
interface User {
  id: number;
  name: string;
}

const fetchUser = async (id: number): Promise<User> => {
  try {
    // ...fetch logic
  } catch (error) {
    // handle error
  }
};

// Bad
interface user {
  Id: number;
  Name: string;
}

async function FetchUser(Id) {
  // ...fetch logic, no error handling
}
```

**Bad Prompt Example:**

1. No instruction file provided. 
2. Expectations for binary executables are not listed in a security-related section in the instruction. 

**Good Output Example:**

```
...

* The backend (usage.service.ts) uses a vendor-provided binary executable (audit_writer) to write audit events.
TypeScript instructions explicitly warn to avoid binary executables by default for security reasons.
* No unit tests were added for the new backend logic, especially for the audit endpoints.
Instructions require unit tests for all exported functions, using Jest.
* The code does not use custom error classes for application-specific errors; it relies on NestJS exceptions.
Instructions recommend custom error classes for better error handling.
* The frontend and backend changes are otherwise modular and follow naming conventions, code style, and error handling for promises.
```

**Bad Output Example:**

```
...

Conclusion
This PR is well-structured and achieves the goal of integrating a vendor-supplied audit workflow. The encapsulation of vendor logic is appropriate for compliance. The API and UI changes are clear and functional. With minor improvements to error handling and security, this is ready to merge.
```

**Why it's bad:**

The most severe issue contained in this PR is that, the binary executable file under the folder `problem_d/problem_d_backend/src/vendor` called `audit_writer` is simulating some malicious behaviours. Don't worry though, it's not really malicious, you can find the file in the `audit_writer-file` branch. All it does is to create a data record with a message: `This is a malicious event in the binary file, your database is now compromised`. However, the real security concern is not the message itself. The core issue is that: A precompiled binary executable has been committed directly into the repository and invoked by backend logic!

Any PR review comments questioning the necessity of doing so, asking for cryptographic evidence, and ways to reproduce it is a good solution. 

There are other severe issues in this PR, your solution is good if you find more or all of them: 
```
1. Missing Type Safety on Event Handler
File: main.ts
Lines: ~382-395
Issue: The auditButton might be null, but the code proceeds without proper guards in some places.

2. Implicit any Type on Error
File: main.ts, usage.service.ts
Lines: ~364-365 and ~393-394, ~46-50
Issue: Bare catch blocks without typing violate the TypeScript standard of avoiding any types.

3. Insufficient Input Validation
File: main.ts
Lines: ~384-388
Issue: No validation of the API response before rendering. The entries array could be malformed.

4. Arbitrary Command Execution Vulnerability
File: usage.service.ts
Lines: ~32-40
Issue: Using execFile with unsanitized user input (message) as a command argument.
Risk: Although execFile is safer than exec, the message parameter should be further validated/sanitized.

5. Missing Input Validation: Message Length Enforcement
File: usage.controller.ts
Lines: ~12-17
Issue: Message length limit (1000 chars) is enforced but could be externalized as a constant.
Recommendation: Create a constants file with validation rules.

6. There are no tests
No unit tests for usage.service.ts (following TypeScript standard that requires unit tests for all exported functions)
No integration tests for the new endpoints (GET /usage/audit-log, POST /usage/audit)
No frontend tests for the new audit panel UI and event handlers
```

#### Problem D.2: Annual Report Generation PR Review

**Good Prompt Example:**

1. In `.github/instructions/typescript.instructions.md`, clearly identify naming convention expectations and code style expectations.

```
---
applyTo: "**/*.ts"
---
# TypeScript Coding Standards
This file defines our TypeScript coding conventions for Copilot code review.

## Naming Conventions

- Use `camelCase` for variables and functions.
- Use `PascalCase` for class and interface names.
- Prefix private variables with `_`.

## Code Style

- Prefer `const` over `let` when variables are not reassigned.
- Use arrow functions for anonymous callbacks.
- Avoid using `any` type; specify more precise types whenever possible.
- Limit line length to 100 characters.

## Error Handling

- Always handle promise rejections with `try/catch` or `.catch()`.
- Use custom error classes for application-specific errors.

## Security

- Be extremely cautious with binary executables. Any binary executable should be avoided by default. 

## Testing

- Write unit tests for all exported functions.
- Use [Jest](https://jestjs.io/) for all testing.
- Name test files as `<filename>.test.ts`.
- All test cases should pass. 
- Test coverage should reach at least 70%.

## Example

```typescript
// Good
interface User {
  id: number;
  name: string;
}

const fetchUser = async (id: number): Promise<User> => {
  try {
    // ...fetch logic
  } catch (error) {
    // handle error
  }
};

// Bad
interface user {
  Id: number;
  Name: string;
}

async function FetchUser(Id) {
  // ...fetch logic, no error handling
}
```

2. In `.github/semgrep.yml`, clearly formulated static anlysis scan patterns for detecting camelCases, PascalCases, and `any` abuse.

For example:

```
  - id: typescript-camelcase-functions
    message: "Use camelCase for function names."
    languages: [typescript]
    severity: WARNING
    patterns:
      - pattern: "function $F(...) { ... }"
      - metavariable-regex:
          metavariable: "$F"
          regex: "(^[A-Z]|.*_.*)"
```

or

```
  - id: typescript-no-any
    message: "Avoid 'any' in TypeScript. Use a specific type or 'unknown'."
    languages: [typescript]
    severity: WARNING
    pattern-either:
      - pattern: "const $X: any = $Y"
      - pattern: "let $X: any = $Y"
      - pattern: "var $X: any = $Y"
      - pattern: "($P: any) => $BODY"
      - pattern: "function $F($P: any, ...$REST) { ... }"
      - pattern: "function $F(...$ARGS: any[]) { ... }"
      - pattern: "class $C { $K: any; }"
      - pattern: "function $F(...$ARGS): any { ... }"
      - pattern: "$M(...$ARGS): any { ... }"
```

etc. 

**Bad Prompt Example:**

1. No instruction file provided. 
2. Expectations for testing are not listed in the instruction file.
3. Static Analysis Tool is not used. 

**Good Output Example:**

```
...
Fix the unit test expectation:
  In problem_d/problem_d_backend/src/modules/reports/reports.service.spec.ts change
    expect(result.year).toBe(2026);
  to expect(result.year).toBe(2025);
Rationale: align test with input and service behavior.
...
Minor code style: RenderReport and LoadReport use any heavily. Consider adding stronger types.
...
Variable naming:
Rename EndDate → endDate in reports.service.ts for consistency.
...
Decision

Recommendation: Request Changes (do not accept yet).
Rationale: The feature is coherent and valuable, but there are at least one concrete test bug (inconsistent expected year), questionable dependency/tsconfig changes for React (likely accidental), usability/security improvements (XSS risk from innerHTML, missing auth), and some small code-quality items (naming, HTTP error semantics). These should be addressed before merging.
```

**Bad Output Example:**

```
...
Decision
Accept — The PR is well-structured, implements the requested feature, and includes tests. Minor issues are not blockers and can be addressed in future PRs.

Recommendation: Merge this PR. Consider reviewing the year logic and dependency choices in a follow-up.
```

**Why it's bad:**
TypeScript is a superset of JavaScript, and it adds static typing and compile-time checks on top of JavaScript, which offers compile-time guarantees, early error detection, and safer refactoring. Therefore, when TypeScript developers bypass the type system by abusing the `any` type, it could cause the guarantee provided by TS collapse to JS-level safety. That undermines the primary reason for choosing TypeScript in the first place. 

And there are other naming conventions in TypeScript like using `camelCase` for variables and functions, and use `PascalCase` for class and interface names. This is for better consistency, readibility and maintainability across the codebase. 

**Criteria 1:** Your solution is good if you catch more or all `any` type abuses:

| # | File Name |   Line Number       | Code Snippet         |
|---|--|--------|-------------------------------------|
| 1 |problem_d/problem_d_backend/src/modules/reports/reports.controller.ts | 10 | `const resolvedYear: any = year ? Number(year) : new Date().getFullYear() - 1;`| 
| 2 |problem_d/problem_d_backend/src/modules/reports/reports.controller.ts | 11 | `const orgId: any = organizationId ?? 'org_001';`|
| 3 |problem_d/problem_d_backend/src/modules/reports/reports.service.ts | 8 | `CompanyReport(organizationId: any, year: any): any {)`|
| 4 |problem_d/problem_d_backend/src/modules/reports/reports.service.ts | 9 | `const org: any = this.db.get()`|
| 5 |problem_d/problem_d_backend/src/modules/reports/reports.service.ts | 22 | ``const startDate: any = `${year}-01-01`;``|
| 6 |problem_d/problem_d_backend/src/modules/reports/reports.service.ts | 23 | ``const EndDate: any = `${year}-12-31`;``|
| 7 |problem_d/problem_d_backend/src/modules/reports/reports.service.ts | 25 | ``const invoiceSummary: any = this.db.get()``|
| 8 |problem_d/problem_d_backend/src/modules/reports/reports.service.ts | 35 | ``const projectSummary: any = this.db.get()``|
| 9 |problem_d/problem_d_backend/src/modules/reports/reports.service.ts | 43 | ``const lastProject: any = this.db.get()``|
| 10 |problem_d/problem_d_backend/src/modules/reports/reports.service.ts | 52 | ``const usage: any = this.db.get()``|
| 11 |problem_d/problem_d_backend/src/modules/reports/reports.service.ts | 61 | ``const summaryPoints: any[] = []``|
| 12 |problem_d/problem_d_backend/src/modules/reports/reports.service.ts | 69 | ``const keyMetrics: any[] = []``|
| 13 |problem_d/problem_d_backend/src/modules/reports/reports.service.ts | 76 | ``const narrative: any =``|
| 14 |problem_d/problem_d_backend/src/modules/reports/reports.service.ts | 80 | ``const ReportData: any = {``|
| 15 |problem_d/problem_d_frontend/src/main.ts | 288 | ``const RenderReport = (payload: any) => {``|
| 16 |problem_d/problem_d_frontend/src/main.ts | 306 | ``(metric: any) => ``|
| 17 |problem_d/problem_d_frontend/src/main.ts | 315 | ``map((item: any) => `<li>${item}</li>`).join('')}``|
| 18 |problem_d/problem_d_frontend/src/main.ts | 321 | ``const lastYear: any = new Date().getFullYear() - 1;``|
| 19 |problem_d/problem_d_frontend/src/main.ts | 322 | ``const reportButton: any = document.getElementById``|
| 20 |problem_d/problem_d_frontend/src/main.ts | 323 | ``const ReportYear: any = document.getElementById()``|
| 21 |problem_d/problem_d_frontend/src/main.ts | 324 | ``const reportStatus: any = document.getElementById()``|
| 22 |problem_d/problem_d_frontend/src/main.ts | 326 | ``let ReportData: any = null;``|
| 23 |problem_d/problem_d_frontend/src/main.ts | 327 | ``const setreportData: any = (value: any) => {``|
| 24 |problem_d/problem_d_frontend/src/main.ts | 344 | ``const res: any = await fetch``|
| 25 |problem_d/problem_d_frontend/src/main.ts | 345 | ``const data: any = await res.json();``|
| 26 |problem_d/problem_d_frontend/src/main.ts | 350 | ``} catch (error: any) {``|

Try to think about:
- Did the LLM-assisted PR review tool catch all these? Why or why not?
- The static analysis tool using our heuristic only caught 21, why do you think this happened (See [PR #11 -> Files changed](https://github.com/U70-TK/cs846-presentation-winter-26/pull/11/changes))?

**Criteria 2:** And your solution is good if you catch more or all naming convention violations:

| # | File Name |   Line Number       | Code Snippet         |
|---|--|--------|-------------------------------------|
| 1 |problem_d/problem_d_backend/src/modules/reports/reports.service.ts | 23 | ``const EndDate: any = `${year}-12-31`;``|
| 2 |problem_d/problem_d_backend/src/modules/reports/reports.service.ts | 8 | ``CompanyReport(organizationId: any, year: any): any {``|
| 3 |problem_d/problem_d_backend/src/modules/reports/reports.service.ts | 80 | ``const ReportData: any = {``|
| 4 |problem_d/problem_d_frontend/src/main.ts | 288 | ``const RenderReport = (payload: any) => {``|
| 5 |problem_d/problem_d_frontend/src/main.ts | 320 | ``const LoadReport = (): any => {``|
| 6 |problem_d/problem_d_frontend/src/main.ts | 323 | ``const ReportYear: any = document.getElementById(``|
| 7 |problem_d/problem_d_frontend/src/main.ts | 326 | ``let ReportData: any = null;``|

Try to think about:
- Did the LLM-assisted PR review tool catch all these? Why or why not?

**Criteria 3:** Your solution is good if you catch that the test cases for this PR will not pass. 

The test case at line 54 of the file `problem_d/problem_d_backend/src/modules/reports/reports.service.spec.ts` will not pass: `expect(result.year).toBe(2026);`. 


## 3. References

---

