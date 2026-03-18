---
name: test-developer
description: "Generate Robot Framework test automation scripts from test cases. Use when: converting test cases (.md or .csv) into executable Robot Framework .robot files, creating automated test suites, building keyword-driven tests with Browser library, scaffolding page objects and resource files."
argument-hint: "Provide the path to test cases file (.md or .csv) or paste test case content directly. Optionally specify: output directory and whether to generate resource files."
---

# Test Developer — Robot Framework Test Automation Generator

## When to Use

- Converting structured test cases (from test-analyst or manual input) into executable `.robot` files
- Test case input is in `.md` (Markdown) or `.csv` (semicolon-delimited) format
- Test case files are typically located in the `artifacts/` folder
- Generated `.robot` files should be saved to the `artifacts/` folder by default
- Creating keyword-driven test suites with proper RF structure
- Generating reusable resource files (keywords, variables) alongside test suites

## Prerequisites

Before generating scripts, use the **rf-mcp** tools to verify the environment:

1. **`robot-get_environment_details`** — check Python version, RF version, and installed tools
2. **`robot-get_library_documentation`** — look up available keywords for the target library (e.g., `Browser`)
3. **`robot-get_keyword_documentation`** — get exact keyword signatures, parameters, and usage when unsure about a specific keyword

Always verify keyword names and parameters against the actual library documentation before generating scripts. Do NOT guess keyword names or parameters.

## Supported Input Formats

### Markdown Test Cases

Test cases in Markdown follow this structure:

```markdown
## TC-01: Test Case Title
**Priority:** High
**Description:** What is being tested
**Steps:**
1. Step one
2. Step two
3. Step three
**Expected Result:** Observable outcome
```

### CSV Test Cases

CSV files use semicolon delimiter with UTF-8-sig encoding:

| Column | Maps to |
|---|---|
| `Summary` | Test case name |
| `Description` | Test documentation + steps |
| `Expected results` | Verification assertions |

## Workflow

```
read_test_cases → resolve_library_keywords → generate_robot_files
  ├─ single suite? → one .robot file → END
  └─ multiple areas? → .robot file per area + shared resource file → END
```

## Procedure

### Step 1 — Parse Test Cases

1. Read the input file (`.md` or `.csv`) from the provided path
2. Extract each test case: title, description, steps, expected result
3. Group test cases by functional area if multiple areas are present

### Step 2 — Resolve Keywords via rf-mcp

Before writing any `.robot` code:

1. Use `robot-get_environment_details` to confirm RF and library versions
2. Use `robot-get_library_documentation` for the target library to get the full keyword list
3. For each test step, identify the correct RF keyword:
   - Map user actions (click, type, navigate) to actual library keywords
   - Use `robot-get_keyword_documentation` to verify exact keyword signatures
   - Confirm required vs optional parameters

**Keyword mapping reference** (verify against actual library docs):

| Test Step Action | Browser Library Keyword |
|---|---|
| Navigate to URL | `New Browser` + `New Page` |
| Click element | `Click` |
| Type text | `Fill Text` |
| Verify text visible | `Get Text` + `Should Be Equal` |
| Verify element visible | `Get Element States` |
| Wait for element | `Wait For Elements State` |
| Select dropdown | `Select Options By` |
| Get page URL | `Get Url` |

**Always confirm these mappings** with `robot-get_keyword_documentation` — do not rely solely on this table.

### Step 3 — Generate Robot Files

Generate well-structured `.robot` files following these rules:

#### File Structure

Every `.robot` file MUST follow this structure:

```robot
*** Settings ***
Documentation     Suite description with traceability to test cases
Library           Browser
Resource          ../resources/common.resource    # if using shared keywords
Suite Setup       Suite Setup Keyword
Suite Teardown    Suite Teardown Keyword
Test Setup        Test Setup Keyword
Test Teardown     Test Teardown Keyword

*** Variables ***
${BASE_URL}       https://example.com
${BROWSER}        chromium

*** Test Cases ***
Test Case Title From TC-01
    [Documentation]    TC-01: Description of what is being tested
    [Tags]    smoke    login    priority-high
    Step One Keyword    argument1    argument2
    Step Two Keyword    argument
    Verification Keyword    expected_value

*** Keywords ***
# Local keywords specific to this suite
Custom Keyword Name
    [Documentation]    What this keyword does
    [Arguments]    ${arg1}    ${arg2}
    Library Keyword    ${arg1}
    Library Keyword    ${arg2}
```

#### Generation Rules

1. **One test case = one `*** Test Cases ***` entry** — map each parsed test case to a RF test case
2. **Test case naming** — use the test case title directly, prefix with TC-ID if available
3. **[Documentation]** — include the test case ID, description, and traceability
4. **[Tags]** — add tags for: priority (`priority-high/medium/low`), functional area, test type
5. **Steps → Keywords** — translate each test step into the correct RF keyword call with proper arguments
6. **Locators** — use descriptive CSS or XPath selectors; prefer `id=`, `css=`, `text=` strategies
7. **Variables** — extract repeated values (URLs, credentials, selectors) into `*** Variables ***` section
8. **Assertions** — translate expected results into proper RF verification keywords
9. **Setup/Teardown** — include appropriate suite and test setup/teardown for browser lifecycle

#### Locator Strategy Priority

Use locators in this order of preference:

1. `id=element-id` — most stable
2. `css=.class-name` or `css=[data-test="value"]` — data attributes preferred
3. `text=Visible Text` — for buttons, links
4. `xpath=//element[@attribute='value']` — last resort

#### Resource File Generation

When multiple test suites share common keywords, generate a shared resource file:

```robot
*** Settings ***
Documentation     Shared keywords for [application name]
Library           Browser

*** Variables ***
${BASE_URL}       https://example.com
${BROWSER}        chromium
${TIMEOUT}        10s

*** Keywords ***
Open Application
    [Documentation]    Opens browser and navigates to the application
    New Browser    ${BROWSER}    headless=false
    New Page    ${BASE_URL}

Close Application
    [Documentation]    Closes all browser instances
    Close Browser

Login As User
    [Documentation]    Logs in with provided credentials
    [Arguments]    ${username}    ${password}
    Fill Text    id=username    ${username}
    Fill Text    id=password    ${password}
    Click    id=login-button
```

### Step 4 — Validate Generated Code

After generating `.robot` files:

1. Use `robot-get_file_imports` on the generated file to verify all imports resolve
2. Check that all keyword names match the actual library keywords (via rf-mcp)
3. Ensure no hardcoded test data — use variables for anything that might change

## Output Conventions

| Item | Convention |
|---|---|
| Test file location | `tests/` directory |
| Resource file location | `resources/` directory |
| Test file naming | `{feature_area}.robot` (e.g., `login.robot`, `cart.robot`) |
| Resource file naming | `common.resource` or `{feature_area}.resource` |
| Variable naming | `${UPPER_CASE}` for global, `${lower_case}` for local/arguments |
| Keyword naming | Title Case With Spaces |
| Tag format | lowercase, hyphen-separated (e.g., `priority-high`, `smoke`) |
| Indentation | 4 spaces for keyword body, aligned arguments with multiple spaces |

## Example: From Test Case to Robot Script

**Input (Markdown test case):**

```markdown
## TC-01: Successful Login with Valid Credentials
**Priority:** High
**Description:** Verify that a user can log in with valid credentials and is redirected to the home page
**Steps:**
1. Navigate to the login page
2. Enter valid username
3. Enter valid password
4. Click the Login button
**Expected Result:** User is redirected to the home page (/home)
```

**Output (Robot Framework):**

```robot
*** Settings ***
Documentation     Login test suite — generated from test case TC-01
Library           Browser
Suite Setup       New Browser    chromium    headless=false
Suite Teardown    Close Browser
Test Setup        New Page    ${BASE_URL}

*** Variables ***
${BASE_URL}       https://example.com
${USERNAME}       valid_user
${PASSWORD}       valid_password

*** Test Cases ***
TC-01: Successful Login With Valid Credentials
    [Documentation]    Verify that a user can log in with valid credentials
    ...    and is redirected to the home page.
    ...    Traceability: TC-01
    [Tags]    smoke    login    priority-high
    Fill Text    id=username    ${USERNAME}
    Fill Text    id=password    ${PASSWORD}
    Click    id=login-button
    Get Url    *=    /home
```
