---
name: Test Specialist
description: >
  Testing frameworks and patterns: unit, integration, E2E tests with Jest, Vitest, pytest, Playwright, and Cypress.
  Trigger: Writing and running test suites to ensure code correctness and prevent regressions.
license: MIT
metadata:
  author: Henry Carmenate
  version: "1.0"
  applies-to:
    - claude-code
    - cursor
    - codex
---

## When to Use
Use this skill when:
- Writing unit tests for functions and components
- Creating integration tests for multiple modules working together
- Building E2E tests for complete user workflows
- Practicing TDD (test-driven development)
- Measuring code coverage and identifying gaps
- Testing accessibility and performance
- Setting up CI/CD test pipelines

## Critical Patterns

### Pattern 1: Unit Tests with Jest (JavaScript/React)
Test individual functions and components in isolation.

```javascript
// math.test.js
import { sum, subtract, multiply } from './math';

describe('Math functions', () => {
  describe('sum', () => {
    it('should add two numbers', () => {
      expect(sum(2, 3)).toBe(5);
      expect(sum(-1, 1)).toBe(0);
    });

    it('should handle floating point numbers', () => {
      expect(sum(0.1, 0.2)).toBeCloseTo(0.3);
    });
  });

  describe('subtract', () => {
    it('should subtract two numbers', () => {
      expect(subtract(5, 3)).toBe(2);
    });
  });

  describe('multiply', () => {
    it('should multiply two numbers', () => {
      expect(multiply(3, 4)).toBe(12);
      expect(multiply(0, 100)).toBe(0);
    });
  });
});

// React component test
import { render, screen, fireEvent } from '@testing-library/react';
import { Counter } from './Counter';

describe('Counter component', () => {
  it('should render initial count of 0', () => {
    render(<Counter />);
    expect(screen.getByText('Count: 0')).toBeInTheDocument();
  });

  it('should increment count when button is clicked', () => {
    render(<Counter />);
    const button = screen.getByRole('button', { name: /increment/i });
    fireEvent.click(button);
    expect(screen.getByText('Count: 1')).toBeInTheDocument();
  });

  it('should handle multiple clicks', () => {
    render(<Counter />);
    const button = screen.getByRole('button', { name: /increment/i });
    fireEvent.click(button);
    fireEvent.click(button);
    fireEvent.click(button);
    expect(screen.getByText('Count: 3')).toBeInTheDocument();
  });

  it('should show error state when count exceeds limit', () => {
    render(<Counter maxCount={2} />);
    const button = screen.getByRole('button', { name: /increment/i });
    fireEvent.click(button);
    fireEvent.click(button);
    fireEvent.click(button);
    expect(screen.getByText(/limit reached/i)).toBeInTheDocument();
  });
});
```

### Pattern 2: Integration Tests
Test multiple components and services working together.

```javascript
// api.integration.test.js
import fetch from 'node-fetch';

describe('API Integration Tests', () => {
  const baseUrl = 'http://localhost:3000/api';

  it('should create and fetch a user', async () => {
    // Create user
    const createRes = await fetch(`${baseUrl}/users`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ name: 'John', email: 'john@example.com' }),
    });
    expect(createRes.status).toBe(201);
    const user = await createRes.json();
    expect(user.id).toBeDefined();

    // Fetch user
    const getRes = await fetch(`${baseUrl}/users/${user.id}`);
    expect(getRes.status).toBe(200);
    const fetched = await getRes.json();
    expect(fetched.name).toBe('John');
    expect(fetched.email).toBe('john@example.com');
  });

  it('should handle concurrent requests', async () => {
    const promises = Array.from({ length: 10 }, (_, i) =>
      fetch(`${baseUrl}/users`, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ name: `User${i}`, email: `user${i}@example.com` }),
      })
    );

    const responses = await Promise.all(promises);
    responses.forEach((res) => {
      expect(res.status).toBe(201);
    });
  });

  it('should return 404 for non-existent user', async () => {
    const res = await fetch(`${baseUrl}/users/99999`);
    expect(res.status).toBe(404);
  });
});
```

### Pattern 3: Python Unit Tests with pytest
Test Python functions with fixtures and parametrization.

```python
# test_calculator.py
import pytest
from calculator import Calculator

class TestCalculator:
    @pytest.fixture
    def calc(self):
        """Fixture: provides Calculator instance"""
        return Calculator()

    def test_add(self, calc):
        assert calc.add(2, 3) == 5
        assert calc.add(-1, 1) == 0

    @pytest.mark.parametrize('a,b,expected', [
        (2, 3, 5),
        (-1, 1, 0),
        (0, 0, 0),
        (100, 50, 150),
    ])
    def test_add_parametrized(self, calc, a, b, expected):
        """Parametrized test: runs for each set of parameters"""
        assert calc.add(a, b) == expected

    def test_divide_by_zero(self, calc):
        """Test error handling"""
        with pytest.raises(ValueError):
            calc.divide(10, 0)

    @pytest.mark.slow
    def test_complex_calculation(self, calc):
        """Test marked as slow; skip with -m "not slow" """
        result = calc.complex_operation()
        assert result > 0

# Run specific tests
# pytest test_calculator.py::TestCalculator::test_add
# pytest -k "parametrized" (run only parametrized tests)
# pytest -m "not slow" (skip slow tests)
# pytest --cov=calculator (coverage report)
```

### Pattern 4: E2E Tests with Playwright
Test complete user journeys across the application.

```javascript
// login.e2e.spec.js
import { test, expect } from '@playwright/test';

test.describe('Login flow', () => {
  test.beforeEach(async ({ page }) => {
    await page.goto('http://localhost:3000/login');
  });

  test('should login with valid credentials', async ({ page }) => {
    // Fill form
    await page.fill('input[name="email"]', 'user@example.com');
    await page.fill('input[name="password"]', 'password123');

    // Submit form
    await page.click('button:has-text("Sign In")');

    // Wait for navigation and verify
    await page.waitForURL('http://localhost:3000/dashboard');
    expect(await page.getByText('Welcome back')).toBeVisible();
  });

  test('should show error with invalid credentials', async ({ page }) => {
    await page.fill('input[name="email"]', 'wrong@example.com');
    await page.fill('input[name="password"]', 'wrongpassword');
    await page.click('button:has-text("Sign In")');

    // Check for error message
    const error = page.getByText('Invalid email or password');
    await expect(error).toBeVisible();

    // Verify we didn't navigate
    expect(page.url()).toContain('/login');
  });

  test('should validate email format', async ({ page }) => {
    await page.fill('input[name="email"]', 'not-an-email');
    await page.click('button:has-text("Sign In")');

    const error = page.getByText('Please enter a valid email');
    await expect(error).toBeVisible();
  });

  test('should show password strength indicator', async ({ page }) => {
    const passwordInput = page.locator('input[name="password"]');

    // Weak password
    await passwordInput.fill('123');
    await expect(page.getByText('Weak')).toBeVisible();

    // Strong password
    await passwordInput.clear();
    await passwordInput.fill('MyStr0ng!P@ssw0rd');
    await expect(page.getByText('Strong')).toBeVisible();
  });
});
```

### Pattern 5: Test Structure and Coverage
Organize tests with AAA (Arrange-Act-Assert) and measure coverage.

```javascript
// user.service.test.js
describe('UserService', () => {
  let userService;
  let mockDatabase;

  beforeEach(() => {
    // Arrange: Set up test fixtures
    mockDatabase = {
      query: jest.fn(),
      insert: jest.fn(),
    };
    userService = new UserService(mockDatabase);
  });

  afterEach(() => {
    jest.clearAllMocks();
  });

  describe('getUser', () => {
    it('should fetch user from database', async () => {
      // Arrange
      const userId = 1;
      const expectedUser = { id: 1, name: 'John', email: 'john@example.com' };
      mockDatabase.query.mockResolvedValue(expectedUser);

      // Act
      const user = await userService.getUser(userId);

      // Assert
      expect(mockDatabase.query).toHaveBeenCalledWith('SELECT * FROM users WHERE id = ?', [userId]);
      expect(user).toEqual(expectedUser);
    });

    it('should throw error if user not found', async () => {
      // Arrange
      mockDatabase.query.mockResolvedValue(null);

      // Act & Assert
      await expect(userService.getUser(999)).rejects.toThrow('User not found');
    });

    it('should return cached user on second call', async () => {
      // Arrange
      const userId = 1;
      const user = { id: 1, name: 'John' };
      mockDatabase.query.mockResolvedValue(user);

      // Act
      await userService.getUser(userId);
      await userService.getUser(userId);

      // Assert: Database should only be queried once due to caching
      expect(mockDatabase.query).toHaveBeenCalledTimes(1);
    });
  });

  // Run with coverage
  // jest --coverage
  // Coverage report shows: Statements, Branches, Functions, Lines
});
```

### Pattern 6: Accessibility Testing
Automated accessibility checks in tests.

```javascript
// accessibility.test.js
import { render } from '@testing-library/react';
import { axe, toHaveNoViolations } from 'jest-axe';
import { Form } from './Form';

expect.extend(toHaveNoViolations);

describe('Accessibility', () => {
  it('should not have any accessibility violations', async () => {
    const { container } = render(<Form />);
    const results = await axe(container);
    expect(results).toHaveNoViolations();
  });

  it('should have proper heading hierarchy', () => {
    const { container } = render(<Form />);
    const headings = container.querySelectorAll('h1, h2, h3');
    let previousLevel = 0;
    headings.forEach((heading) => {
      const level = parseInt(heading.tagName[1]);
      expect(level).toBeLessThanOrEqual(previousLevel + 1);
      previousLevel = level;
    });
  });

  it('should have proper button labels', () => {
    const { getByRole } = render(<Form />);
    const buttons = getByRole('button');
    buttons.forEach((button) => {
      expect(button.textContent || button.getAttribute('aria-label')).toBeTruthy();
    });
  });
});
```

## Folder Structure
```
tests/
├── unit/
│   ├── utils.test.js
│   ├── services.test.js
│   └── components.test.js
├── integration/
│   ├── api.test.js
│   └── database.test.js
├── e2e/
│   ├── login.spec.js
│   ├── checkout.spec.js
│   └── user-profile.spec.js
├── fixtures/
│   ├── test-data.js
│   └── mocks.js
├── setup.js                      # Test configuration
└── README.md

src/
├── ... (application code)

jest.config.js
vitest.config.js (if using Vitest)
playwright.config.js (if using Playwright)
```

## Key Principles
- **Test behavior, not implementation**: Test what the code does, not how it does it.
- **AAA pattern**: Arrange (setup), Act (execute), Assert (verify).
- **One assertion per test**: Keep tests focused and clear.
- **Mock external dependencies**: Use mocks for databases, APIs, external services.
- **Avoid test interdependence**: Tests should run independently, in any order.
- **Meaningful test names**: Test names should describe what is being tested and what the expectation is.
- **Aim for 80%+ coverage**: Measure coverage; prioritize critical paths.
- **Test error states**: Happy path is not enough; test edge cases and error conditions.
- **Fast feedback**: Unit tests run in milliseconds; E2E tests take longer but catch integration bugs.

## Commands & Setup
```bash
# Jest (Node.js, React)
npm install --save-dev jest @testing-library/react @testing-library/jest-dom
npx jest --init
npm test

# Vitest (Vite projects, faster)
npm install --save-dev vitest
npm run test

# pytest (Python)
pip install pytest pytest-cov
pytest tests/
pytest --cov=src

# Playwright (E2E)
npm install --save-dev @playwright/test
npx playwright install
npx playwright test

# Cypress (E2E, slower but interactive)
npm install --save-dev cypress
npx cypress open

# Coverage report
npm test -- --coverage
pytest --cov=src --cov-report=html
```
