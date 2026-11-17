# Automation & Cypress Fundamentals: Research & Practice

A comprehensive reference guide and practice repository for mastering test automation fundamentals using **Cypress**. This document covers everything from basic test structure to advanced browser interactions, complete with real-world examples and best practices.

---

## Table of Contents

- [Part A: Test Structure](#part-a-test-structure)
- [Part B: Assertions](#part-b-assertions)
- [Part C: Basic Cypress Commands](#part-c-basic-cypress-commands)
- [Part D: Locators](#part-d-locators)
- [Part E: Assertions Practice](#part-e-assertions-practice)
- [Part F: Working with Elements](#part-f-working-with-elements)
- [Part G: Waiting](#part-g-waiting)
- [Part H: Forms](#part-h-forms)
- [Part I: Tables](#part-i-tables)
- [Part J: Browser Interactions](#part-j-browser-interactions)
- [Part K: Keyboard Actions](#part-k-keyboard-actions)
- [Part L: Scrolling](#part-l-scrolling)

---

## Part A: Test Structure

Understanding the building blocks of a Cypress test suite.

### `describe()`
Groups related tests together into a single block or suite.

```javascript
describe('Login Functionality', () => {
  // Individual tests go inside here
});
```

**Real-world use case:** Grouping all tests related to a website's shopping cart checkout process.

---

### `it()`
Defines a single, specific test case.

```javascript
it('Should log in successfully with valid credentials', () => {
  // Steps to type username, password, and click submit
});
```

**Real-world use case:** Verifying that clicking the "Forgot Password" link opens the password reset page.

---

### `before()`
Runs code **exactly once** before any tests in the current group start.

```javascript
before(() => {
  // Connect to the test database
});
```

**Real-world use case:** Launching the Chrome browser at the beginning of a UI test suite.

---

### `beforeEach()`
Runs code **before every single** individual test in the group.

```javascript
beforeEach(() => {
  // Navigate to the homepage
});
```

**Real-world use case:** Clearing browser cookies and navigating back to the login page before each test to ensure a clean slate.

---

### `after()`
Runs code **exactly once** after all tests in the current group have finished.

```javascript
after(() => {
  // Close the database connection
});
```

**Real-world use case:** Closing the browser window and shutting down the automation server once all tests complete.

---

### `afterEach()`
Runs code **immediately after every single** individual test finishes.

```javascript
afterEach(() => {
  // Delete test data created during the test
});
```

**Real-world use case:** Taking a screenshot of the application if a test fails so the QA team can see what went wrong.

---

## Part B: Assertions

The key difference between assertions:

| Feature | `expect()` | `should()` |
|---------|-----------|-----------|
| **Type** | Function call | Property extension |
| **Syntax** | Pass actual value as parameter | Attaches directly to the object |
| **Reads like** | A statement | A natural English sentence |

### When to use `expect()`

- **Safety is a priority:** Fails gracefully with a clear error message if an element or API response is `null` or `undefined`.
- **Modern tooling:** Default built-in assertion in Jest, Playwright, and Cypress.
- **Strict browser environments:** Avoids modifying JavaScript prototype chains.

### When to use `should()`

- **Readability is the goal:** Non-technical team members, product owners, and manual QAs can easily read the test code.
- **Legacy Chai/Mocha frameworks:** Working in a framework that explicitly enables `should()`.

### Examples

```javascript
// expect() examples
expect(loginStatus).to.equal('success');
expect(cartItems.length).to.be.greaterThan(0);
expect(response.statusCode).to.equal(200);

// should() examples
loginStatus.should.equal('success');
cartItems.length.should.be.greaterThan(0);
response.statusCode.should.equal(200);
```

---

## Part C: Basic Cypress Commands

| Action | Command | Example |
|--------|---------|---------|
| Visit a page | `cy.visit()` | `cy.visit('https://example.com')` |
| Type into a textbox | `cy.type()` | `cy.get('#username').type('qa_user')` |
| Click a button | `cy.click()` | `cy.get('.btn-submit').click()` |
| Clear a field | `cy.clear()` | `cy.get('[name="email"]').clear()` |
| Check a checkbox | `cy.check()` | `cy.get('[type="checkbox"]').check()` |
| Uncheck a checkbox | `cy.uncheck()` | `cy.get('#terms-conditions').uncheck()` |
| Select from a dropdown | `cy.select()` | `cy.get('select#country').select('Kenya')` |
| Scroll to an element | `cy.scrollIntoView()` | `cy.get('#footer-links').scrollIntoView()` |
| Upload a file | `cy.selectFile()` | `cy.get('#avatar-upload').selectFile('path/to/image.png')` |
| Hover over an element | `cy.trigger('mouseover')` | `cy.get('.menu-item').trigger('mouseover')` |
| Right click | `cy.rightclick()` | `cy.get('.context-menu').rightclick()` |
| Double click | `cy.dblclick()` | `cy.get('.file-icon').dblclick()` |
| Press keyboard keys | `cy.type('{keyname}')` | `cy.get('body').type('{enter}')` |

---

## Part D: Locators

Cypress provides powerful selectors to locate elements on a page.

### Selector Reference

| Command | Description | Example |
|---------|-------------|---------|
| `cy.get()` | Searches the entire page for elements matching a CSS selector | `cy.get('button.btn-submit')` |
| `cy.contains()` | Finds an element by its visible text content | `cy.contains('Sign In')` |
| `.find()` | Searches down the HTML tree for child elements inside a parent | `cy.get('#navbar').find('.nav-item')` |
| `.children()` | Gets the immediate next-level child elements | `cy.get('ul.menu-list').children()` |
| `.parent()` | Moves exactly one level up the HTML tree | `cy.get('#child-span').parent()` |
| `.closest()` | Travels up the tree to the first matching ancestor | `cy.get('.delete-btn').closest('form')` |
| `.eq()` | Selects a specific element by index (starting at 0) | `cy.get('table tr').eq(2)` *(3rd row)* |
| `.first()` | Grabs the very first matching element | `cy.get('ul li').first()` |
| `.last()` | Grabs the very last matching element | `cy.get('ul li').last()` |
| `.within()` | Scopes subsequent commands to look inside a specific container | See example below |

### `.within()` Example

```javascript
cy.get('#login-form').within(() => {
  cy.get('[name="username"]').type('user1');
  cy.get('[name="password"]').type('pass1');
});
```

### Locator Best Practices

**Why are IDs preferred?**
IDs are unique on a webpage. An ID should only belong to one element, ensuring Cypress always finds the exact item you intend to test without accidental duplicates.

**What are CSS selectors?**
CSS selectors are strings used to identify and target specific HTML elements based on their tag names, classes, IDs, or relationships. Automated tests use them to tell the tool exactly which element to interact with.

**What are data attributes (`data-cy`, `data-testid`)?**
Custom labels added to HTML tags specifically for automated testing. They separate your test logic from the website's design, so layout or color changes won't break your tests as long as the test label stays the same.

**Why are long CSS selectors discouraged?**
Long, deeply nested selectors (e.g., `div > ul > li > span > button`) are fragile and break easily. If a developer adds or removes even a single element in the chain, the selector fails instantly, forcing the QA team to waste time fixing broken tests.

---

## Part E: Assertions Practice

Common Cypress assertions with examples.

```javascript
// Visibility
cy.get('[data-cy="success-alert"]').should('be.visible');

// Existence
cy.get('[data-cy="hidden-modal-overlay"]').should('exist');

// Text content (partial)
cy.get('[data-cy="welcome-banner"]').should('contain', 'Welcome back');

// Text content (exact)
cy.get('[data-cy="submit-btn"]').should('have.text', 'Save Changes');

// Input value
cy.get('[data-cy="email-input"]').should('have.value', 'user@example.com');

// Collection length
cy.get('[data-cy="cart-item"]').should('have.length', 3);

// Enabled / Disabled state
cy.get('[data-cy="continue-btn"]').should('be.enabled');
cy.get('[data-cy="submit-btn"]').should('be.disabled');

// Checkbox state
cy.get('[data-cy="agree-terms-checkbox"]').should('be.checked');

// Attribute validation
cy.get('[data-cy="profile-link"]').should('have.attr', 'href', '/profile');
```

---

## Part F: Working with Elements

Complete reference for interacting with common UI elements.

### Buttons

```javascript
cy.get('[data-cy="submit-button"]')
  .click()
  .should('be.disabled');
```

### Text Fields

```javascript
cy.get('[data-cy="username-field"]')
  .clear()
  .type('qa_tester')
  .should('have.value', 'qa_tester');
```

### Password Fields

```javascript
cy.get('[data-cy="password-input"]')
  .type('SecurePass123!', { log: false })
  .should('have.attr', 'type', 'password');
```

### Checkboxes

```javascript
cy.get('[data-cy="newsletter-checkbox"]')
  .check()
  .should('be.checked');
```

### Radio Buttons

```javascript
cy.get('[data-cy="payment-method-credit"]')
  .check()
  .should('be.checked');
```

### Dropdowns

```javascript
cy.get('[data-cy="country-dropdown"]')
  .select('Kenya')
  .should('have.value', 'KE');
```

### Text Areas

```javascript
cy.get('[data-cy="feedback-textarea"]')
  .clear()
  .type('The checkout process was fast. Great application!')
  .should('have.value', 'The checkout process was fast. Great application!');
```

### Links

```javascript
cy.get('[data-cy="privacy-policy-link"]')
  .should('have.attr', 'href', '/privacy-policy');
```

### Images

```javascript
cy.get('[data-cy="company-logo"]')
  .should('be.visible')
  .and('have.attr', 'src', '/assets/logo.png');
```

---

## Part G: Waiting

### Why `cy.wait(5000)` is Bad Practice

Using hardcoded delays like `cy.wait(5000)` makes tests **slow and unreliable**. If the application responds faster, the test wastes time. If it responds slower, the test fails even though the application works perfectly.

### Better Alternatives

#### 1. Retry-ability
Cypress commands automatically retry (up to 4 seconds by default) for elements to exist and become visible. You can increase this timeout on specific commands.

```javascript
cy.get('[data-cy="slow-element"]', { timeout: 10000 }).should('be.visible');
```

#### 2. Waiting for API Requests (Best Practice)
Intercept the network request, give it an alias, and explicitly wait for that call to finish.

```javascript
cy.intercept('GET', '/api/v1/users').as('getUsers');
cy.get('[data-cy="load-users-btn"]').click();
cy.wait('@getUsers').its('response.statusCode').should('eq', 200);
```

#### 3. Waiting for Page Loading
Instead of pausing for the `load` event, wait for a **specific visual anchor** that proves the page has fully loaded.

```javascript
cy.get('[data-cy="dashboard-header"]').should('be.visible');
```

---

## Part H: Forms

Full example of filling out and submitting a user registration form.

```javascript
describe('User Registration Form', () => {
  beforeEach(() => {
    cy.visit('/register');
  });

  it('should successfully fill out and submit the registration form', () => {
    // Intercept the form submission API request
    cy.intercept('POST', '/api/v1/register').as('registrationSubmit');

    // Fill out text fields
    cy.get('[data-cy="first-name-input"]').type('John');
    cy.get('[data-cy="last-name-input"]').type('Doe');
    cy.get('[data-cy="email-input"]').type('john.doe@example.com');
    cy.get('[data-cy="password-input"]').type('SecurePass123!', { log: false });

    // Date picker
    cy.get('[data-cy="birth-date-picker"]').type('1995-08-15');

    // Dropdown
    cy.get('[data-cy="country-dropdown"]').select('Kenya');

    // Checkbox
    cy.get('[data-cy="terms-checkbox"]').check();

    // Radio button
    cy.get('[data-cy="premium-plan-radio"]').check();

    // Submit
    cy.get('[data-cy="submit-form-btn"]').click();

    // Wait for API response
    cy.wait('@registrationSubmit').its('response.statusCode').should('eq', 200);

    // Verify UI success
    cy.get('[data-cy="success-message"]')
      .should('be.visible')
      .and('contain.text', 'Thank you for registering!');
    cy.url().should('include', '/dashboard');
  });
});
```

---

## Part I: Tables

Working with HTML tables using the [Test Automation Practice](https://testautomationpractice.blogspot.com/) demo site.

```javascript
describe('Web Table Automation Practice', () => {
  beforeEach(() => {
    cy.visit('https://testautomationpractice.blogspot.com/');
  });

  // 1. Count Rows
  it('should count the total number of rows in the table', () => {
    cy.get('table[name="BookTable"] tr').should('have.length', 7);
  });

  // 2. Count Columns
  it('should count the total number of columns in the table', () => {
    cy.get('table[name="BookTable"] tr').first().find('th')
      .should('have.length', 4);
  });

  // 3. Read Data from a Row
  it('should read and validate data from a specific row', () => {
    cy.get('table[name="BookTable"] tr').eq(1).within(() => {
      cy.get('td').eq(0).should('have.text', 'Learn Selenium');
      cy.get('td').eq(1).should('have.text', 'Amit');
    });
  });

  // 4. Click a Button/Action Inside a Row Based on Text
  it('should locate a specific row by its text and perform an action', () => {
    cy.contains('table[name="BookTable"] td', 'Learn Java')
      .parent('tr')
      .within(() => {
        cy.get('td').eq(1).click();
      });
  });

  // 5. Verify Table Contents by Iterating
  it('should iterate through rows to verify table contents', () => {
    cy.get('table[name="BookTable"] tr').each(($row, index) => {
      if (index > 0) {
        const author = $row.find('td').eq(1).text();
        if (author === 'Amit') {
          const bookName = $row.find('td').eq(0).text();
          expect(bookName).to.match(/Learn Selenium|Master In JS/);
        }
      }
    });
  });
});
```

---

## Part J: Browser Interactions

### Browser Back / Forward

```javascript
cy.go('back');   // or cy.go(-1);
cy.go('forward'); // or cy.go(1);
```

### Reload

```javascript
cy.reload();
cy.reload(true); // force reload from server
```

### Opening a New Tab

Cypress runs inside a single browser tab and cannot control a second tab. Use `.invoke('removeAttr', 'target')` to make the link open in the active window.

```javascript
it('should open a new tab link in the same window', () => {
  cy.visit('https://example.com');

  cy.get('[data-cy="external-link"]')
    .invoke('removeAttr', 'target')
    .click();

  cy.url().should('include', '/new-page-destination');
});
```

### Browser Alerts

Cypress automatically clicks **"OK"** on standard alerts.

```javascript
it('should validate the text inside a standard browser alert', () => {
  cy.on('window:alert', (alertText) => {
    expect(alertText).to.equal('Your session is about to expire!');
  });

  cy.get('[data-cy="trigger-alert-btn"]').click();
});
```

### Confirmation Dialogs

Cypress clicks **"OK"** by default. Return `true` to accept or `false` to cancel.

```javascript
it('should handle confirmation dialogs (Accept or Cancel)', () => {
  // Accept
  cy.on('window:confirm', (confirmText) => {
    expect(confirmText).to.equal('Are you sure you want to delete this?');
    return true;
  });
  cy.get('[data-cy="delete-item-btn"]').click();

  // Cancel
  cy.on('window:confirm', () => false);
  cy.get('[data-cy="delete-item-btn"]').click();
});
```

### Prompt Dialogs

Requires stubbing the `window` object **before** triggering the prompt.

```javascript
it('should feed test input text into a browser prompt dialog', () => {
  cy.visit('https://example.com');

  cy.window().then((win) => {
    cy.stub(win, 'prompt').returns(' Nairobi, Kenya ');
  });

  cy.get('[data-cy="open-prompt-btn"]').click();

  cy.get('[data-cy="result-message"]').should('contain', 'Nairobi, Kenya');
});
```

---

## Part K: Keyboard Actions

| Key | Syntax | Use Case |
|-----|--------|----------|
| **Enter** | `{enter}` | Submit a form or select a highlighted item |
| **Tab** | `.tab()` | Move focus to the next input field |
| **Escape** | `{esc}` | Close a modal, popup, or dropdown |
| **Arrow Up** | `{uparrow}` | Increase a numeric value or navigate up in a list |
| **Arrow Down** | `{downarrow}` | Navigate down a menu or autocomplete results |
| **Arrow Left** | `{leftarrow}` | Move text cursor or shift a slider left |
| **Arrow Right** | `{rightarrow}` | Move text cursor or shift a slider right |
| **Delete** | `{del}` | Remove the character to the right of the cursor |
| **Backspace** | `{backspace}` | Erase the character to the left of the cursor |

### Examples

```javascript
// Enter - Submit search
cy.get('[data-cy="search-input"]').type('Cypress Testing{enter}');

// Escape - Close a modal
cy.get('body').type('{esc}');

// Arrow Down - Navigate autocomplete
cy.get('[data-cy="search-autocomplete"]').type('{downarrow}');
```

---

## Part L: Scrolling

```javascript
describe('Cypress Scrolling Operations', () => {
  beforeEach(() => {
    cy.visit('/long-page');
  });

  // 1. Scroll to the bottom of a page
  it('should scroll down to the bottom of the window', () => {
    cy.scrollTo('bottom');
    cy.scrollTo('bottom', { duration: 1000 }); // smooth variant
  });

  // 2. Scroll to the top of a page
  it('should scroll back up to the top of the window', () => {
    cy.scrollTo('bottom');
    cy.scrollTo('top');
  });

  // 3 & 4. Scroll to a specific element and confirm visibility
  it('should scroll to a specific element and confirm its visibility', () => {
    cy.get('[data-cy="footer-terms-link"]')
      .scrollIntoView()
      .should('be.visible');
  });

  // Pro-tip: Scoped element scrolling
  it('should scroll inside a specific scrollable container', () => {
    cy.get('[data-cy="scrollable-terms-box"]').scrollTo('bottom');
  });
});
```

---

## 🛠️ Getting Started

### Prerequisites

- [Node.js](https://nodejs.org/) (v14 or higher)
- [Cypress](https://www.cypress.io/) installed

### Installation

```bash
# Clone the repository
git clone <your-repo-url>
cd automation-cypress-fundamentals

# Install dependencies
npm install

# Open Cypress Test Runner
npx cypress open

# Or run tests headlessly
npx cypress run
```

### Project Structure

```
├── cypress/
│   ├── e2e/              # Test spec files
│   ├── fixtures/         # Test data
│   ├── support/          # Custom commands & setup
│   └── screenshots/      # Failure screenshots
├── cypress.config.js     # Cypress configuration
├── package.json
└── README.md
```

---

## Resources

- [Cypress Documentation](https://docs.cypress.io/)
- [Cypress Best Practices](https://docs.cypress.io/guides/references/best-practices)
- [Test Automation Practice Site](https://testautomationpractice.blogspot.com/)
- [Chai Assertion Library](https://www.chaijs.com/)

---

## Contributing

Contributions, issues, and feature requests are welcome! Feel free to check the [issues page](../../issues) or submit a pull request.

1. Fork the project
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---
