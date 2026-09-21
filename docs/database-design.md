# Database Design

## 1. Overview

The Personal Finance & Expense Management System uses a relational database to store users, expense categories, expenses, and monthly budgets.

The initial database contains four main entities:

* User
* Category
* Expense
* Budget

The relationships between these entities ensure that each user's financial data remains associated with that user.

---

## 2. Entities

### 2.1 User

The `users` table stores application users.

| Column     | Type         | Constraints      | Description            |
| ---------- | ------------ | ---------------- | ---------------------- |
| id         | BIGINT       | Primary Key      | Unique user identifier |
| name       | VARCHAR(100) | NOT NULL         | User's name            |
| email      | VARCHAR(150) | NOT NULL, UNIQUE | User's email           |
| password   | VARCHAR(255) | NOT NULL         | Hashed password        |
| role       | VARCHAR(20)  | NOT NULL         | User role              |
| created_at | TIMESTAMP    | NOT NULL         | Account creation time  |
| updated_at | TIMESTAMP    | NOT NULL         | Last update time       |

### Relationships

A user can have:

* Many categories
* Many expenses
* Many budgets

Therefore:

```text
User 1 ──────── * Category

User 1 ──────── * Expense

User 1 ──────── * Budget
```

---

## 3. Category

The `categories` table stores expense categories.

Examples:

* Food
* Transportation
* Shopping
* Bills
* Entertainment
* Healthcare
* Education
* Other

| Column      | Type         | Constraints | Description                |
| ----------- | ------------ | ----------- | -------------------------- |
| id          | BIGINT       | Primary Key | Unique category identifier |
| name        | VARCHAR(100) | NOT NULL    | Category name              |
| description | VARCHAR(255) | NULL        | Category description       |
| user_id     | BIGINT       | Foreign Key | Owner of the category      |
| created_at  | TIMESTAMP    | NOT NULL    | Creation time              |
| updated_at  | TIMESTAMP    | NOT NULL    | Last update time           |

### Relationship with User

One user can create multiple categories.

```text
User
  |
  | 1
  |
  | *
Category
```

Therefore:

```text
users.id
     ↓
categories.user_id
```

---

## 4. Expense

The `expenses` table stores individual financial transactions.

| Column         | Type          | Constraints | Description               |
| -------------- | ------------- | ----------- | ------------------------- |
| id             | BIGINT        | Primary Key | Unique expense identifier |
| amount         | DECIMAL(12,2) | NOT NULL    | Expense amount            |
| description    | VARCHAR(255)  | NOT NULL    | Expense description       |
| expense_date   | DATE          | NOT NULL    | Date of expense           |
| payment_method | VARCHAR(30)   | NOT NULL    | Payment method            |
| category_id    | BIGINT        | Foreign Key | Expense category          |
| user_id        | BIGINT        | Foreign Key | Owner of the expense      |
| created_at     | TIMESTAMP     | NOT NULL    | Creation time             |
| updated_at     | TIMESTAMP     | NOT NULL    | Last update time          |

### Relationship with User

One user can have many expenses.

```text
User 1 ───────── * Expense
```

Therefore:

```text
users.id
     ↓
expenses.user_id
```

### Relationship with Category

One category can contain many expenses.

```text
Category 1 ─────── * Expense
```

Therefore:

```text
categories.id
        ↓
expenses.category_id
```

An expense belongs to one category.

Example:

```text
Expense
----------------------
Amount: ₹350
Description: Lunch
Category: Food
User: Sumana
```

---

## 5. Budget

The `budgets` table stores monthly spending limits.

| Column     | Type          | Constraints | Description              |
| ---------- | ------------- | ----------- | ------------------------ |
| id         | BIGINT        | Primary Key | Unique budget identifier |
| amount     | DECIMAL(12,2) | NOT NULL    | Budget amount            |
| month      | INTEGER       | NOT NULL    | Budget month             |
| year       | INTEGER       | NOT NULL    | Budget year              |
| user_id    | BIGINT        | Foreign Key | Owner of the budget      |
| created_at | TIMESTAMP     | NOT NULL    | Creation time            |
| updated_at | TIMESTAMP     | NOT NULL    | Last update time         |

### Relationship with User

One user can have multiple monthly budgets.

```text
User 1 ──────── * Budget
```

Therefore:

```text
users.id
     ↓
budgets.user_id
```

A user should have at most one budget for a particular month and year.

For example:

```text
User
September 2026 → ₹30,000
October 2026   → ₹35,000
November 2026  → ₹30,000
```

A unique constraint should therefore be considered on:

```text
(user_id, month, year)
```

---

# 6. Entity Relationship Diagram

The initial relationship model is:

```text
                       ┌──────────────┐
                       │     USER     │
                       │──────────────│
                       │ id           │
                       │ name         │
                       │ email        │
                       │ password     │
                       │ role         │
                       └──────┬───────┘
                              │
                ┌─────────────┼─────────────┐
                │             │             │
                │ 1           │ 1           │ 1
                │             │             │
                │ *           │ *           │ *
                ▼             ▼             ▼
         ┌────────────┐ ┌────────────┐ ┌────────────┐
         │  CATEGORY  │ │  EXPENSE   │ │   BUDGET   │
         │────────────│ │────────────│ │────────────│
         │ id         │ │ id         │ │ id         │
         │ name       │ │ amount     │ │ amount     │
         │ user_id    │ │ user_id    │ │ month      │
         └─────┬──────┘ │ category_id│ │ year       │
               │        └────────────┘ │ user_id    │
               │                       └────────────┘
               │
               │ 1
               │
               │ *
               ▼
          ┌────────────┐
          │  EXPENSE   │
          └────────────┘
```

---

# 7. Relationship Summary

| Relationship       | Type        | Explanation                              |
| ------------------ | ----------- | ---------------------------------------- |
| User → Category    | One-to-Many | A user can have multiple categories      |
| User → Expense     | One-to-Many | A user can have multiple expenses        |
| User → Budget      | One-to-Many | A user can have multiple monthly budgets |
| Category → Expense | One-to-Many | A category can contain multiple expenses |
| Expense → User     | Many-to-One | Each expense belongs to one user         |
| Expense → Category | Many-to-One | Each expense belongs to one category     |

---

# 8. Foreign Keys

The following foreign keys will be used:

```text
categories.user_id
        ↓
users.id
```

```text
expenses.user_id
        ↓
users.id
```

```text
expenses.category_id
        ↓
categories.id
```

```text
budgets.user_id
        ↓
users.id
```

These foreign keys maintain referential integrity between the tables.

---

# 9. Important Business Rules

### Rule 1 — User data isolation

A user must only be able to access their own:

* Expenses
* Categories
* Budgets

The backend must enforce this rule.

---

### Rule 2 — Expense ownership

Every expense must belong to exactly one user.

```text
Expense → User
```

An expense cannot exist without an owner.

---

### Rule 3 — Category ownership

Categories will initially belong to a specific user.

This means:

```text
User A
 ├── Food
 ├── Travel
 └── Shopping

User B
 ├── Food
 └── Bills
```

Two users can therefore have categories with the same name.

---

### Rule 4 — Budget uniqueness

A user should not have multiple budgets for the same month and year.

Example:

```text
User A
September 2026 → ₹30,000 ✓

User A
September 2026 → ₹25,000 ✗
```

The database should enforce uniqueness using:

```text
(user_id, month, year)
```

---

### Rule 5 — Positive amounts

The following values must be greater than zero:

```text
Expense.amount
Budget.amount
```

Validation should be implemented at the application level, with appropriate database constraints where supported.

---

# 10. Example Data

### Users

| id | name   | email                                         |
| -: | ------ | --------------------------------------------- |
|  1 | User A | [usera@example.com](mailto:usera@example.com) |
|  2 | User B | [userb@example.com](mailto:userb@example.com) |

### Categories

| id | name           | user_id |
| -: | -------------- | ------: |
|  1 | Food           |       1 |
|  2 | Transportation |       1 |
|  3 | Shopping       |       1 |
|  4 | Food           |       2 |

### Expenses

| id |  amount | description | category_id | user_id |
| -: | ------: | ----------- | ----------: | ------: |
|  1 |  350.00 | Lunch       |           1 |       1 |
|  2 |  200.00 | Metro       |           2 |       1 |
|  3 | 1200.00 | Clothes     |           3 |       1 |
|  4 |  500.00 | Dinner      |           4 |       2 |

### Budgets

| id |   amount | month | year | user_id |
| -: | -------: | ----: | ---: | ------: |
|  1 | 30000.00 |     9 | 2026 |       1 |
|  2 | 25000.00 |     9 | 2026 |       2 |

---

# 11. Future Database Extensions

The initial version intentionally keeps the database simple.

Future versions may add:

```text
Payment
RecurringExpense
Notification
FinancialGoal
Income
Attachment
AuditLog
```

These will not be included in Version 1 unless required by the project roadmap.

---

# 12. Version 1 Database Scope

For Version 1, the database will contain:

```text
users
categories
expenses
budgets
```

Relationship structure:

```text
                 USER
              /    |    \
             /     |     \
            ▼      ▼      ▼
       CATEGORY  EXPENSE  BUDGET
                    │
                    │
                    ▼
                CATEGORY
```

This design will be implemented using PostgreSQL and mapped to Java entities using JPA/Hibernate.
