# Finance App API Documentation

## Overview

The Finance App is a full-stack personal finance management application with:

- **Frontend**: React + TypeScript + Vite + Tailwind CSS
- **Backend**: Node.js + Express + MongoDB (Mongoose)
- **Authentication**: JWT-based token authentication

## Base URL

```
Development: http://localhost:5000
```

All API endpoints (except auth) require the `Authorization` header:
```
Authorization: Bearer <your_jwt_token>
```

---

## Table of Contents

1. [Authentication](#authentication)
   - [Register](#register)
   - [Login](#login)
   - [Get Current User](#get-current-user)
2. [Profile](#profile)
   - [Update Profile](#update-profile)
   - [Change Password](#change-password)
   - [Wipe Financial Data](#wipe-financial-data)
3. [Budget](#budget)
   - [Get Budget](#get-budget)
   - [Get Multiple Budgets](#get-multiple-budgets)
   - [Save Budget](#save-budget)
   - [Update Income](#update-income)
   - [Save Widget](#save-widget)
   - [Delete Widget](#delete-widget)
4. [Transactions](#transactions)
   - [Add Transaction](#add-transaction)
   - [Get All Transactions](#get-all-transactions)
   - [Get Summary](#get-summary)
   - [Delete Transaction](#delete-transaction)
5. [Models](#models)
6. [Error Handling](#error-handling)

---

## Authentication

### Register

Create a new user account.

**Endpoint**: `POST /api/auth/register`

**Request Body**:
```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "password123"
}
```

**Response** (201 Created):
```json
{
  "message": "User created successfully",
  "user": {
    "_id": "64abc123...",
    "name": "John Doe",
    "email": "john@example.com"
  }
}
```

**Errors**:
- `400` - User already exists
- `500` - Error creating user

---

### Login

Authenticate and receive a JWT token.

**Endpoint**: `POST /api/auth/login`

**Request Body**:
```json
{
  "email": "john@example.com",
  "password": "password123"
}
```

**Response** (200 OK):
```json
{
  "message": "Login successful",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "_id": "64abc123...",
    "name": "John Doe",
    "email": "john@example.com"
  }
}
```

**Token Expiry**: 1 day

**Errors**:
- `400` - Invalid credentials

---

### Get Current User

Get the currently authenticated user's profile.

**Endpoint**: `GET /api/profile/me`

**Headers**:
```
Authorization: Bearer <token>
```

**Response** (200 OK):
```json
{
  "user": {
    "_id": "64abc123...",
    "name": "John Doe",
    "email": "john@example.com",
    "phone": "+1 555 123 4567",
    "dateOfBirth": "1990-01-15",
    "country": "United States",
    "stateProvince": "California",
    "zipCode": "90210",
    "streetAddress": "123 Main Street",
    "createdAt": "2024-01-01T00:00:00.000Z",
    "updatedAt": "2024-01-15T00:00:00.000Z"
  }
}
```

---

## Profile

### Update Profile

Update user profile information.

**Endpoint**: `PUT /api/profile/me`

**Headers**:
```
Authorization: Bearer <token>
Content-Type: application/json
```

**Request Body** (all fields optional):
```json
{
  "phone": "+1 555 123 4567",
  "dateOfBirth": "1990-01-15",
  "country": "United States",
  "stateProvince": "California",
  "zipCode": "90210",
  "streetAddress": "123 Main Street"
}
```

**Response** (200 OK):
```json
{
  "message": "Profile updated",
  "user": {
    "_id": "64abc123...",
    "name": "John Doe",
    "email": "john@example.com",
    "phone": "+1 555 123 4567",
    "dateOfBirth": "1990-01-15",
    "country": "United States",
    "stateProvince": "California",
    "zipCode": "90210",
    "streetAddress": "123 Main Street"
  }
}
```

---

### Change Password

Change the user's password.

**Endpoint**: `PUT /api/profile/change-password`

**Headers**:
```
Authorization: Bearer <token>
Content-Type: application/json
```

**Request Body**:
```json
{
  "currentPassword": "oldpassword123",
  "newPassword": "newpassword456"
}
```

**Response** (200 OK):
```json
{
  "message": "Password updated successfully"
}
```

**Errors**:
- `400` - Current password is incorrect
- `500` - Error changing password

---

### Wipe Financial Data

Delete all financial data (transactions) for the user. User account remains active.

**Endpoint**: `DELETE /api/profile/wipe-data`

**Headers**:
```
Authorization: Bearer <token>
Content-Type: application/json
```

**Request Body**:
```json
{
  "password": "userpassword123"
}
```

**Response** (200 OK):
```json
{
  "message": "All financial data wiped successfully"
}
```

**Errors**:
- `400` - Incorrect password
- `500` - Error wiping data

---

## Budget

### Get Budget

Get budget data for a specific month.

**Endpoint**: `GET /api/budget?monthKey=YYYY-MM`

**Headers**:
```
Authorization: Bearer <token>
```

**Query Parameters**:
- `monthKey` (required): Format `YYYY-MM` (e.g., `2026-03`)

**Response** (200 OK):
```json
{
  "income": 5000,
  "widgets": [
    {
      "id": "12345",
      "category": "Housing",
      "name": "Rent",
      "budgeted": 1500,
      "spent": 1500,
      "dueDate": "2026-03-01"
    },
    {
      "id": "12346",
      "category": "Food",
      "name": "Groceries",
      "budgeted": 500,
      "spent": 320.50,
      "dueDate": null
    }
  ],
  "monthKey": "2026-03"
}
```

---

### Get Multiple Budgets

Get budget data for multiple months at once.

**Endpoint**: `GET /api/budget/multiple?monthKeys=2026-02,2026-03,2026-04`

**Headers**:
```
Authorization: Bearer <token>
```

**Query Parameters**:
- `monthKeys` (required): Comma-separated list of month keys

**Response** (200 OK):
```json
{
  "2026-02": {
    "income": 5000,
    "widgets": [...],
    "monthKey": "2026-02"
  },
  "2026-03": {
    "income": 5000,
    "widgets": [...],
    "monthKey": "2026-03"
  },
  "2026-04": {
    "income": 0,
    "widgets": [],
    "monthKey": "2026-04"
  }
}
```

---

### Save Budget

Create or update an entire budget for a month.

**Endpoint**: `POST /api/budget`

**Headers**:
```
Authorization: Bearer <token>
Content-Type: application/json
```

**Request Body**:
```json
{
  "monthKey": "2026-03",
  "income": 5000,
  "widgets": [
    {
      "id": "12345",
      "category": "Housing",
      "name": "Rent",
      "budgeted": 1500,
      "spent": 1500,
      "dueDate": "2026-03-01"
    }
  ]
}
```

**Response** (200 OK):
```json
{
  "message": "Budget saved successfully",
  "budget": {
    "income": 5000,
    "widgets": [...],
    "monthKey": "2026-03"
  }
}
```

---

### Update Income

Update only the income for a specific month.

**Endpoint**: `PUT /api/budget/income`

**Headers**:
```
Authorization: Bearer <token>
Content-Type: application/json
```

**Request Body**:
```json
{
  "monthKey": "2026-03",
  "income": 5500
}
```

**Response** (200 OK):
```json
{
  "message": "Income updated",
  "income": 5500
}
```

---

### Save Widget

Add or update a single budget widget within a month's budget.

**Endpoint**: `PUT /api/budget/widget`

**Headers**:
```
Authorization: Bearer <token>
Content-Type: application/json
```

**Request Body**:
```json
{
  "monthKey": "2026-03",
  "widget": {
    "id": "12345",
    "category": "Food",
    "name": "Groceries",
    "budgeted": 500,
    "spent": 320.50,
    "dueDate": null
  }
}
```

**Widget Categories**:
- `Personal`
- `Medical`
- `Housing`
- `Bills/Utilities`
- `Transportation`
- `Food`

**Response** (200 OK):
```json
{
  "message": "Widget saved",
  "widget": {
    "id": "12345",
    "category": "Food",
    "name": "Groceries",
    "budgeted": 500,
    "spent": 320.50,
    "dueDate": null
  }
}
```

---

### Delete Widget

Remove a budget widget from a month's budget.

**Endpoint**: `DELETE /api/budget/widget`

**Headers**:
```
Authorization: Bearer <token>
Content-Type: application/json
```

**Request Body**:
```json
{
  "monthKey": "2026-03",
  "widgetId": "12345"
}
```

**Response** (200 OK):
```json
{
  "message": "Widget deleted"
}
```

---

## Transactions

### Add Transaction

Create a new income or expense transaction.

**Endpoint**: `POST /api/transactions`

**Headers**:
```
Authorization: Bearer <token>
Content-Type: application/json
```

**Request Body**:
```json
{
  "amount": 150.00,
  "type": "expense",
  "category": "Food",
  "description": "Weekly groceries",
  "date": "2026-03-15"
}
```

**Fields**:
- `amount` (required): Number, must be positive
- `type` (required): Either `"income"` or `"expense"`
- `category` (optional): Category name string
- `description` (optional): Text description
- `date` (optional): ISO date string, defaults to current date

**Response** (201 Created):
```json
{
  "message": "Transaction added successfully",
  "transaction": {
    "_id": "64xyz789...",
    "userId": "64abc123...",
    "amount": 150.00,
    "type": "expense",
    "category": "Food",
    "description": "Weekly groceries",
    "date": "2026-03-15T00:00:00.000Z"
  }
}
```

---

### Get All Transactions

Retrieve all transactions for the authenticated user, sorted by date descending.

**Endpoint**: `GET /api/transactions`

**Headers**:
```
Authorization: Bearer <token>
```

**Response** (200 OK):
```json
[
  {
    "_id": "64xyz789...",
    "userId": "64abc123...",
    "amount": 150.00,
    "type": "expense",
    "category": "Food",
    "description": "Weekly groceries",
    "date": "2026-03-15T00:00:00.000Z"
  },
  {
    "_id": "64xyz790...",
    "userId": "64abc123...",
    "amount": 5000.00,
    "type": "income",
    "category": "Income",
    "description": "Monthly salary",
    "date": "2026-03-01T00:00:00.000Z"
  }
]
```

---

### Get Summary

Get financial summary (total income, total expenses, balance).

**Endpoint**: `GET /api/transactions/summary`

**Headers**:
```
Authorization: Bearer <token>
```

**Response** (200 OK):
```json
{
  "income": 5000.00,
  "expense": 1234.56,
  "balance": 3765.44
}
```

---

### Delete Transaction

Delete a specific transaction by ID.

**Endpoint**: `DELETE /api/transactions/:id`

**Headers**:
```
Authorization: Bearer <token>
```

**URL Parameters**:
- `id`: The transaction ID to delete

**Response** (200 OK):
```json
{
  "message": "Transaction deleted"
}
```

---

## Models

### User Model

```javascript
{
  _id: ObjectId,
  name: String,
  email: String (unique),
  password: String (hashed),
  phone: String (optional),
  dateOfBirth: String (optional),
  country: String (optional),
  stateProvince: String (optional),
  zipCode: String (optional),
  streetAddress: String (optional),
  createdAt: Date,
  updatedAt: Date
}
```

### Budget Model

```javascript
{
  _id: ObjectId,
  userId: ObjectId (ref: User),
  monthKey: String, // "YYYY-MM" format
  income: Number,
  widgets: [{
    id: String,
    category: String (enum),
    name: String,
    budgeted: Number,
    spent: Number,
    dueDate: String (optional)
  }],
  createdAt: Date,
  updatedAt: Date
}
```

### Transaction Model

```javascript
{
  _id: ObjectId,
  userId: ObjectId (ref: User),
  amount: Number,
  type: String (enum: "income", "expense"),
  category: String,
  description: String,
  date: Date
}
```

---

## Error Handling

All error responses follow this format:

```json
{
  "message": "Error description"
}
```

### Common HTTP Status Codes

| Code | Meaning |
|------|---------|
| 200 | Success |
| 201 | Created |
| 400 | Bad Request / Invalid input |
| 401 | Unauthorized (missing or invalid token) |
| 404 | Not Found |
| 500 | Internal Server Error |

---

## Frontend Integration

### API Base Configuration

The Vite dev server proxies `/api/*` requests to `http://localhost:5000`. In production, update your environment variables accordingly.

### Auth Headers Helper

```typescript
// Helper to get auth headers
const getAuthHeaders = () => ({
  "Content-Type": "application/json",
  Authorization: `Bearer ${localStorage.getItem("token")}`,
});

// Example usage
fetch("/api/transactions", {
  headers: getAuthHeaders(),
});
```

### Connecting Frontend to Backend

1. **Authentication Flow**:
   - Register/Login stores the JWT token in localStorage
   - All authenticated requests include `Authorization: Bearer <token>` header
   - Token expires after 1 day

2. **Data Flow**:
   - Budget and transaction data is persisted to MongoDB via API
   - On app load, context providers fetch initial data
   - Optimistic updates for better UX with rollback on error

3. **Profile Updates**:
   - Profile changes call `PUT /api/profile/me`
   - Password changes call `PUT /api/profile/change-password`
   - Data wipe calls `DELETE /api/profile/wipe-data`

---

## Environment Variables

Create a `.env` file in the root directory:

```env
MONGO_URI=mongodb://localhost:27017/finance-app
JWT_SECRET=your-secret-key-change-in-production
PORT=5000
```

---

## Running the Application

### Development

```bash
# Start backend (from finance-code directory)
npm run server

# Start frontend (from finance-code directory, separate terminal)
npm run dev

# Or run both together
npm run dev:full
```

### Production

1. Build the frontend:
```bash
npm run build
```

2. Set environment variables on your server

3. Start the backend:
```bash
npm start
```
