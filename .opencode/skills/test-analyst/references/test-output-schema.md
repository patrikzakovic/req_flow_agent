# Test Output Schema

## Full Response Schema

The agent returns a combined analysis + test cases structure:

```json
{
  "analysis": {
    "key_features": ["string"],
    "user_workflows": ["string"],
    "edge_cases": ["string"],
    "integration_points": ["string"]
  },
  "test_cases": [
    {
      "title": "string (required)",
      "priority": "High | Medium | Low (required)",
      "description": "string (required)",
      "steps": ["string"] ,
      "expected_result": "string (required)"
    }
  ]
}
```

## Field Definitions

| Field | Type | Required | Description |
|---|---|---|---|
| `title` | string | Yes | Descriptive name for the test case. Must be unique within the suite. |
| `priority` | enum | Yes | `High` = core functionality, `Medium` = important variants, `Low` = edge cases |
| `description` | string | Yes | What is being tested and why |
| `steps` | string[] | Yes | Ordered list of atomic, verifiable actions |
| `expected_result` | string | Yes | Observable, measurable outcome |

## Defaults Applied During Finalization

The agent's `_finalize_tests` step fills missing fields with defaults:

| Field | Default Value |
|---|---|
| `title` | `"Test Case {index}"` |
| `priority` | `"Medium"` |
| `description` | `"No description provided"` |
| `steps` | `["No steps defined"]` |
| `expected_result` | `"No expected result defined"` |

## CSV Export Mapping

When exported to CSV (semicolon-delimited, UTF-8-sig):

| CSV Column | Source |
|---|---|
| `Summary` | `title` |
| `Description` | `description` + numbered `steps` joined |
| `Expected results` | `expected_result` |
