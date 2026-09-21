# Personal Finance & Expense Management System

## 1. Project Overview

The Personal Finance & Expense Management System is a full-stack web application that helps users track their expenses, manage monthly budgets, categorize spending, and view financial summaries.

The application will provide a secure REST API using Spring Boot and a React-based frontend.

---

## 2. Objectives

The main objectives of the system are:

* Allow users to securely register and log in.
* Allow users to record and manage their expenses.
* Organize expenses using categories.
* Allow users to define monthly budgets.
* Provide filtering and pagination for expenses.
* Provide monthly and category-wise expense summaries.
* Provide a dashboard for understanding spending patterns.
* Ensure users can access only their own financial data.
* Provide a maintainable and testable codebase suitable for production-style development.

---

## 3. Target Users

### User

A normal application user who can:

* Register an account.
* Log in securely.
* Manage personal expenses.
* Manage personal categories.
* Create and manage monthly budgets.
* View personal financial summaries.

### Admin

An administrator who may have additional system-management capabilities.

Admin functionality will be kept limited in the initial version and can be expanded later.

---

# 4. Functional Requirements

## 4.1 User Registration

The system shall allow a new user to create an account using:

* Name
* Email
* Password

### Requirements

* Email must be unique.
* Password must not be stored as plain text.
* Required fields must be validated.
* Invalid input must return an appropriate error response.

---

## 4.2 User Login

The system shall allow registered users to log in using their email and password.

After successful authentication, the system shall provide a JWT access token.

Protected APIs shall require valid authentication.

---

## 4.3 Expense Management

Authenticated users shall be able to create, view, update, and delete their own expenses.

Each expense shall contain:

* Amount
* Description
* Expense date
* Category
* Payment method
* Creation timestamp
* Last updated timestamp

### Expense APIs

```text
POST   /api/expenses
GET    /api/expenses
GET    /api/expenses/{id}
PUT    /api/expenses/{id}
DELETE /api/expenses/{id}
```

---

## 4.4 Expense Categories

Users shall be able to organize expenses into categories.

Example categories:

* Food
* Transportation
* Shopping
* Bills
* Entertainment
* Healthcare
* Education
* Other

Users shall be able to:

* Create a category.
* View categories.
* Update a category.
* Delete a category.

### Category APIs

```text
POST   /api/categories
GET    /api/categories
PUT    /api/categories/{id}
DELETE /api/categories/{id}
```

---

## 4.5 Monthly Budget

Users shall be able to create a monthly spending budget.

A budget shall contain:

* Amount
* Month
* Year
* User

Example:

```text
September 2026
Budget: ₹30,000
```

The system shall calculate the amount spent against the budget.

Example:

```text
Budget       ₹30,000
Spent        ₹21,500
Remaining     ₹8,500
```

### Budget APIs

```text
POST /api/budgets
GET  /api/budgets
PUT  /api/budgets/{id}
```

---

## 4.6 Expense Search and Filtering

Users shall be able to filter their expenses using criteria such as:

* Category
* Date range
* Minimum amount
* Maximum amount
* Payment method

Example:

```text
GET /api/expenses?category=Food
```

The system shall support pagination.

Example:

```text
GET /api/expenses?page=0&size=20
```

The system shall also support sorting.

Example:

```text
GET /api/expenses?sort=amount,desc
```

---

## 4.7 Dashboard

The application shall provide a financial dashboard.

The dashboard shall display:

* Total monthly budget
* Total expenses
* Remaining budget
* Number of expenses
* Monthly spending
* Category-wise spending

Example:

```text
Monthly Budget       ₹30,000
Total Expenses       ₹21,500
Remaining             ₹8,500
```

---

## 4.8 Financial Analytics

The system shall provide aggregated financial information.

Examples:

### Monthly spending

```text
January      ₹18,500
February     ₹22,000
March        ₹19,800
April        ₹25,400
```

### Category spending

```text
Food             ₹7,000
Transportation   ₹4,000
Shopping         ₹5,500
Bills            ₹3,000
Other            ₹2,000
```

---

# 5. Security Requirements

The system shall use authentication and authorization.

### Authentication

* JWT-based authentication.
* Passwords shall be securely hashed.
* Protected APIs shall require a valid JWT.

### Authorization

Users shall only be able to access their own financial data.

For example:

```text
User A → User A's expenses ✓
User A → User B's expenses ✗
```

The backend shall enforce this rule rather than relying only on frontend restrictions.

---

# 6. Validation Requirements

The system shall validate incoming API requests.

Examples:

* Amount must be greater than zero.
* Email must have a valid format.
* Required fields cannot be empty.
* Password must satisfy minimum requirements.
* Expense date must be valid.
* Budget amount must be greater than zero.

Invalid requests shall return meaningful error responses.

---

# 7. Error Handling

The backend shall use centralized exception handling.

The API should return a consistent error structure.

Example:

```json
{
  "timestamp": "2026-09-19T10:30:00",
  "status": 400,
  "error": "VALIDATION_ERROR",
  "message": "Amount must be greater than zero"
}
```

The system should handle common errors including:

* Validation errors
* Authentication errors
* Authorization errors
* Resource not found
* Duplicate email
* Database errors
* Invalid request parameters

---

# 8. Non-Functional Requirements

## Performance

* APIs should return responses within a reasonable time under normal load.
* Expense listing shall use pagination.
* Frequently used database queries should be optimized.
* Appropriate database indexes should be used.

## Scalability

The application should be designed so that the backend can be scaled horizontally in the future.

## Maintainability

The backend should follow a clear layered architecture:

```text
Controller
    ↓
Service
    ↓
Repository
    ↓
Database
```

DTOs should be used for API requests and responses rather than exposing database entities directly.

## Testability

Business logic should be independently testable.

The project shall include:

* Unit tests
* Controller/API tests
* Integration tests

---

# 9. Technology Requirements

## Backend

* Java 17
* Spring Boot
* Spring Web
* Spring Data JPA
* Hibernate
* Spring Security
* JWT
* Maven

## Database

* PostgreSQL

## Frontend

* React
* TypeScript
* React Router
* Axios
* Recharts

## Testing

* JUnit 5
* Mockito
* Spring Boot Test

## DevOps

* Git
* GitHub
* Docker
* Docker Compose
* GitHub Actions

---

# 10. Initial API Summary

| Module     | Method | Endpoint                    | Purpose            |
| ---------- | ------ | --------------------------- | ------------------ |
| Auth       | POST   | `/api/auth/register`        | Register user      |
| Auth       | POST   | `/api/auth/login`           | Login              |
| Expenses   | POST   | `/api/expenses`             | Create expense     |
| Expenses   | GET    | `/api/expenses`             | List expenses      |
| Expenses   | GET    | `/api/expenses/{id}`        | View expense       |
| Expenses   | PUT    | `/api/expenses/{id}`        | Update expense     |
| Expenses   | DELETE | `/api/expenses/{id}`        | Delete expense     |
| Categories | POST   | `/api/categories`           | Create category    |
| Categories | GET    | `/api/categories`           | List categories    |
| Categories | PUT    | `/api/categories/{id}`      | Update category    |
| Categories | DELETE | `/api/categories/{id}`      | Delete category    |
| Budgets    | POST   | `/api/budgets`              | Create budget      |
| Budgets    | GET    | `/api/budgets`              | View budgets       |
| Budgets    | PUT    | `/api/budgets/{id}`         | Update budget      |
| Dashboard  | GET    | `/api/dashboard/summary`    | Financial summary  |
| Dashboard  | GET    | `/api/dashboard/monthly`    | Monthly analytics  |
| Dashboard  | GET    | `/api/dashboard/categories` | Category analytics |

---

# 11. Out of Scope for Version 1

The following features will **not** be implemented initially:

* Bank account integration
* Real-time bank transactions
* Payment gateway
* Investment tracking
* Cryptocurrency tracking
* AI financial advisor
* Microservices
* Kafka
* Redis
* Mobile application

These may be considered in future versions.

---

# 12. Future Enhancements

Possible future features include:

* Redis caching
* Email notifications
* Recurring expenses
* Recurring budgets
* CSV/PDF export
* Financial reports
* AI-powered expense categorization
* Spending recommendations
* Bank API integration
* Notification service
* Microservices architecture

---

# 13. Definition of Done

Version 1 will be considered complete when:

* [ ] User can register.
* [ ] User can log in.
* [ ] JWT authentication works.
* [ ] User can create expenses.
* [ ] User can view expenses.
* [ ] User can update expenses.
* [ ] User can delete expenses.
* [ ] User can manage categories.
* [ ] User can create monthly budgets.
* [ ] Expense filtering works.
* [ ] Pagination works.
* [ ] Dashboard displays financial summaries.
* [ ] Backend has unit and integration tests.
* [ ] Frontend is connected to backend.
* [ ] Application runs using Docker.
* [ ] GitHub Actions build and test the project.
* [ ] README contains setup and architecture documentation.
