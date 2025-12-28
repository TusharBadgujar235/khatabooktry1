# Khatabook - Account Management System

A complete full-stack application for managing Lent (Udhar Diya) and Borrowed (Udhar Liya) money transactions between shop owners and customers.

## 🚀 Features

### Admin (Shop Owner)
- **Dashboard**: View total lent, borrowed, and net balance
- **Customer Management**: Add, edit, delete, and search customers
- **Transaction Management**: Create, update, and delete transactions
- **Balance Tracking**: See individual customer balances
- **Transaction History**: View all transactions with filters
- **Monthly Summary**: Track trends and patterns

### Customer
- **Login**: Secure authentication via email/phone
- **Transaction View**: See all transactions in read-only mode
- **Balance Overview**: Clear view of what they owe/receive
- **Transaction History**: Detailed list with dates and amounts

## 🛠️ Tech Stack

### Backend
- **Node.js** - Runtime environment
- **Express.js** - Web framework
- **MongoDB** - Database
- **Mongoose** - ODM
- **JWT** - Authentication
- **bcryptjs** - Password hashing
- **express-validator** - Input validation

### Frontend
- **React.js** - UI library
- **Vite** - Build tool
- **Tailwind CSS** - Styling
- **React Router** - Navigation
- **Axios** - HTTP client
- **date-fns** - Date formatting

## 📋 Prerequisites

- Node.js (v14 or higher)
- MongoDB (local or Atlas)
- npm or yarn

## 🔧 Installation & Setup

### 1. Clone/Extract the Project

```bash
cd khatabook
```

### 2. Backend Setup

```bash
cd backend

# Copy environment file
cp .env.example .env

# Install dependencies
npm install

# Update .env with your MongoDB URI and JWT secret
# MONGODB_URI=mongodb://localhost:27017/khatabook
# JWT_SECRET=your_secret_key_here
```

### 3. Frontend Setup

```bash
cd ../frontend

# Copy environment file
cp .env.example .env

# Install dependencies
npm install
```

## 🚀 Running the Application

### Terminal 1 - Backend Server

```bash
cd backend
npm run dev
```

The backend will start on `http://localhost:5000`

### Terminal 2 - Frontend Development

```bash
cd frontend
npm run dev
```

The frontend will open at `http://localhost:5173`

## 📁 Project Structure

```
khatabook/
├── backend/
│   ├── src/
│   │   ├── config/
│   │   │   └── database.js          # MongoDB connection
│   │   ├── controllers/
│   │   │   ├── authController.js    # Auth logic
│   │   │   ├── transactionController.js
│   │   │   └── customerController.js
│   │   ├── middleware/
│   │   │   ├── auth.js              # JWT verification
│   │   │   └── validation.js
│   │   ├── models/
│   │   │   ├── User.js
│   │   │   └── Transaction.js
│   │   ├── routes/
│   │   │   ├── authRoutes.js
│   │   │   ├── transactionRoutes.js
│   │   │   └── customerRoutes.js
│   │   └── server.js               # Main server file
│   ├── package.json
│   ├── .env.example
│   └── .gitignore
│
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   ├── admin/
│   │   │   │   ├── AdminDashboardComponents.jsx
│   │   │   │   └── CustomerManagement.jsx
│   │   │   ├── customer/
│   │   │   └── common/
│   │   │       ├── UIComponents.jsx
│   │   │       └── Navbar.jsx
│   │   ├── pages/
│   │   │   ├── Login.jsx
│   │   │   ├── Register.jsx
│   │   │   ├── AdminPage.jsx
│   │   │   └── CustomerPage.jsx
│   │   ├── services/
│   │   │   ├── api.js
│   │   │   └── authService.js
│   │   ├── context/
│   │   │   └── AuthContext.jsx
│   │   ├── App.jsx
│   │   ├── main.jsx
│   │   └── index.css
│   ├── public/
│   ├── index.html
│   ├── vite.config.js
│   ├── tailwind.config.js
│   ├── package.json
│   ├── .env.example
│   └── .gitignore
│
└── README.md
```

## 🔐 Authentication

### User Roles
- **Admin**: Shop owner with full access
- **Customer**: Can only view their own transactions

### JWT Token
- Tokens are stored in localStorage
- Automatically included in all API requests
- Expires after 7 days (configurable)

## 📊 Database Schema

### User Model
```javascript
{
  name: String,
  email: String (unique),
  phone: String (unique),
  password: String (hashed),
  role: 'admin' | 'customer',
  shopName: String (for admin),
  isActive: Boolean,
  createdAt: Date,
  updatedAt: Date
}
```

### Transaction Model
```javascript
{
  amount: Number,
  type: 'LENT' | 'BORROW',
  description: String,
  notes: String,
  customer: ObjectId (ref: User),
  admin: ObjectId (ref: User),
  date: Date,
  paymentStatus: 'PENDING' | 'COMPLETED',
  dueDate: Date,
  createdAt: Date,
  updatedAt: Date
}
```

## 🔌 API Endpoints

### Authentication
- `POST /api/auth/register` - Register new user
- `POST /api/auth/login` - Login user
- `GET /api/auth/profile` - Get current user profile

### Transactions (Admin Only)
- `POST /api/transactions/create` - Create transaction
- `GET /api/transactions/admin-transactions` - Get admin transactions
- `PUT /api/transactions/:id` - Update transaction
- `DELETE /api/transactions/:id` - Delete transaction
- `GET /api/transactions/balance/:customerId` - Get customer balance

### Transactions (Customer)
- `GET /api/transactions/customer-transactions` - Get own transactions
- `GET /api/transactions/customer-balance` - Get own balance

### Customers (Admin Only)
- `GET /api/customers/list` - Get all customers with search
- `GET /api/customers/:id` - Get customer details
- `GET /api/customers/stats` - Get dashboard stats
- `PUT /api/customers/:id` - Update customer
- `DELETE /api/customers/:id` - Delete customer (soft delete)

## 🎨 UI/UX Features

- **Responsive Design**: Works on mobile, tablet, and desktop
- **Modern Dashboard**: Cards with icons and colors
- **Smooth Animations**: Fade-in and slide-up effects
- **Form Validation**: Real-time error messages
- **Loading States**: Skeleton screens and spinners
- **Error Handling**: User-friendly error messages
- **Dark Mode Ready**: Tailwind CSS customizable

## 🔒 Security Features

- Password hashing with bcryptjs
- JWT-based authentication
- Role-based access control
- Input validation with express-validator
- CORS protection
- Environment variables for sensitive data

## 📱 Responsive Breakpoints

- Mobile: 320px and up
- Tablet: 768px and up
- Desktop: 1024px and up

## 🚢 Deployment

### Backend (Heroku/Render)
```bash
# Set environment variables in deployment platform
# Deploy the backend folder
```

### Frontend (Vercel/Netlify)
```bash
# Build
npm run build

# Deploy the dist folder
```

## 🐛 Troubleshooting

### MongoDB Connection Error
- Ensure MongoDB is running
- Check MONGODB_URI in .env
- Verify network access if using MongoDB Atlas

### CORS Error
- Check FRONTEND_URL in backend .env
- Ensure frontend and backend are running on correct ports

### API Not Found
- Verify backend server is running
- Check API_URL in frontend .env

## 📚 Additional Features (Future)

- [ ] Export transactions to PDF/Excel
- [ ] SMS notifications for transactions
- [ ] Payment reminders
- [ ] Multi-currency support
- [ ] Advanced analytics and reports
- [ ] Mobile app (React Native)
- [ ] Dark mode
- [ ] Two-factor authentication

## 📄 License

This project is open-source and available under the MIT License.

## 👨‍💻 Support

For issues or questions, please create an issue in the repository or contact support.

---

**Happy Accounting! 📕**
