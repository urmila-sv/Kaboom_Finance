# Finance App - Project Structure Guide

A complete full-stack personal finance application with React frontend and Node.js backend.

---

## Overall Project Structure

```
D:/finance-project/
├── finance-code/           # Main project folder
│   ├── server/            # Backend (Node.js + Express + MongoDB)
│   │   ├── config/        # Database configuration
│   │   ├── Controllers/    # Request handlers (business logic)
│   │   ├── middleware/     # Authentication middleware
│   │   ├── models/        # Database schemas
│   │   ├── routers/        # API route definitions
│   │   └── server.js      # Express server entry point
│   │
│   ├── src/               # Frontend (React + TypeScript)
│   │   ├── app/           # Main application code
│   │   │   ├── components/# Reusable UI components
│   │   │   ├── context/   # React Context (state management)
│   │   │   └── pages/     # Page components
│   │   ├── styles/         # CSS files
│   │   └── main.tsx        # React entry point
│   │
│   ├── server/            # Server package.json
│   ├── package.json       # Frontend package.json
│   ├── vite.config.ts     # Vite build configuration
│   ├── API_DOCUMENTATION.md # API documentation
│   └── .env               # Environment variables
│
└── .env                   # Root environment variables
```

---

## Backend Structure (`finance-code/server/`)

### `server.js` - The Entry Point

**What it does:** This is the first file that runs when you start the backend server.

```javascript
// Key responsibilities:
1. Setup Express app
2. Connect to MongoDB database
3. Register all API route handlers
4. Start the server on port 5000
```

**Important paths:**
- `/api/auth/*` → Authentication routes (login, register)
- `/api/profile/*` → User profile routes
- `/api/budget/*` → Budget management routes
- `/api/transactions/*` → Transaction routes

---

### `config/db.js` - Database Connection

**What it does:** Connects your Node.js server to MongoDB.

```javascript
// What happens:
1. Uses Mongoose (MongoDB library)
2. Reads MONGO_URI from .env file
3. Connects to MongoDB
4. Logs "MongoDB connected" on success
```

**You don't need to modify this file** - it just handles the connection.

---

### `models/` - Database Schemas

Think of models as "blueprints" for your data. They define what each piece of data looks like.

#### `models/user.js`
```javascript
User {
  _id: ObjectId,        // Auto-generated unique ID
  name: String,          // User's full name
  email: String,         // Unique email address
  password: String,      // Hashed password (never plain text!)
  phone: String,         // Optional phone number
  dateOfBirth: String,   // Optional date of birth
  country: String,       // Optional country
  stateProvince: String,  // Optional state/province
  zipCode: String,       // Optional zip/postal code
  streetAddress: String, // Optional street address
  createdAt: Date,       // Auto-set when created
  updatedAt: Date        // Auto-set when updated
}
```

#### `models/Transaction.js`
```javascript
Transaction {
  _id: ObjectId,         // Auto-generated unique ID
  userId: ObjectId,      // Reference to the User who owns this
  amount: Number,        // How much money (e.g., 150.00)
  type: String,          // Either "income" or "expense"
  category: String,      // Category name (e.g., "Food", "Housing")
  description: String,   // Optional description
  date: Date            // When the transaction happened
}
```

#### `models/Budget.js`
```javascript
Budget {
  _id: ObjectId,         // Auto-generated unique ID
  userId: ObjectId,      // Reference to the User
  monthKey: String,      // Format "YYYY-MM" (e.g., "2026-03")
  income: Number,        // Total monthly income
  widgets: [             // Array of budget items
    {
      id: String,        // Unique ID for this widget
      category: String,  // Category name
      name: String,      // Display name (e.g., "Rent")
      budgeted: Number,  // Budgeted amount
      spent: Number,     // Amount actually spent
      dueDate: String   // Optional due date
    }
  ]
}
```

---

### `middleware/authMiddleware.js` - Security Guard

**What it does:** Protects routes by checking if the user is logged in.

```javascript
// How it works:
1. Looks for "Authorization" header in request
2. Extracts the JWT token (format: "Bearer <token>")
3. Verifies the token is valid (not expired, correct secret)
4. If valid: adds user info to request and continues
5. If invalid: returns 401 error
```

**You add this middleware to any route that needs protection.**

---

### `Controllers/` - Business Logic

Controllers contain the actual logic for handling requests. They:
1. Receive the request
2. Do stuff with the database (using Models)
3. Send back a response

#### `Controllers/authController.js`

| Function | What it does |
|----------|--------------|
| `getMe` | Gets the current user's profile |
| `register` | Creates a new user account |
| `login` | Checks email/password, returns JWT token |

#### `Controllers/profileController.js`

| Function | What it does |
|----------|--------------|
| `getMe` | Gets user profile |
| `updateProfile` | Updates user info (phone, address, etc.) |
| `changePassword` | Changes user's password |
| `wipeData` | Deletes all user's transactions |

#### `Controllers/budgetController.js`

| Function | What it does |
|----------|--------------|
| `getBudget` | Gets budget for one month |
| `getBudgets` | Gets budgets for multiple months |
| `saveBudget` | Saves entire budget for a month |
| `updateIncome` | Updates only the income field |
| `saveWidget` | Adds or updates a budget widget |
| `deleteWidget` | Removes a budget widget |

#### `Controllers/transactionController.js`

| Function | What it does |
|----------|--------------|
| `addTransaction` | Creates a new income/expense |
| `getTransactions` | Gets all user's transactions |
| `getSummary` | Calculates total income, expense, balance |
| `deleteTransaction` | Removes a transaction |

---

### `routers/` - Route Definitions

Routers connect URLs to Controllers. Think of them as a "table of contents" for your API.

#### `routers/authroutes.js`

```javascript
POST /api/auth/register  → authController.register
POST /api/auth/login     → authController.login
GET  /api/auth/me       → authController.getMe (protected)
```

#### `routers/profileroutes.js`

```javascript
GET  /api/profile/me           → profileController.getMe (protected)
PUT  /api/profile/me           → profileController.updateProfile (protected)
PUT  /api/profile/change-password → profileController.changePassword (protected)
DELETE /api/profile/wipe-data  → profileController.wipeData (protected)
```

#### `routers/budgetroutes.js`

```javascript
GET    /api/budget          → budgetController.getBudget (protected)
GET    /api/budget/multiple → budgetController.getBudgets (protected)
POST   /api/budget         → budgetController.saveBudget (protected)
PUT    /api/budget/income  → budgetController.updateIncome (protected)
PUT    /api/budget/widget  → budgetController.saveWidget (protected)
DELETE /api/budget/widget  → budgetController.deleteWidget (protected)
```

#### `routers/transactionroutes.js`

```javascript
POST   /api/transactions      → transactionController.addTransaction (protected)
GET    /api/transactions      → transactionController.getTransactions (protected)
GET    /api/transactions/summary → transactionController.getSummary (protected)
DELETE /api/transactions/:id  → transactionController.deleteTransaction (protected)
```

---

## Frontend Structure (`finance-code/src/`)

### `main.tsx` - React Entry Point

**What it does:** This is the first React file that runs.

```javascript
// What happens:
1. Creates a React root
2. Renders the App component
3. Shows toast notifications (success/error messages)
```

---

### `app/App.tsx` - Root Component

**What it does:** Sets up the application with all providers.

```javascript
// Structure:
<ThemeProvider>           // Handles dark/light mode
  <AuthProvider>          // Handles login/logout/user state
    <BudgetProvider>      // Handles budget and transaction state
      <RouterProvider>    // Handles routing between pages
    </BudgetProvider>
  </AuthProvider>
</ThemeProvider>
```

---

### `app/routes.ts` - Route Definitions

**What it does:** Defines which page shows for each URL.

```javascript
/          → Redirects to /login
/login     → LoginPage (public)
/signup    → SignUpPage (public)
/dashboard → DashboardPage (protected)
/budget    → BudgetPage (protected)
/profile   → ProfilePage (protected)
```

**Protected routes** mean you need to be logged in to see them. If you're not logged in, it redirects you to `/login`.

---

### `app/context/` - State Management

React Context is like a "global variable" system. It lets any component access shared data without passing props everywhere.

#### `context/theme-context.tsx`

**What it does:** Manages dark/light theme.

```typescript
// Provides:
- theme: "dark" | "light"
- toggleTheme(): Switch between dark and light
```

#### `context/auth-context.tsx`

**What it does:** Manages user authentication state.

```typescript
// Provides:
- user: User | null          // Current logged-in user
- token: string | null       // JWT token
- login(email, password)       // Sign in
- register(name, email, password)  // Create account
- logout()                    // Sign out
- refreshUser()              // Fetch latest user data
- loading: boolean           // Is something loading?
```

#### `context/budget-context.tsx`

**What it does:** Manages budgets and transactions.

```typescript
// Provides:
- monthlyData: {[monthKey]: { income, widgets }}
- transactions: Transaction[]
- summary: { income, expense, balance }
- getMonthData(monthKey)      // Get budget for a month
- updateMonthIncome(key, income)
- addWidget(key, widget)
- updateWidget(key, widgetId, updates)
- deleteWidget(key, widgetId)
- addTransaction(tx)           // Add income/expense
- deleteTransaction(id)
- refreshTransactions()
- refreshBudget(key)
```

---

### `app/pages/` - Page Components

Each page is a full screen in the application.

#### `pages/login-page.tsx` & `pages/signup-page.tsx`

**What it does:** Authentication pages with forms.

```
┌─────────────────────────────────────┐
│                                     │
│   Left Panel (decorative)           │
│   - Gradient background             │
│   - Welcome message                │
│                                     │
├─────────────────────────────────────┤
│                                     │
│   Right Panel                       │
│   - LoginForm / SignUpForm          │
│   - Email input                     │
│   - Password input                  │
│   - Submit button                   │
│   - Link to other page             │
│                                     │
└─────────────────────────────────────┘
```

#### `pages/dashboard-page.tsx`

**What it does:** Main overview page showing financial health.

```
┌─────────────────────────────────────────────────┐
│  Header: "Dashboard" + Add Transaction button   │
├─────────────────────────────────────────────────┤
│                                                 │
│  ┌─────────────────────────────────────────┐   │
│  │ Financial Overview Card                  │   │
│  │ ┌─────────┐ ┌─────────┐ ┌─────────┐    │   │
│  │ │ Income  │ │ Expenses│ │ Balance │    │   │
│  │ │ $5000   │ │ $1234   │ │ $3766   │    │   │
│  │ └─────────┘ └─────────┘ └─────────┘    │   │
│  └─────────────────────────────────────────┘   │
│                                                 │
│  ┌───────────────────┐ ┌─────────────────┐     │
│  │ Recent             │ │ Spending by     │     │
│  │ Transactions       │ │ Category        │     │
│  │                    │ │                 │     │
│  │ • Grocery $150    │ │    [Pie Chart]  │     │
│  │ • Salary $5000    │ │                 │     │
│  │ • Rent $1500      │ │ Personal 40%    │     │
│  │                    │ │ Food 25%       │     │
│  └───────────────────┘ └─────────────────┘     │
└─────────────────────────────────────────────────┘
```

#### `pages/budget-page.tsx`

**What it does:** Monthly budget management.

```
┌─────────────────────────────────────────────────┐
│  Header: "Budget" + Month Picker + Add Tx btn   │
├─────────────────────────────────────────────────┤
│                                                 │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐│
│  │ Income Card │ │ Budget      │ │ Budget      ││
│  │             │ │ Widget 1    │ │ Widget 2    ││
│  │ $5000       │ │ Rent $1500  │ │ Groceries   ││
│  │ [Edit]      │ │ Spent: $1500│ │ $500        ││
│  │             │ │ [Progress]  │ │ Spent: $320 ││
│  │ [Progress]  │ │             │ │ [Progress]  ││
│  └─────────────┘ └─────────────┘ └─────────────┘│
│                                                 │
│  ┌─────────────┐ ┌─────────────────────────────┐│
│  │ + Add New   │ │ OR: New Budget Item Form    ││
│  │   Widget    │ │ Category: [Dropdown]        ││
│  │             │ │ Name: [_______________]     ││
│  │             │ │ Budgeted: [__________]       ││
│  └─────────────┘ └─────────────────────────────────┘│
└─────────────────────────────────────────────────┘
```

#### `pages/profile-page.tsx`

**What it does:** User settings and account management.

```
┌─────────────────────────────────────────────────┐
│  Header: "Profile & Settings"                   │
├───────────────┬─────────────────────────────────┤
│               │                                 │
│  Profile      │  Contact Information            │
│  Picture      │  ┌─────────────────────────┐   │
│  ┌─────────┐  │  │ Email: [____________]  │   │
│  │  Photo  │  │  │ Phone: [____________]  │   │
│  │  [📷]   │  │  └─────────────────────────┘   │
│  └─────────┘  │                                 │
│               │  Change Password                │
│  Theme        │  ┌─────────────────────────┐   │
│  [Toggle]     │  │ Current: [____________] │   │
│               │  │ New: [____________]     │   │
│  Account      │  │ Confirm: [____________] │   │
│  ─────────    │  └─────────────────────────┘   │
│  John Doe     │                                 │
│  email@...    │  Personal Information           │
│               │  (Date of Birth, Address...)   │
│               │                                 │
│               │  ⚠️ Wipe Financial Data         │
│               │  (Danger zone - deletes data)   │
└───────────────┴─────────────────────────────────┘
```

---

### `app/components/` - Reusable UI Components

#### `components/floating-nav.tsx`

**What it does:** Bottom navigation bar that stays visible.

```
┌─────────────────────────────────────────────────┐
│                                                 │
│              [Page Content]                      │
│                                                 │
│                                                 │
├─────────────────────────────────────────────────┤
│  ┌──────────┐  ┌──────────┐  ┌──────────┐      │
│  │Dashboard │  │ Budget   │  │ Profile  │      │
│  │    📊    │  │    💰    │  │    ⚙️    │      │
│  └──────────┘  └──────────┘  └──────────┘  [🚪]│
│                 (active)                         logout
└─────────────────────────────────────────────────┘
```

#### `components/transaction-dialog.tsx`

**What it does:** Modal popup for adding transactions.

```
┌─────────────────────────────────────┐
│  Add Transaction                [×] │
├─────────────────────────────────────┤
│                                     │
│  ┌───────────────┐ ┌───────────────┐│
│  │  Income ↑    │ │  Expense ↓    ││
│  └───────────────┘ └───────────────┘│
│                                     │
│  Amount: [________]                 │
│  Category: [Dropdown]               │
│  Description: [________]            │
│  Date: [__________]                  │
│                                     │
│  [Cancel]  [Add Transaction]        │
└─────────────────────────────────────┘
```

#### `components/login-form.tsx` & `components/signup-form.tsx`

**What they do:** Form components for authentication.

#### `components/ui/` - Shadcn UI Components

These are pre-built components from the Shadcn UI library:

| Component | Purpose |
|-----------|---------|
| `button.tsx` | Clickable buttons |
| `input.tsx` | Text input fields |
| `dialog.tsx` | Modal popups |
| `card.tsx` | Card containers |
| `sheet.tsx` | Slide-out panels |
| `toast.tsx` | Notification popups |
| ... and many more | |

---

## Data Flow - How Everything Connects

### 1. User Logs In

```
┌────────┐      POST /api/auth/login      ┌─────────────┐
│ Login  │  ─────────────────────────────→│   Server    │
│  Form  │                                │             │
└────────┘  ←───────────────────────────── │  Validates  │
         { token: "abc123", user: {...} } │  password   │
                                          │  Returns    │
                                          │  JWT token  │
                                          └─────────────┘
                    ↓
         Token saved to localStorage
                    ↓
         AuthContext.user = user data
                    ↓
         Navigate to /dashboard
```

### 2. Dashboard Loads Data

```
┌────────────┐                              ┌─────────────┐
│ Dashboard  │                              │   MongoDB   │
│  Page      │  GET /api/transactions       │             │
└────────────┘  ───────────────────────────→  │   Finds    │
              Authorization: Bearer <token> │   user's    │
                                            │   data      │
              ←────────────────────────────── └─────────────┘
              { transactions: [...], ... }
                       ↓
              BudgetContext.updates
                       ↓
              UI re-renders with data
```

### 3. User Adds Transaction

```
┌──────────────────┐                        ┌─────────────┐
│ TransactionDialog │  POST /api/transactions │   Server    │
│                  │  ─────────────────────→  │             │
│ User fills form  │  { amount, type, ... }  │  Creates    │
└──────────────────┘                         │  Transaction│
              ←─────────────────────────────  │  in MongoDB │
               { message: "Success",         └─────────────┘
                 transaction: {...} }
                       ↓
              refreshTransactions()
                       ↓
              Dashboard shows new transaction
```

---

## Environment Variables

### `.env` (in `D:/finance-project/`)

```env
MONGO_URI=mongodb://localhost:27017/finance-app
JWT_SECRET=your-super-secret-key-change-this
PORT=5000
```

| Variable | Purpose |
|----------|---------|
| `MONGO_URI` | Connection string for MongoDB database |
| `JWT_SECRET` | Secret key for signing JWT tokens (change in production!) |
| `PORT` | Port the server runs on (default: 5000) |

---

## How to Run the Project

### 1. Start MongoDB

Make sure MongoDB is running on your computer.

```bash
# If using MongoDB locally, just make sure the service is running
# Or use MongoDB Atlas (cloud) by updating MONGO_URI
```

### 2. Start the Backend Server

```bash
cd D:/finance-project/finance-code

# Start only the backend
npm run server

# You should see:
# MongoDB connected
# Server running on port 5000
```

### 3. Start the Frontend (New Terminal)

```bash
cd D:/finance-project/finance-code

# Start the frontend dev server
npm run dev

# You should see:
# VITE v6.3.5  ready in 500 ms
# ➜  Local:   http://localhost:5173/
```

### 4. Open in Browser

Go to `http://localhost:5173`

---

## API Request/Response Examples

### Register a New User

**Request:**
```bash
curl -X POST http://localhost:5000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"name":"John Doe","email":"john@example.com","password":"password123"}'
```

**Response:**
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

### Login

**Request:**
```bash
curl -X POST http://localhost:5000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"john@example.com","password":"password123"}'
```

**Response:**
```json
{
  "message": "Login successful",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": { "_id": "64abc123...", "name": "John Doe", ... }
}
```

### Add a Transaction (Authenticated)

**Request:**
```bash
curl -X POST http://localhost:5000/api/transactions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -d '{"amount":150.00,"type":"expense","category":"Food","description":"Weekly groceries"}'
```

**Response:**
```json
{
  "message": "Transaction added successfully",
  "transaction": {
    "_id": "64xyz789...",
    "userId": "64abc123...",
    "amount": 150,
    "type": "expense",
    "category": "Food",
    "description": "Weekly groceries",
    "date": "2026-03-27T00:00:00.000Z"
  }
}
```

---

## Common Issues & Solutions

### "Cannot connect to MongoDB"

**Solution:** Make sure MongoDB is running. If using Atlas, check your `MONGO_URI` in `.env`.

### "Invalid token" errors

**Solution:** Your JWT token expired (24 hours). Log out and log back in.

### "CORS error" in browser

**Solution:** Make sure the backend is running on port 5000. The Vite proxy is configured to forward `/api` requests there.

### "401 Unauthorized"

**Solution:** You're trying to access a protected route without logging in, or your token is invalid/expired.

---

## Key Concepts to Remember

1. **JWT Authentication**: Token-based auth where the server signs a token, and the client sends it with each request.

2. **REST API**: Standard way to structure API endpoints:
   - `GET` - Retrieve data
   - `POST` - Create new data
   - `PUT` - Update existing data
   - `DELETE` - Remove data

3. **Context API**: React's way of sharing state across components without prop drilling.

4. **Mongoose**: Library that makes it easy to work with MongoDB by providing a schema-based interface.

5. **Optimistic Updates**: Updating the UI immediately, then syncing with the server. If the server fails, revert the UI change.

6. **Protected Routes**: Routes that require authentication middleware to run first.

---

## File Summary Table

| File/Folder | Purpose |
|------------|---------|
| `server/server.js` | Express server entry point |
| `server/config/db.js` | MongoDB connection |
| `server/models/*.js` | Database schemas |
| `server/controllers/*.js` | Business logic |
| `server/routers/*.js` | API route definitions |
| `server/middleware/authMiddleware.js` | JWT authentication |
| `src/main.tsx` | React entry point |
| `src/app/App.tsx` | Root component with providers |
| `src/app/routes.ts` | Page routing |
| `src/app/context/*.tsx` | State management |
| `src/app/pages/*.tsx` | Page components |
| `src/app/components/*.tsx` | Reusable UI components |
| `src/styles/*.css` | Styling |
| `.env` | Environment variables |
| `API_DOCUMENTATION.md` | API reference |
