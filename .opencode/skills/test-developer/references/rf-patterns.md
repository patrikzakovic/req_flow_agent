# Robot Framework Patterns Reference

## Test Suite Patterns

### Minimal Test Suite (Browser Library)

```robot
*** Settings ***
Documentation     Minimal suite example
Library           Browser
Test Setup        New Page    ${BASE_URL}
Suite Setup       New Browser    chromium    headless=false
Suite Teardown    Close Browser

*** Variables ***
${BASE_URL}    https://example.com

*** Test Cases ***
Example Test
    [Documentation]    Example test case
    [Tags]    smoke
    Get Title    ==    My Application
```

### Suite With Resource File

```robot
*** Settings ***
Documentation     Suite using shared resource
Library           Browser
Resource          ../resources/common.resource
Suite Setup       Open Application
Suite Teardown    Close Application
Test Setup        New Page    ${BASE_URL}

*** Test Cases ***
Login Test
    [Documentation]    Test login flow
    [Tags]    smoke    login
    Login As User    ${VALID_USERNAME}    ${VALID_PASSWORD}
    Get Url    *=    /home
```

## Resource File Pattern

```robot
*** Settings ***
Documentation     Shared keywords and variables
Library           Browser

*** Variables ***
${BASE_URL}           https://example.com
${BROWSER}            chromium
${VALID_USERNAME}     valid_user
${VALID_PASSWORD}     valid_password
${TIMEOUT}            10s

*** Keywords ***
Open Application
    [Documentation]    Launch browser and navigate to app
    New Browser    ${BROWSER}    headless=false

Close Application
    [Documentation]    Tear down browser
    Close Browser

Login As User
    [Documentation]    Perform login with given credentials
    [Arguments]    ${username}    ${password}
    Fill Text    id=username    ${username}
    Fill Text    id=password    ${password}
    Click    id=login-button
```

## Common Assertion Patterns

### Browser Library

```robot
# URL verification
Get Url    *=    /home

# Text verification
Get Text    css=.page-title    ==    Dashboard
Get Text    css=.error-message    *=    Invalid input

# Element state verification
Get Element States    id=submit-button    contains    visible
Get Element States    css=.notification-badge    contains    visible

# Element count
Get Element Count    css=.list-item    ==    6
```

## Input Format — Markdown Test Cases

Expected structure when reading `.md` input:

```markdown
## TC-01: Test Case Title
**Priority:** High | Medium | Low
**Description:** What is being tested
**Steps:**
1. First action
2. Second action
3. Third action
**Expected Result:** What should happen
```

## Input Format — CSV Test Cases

Expected CSV structure (semicolon-delimited, UTF-8-sig):

```csv
Summary;Description;Expected results
"Login with valid credentials";"1. Navigate to login page\n2. Enter username\n3. Click Login";"User redirected to inventory"
```
