# Functional Specification: Login & Add to Cart — Sauce Demo

**Application URL:** https://www.saucedemo.com/  
**Version:** 1.0  
**Date:** 2026-03-11

---

## 1. Overview

This specification covers the **Login** functionality and **Adding Items to Shopping Cart** for the Sauce Demo web application (Swag Labs). The application is a sample e-commerce platform used for testing purposes.

---

## 2. Login Functionality

### 2.1 Login Page

- **URL:** https://www.saucedemo.com/
- The login page is the entry point of the application.
- The page displays the application logo "Swag Labs" at the top.
- Two input fields are presented:
  - **Username** — text input field with placeholder "Username"
  - **Password** — password input field with placeholder "Password"
- A **Login** button is displayed below the input fields.
- Below the login form, accepted usernames and the shared password are listed for reference.

### 2.2 Accepted User Accounts

| Username                   | Behavior                                                        |
|----------------------------|-----------------------------------------------------------------|
| `standard_user`            | Standard user with full access to all features                  |
| `locked_out_user`          | User account is locked; login is denied                         |
| `problem_user`             | User experiences UI glitches (e.g., wrong product images)       |
| `performance_glitch_user`  | User experiences slow page load times                           |
| `error_user`               | User encounters errors during various operations                |
| `visual_user`              | User sees visual/layout inconsistencies                         |

- **Password for all users:** `secret_sauce`

### 2.3 Successful Login

- **Precondition:** User is on the login page.
- **Input:** Valid username and correct password.
- **Expected behavior:**
  1. User enters a valid username (e.g., `standard_user`) in the Username field.
  2. User enters `secret_sauce` in the Password field.
  3. User clicks the **Login** button.
  4. The system authenticates the credentials.
  5. User is redirected to the **Inventory page** (`/inventory.html`).
  6. The inventory page displays the product catalog, a hamburger navigation menu, a shopping cart icon, and a sort dropdown.

### 2.4 Failed Login — Invalid Credentials

- **Precondition:** User is on the login page.
- **Input:** Invalid username or incorrect password.
- **Expected behavior:**
  1. User enters an invalid username or wrong password.
  2. User clicks the **Login** button.
  3. An error message is displayed: **"Epic sadface: Username and password do not match any user in this service"**
  4. The error message appears in a red banner with an **X** (close) button.
  5. The user remains on the login page.
  6. Both input fields retain their entered values.

### 2.5 Failed Login — Empty Fields

- **Precondition:** User is on the login page.
- **Scenario A — Empty username:**
  1. User leaves the Username field empty and enters a password.
  2. User clicks **Login**.
  3. Error message displayed: **"Epic sadface: Username is required"**
- **Scenario B — Empty password:**
  1. User enters a username and leaves the Password field empty.
  2. User clicks **Login**.
  3. Error message displayed: **"Epic sadface: Password is required"**
- **Scenario C — Both fields empty:**
  1. User leaves both fields empty and clicks **Login**.
  2. Error message displayed: **"Epic sadface: Username is required"**

### 2.6 Failed Login — Locked Out User

- **Precondition:** User is on the login page.
- **Input:** Username `locked_out_user` with password `secret_sauce`.
- **Expected behavior:**
  1. User enters `locked_out_user` and the correct password.
  2. User clicks **Login**.
  3. Error message displayed: **"Epic sadface: Sorry, this user has been locked out."**
  4. The user remains on the login page.

### 2.7 Error Message Dismissal

- When an error message is displayed, it contains an **X** button on the right side.
- Clicking the **X** button closes the error message banner.
- The error icon indicators on the input fields are also removed.

### 2.8 Page Access Without Login

- If a user attempts to navigate directly to any authenticated page (e.g., `/inventory.html`, `/cart.html`) without logging in, the system redirects to the login page.
- An error message is displayed: **"Epic sadface: You can only access '/inventory.html' when you are logged in."**

---

## 3. Adding Items to Shopping Cart

### 3.1 Inventory Page

- **URL:** https://www.saucedemo.com/inventory.html
- **Precondition:** User is logged in.
- The inventory page displays a grid/list of products (6 products by default).
- Each product item shows:
  - **Product image**
  - **Product name** (clickable link to product detail)
  - **Product description**
  - **Product price** (formatted as `$XX.XX`)
  - **Add to cart** button

### 3.2 Available Products

| Product Name                      | Price   |
|-----------------------------------|---------|
| Sauce Labs Backpack               | $29.99  |
| Sauce Labs Bike Light             | $9.99   |
| Sauce Labs Bolt T-Shirt           | $15.99  |
| Sauce Labs Fleece Jacket          | $49.99  |
| Sauce Labs Onesie                 | $7.99   |
| Test.allTheThings() T-Shirt (Red) | $15.99  |

### 3.3 Adding an Item from Inventory Page

- **Precondition:** User is logged in and on the inventory page.
- **Expected behavior:**
  1. User clicks the **"Add to cart"** button on a product card.
  2. The button text changes to **"Remove"** (indicating the item is in the cart).
  3. The **shopping cart icon** in the top-right corner displays a **badge** with the number of items in the cart.
  4. The badge number increments by 1 for each item added.

### 3.4 Adding an Item from Product Detail Page

- **Precondition:** User is logged in.
- **Expected behavior:**
  1. User clicks on a product name or image from the inventory page.
  2. The system navigates to the **product detail page** (`/inventory-item.html?id=X`).
  3. The detail page shows: product image (larger), product name, product description, product price, and an **"Add to cart"** button.
  4. User clicks **"Add to cart"**.
  5. The button text changes to **"Remove"**.
  6. The shopping cart badge updates to reflect the total item count.
  7. User can click **"Back to products"** to return to the inventory page.

### 3.5 Adding Multiple Items

- **Precondition:** User is logged in and on the inventory page.
- **Expected behavior:**
  1. User can add multiple different products to the cart.
  2. Each added product's button changes from **"Add to cart"** to **"Remove"**.
  3. The cart badge displays the total count of all added items (e.g., adding 3 items shows badge "3").
  4. All added items persist in the cart when navigating between pages.

### 3.6 Removing an Item from Inventory Page

- **Precondition:** User has at least one item in the cart.
- **Expected behavior:**
  1. User clicks the **"Remove"** button on a product that is already in the cart.
  2. The button text changes back to **"Add to cart"**.
  3. The cart badge number decrements by 1.
  4. If no items remain in the cart, the badge disappears entirely.

### 3.7 Shopping Cart Page

- **URL:** https://www.saucedemo.com/cart.html
- **Precondition:** User is logged in.
- **Navigation:** User clicks the **shopping cart icon** in the top-right corner.
- The cart page displays:
  - **QTY** column — quantity of each item (always 1 per line item)
  - **Description** column — product name, description, and price
  - **Remove** button for each item
  - **Continue Shopping** button — navigates back to inventory page
  - **Checkout** button — proceeds to checkout flow

### 3.8 Removing an Item from Cart Page

- **Precondition:** User is on the cart page with items in the cart.
- **Expected behavior:**
  1. User clicks the **"Remove"** button next to a cart item.
  2. The item is immediately removed from the cart list.
  3. The cart badge updates accordingly.
  4. If the cart becomes empty, no items are displayed but the **Continue Shopping** and **Checkout** buttons remain visible.

### 3.9 Cart Persistence

- Items added to the cart persist across page navigation within the session.
- Navigating to product details, back to inventory, or to the cart page does not remove items.
- The cart is cleared when the user logs out or the browser session ends.

### 3.10 Sort Functionality on Inventory Page

- A dropdown in the top-right area of the inventory page allows sorting products:
  - **Name (A to Z)** — default sort order
  - **Name (Z to A)**
  - **Price (low to high)**
  - **Price (high to low)**
- Changing the sort order re-arranges product cards on the page.
- Items already in the cart retain their **"Remove"** button state after sorting.

---

## 4. UI / Navigation Elements

### 4.1 Header

- **Hamburger menu** (top-left) — opens side navigation with:
  - **All Items** — navigates to inventory page
  - **About** — navigates to https://saucelabs.com/
  - **Logout** — logs the user out and returns to login page
  - **Reset App State** — clears cart and resets all product states
- **App title** "Swag Labs" — displayed in the center of the header
- **Shopping cart icon** (top-right) — navigates to cart page; shows badge with item count when items are present

### 4.2 Footer

- Social media links (Twitter, Facebook, LinkedIn)
- Copyright text: "© 2025 Sauce Labs. All Rights Reserved. Terms of Service | Privacy Policy"

---

## 5. Non-Functional Requirements

- The login page must load within 3 seconds under normal network conditions.
- Adding an item to the cart must reflect immediately in the UI (button change + badge update) without a full page reload.
- The application is a single-page-like application with client-side routing.
- The application is responsive and functional on modern desktop browsers (Chrome, Firefox, Edge, Safari).
