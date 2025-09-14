# 🌐 API Automation Framework - REST Assured + SuperTest

Enterprise-grade API testing frameworks featuring REST Assured (Java) for robust enterprise testing and SuperTest (Node.js) for modern, fast API validation with comprehensive reporting and CI/CD integration.

## 📋 Table of Contents

- [Framework Comparison](#-framework-comparison)
- [Quick Start](#-quick-start)
- [Project Structure](#-project-structure)
- [REST Assured + Java](#-rest-assured--java)
- [SuperTest + Node.js](#-supertest--nodejs)
- [Common Features](#-common-features)
- [Configuration](#-configuration)
- [Test Examples](#-test-examples)
- [Reporting](#-reporting)
- [CI/CD Integration](#-cicd-integration)
- [Best Practices](#-best-practices)

## 🔧 Framework Comparison

| Feature | REST Assured (Java) | SuperTest (Node.js) |
|---------|---------------------|---------------------|
| **Performance** | High throughput | Ultra-fast execution |
| **Learning Curve** | Moderate | Easy |
| **Enterprise Ready** | ✅ Excellent | ✅ Good |
| **JSON Handling** | JsonPath | Native JS Objects |
| **Schema Validation** | JSON Schema | Joi/Ajv |
| **Parallel Execution** | TestNG/JUnit | Mocha/Jest |
| **Reporting** | ExtentReports/Allure | Mochawesome/Allure |
| **CI/CD Integration** | Maven/Gradle | npm scripts |

## 🚀 Quick Start

### REST Assured + Java Setup

```bash
# Clone and setup Java framework
git clone https://github.com/bhargav-thakkar/api-automation.git
cd api-automation/rest-assured-java

# Install dependencies
mvn clean install

# Run API tests
mvn test -Dsuite=smoke
```

### SuperTest + Node.js Setup

```bash
# Setup Node.js framework
cd api-automation/supertest-nodejs

# Install dependencies
npm install

# Run API tests
npm run test:smoke
```

## 📁 Project Structure

```
api-automation/
├── rest-assured-java/                 # Java-based API testing
│   ├── src/
│   │   ├── main/java/
│   │   │   ├── clients/               # API client classes
│   │   │   ├── models/                # POJO/Data models
│   │   │   ├── utils/                 # Utilities & helpers
│   │   │   └── config/                # Configuration classes
│   │   └── test/java/
│   │       ├── tests/                 # Test classes
│   │       ├── data/                  # Test data providers
│   │       └── listeners/             # TestNG listeners
│   ├── src/test/resources/
│   │   ├── config.properties          # Environment config
│   │   ├── testdata/                  # JSON/Excel test data
│   │   └── suites/                    # TestNG suites
│   └── pom.xml                        # Maven configuration
├── supertest-nodejs/                  # Node.js-based API testing
│   ├── src/
│   │   ├── clients/                   # API client modules
│   │   ├── models/                    # Data models & schemas
│   │   ├── utils/                     # Helper functions
│   │   └── config/                    # Configuration files
│   ├── tests/                         # Test specifications
│   │   ├── smoke/                     # Smoke test suite
│   │   ├── regression/                # Regression tests
│   │   └── integration/               # Integration tests
│   ├── data/                          # Test data (JSON)
│   ├── reports/                       # Generated reports
│   └── package.json                   # Node.js dependencies
└── README.md
```

## ☕ REST Assured + Java

### 🏗️ Core Features
- **Fluent API**: Human-readable test syntax
- **JSON/XML Support**: Built-in parsers and validators
- **Authentication**: OAuth, Basic Auth, API Keys
- **Schema Validation**: JSON Schema validation
- **Data-Driven Testing**: Excel/JSON data providers
- **Parallel Execution**: TestNG-based parallel testing

### 🔧 Dependencies (Maven)

```xml
<dependencies>
    <dependency>
        <groupId>io.rest-assured</groupId>
        <artifactId>rest-assured</artifactId>
        <version>5.3.2</version>
    </dependency>
    <dependency>
        <groupId>org.testng</groupId>
        <artifactId>testng</artifactId>
        <version>7.8.0</version>
    </dependency>
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>2.15.2</version>
    </dependency>
    <dependency>
        <groupId>com.aventstack</groupId>
        <artifactId>extentreports</artifactId>
        <version>5.0.9</version>
    </dependency>
</dependencies>
```

### 📝 Example Test Class

```java
public class UserAPITest extends BaseTest {
    
    @Test(groups = "smoke")
    public void testCreateUser() {
        User user = User.builder()
            .name("John Doe")
            .email("john@example.com")
            .build();
        
        Response response = given()
            .spec(requestSpec)
            .body(user)
        .when()
            .post("/users")
        .then()
            .statusCode(201)
            .body("name", equalTo(user.getName()))
            .body("email", equalTo(user.getEmail()))
            .extract().response();
        
        // Store user ID for cleanup
        String userId = response.jsonPath().getString("id");
        testDataManager.storeUserId(userId);
    }
    
    @Test(groups = "regression", dependsOnMethods = "testCreateUser")
    public void testGetUser() {
        String userId = testDataManager.getUserId();
        
        given()
            .spec(requestSpec)
            .pathParam("id", userId)
        .when()
            .get("/users/{id}")
        .then()
            .statusCode(200)
            .body("id", equalTo(userId))
            .body("name", notNullValue())
            .body("email", matchesPattern(EMAIL_REGEX));
    }
}
```

## 🚀 SuperTest + Node.js

### 🏗️ Core Features
- **Lightweight**: Minimal overhead, maximum speed
- **Modern Syntax**: ES6/ES7 with async/await
- **Flexible Assertions**: Chai, Should.js integration
- **JSON Schema**: Joi/Ajv validation
- **Mocking**: Nock for API mocking
- **Real-time Reporting**: Live test results

### 🔧 Dependencies (package.json)

```json
{
  "dependencies": {
    "supertest": "^6.3.3",
    "mocha": "^10.2.0",
    "chai": "^4.3.8",
    "joi": "^17.9.2",
    "axios": "^1.5.0",
    "dotenv": "^16.3.1"
  },
  "devDependencies": {
    "mochawesome": "^7.1.3",
    "nyc": "^15.1.0",
    "eslint": "^8.47.0"
  }
}
```

### 📝 Example Test Suite

```javascript
const request = require('supertest');
const { expect } = require('chai');
const Joi = require('joi');
const app = require('../src/app');
const { userSchema, errorSchema } = require('../src/schemas/userSchemas');

describe('User API Tests', () => {
  let createdUserId;

  describe('POST /api/users - Create User', () => {
    it('should create a new user with valid data', async () => {
      const userData = {
        name: 'Jane Doe',
        email: 'jane@example.com',
        age: 25
      };

      const response = await request(app)
        .post('/api/users')
        .send(userData)
        .expect(201)
        .expect('Content-Type', /json/);

      // Schema validation
      const { error, value } = userSchema.validate(response.body);
      expect(error).to.be.undefined;
      
      // Assertions
      expect(response.body).to.have.property('id');
      expect(response.body.name).to.equal(userData.name);
      expect(response.body.email).to.equal(userData.email);
      
      // Store for cleanup
      createdUserId = response.body.id;
    });

    it('should return 400 for invalid email format', async () => {
      const invalidUser = {
        name: 'Invalid User',
        email: 'invalid-email',
        age: 25
      };

      const response = await request(app)
        .post('/api/users')
        .send(invalidUser)
        .expect(400);

      expect(response.body).to.have.property('error');
      expect(response.body.error).to.include('email');
    });
  });

  describe('GET /api/users/:id - Get User', () => {
    it('should retrieve user by ID', async () => {
      const response = await request(app)
        .get(`/api/users/${createdUserId}`)
        .expect(200)
        .expect('Content-Type', /json/);

      expect(response.body.id).to.equal(createdUserId);
      expect(response.body).to.have.all.keys('id', 'name', 'email', 'age', 'createdAt');
    });

    it('should return 404 for non-existent user', async () => {
      await request(app)
        .get('/api/users/999999')
        .expect(404);
    });
  });

  // Cleanup
  after(async () => {
    if (createdUserId) {
      await request(app)
        .delete(`/api/users/${createdUserId}`)
        .expect(204);
    }
  });
});
```

## 🚀 Common Features

### 🔄 Data-Driven Testing

**REST Assured (Java)**:
```java
@DataProvider(name = "userTestData")
public Object[][] getUserData() {
    return ExcelUtils.getTestData("users.xlsx", "ValidUsers");
}

@Test(dataProvider = "userTestData")
public void testCreateUserWithDifferentData(String name, String email, int expectedStatus) {
    // Test implementation
}
```

**SuperTest (Node.js)**:
```javascript
const testData = require('../data/users.json');

testData.validUsers.forEach((userData, index) => {
  it(`should create user ${index + 1}`, async () => {
    await request(app)
      .post('/api/users')
      .send(userData)
      .expect(201);
  });
});
```

### 🔐 Authentication Handling

**REST Assured**:
```java
// OAuth 2.0
given()
    .auth().oauth2(accessToken)
    .when()
    .get("/protected/resource")

// API Key
given()
    .header("X-API-Key", apiKey)
    .when()
    .get("/api/data")
```

**SuperTest**:
```javascript
// Bearer Token
await request(app)
  .get('/api/protected')
  .set('Authorization', `Bearer ${token}`)
  .expect(200);

// API Key
await request(app)
  .get('/api/data')
  .set('X-API-Key', process.env.API_KEY)
  .expect(200);
```

## ⚙️ Configuration

### Environment Configuration

**REST Assured (config.properties)**:
```properties
# API Configuration
base.url=https://api.example.com
api.version=v1
timeout=30000

# Authentication
api.key=${API_KEY}
oauth.client.id=${OAUTH_CLIENT_ID}
oauth.client.secret=${OAUTH_CLIENT_SECRET}

# Database
db.url=${DB_URL}
db.username=${DB_USERNAME}
db.password=${DB_PASSWORD}

# Reporting
extent.report.path=reports/api-test-report.html
```

**SuperTest (.env)**:
```env
# API Configuration
BASE_URL=https://api.example.com
API_VERSION=v1
REQUEST_TIMEOUT=30000

# Authentication
API_KEY=your_api_key_here
OAUTH_CLIENT_ID=your_client_id
OAUTH_CLIENT_SECRET=your_client_secret

# Test Environment
NODE_ENV=qa
LOG_LEVEL=info
```

## 📊 Reporting

### REST Assured - ExtentReports Integration

```java
public class ExtentReportManager {
    private static ExtentReports extent;
    
    public static void initReports() {
        ExtentSparkReporter sparkReporter = new ExtentSparkReporter("reports/api-test-report.html");
        sparkReporter.config().setTheme(Theme.STANDARD);
        sparkReporter.config().setDocumentTitle("API Test Report");
        
        extent = new ExtentReports();
        extent.attachReporter(sparkReporter);
        extent.setSystemInfo("Framework", "REST Assured");
        extent.setSystemInfo("Environment", ConfigReader.getProperty("environment"));
    }
}
```

### SuperTest - Mochawesome Reports

```javascript
// mocha.opts or package.json script
"test": "mocha tests/**/*.spec.js --reporter mochawesome --reporter-options reportDir=reports,reportFilename=api-test-report"
```

## 🔄 CI/CD Integration

### GitHub Actions Workflow

```yaml
name: API Tests

on:
  push:
    branches: [ main, develop ]
  schedule:
    - cron: '0 */4 * * *'  # Every 4 hours

jobs:
  rest-assured-tests:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    
    - name: Set up JDK 11
      uses: actions/setup-java@v3
      with:
        java-version: '11'
    
    - name: Run REST Assured Tests
      run: |
        cd rest-assured-java
        mvn clean test -Denv=qa
    
    - name: Upload Reports
      uses: actions/upload-artifact@v3
      with:
        name: rest-assured-reports
        path: rest-assured-java/reports/

  supertest-tests:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'
    
    - name: Install Dependencies
      run: |
        cd supertest-nodejs
        npm ci
    
    - name: Run SuperTest Tests
      run: |
        cd supertest-nodejs
        npm run test:ci
    
    - name: Upload Reports
      uses: actions/upload-artifact@v3
      with:
        name: supertest-reports
        path: supertest-nodejs/reports/
```

## 🏆 Best Practices

### 🔧 Framework Guidelines

#### REST Assured Best Practices
- **Request Specification**: Use RequestSpecBuilder for common configs
- **Response Validation**: Implement comprehensive response validation
- **Data Management**: Use builders for test data creation
- **Error Handling**: Proper exception handling and logging
- **Parallel Execution**: Configure TestNG for optimal parallel runs

#### SuperTest Best Practices
- **Async/Await**: Use modern async patterns consistently
- **Schema Validation**: Validate response structure with Joi
- **Test Isolation**: Ensure tests don't depend on each other
- **Environment Variables**: Use dotenv for configuration
- **Cleanup**: Implement proper test data cleanup

### 📈 Performance Optimization
- **Connection Pooling**: Reuse HTTP connections
- **Parallel Execution**: Run tests in parallel
- **Data Setup**: Minimize database calls
- **Assertions**: Use efficient assertion libraries
- **Reporting**: Generate reports asynchronously

## 🐛 Troubleshooting

### Common Issues

**Connection Timeouts**:
```java
// REST Assured
RequestSpecBuilder builder = new RequestSpecBuilder()
    .setConnectTimeout(10000)
    .setSocketTimeout(60000);
```

```javascript
// SuperTest
const request = require('supertest');
app.timeout(60000);
```

**JSON Parsing Errors**:
```java
// Validate JSON before parsing
if (response.getContentType().contains("application/json")) {
    JsonPath jsonPath = response.jsonPath();
}
```

```javascript
// Check content type
if (response.headers['content-type'].includes('application/json')) {
    const data = response.body;
}
```

---

**Enterprise Ready** | **Battle Tested** | **Production Proven**

