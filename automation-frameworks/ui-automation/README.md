# 🚀 Enterprise UI Automation Framework - Selenium + Java

A robust, scalable, and maintainable UI automation framework built with Selenium WebDriver and Java, designed for enterprise-grade applications with advanced reporting, parallel execution, and CI/CD integration.

## 📋 Table of Contents

- [Features](#-features)
- [Architecture](#-architecture)
- [Quick Start](#-quick-start)
- [Project Structure](#-project-structure)
- [Configuration](#-configuration)
- [Writing Tests](#-writing-tests)
- [Execution](#-execution)
- [Reporting](#-reporting)
- [CI/CD Integration](#-cicd-integration)
- [Best Practices](#-best-practices)
- [Contributing](#-contributing)
- [Troubleshooting](#-troubleshooting)

## ✨ Features

### 🏗️ Framework Capabilities
- **Cross-browser Support**: Chrome, Firefox, Safari, Edge with WebDriverManager
- **Parallel Execution**: TestNG-based parallel test execution across multiple browsers
- **Page Object Model**: Clean, maintainable page object implementation with PageFactory
- **Data-Driven Testing**: Excel, CSV, JSON, and database integration
- **Advanced Reporting**: ExtentReports with screenshots, videos, and logs
- **CI/CD Ready**: Jenkins, GitHub Actions, Azure DevOps integration
- **Docker Support**: Containerized execution with Selenium Grid
- **API Integration**: REST Assured for API validation within UI flows

### 🔧 Technical Stack
- **Java 11+** - Core programming language
- **Selenium 4.x** - WebDriver automation
- **TestNG** - Test framework and execution
- **Maven** - Dependency management and build tool
- **ExtentReports** - Advanced HTML reporting
- **WebDriverManager** - Automatic driver management
- **Log4j2** - Comprehensive logging
- **Jackson** - JSON data handling
- **Apache POI** - Excel file operations

## 🏛️ Architecture

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Test Layer    │    │  Page Objects   │    │   Utilities     │
│                 │    │                 │    │                 │
│ • Test Classes  │───▶│ • Page Classes  │───▶│ • WebDriver     │
│ • Test Data     │    │ • Page Elements │    │ • Screenshots   │
│ • Assertions    │    │ • Page Actions  │    │ • Reports       │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         └───────────────────────┼───────────────────────┘
                                 │
                    ┌─────────────────┐
                    │  Configuration  │
                    │                 │
                    │ • Browser Setup │
                    │ • Environment   │
                    │ • Test Data     │
                    └─────────────────┘
```

## 🚀 Quick Start

### Prerequisites
- Java 11 or higher
- Maven 3.6+
- Git
- IDE (IntelliJ IDEA recommended)

### Installation

```bash
# Clone the repository
git clone https://github.com/bhargav-thakkar/ui-automation-selenium.git
cd ui-automation-selenium

# Install dependencies
mvn clean install

# Run smoke tests
mvn test -Dsuite=smoke
```

### Your First Test

```java
@Test(groups = "smoke")
public void loginTest() {
    LoginPage loginPage = new LoginPage(driver);
    DashboardPage dashboard = loginPage
        .enterUsername("testuser@example.com")
        .enterPassword("password123")
        .clickLogin();
    
    Assert.assertTrue(dashboard.isWelcomeMessageDisplayed());
}
```

## 📁 Project Structure

```
ui-automation-selenium/
├── src/
│   ├── main/java/
│   │   ├── pages/                     # Page Object classes
│   │   │   ├── common/                # Common page components
│   │   │   └── modules/               # Feature-specific pages
│   │   ├── utils/                     # Utility classes
│   │   │   ├── DriverManager.java     # WebDriver management
│   │   │   ├── ConfigReader.java      # Configuration reader
│   │   │   └── ReportManager.java     # Reporting utilities
│   │   └── constants/                 # Constants and enums
│   └── test/java/
│       ├── tests/                     # Test classes
│       │   ├── smoke/                 # Smoke test suite
│       │   ├── regression/            # Regression test suite
│       │   └── api/                   # API integration tests
│       └── listeners/                 # TestNG listeners
├── src/test/resources/
│   ├── config/                        # Configuration files
│   │   ├── config.properties          # Environment configuration
│   │   └── browsers.json              # Browser configurations
│   ├── testdata/                      # Test data files
│   │   ├── users.xlsx                 # User test data
│   │   └── testcases.json             # Test case data
│   └── suites/                        # TestNG suite files
│       ├── smoke.xml                  # Smoke test suite
│       └── regression.xml             # Regression test suite
├── reports/                           # Generated reports
├── screenshots/                       # Test screenshots
├── docker/                           # Docker configurations
├── .github/workflows/                # GitHub Actions
├── pom.xml                           # Maven configuration
└── README.md
```

## ⚙️ Configuration

### Browser Configuration (`config.properties`)

```properties
# Browser Settings
browser=chrome
headless=false
implicit.wait=10
explicit.wait=20
page.load.timeout=30

# Environment Settings
base.url=https://demo.example.com
api.base.url=https://api.example.com
environment=qa

# Test Data
test.data.excel.path=src/test/resources/testdata/users.xlsx
screenshot.on.failure=true
video.recording=false

# Reporting
extent.report.path=reports/extent-report.html
extent.report.name=UI Automation Test Report
```

### TestNG Suite Configuration (`smoke.xml`)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<suite name="SmokeTestSuite" parallel="methods" thread-count="3">
    <parameter name="browser" value="chrome"/>
    
    <test name="LoginTests">
        <groups>
            <run>
                <include name="smoke"/>
            </run>
        </groups>
        <classes>
            <class name="tests.smoke.LoginTest"/>
            <class name="tests.smoke.DashboardTest"/>
        </classes>
    </test>
</suite>
```

## 🧪 Writing Tests

### Page Object Example

```java
@Component
public class LoginPage extends BasePage {
    
    @FindBy(id = "username")
    private WebElement usernameField;
    
    @FindBy(id = "password")
    private WebElement passwordField;
    
    @FindBy(css = "button[type='submit']")
    private WebElement loginButton;
    
    @FindBy(css = ".error-message")
    private WebElement errorMessage;
    
    public LoginPage(WebDriver driver) {
        super(driver);
        PageFactory.initElements(driver, this);
    }
    
    public LoginPage enterUsername(String username) {
        waitForElementVisible(usernameField);
        usernameField.clear();
        usernameField.sendKeys(username);
        return this;
    }
    
    public LoginPage enterPassword(String password) {
        passwordField.clear();
        passwordField.sendKeys(password);
        return this;
    }
    
    public DashboardPage clickLogin() {
        loginButton.click();
        return new DashboardPage(driver);
    }
    
    public String getErrorMessage() {
        waitForElementVisible(errorMessage);
        return errorMessage.getText();
    }
}
```

### Test Class Example

```java
public class LoginTest extends BaseTest {
    
    @Test(groups = {"smoke", "login"}, 
          description = "Verify successful login with valid credentials")
    public void testValidLogin() {
        ExtentTestManager.getTest().info("Starting valid login test");
        
        LoginPage loginPage = new LoginPage(driver);
        DashboardPage dashboard = loginPage
            .enterUsername(TestDataProvider.getValidUser().getUsername())
            .enterPassword(TestDataProvider.getValidUser().getPassword())
            .clickLogin();
        
        SoftAssert softAssert = new SoftAssert();
        softAssert.assertTrue(dashboard.isWelcomeMessageDisplayed(), 
                             "Welcome message should be displayed");
        softAssert.assertTrue(dashboard.isUserProfileVisible(), 
                             "User profile should be visible");
        softAssert.assertAll();
        
        ExtentTestManager.getTest().pass("Login test completed successfully");
    }
    
    @Test(groups = {"smoke", "login"}, 
          dataProvider = "invalidLoginData",
          description = "Verify login fails with invalid credentials")
    public void testInvalidLogin(String username, String password, String expectedError) {
        LoginPage loginPage = new LoginPage(driver);
        loginPage.enterUsername(username)
                .enterPassword(password)
                .clickLogin();
        
        String actualError = loginPage.getErrorMessage();
        Assert.assertEquals(actualError, expectedError, 
                           "Error message should match expected");
    }
    
    @DataProvider(name = "invalidLoginData")
    public Object[][] getInvalidLoginData() {
        return new Object[][] {
            {"invalid@email.com", "wrongpassword", "Invalid credentials"},
            {"", "password", "Username is required"},
            {"user@email.com", "", "Password is required"}
        };
    }
}
```

## 🎯 Execution

### Local Execution

```bash
# Run all tests
mvn clean test

# Run specific suite
mvn test -Dsuite=smoke

# Run with specific browser
mvn test -Dbrowser=firefox

# Run in headless mode
mvn test -Dheadless=true

# Run with custom thread count
mvn test -DthreadCount=5

# Run specific test groups
mvn test -Dgroups="smoke,regression"
```

### Docker Execution

```bash
# Build Docker image
docker build -t ui-automation-selenium .

# Run tests in container
docker run --rm \
  -v $(pwd)/reports:/app/reports \
  -e BROWSER=chrome \
  -e HEADLESS=true \
  ui-automation-selenium

# Run with Selenium Grid
docker-compose up -d
mvn test -Dremote=true -Dgrid.url=http://localhost:4444
```

### Parallel Execution

```xml
<!-- TestNG Suite for parallel execution -->
<suite name="ParallelSuite" parallel="methods" thread-count="5">
    <parameter name="browser" value="chrome"/>
    <!-- Test configuration -->
</suite>
```

## 📊 Reporting

### ExtentReports Features
- **Rich HTML Reports**: Interactive reports with charts and graphs
- **Screenshot Integration**: Automatic screenshots on failures
- **Test Categorization**: Group tests by features, priorities
- **Historical Trending**: Track test results over time
- **Email Integration**: Send reports via email

### Sample Report Generation

```java
public class ReportManager {
    private static ExtentReports extent;
    
    public static void initReports() {
        ExtentSparkReporter sparkReporter = new ExtentSparkReporter("reports/extent-report.html");
        sparkReporter.config().setTheme(Theme.STANDARD);
        sparkReporter.config().setDocumentTitle("UI Automation Report");
        
        extent = new ExtentReports();
        extent.attachReporter(sparkReporter);
        extent.setSystemInfo("Environment", ConfigReader.getProperty("environment"));
        extent.setSystemInfo("Browser", ConfigReader.getProperty("browser"));
    }
}
```

## 🔄 CI/CD Integration

### GitHub Actions Workflow

```yaml
name: UI Automation Tests

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]
  schedule:
    - cron: '0 2 * * *'  # Run daily at 2 AM

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        browser: [chrome, firefox]
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Set up JDK 11
      uses: actions/setup-java@v3
      with:
        java-version: '11'
        distribution: 'adopt'
    
    - name: Cache Maven dependencies
      uses: actions/cache@v3
      with:
        path: ~/.m2
        key: ${{ runner.os }}-m2-${{ hashFiles('**/pom.xml') }}
    
    - name: Run Tests
      run: |
        mvn clean test -Dbrowser=${{ matrix.browser }} -Dheadless=true
    
    - name: Upload Reports
      uses: actions/upload-artifact@v3
      if: always()
      with:
        name: test-reports-${{ matrix.browser }}
        path: reports/
    
    - name: Publish Test Results
      uses: dorny/test-reporter@v1
      if: always()
      with:
        name: Test Results (${{ matrix.browser }})
        path: target/surefire-reports/*.xml
        reporter: java-junit
```

### Jenkins Pipeline

```groovy
pipeline {
    agent any
    
    parameters {
        choice(
            name: 'BROWSER',
            choices: ['chrome', 'firefox', 'safari'],
            description: 'Browser to run tests'
        )
        choice(
            name: 'SUITE',
            choices: ['smoke', 'regression', 'full'],
            description: 'Test suite to execute'
        )
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', 
                    url: 'https://github.com/bhargav-thakkar/ui-automation-selenium.git'
            }
        }
        
        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }
        
        stage('Test') {
            steps {
                sh """
                    mvn test \
                    -Dbrowser=${params.BROWSER} \
                    -Dsuite=${params.SUITE} \
                    -Dheadless=true
                """
            }
            post {
                always {
                    publishHTML([
                        allowMissing: false,
                        alwaysLinkToLastBuild: true,
                        keepAll: true,
                        reportDir: 'reports',
                        reportFiles: 'extent-report.html',
                        reportName: 'Automation Test Report'
                    ])
                    
                    publishTestResults testResultsPattern: 'target/surefire-reports/*.xml'
                }
            }
        }
    }
    
    post {
        failure {
            emailext (
                subject: "UI Automation Tests Failed - ${env.JOB_NAME} - ${env.BUILD_NUMBER}",
                body: "Test execution failed. Please check the attached report.",
                attachmentsPattern: 'reports/*.html',
                to: 'qa-team@company.com'
            )
        }
    }
}
```

## 🏆 Best Practices

### Code Organization
- **Single Responsibility**: Each page object handles one page/component
- **DRY Principle**: Reusable methods in base classes
- **Meaningful Names**: Clear, descriptive method and variable names
- **Constants**: Use constants for locators and test data

### Test Design
- **Independent Tests**: Each test should run independently
- **Data-Driven**: Parameterize tests with external data
- **Proper Assertions**: Use meaningful assertions with descriptive messages
- **Test Categorization**: Group tests by functionality and priority

### Maintenance
- **Regular Updates**: Keep dependencies and drivers updated
- **Code Reviews**: Implement peer review process
- **Documentation**: Maintain up-to-date documentation
- **Refactoring**: Regular code cleanup and optimization

### Performance
- **Explicit Waits**: Use WebDriverWait instead of Thread.sleep
- **Page Load Strategy**: Configure appropriate page load strategies
- **Resource Cleanup**: Properly close browsers and clean up resources
- **Parallel Execution**: Utilize parallel testing capabilities

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### Code Style Guidelines
- Follow Java naming conventions
- Use meaningful variable and method names
- Add proper JavaDoc comments
- Ensure code coverage > 80%
- Run static code analysis

## 🐛 Troubleshooting

### Common Issues

#### WebDriver Issues
```java
// Issue: WebDriverException - Driver executable not found
// Solution: Use WebDriverManager
WebDriverManager.chromedriver().setup();
```

#### Element Not Found
```java
// Issue: NoSuchElementException
// Solution: Add proper waits
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
WebElement element = wait.until(ExpectedConditions.elementToBeClickable(locator));
```

#### Flaky Tests
```java
// Issue: Intermittent failures
// Solution: Implement retry mechanism
@RetryAnalyzer(RetryAnalyzer.class)
@Test
public void flakyTest() {
    // Test implementation
}
```

### Debug Mode
```bash
# Run with debug logging
mvn test -Dlog4j.configuration=debug-log4j2.xml

# Run single test with debugging
mvn test -Dtest=LoginTest#testValidLogin -X
```

### Performance Optimization
- Use CSS selectors over XPath when possible
- Implement Page Load Strategy
- Optimize test data setup/teardown
- Use headless mode for faster execution

## 📞 Support

- **Email**: thakkar.bhargav001@gmail.com

---

**Made with ❤️ by Bhargav Thakkar** | [LinkedIn](https://linkedin.com/in/bhargav-thakkar-7a56a355)



