# Specification Example

Example functional specification that serves as input for test case generation.

## Expected Format

Specifications can be uploaded as `.pdf`, `.docx`, `.md`, or `.txt` files. The app aggregates multiple files into a single specification.

## Sample Specification Structure

```markdown
# Functional Specification: Login & Add to Cart — Sauce Demo

**Application URL:** https://www.saucedemo.com/
**Version:** 1.0

---

## 1. Overview

Brief description of what the specification covers and the application context.

## 2. Login Functionality

### 2.1 Login Page
- URL and entry point
- UI elements: input fields, buttons, labels

### 2.2 User Accounts
| Username | Behavior |
|----------|----------|
| standard_user | Full access |
| locked_out_user | Login denied |

### 2.3 Successful Login
- **Precondition:** User is on the login page
- **Input:** Valid username and correct password
- **Expected behavior:** Step-by-step flow ending with redirect

### 2.4 Failed Login — Invalid Credentials
- **Precondition:** User is on the login page
- **Input:** Invalid username or wrong password
- **Expected behavior:** Error message, user stays on page

### 2.5 Failed Login — Empty Fields
- Scenario A: Empty username
- Scenario B: Empty password
- Scenario C: Both empty

## 3. Shopping Cart
### 3.1 Add Item to Cart
- Click "Add to cart" button on product
- Cart badge updates with count
- Item appears in cart page
```

## Key Elements to Look For

When analyzing a specification, focus on:

1. **Preconditions** — what state the system must be in before the test
2. **Inputs** — specific data values, valid and invalid
3. **Expected behaviors** — step-by-step flows with observable outcomes
4. **Error messages** — exact text strings to verify
5. **Scenarios/variants** — different user types, edge cases listed
6. **UI elements** — buttons, fields, labels, icons mentioned by name
