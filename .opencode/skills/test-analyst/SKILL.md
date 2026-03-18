---
name: test-analyst
description: "Generate test cases from functional specifications. Use when: writing acceptance tests, API tests, UI tests, integration tests, error handling tests, creating test scenarios, analyzing specifications for testable requirements, generating test suites in EN or SK, exporting test cases to CSV/Markdown, reviewing test coverage, refining tests with feedback."
argument-hint: "Paste or describe the functional specification. Optionally specify test type (acceptance, API, UI, integration, error-handling, mixed) and test count (1-2, 3-8, 5-8, 10-15, 15-25, 25-40, 40+)."
---

# Test Analyst — Test Case Generator

## When to Use

- Generating test cases from a functional specification document
- Functional specifications are stored in the `specs/` folder by default
- Analyzing specifications to identify testable requirements, happy paths, edge cases, and error conditions
- Creating structured test cases with title, priority, description, steps, and expected results
- Producing test suites in English or Slovak language
- Refining existing test cases based on reviewer feedback
- Exporting test cases to CSV (semicolon-delimited, UTF-8-sig) or Markdown format
- Generated test case files should be saved to the `artifacts/` folder by default; suggest a folder if it doesn't exist

## Supported Test Case Types

| Type | When to use | Focus |
|---|---|---|
| **Acceptance** | Validate end-to-end user workflows against business requirements | User journeys, business rules, UI flows |
| **API** | Verify API endpoints, request/response contracts, status codes | REST/GraphQL calls, headers, payloads, status codes |
| **UI** | Test user interface elements, navigation, form validation | Page elements, interactions, visual feedback |
| **Integration** | Verify interactions between components or external systems | Data flow between systems, redirects, callbacks |
| **Error Handling** | Confirm proper behavior under invalid inputs and failure conditions | Validation messages, error codes, graceful degradation |
| **Mixed** (default) | Combine all relevant types based on specification content | Auto-selects types based on what the spec describes |

If no type is specified, default to **Mixed** — analyze the specification and generate the appropriate mix of test case types.

## Workflow

```
analyze_and_generate → conditional edge
  ├─ has feedback? → refine_with_feedback → finalize_tests → END
  └─ no feedback?  → finalize_tests → END
```

Analysis and generation are merged into a single step with chain-of-thought reasoning for better quality and efficiency.

## Test Case Structure

Every generated test case MUST follow this exact schema:

```json
{
  "title": "Descriptive test title",
  "priority": "High | Medium | Low",
  "description": "What is being tested and why",
  "steps": ["Step 1", "Step 2", "Step 3"],
  "expected_result": "Clear expected outcome"
}
```

The full response includes both analysis and test cases.

## Procedure

### Step 1 — Analyze and Generate (Merged)

Read the functional specification and use chain-of-thought reasoning to identify:

1. **Key features** — main functionality, endpoints, UI elements described
2. **User workflows** — happy path scenarios from start to finish
3. **Edge cases** — boundary values, empty inputs, max lengths, special characters
4. **Error conditions** — invalid inputs, unauthorized access, locked accounts, timeouts
5. **Integration points** — API calls, redirects, external system interactions

Use `<thinking>` tags to reason through the specification before producing output:

```
<thinking>
1. What are the main features/endpoints/workflows described?
2. What are the happy path scenarios?
3. What edge cases and error conditions are mentioned?
4. What validation rules or constraints exist?
5. What integration points need testing?
</thinking>
```

Then produce a single combined JSON response:

```json
{
  "analysis": {
    "key_features": ["Feature 1", "Feature 2"],
    "user_workflows": ["Workflow 1", "Workflow 2"],
    "edge_cases": ["Edge case 1", "Edge case 2"],
    "integration_points": ["Integration 1"]
  },
  "test_cases": [...]
}
```

#### Determine Test Count by Detail Level

The user specifies the desired test count as an argument. Use these ranges based on desired coverage. STAY IN RANGE. Default is `10-15` if not specified.

| Detail Level | Count | Guidance |
|---|---|---|
| `1-2` | 1–2 | Smoke test only — verify the core happy path. STAY IN RANGE. |
| `3-8` | 3–8 | Essential test cases covering main functionality. STAY IN RANGE. |
| `10-15` | 10–15 | Include edge cases and error scenarios. STAY IN RANGE. |
| `15-25` | 15–25 | Thorough coverage with boundary testing. STAY IN RANGE. |
| `25-40` | 25–40 | Comprehensive coverage across all scenarios. STAY IN RANGE. |
| `40+` | 40+ | Exhaustive — every identifiable path and combination. |

#### Generation Rules

Follow these rules strictly:

1. **Only test what the specification states** — do NOT infer features, fields, status codes, or constraints not explicitly mentioned
2. **Respect the requested test type** — if a specific type is requested, generate only that type; if Mixed, select types appropriate to the specification content
3. **Cover all three categories**: happy paths, edge cases, error conditions
4. **Assign priorities**: High for core functionality, Medium for important variants, Low for edge cases
5. **Write clear steps** — each step should be a single verifiable action
6. **Write precise expected results** — observable, measurable outcomes
7. **Use the specified output language** (English or Slovak) for all content

#### Test Case Types with Examples

**UI/Acceptance Test:**
```json
{
  "title": "User Login with Valid Credentials",
  "priority": "High",
  "description": "Verify that registered users can successfully log in with correct email and password",
  "steps": [
    "Navigate to login page",
    "Enter valid email: user@example.com",
    "Enter valid password",
    "Click 'Login' button"
  ],
  "expected_result": "User is redirected to dashboard, session token created, welcome message displayed"
}
```

**API Integration Test:**
```json
{
  "title": "Create User Account via POST /api/users",
  "priority": "High",
  "description": "Verify that a new user account can be created through the API with valid registration data",
  "steps": [
    "Send POST request to /api/users",
    "Set headers: Content-Type: application/json",
    "Send JSON body: {\"email\": \"test@example.com\", \"password\": \"SecurePass123\", \"firstName\": \"John\", \"lastName\": \"Doe\"}",
    "Verify HTTP status 201 Created",
    "Verify response contains userId, email, createdAt",
    "Query database to confirm user was persisted",
    "Verify confirmation email was sent"
  ],
  "expected_result": "API returns 201 Created, response contains user object with userId and timestamps, user exists in database, confirmation email queued"
}
```

**API Error Handling Test:**
```json
{
  "title": "Create User with Duplicate Email - Expect Error",
  "priority": "High",
  "description": "Verify API properly rejects user creation when email already exists",
  "steps": [
    "Create initial user with email test@example.com",
    "Send POST request to /api/users with same email",
    "Verify HTTP status 409 Conflict or 400 Bad Request",
    "Verify error response contains clear error message",
    "Verify no duplicate user created in database"
  ],
  "expected_result": "API returns 409/400 error, response includes error message about duplicate email, database contains only one user with that email"
}
```

**UI Error Handling Test:**
```json
{
  "title": "Login with Empty Username Field",
  "priority": "Medium",
  "description": "Verify that the system shows an appropriate error when username is empty",
  "steps": [
    "Navigate to login page",
    "Leave Username field empty",
    "Enter a password",
    "Click Login button"
  ],
  "expected_result": "Error message displayed: 'Username is required'. User remains on login page."
}
```

### Step 2 — Refine with Feedback (Optional)

After the initial test cases are generated, the user can provide feedback as a follow-up message in the chat to iteratively refine the results. The user types their comments directly in the chat input, for example:

- *"Add more edge cases for the login form"*
- *"Remove test cases about email — it's not in scope"*
- *"Change priority of TC-3 to High"*
- *"Add a test for session timeout after 30 minutes"*

When refining, use chain-of-thought reasoning:

```
<thinking>
1. What specific changes is the user requesting?
2. Should I modify existing tests, add new ones, or remove some?
3. Am I staying within the scope of the original specification?
4. Is the language consistent?
</thinking>
```

Rules for refinement:

1. Re-read the original specification to stay grounded (prevents hallucination)
2. Apply the feedback: add, modify, or remove test cases as requested
3. Keep the same output language and JSON structure
4. Do NOT add features or behaviors not in the original specification
5. Return the refined test cases as a JSON array

### Step 3 — Finalize

Validate every test case has all required fields. Apply defaults for any missing fields:

| Field | Default Value |
|---|---|
| `title` | `"Test Case {index}"` |
| `priority` | `"Medium"` |
| `description` | `"No description provided"` |
| `steps` | `["No steps defined"]` |
| `expected_result` | `"No expected result defined"` |

### Step 4 — Export

Format test cases for export in one of two formats:

**Markdown** — full test case details with headers, numbered steps, expected results, separated by `---`.

**CSV** (for Jira/Excel import) — semicolon-delimited, UTF-8-sig encoding:
- Column 1: `Summary` — test case title
- Column 2: `Description` — description + numbered steps combined (joined with newline)
- Column 3: `Expected results` — expected result text

```python
# CSV uses semicolon delimiter and BOM for Slovak diacritics in Excel
writer = csv.writer(output, delimiter=';', quoting=csv.QUOTE_MINIMAL)
output.getvalue().encode('utf-8-sig')
```

## Quality Checklist

Before finalizing, verify each test case:

- [ ] Title is descriptive and unique
- [ ] Priority is assigned (High/Medium/Low)
- [ ] Description explains what is being tested
- [ ] Steps are atomic — one action per step
- [ ] Expected result is specific and verifiable
- [ ] Content is in the correct output language
- [ ] No features are tested that aren't in the specification
- [ ] Test cases match the requested test type (or appropriate mix)
- [ ] Test count stays within the requested detail level range

## Reference

- [Specification example](./references/specification-example.md) — sample input format
