# Finance Management Application

A full-stack personal finance management application built with the MERN stack (MongoDB, Express.js, React.js, Node.js).

## Features

### Core Features
- **User Authentication** - Secure login/signup with JWT tokens and bcrypt password hashing
- **Dashboard** - Financial overview with income, expenses, balance, and spending by category
- **Budget Management** - Track monthly income, add budget categories, mark bills as paid
- **Credit Card Management** - Track multiple credit cards, balances, utilization, and due dates
- **Profile & Settings** - Manage account info, change password, toggle dark/light mode, wipe data

### Technical Features
- **Responsive Design** - Works on desktop, tablet, and mobile devices
- **Dark/Light Mode** - Theme toggle with persistent preferences
- **Real-time Updates** - Optimistic UI updates with backend synchronization
- **Data Visualization** - Pie charts for expense category breakdown
- **Floating Navigation** - Modern bottom navigation bar

## Tech Stack

### Frontend
- React 18 with Vite
- TypeScript
- Tailwind CSS
- Radix UI components (shadcn/ui)
- Recharts for data visualization
- React Router for navigation
- Context API for state management

### Backend
- Node.js with Express.js
- MongoDB with Mongoose
- JWT for authentication
- bcryptjs for password hashing
- CORS for cross-origin requests

## Project Structure

```
finance-code/
├── src/
│   ├── app/
│   │   ├── components/     # Reusable UI components
│   │   ├── context/        # React context providers (Auth, Budget, Credit, Theme)
│   │   ├── pages/          # Page components
│   │   ├── App.tsx         # Main app component
│   │   ├── routes.tsx      # Route configuration
│   │   └── main.tsx        # Entry point
│   ├── styles/
│   │   ├── index.css       # Main styles
│   │   ├── tailwind.css    # Tailwind configuration
│   │   ├── theme.css       # Theme variables
│   │   └── fonts.css       # Font definitions
│   └── main.tsx
├── server/
│   ├── config/
│   │   └── db.js           # MongoDB connection
│   ├── controllers/        # Route handlers
│   ├── middleware/         # Express middleware (auth)
│   ├── models/             # Mongoose schemas
│   ├── routers/            # Express routers
│   └── server.js           # Server entry point
├── .env                    # Environment variables
├── package.json
└── vite.config.js
```

## Getting Started

### Prerequisites
- Node.js (v18 or higher)
- MongoDB (local installation or MongoDB Atlas)
- npm or pnpm

### Installation

1. **Clone the repository**
   ```bash
   cd finance-code
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Configure environment variables**

   Copy `.env.example` to `.env` and update the values:
   ```bash
   cp .env.example .env
   ```

   Edit `.env` with your configuration:
   ```
   MONGO_URI=mongodb://localhost:27017/finance-app
   JWT_SECRET=your-super-secret-jwt-key
   PORT=5000
   NODE_ENV=development
   ```

4. **Start MongoDB**

   If using local MongoDB:
   ```bash
   mongod
   ```

5. **Run the application**

   Start both frontend and backend:
   ```bash
   npm run dev:full
   ```

   Or run separately:
   ```bash
   # Terminal 1 - Backend
   npm run server

   # Terminal 2 - Frontend
   npm run dev
   ```

6. **Open in browser**

   Navigate to `http://localhost:5173`

## API Endpoints

### Authentication
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | /api/auth/register | Create new account |
| POST | /api/auth/login | Login with credentials |
| GET | /api/auth/me | Get current user |

### Profile
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | /api/profile/me | Get user profile |
| PUT | /api/profile/me | Update profile |
| PUT | /api/profile/change-password | Change password |
| DELETE | /api/profile/wipe-data | Wipe financial data |

### Transactions
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | /api/transactions | Add transaction |
| GET | /api/transactions | Get all transactions |
| GET | /api/transactions/summary | Get income/expense summary |
| DELETE | /api/transactions/:id | Delete transaction |

### Budget
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | /api/budget | Get budget for month |
| POST | /api/budget | Save budget |
| PUT | /api/budget/income | Update monthly income |
| PUT | /api/budget/widget | Add/update budget widget |
| DELETE | /api/budget/widget | Delete budget widget |

### Credit Cards
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | /api/credit | Get all credit cards |
| GET | /api/credit/:id | Get single card |
| GET | /api/credit/summary | Get credit summary |
| POST | /api/credit | Add credit card |
| PUT | /api/credit/:id | Update credit card |
| PATCH | /api/credit/:id/balance | Update balance |
| DELETE | /api/credit/:id | Delete credit card |

## Database Models

### User
```javascript
{
  name: String,
  email: String (unique),
  password: String (hashed),
  phone: String,
  dateOfBirth: String,
  country: String,
  stateProvince: String,
  zipCode: String,
  streetAddress: String,
  timestamps: true
}
```

### Transaction
```javascript
{
  userId: ObjectId (ref: User),
  amount: Number,
  type: "income" | "expense",
  category: String,
  description: String,
  date: Date,
  timestamps: true
}
```

### Budget
```javascript
{
  userId: ObjectId (ref: User),
  monthKey: String (YYYY-MM),
  income: Number,
  widgets: [{
    id: String,
    category: String,
    name: String,
    budgeted: Number,
    spent: Number,
    dueDate: String
  }],
  timestamps: true
}
```

### CreditCard
```javascript
{
  userId: ObjectId (ref: User),
  cardName: String,
  cardNumber: String,
  creditLimit: Number,
  currentBalance: Number,
  minimumPayment: Number,
  dueDate: Date,
  paymentDue: Number,
  interestRate: Number,
  status: "active" | "paid" | "overdue",
  timestamps: true
}
```

## Security Features

- **Password Hashing** - bcrypt with salt rounds
- **JWT Authentication** - Secure token-based auth
- **Protected Routes** - Middleware for API authentication
- **Input Validation** - Server-side validation
- **CORS Protection** - Configured cross-origin policy

## Accessibility

The application includes:
- Semantic HTML structure
- ARIA labels on interactive elements
- Keyboard navigation support
- High contrast mode support
- Screen reader compatibility

## Browser Support

- Chrome (latest)
- Firefox (latest)
- Safari (latest)
- Edge (latest)

## License

MIT

## Contributors

Finance Project Team
