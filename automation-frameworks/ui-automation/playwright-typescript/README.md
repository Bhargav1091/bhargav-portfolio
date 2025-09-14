# ⚡ Modern UI Automation Framework - Playwright + TypeScript

A cutting-edge, enterprise-ready UI automation framework powered by Microsoft Playwright and TypeScript. Built for modern web applications with advanced features like auto-waiting, network interception, visual testing, and comprehensive reporting.

## 📋 Table of Contents

- [Why Playwright?](#-why-playwright)
- [Features](#-features)
- [Architecture](#-architecture)
- [Quick Start](#-quick-start)
- [Project Structure](#-project-structure)
- [Configuration](#-configuration)
- [Writing Tests](#-writing-tests)
- [Page Object Model](#-page-object-model)
- [Advanced Features](#-advanced-features)
- [Execution & Reporting](#-execution--reporting)
- [CI/CD Integration](#-cicd-integration)
- [Visual Testing](#-visual-testing)
- [API Testing](#-api-testing)
- [Best Practices](#-best-practices)
- [Troubleshooting](#-troubleshooting)

## 🚀 Why Playwright?

### 🏆 Playwright Advantages
- **Auto-waiting**: No more manual waits - Playwright waits for elements automatically
- **Multi-browser**: Chrome, Firefox, Safari, Edge - all with one codebase
- **Mobile Testing**: Built-in device emulation for mobile testing
- **Network Interception**: Mock APIs, intercept requests, test offline scenarios
- **Visual Testing**: Pixel-perfect screenshot comparisons
- **Fast & Reliable**: Parallel execution by default, intelligent retry mechanisms
- **Modern Web Support**: SPA, PWA, Shadow DOM, frames support

## ✨ Features

### 🏗️ Core Capabilities
- **Cross-browser Testing**: Chrome, Firefox, Safari, Edge with consistent APIs
- **Mobile & Tablet**: Device emulation with touch, geolocation, camera
- **Parallel Execution**: Built-in parallel testing across browsers and workers
- **Auto-waiting**: Smart waits for elements, network requests, and page loads
- **Network Control**: Request/response mocking, offline testing, performance metrics
- **Visual Regression**: Screenshot comparison with threshold controls
- **Trace Viewer**: Debug tests with timeline, DOM snapshots, and network logs
- **Code Generation**: Generate tests by recording user interactions

### 🔧 Technical Stack
- **Playwright 1.40+** - Core automation framework
- **TypeScript 5.0+** - Type-safe test development
- **Node.js 18+** - Runtime environment
- **Allure Reports** - Enterprise-grade reporting
- **ESLint + Prettier** - Code quality and formatting
- **Husky** - Git hooks for quality gates
- **Docker** - Containerized execution

## 🏛️ Architecture

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Test Layer    │    │  Page Objects   │    │   Utilities     │
│                 │    │                 │    │                 │
│ • Test Specs    │───▶│ • Page Classes  │───▶│ • Fixtures      │
│ • Test Data     │    │ • Components    │    │ • Helpers       │
│ • Assertions    │    │ • Actions       │    │ • API Utils     │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         └───────────────────────┼───────────────────────┘
                                 │
                    ┌─────────────────┐
                    │  Configuration  │
                    │                 │
                    │ • Playwright    │
                    │ • Environment   │
                    │ • Test Data     │
                    └─────────────────┘
```

## 🚀 Quick Start

### Prerequisites
- Node.js 18+ and npm/yarn
- Git
- VS Code (recommended with Playwright extension)

### Installation

```bash
# Clone the repository
git clone https://github.com/bhargav-thakkar/ui-automation-playwright.git
cd ui-automation-playwright

# Install dependencies
npm install

# Install Playwright browsers
npx playwright install

# Run your first test
npm run test:smoke
```

### Your First Test

```typescript
import { test, expect } from '@playwright/test';
import { LoginPage } from '../pages/LoginPage';
import { DashboardPage } from '../pages/DashboardPage';
import { testData } from '../data/users.json';

test.describe('User Authentication', () => {
  test('successful login with valid credentials', async ({ page }) => {
    const loginPage = new LoginPage(page);
    const dashboardPage = new DashboardPage(page);
    
    await loginPage.navigate();
    await loginPage.login(testData.validUser.email, testData.validUser.password);
    
    await expect(dashboardPage.welcomeMessage).toBeVisible();
    await expect(page).toHaveURL(/dashboard/);
  });
});
```

## 📁 Project Structure

```
ui-automation-playwright/
├── src/
│   ├── pages/                         # Page Object Model classes
│   │   ├── components/                # Reusable UI components
│   │   │   ├── Header.ts              # Header component
│   │   │   └── Navigation.ts          # Navigation component
│   │   ├── LoginPage.ts               # Login page object
│   │   ├── DashboardPage.ts           # Dashboard page object
│   │   └── BasePage.ts                # Base page with common methods
│   ├── tests/                         # Test specifications
│   │   ├── smoke/                     # Smoke test suite
│   │   │   ├── login.spec.ts          # Login functionality tests
│   │   │   └── dashboard.spec.ts      # Dashboard tests
│   │   ├── regression/                # Regression test suite
│   │   ├── visual/                    # Visual regression tests
│   │   └── api/                       # API integration tests
│   ├── fixtures/                      # Custom Playwright fixtures
│   │   ├── basePage.ts                # Page fixtures
│   │   └── testData.ts                # Test data fixtures
│   ├── utils/                         # Utility functions
│   │   ├── helpers.ts                 # Common helper functions
│   │   ├── apiClient.ts               # API client wrapper
│   │   └── dataGenerator.ts           # Test data generation
│   └── data/                          # Test data files
│       ├── users.json                 # User test data
│       ├── products.json              # Product test data
│       └── config/                    # Environment configs
├── tests-results/                     # Test execution results
├── test-results/                      # Playwright test results
├── allure-results/                    # Allure report data
├── playwright-report/                 # Playwright HTML reports
├── screenshots/                       # Visual testing screenshots
├── videos/                           # Test execution recordings
├── .github/
│   └── workflows/                    # GitHub Actions workflows
│       ├── playwright.yml            # Main test workflow
│       └── visual-tests.yml          # Visual regression workflow
├── playwright.config.ts              # Playwright configuration
├── package.json                      # Dependencies and scripts
├── tsconfig.json                     # TypeScript configuration
├── .eslintrc.js                      # ESLint configuration
└── README.md
```

## ⚙️ Configuration

### Playwright Configuration (`playwright.config.ts`)

```typescript
import { defineConfig, devices } from '@playwright/test';

export default defineConfig({
  testDir: './src/tests',
  fullyParallel: true,
  forbidOnly: !!process.env.CI,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 1 : undefined,
  reporter: [
    ['html'],
    ['allure-playwright'],
    ['junit', { outputFile: 'test-results/junit-report.xml' }]
  ],
  
  use: {
    baseURL: process.env.BASE_URL || 'https://demo.example.com',
    trace: 'on-first-retry',
    screenshot: 'only-on-failure',
    video: 'retain-on-failure',
    actionTimeout: 10000,
    navigationTimeout: 30000,
  },

  projects: [
    // Desktop browsers
    {
      name: 'chromium',
      use: { ...devices['Desktop Chrome'] },
    },
    {
      name: 'firefox',
      use: { ...devices['Desktop Firefox'] },
    },
    {
      name: 'webkit',
      use: { ...devices['Desktop Safari'] },
    },
    
    // Mobile devices
    {
      name: 'Mobile Chrome',
      use: { ...devices['Pixel 5'] },
    },
    {
      name: 'Mobile Safari',
      use: { ...devices['iPhone 12'] },
    },
    
    // Tablet
    {
      name: 'iPad',
      use: { ...devices['iPad Pro'] },
    },
  ],

  webServer: {
    command: 'npm run start:test-server',
    url: 'http://127.0.0.1:3000',
    reuseExistingServer: !process.env.CI,
  },
});
```

### Environment Configuration

```typescript
// src/utils/config.ts
export const config = {
  baseUrl: process.env.BASE_URL || 'https://demo.example.com',
  apiUrl: process.env.API_URL || 'https://api.example.com',
  environment: process.env.NODE_ENV || 'qa',
  
  timeouts: {
    default: 10000,
    navigation: 30000,
    api: 5000,
  },
  
  users: {
    admin: {
      email: process.env.ADMIN_EMAIL || 'admin@example.com',
      password: process.env.ADMIN_PASSWORD || 'password123',
    },
    standard: {
      email: process.env.USER_EMAIL || 'user@example.com', 
      password: process.env.USER_PASSWORD || 'password123',
    },
  },
};
```

## 🧪 Writing Tests

### Basic Test Structure

```typescript
import { test, expect, Page } from '@playwright/test';
import { LoginPage } from '../pages/LoginPage';
import { DashboardPage } from '../pages/DashboardPage';

test.describe('User Management', () => {
  let loginPage: LoginPage;
  let dashboardPage: DashboardPage;

  test.beforeEach(async ({ page }) => {
    loginPage = new LoginPage(page);
    dashboardPage = new DashboardPage(page);
    
    // Navigate to application
    await loginPage.navigate();
  });

  test('should display user profile after login', async ({ page }) => {
    // Login with valid credentials
    await loginPage.login('user@example.com', 'password123');
    
    // Verify
