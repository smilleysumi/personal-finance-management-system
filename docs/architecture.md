# System Architecture

## 1. Overview

The Personal Finance & Expense Management System is a full-stack web application designed to help users manage personal expenses, categories, and monthly budgets.

Version 1 will use a **modular monolithic architecture**.

The application will have:

* React + TypeScript frontend
* Spring Boot REST API backend
* PostgreSQL database

The backend will follow a layered architecture to keep business logic maintainable, testable, and easy to extend.

---

# 2. High-Level Architecture

```text
                    ┌──────────────────────┐
                    │      Web Browser     │
                    │   React + TypeScript │
                    └──────────┬───────────┘
                               │
                               │ HTTPS / REST
                               ▼
                    ┌──────────────────────┐
                    │    Spring Boot API   │
                    │                      │
                    │  Spring Security     │
                    │  REST Controllers    │
                    │  Services            │
                    │  Repositories        │
                    └──────────┬───────────┘
                               │
                               │ JPA / Hibernate
                               ▼
                    ┌──────────────────────┐
                    │      PostgreSQL      │
                    │                      │
                    │  users               │
                    │  categories          │
                    │  expenses            │
                    │  budgets             │
                    └──────────────────────┘
```

---

# 3. Architecture Style

The backend will initially follow a **modular monolith** architecture.

All core business modules will run inside one Spring Boot application.

```text
                    Spring Boot Application
                             │
          ┌──────────────────┼──────────────────┐
          │                  │                  │
          ▼                  ▼                  ▼
       Auth Module       Expense Module     Budget Module
                              │
                              ▼
                       Category Module
                             │
                             ▼
                       Dashboard Module
```

This approach keeps Version 1 simple while allowing the application to evolve into microservices in the future if required.

---

# 4. Frontend Architecture

The frontend will use React with TypeScript.

```text
frontend/
│
├── components/
├── pages/
├── layouts/
├── services/
├── hooks/
├── types/
├── utils/
└── routes/
```

## Responsibilities

### Components

Reusable UI elements.

Examples:

```text
ExpenseTable
ExpenseForm
BudgetCard
DashboardCard
CategoryChart
```

### Pages

Application-level screens.

Examples:

```text
LoginPage
RegisterPage
DashboardPage
ExpensesPage
CategoriesPage
BudgetsPage
```

### Services

Responsible for communication with the backend.

Example:

```text
expenseService
categoryService
budgetService
authService
dashboardService
```

### Types

TypeScript interfaces and types for API requests and responses.

---

# 5. Backend Architecture

The backend will follow a layered architecture.

```text
                    REST Request
                         │
                         ▼
                ┌─────────────────┐
                │   Controller    │
                └────────┬────────┘
                         │
                         ▼
                ┌─────────────────┐
                │     Service     │
                └────────┬────────┘
                         │
                         ▼
                ┌─────────────────┐
                │   Repository    │
                └────────┬────────┘
                         │
                         ▼
                ┌─────────────────┐
                │    PostgreSQL   │
                └─────────────────┘
```

---

# 6. Controller Layer

The Controller layer handles HTTP requests and responses.

Responsibilities:

* Receive HTTP requests.
* Validate request data.
* Call the appropriate service.
* Return HTTP responses.
* Avoid containing business logic.

Example:

```text
ExpenseController
CategoryController
BudgetController
AuthController
DashboardController
```

Example endpoint:

```text
POST /api/expenses
```

Flow:

```text
HTTP Request
     ↓
ExpenseController
     ↓
ExpenseService
```

---

# 7. Service Layer

The Service layer contains business logic.

Responsibilities:

* Apply business rules.
* Coordinate multiple repositories.
* Handle transactions.
* Perform calculations.
* Enforce application-level authorization rules.

Examples:

```text
ExpenseService
CategoryService
BudgetService
AuthService
DashboardService
```

Example:

```text
Create Expense
      ↓
Validate business rules
      ↓
Verify category ownership
      ↓
Create expense
      ↓
Save expense
```

---

# 8. Repository Layer

The Repository layer handles database access.

Spring Data JPA will be used.

Examples:

```text
UserRepository
ExpenseRepository
CategoryRepository
BudgetRepository
```

The Repository layer should focus on data access rather than business logic.

Example:

```text
ExpenseService
      ↓
ExpenseRepository
      ↓
PostgreSQL
```

---

# 9. Entity Layer

JPA entities represent database tables.

Initial entities:

```text
User
Category
Expense
Budget
```

Example relationship:

```text
User
 │
 ├── Category
 │
 ├── Expense
 │      │
 │      └── Category
 │
 └── Budget
```

Entities will not be exposed directly through REST APIs.

DTOs will be used for API requests and responses.

---

# 10. DTO Layer

DTO stands for Data Transfer Object.

DTOs will separate API models from database entities.

Example:

```text
CreateExpenseRequest
UpdateExpenseRequest
ExpenseResponse
```

Request flow:

```text
HTTP Request
     ↓
CreateExpenseRequest
     ↓
ExpenseService
     ↓
Expense Entity
     ↓
Database
```

Response flow:
