# Quick Start Guide

## ⚡ 5-Minute Setup

### Step 1: Install MongoDB (if needed)

**Windows:**
- Download from: https://www.mongodb.com/try/download/community
- Run installer and follow steps
- MongoDB runs at `mongodb://localhost:27017`

**Mac:**
```bash
brew tap mongodb/brew
brew install mongodb-community
brew services start mongodb-community
```

**Linux:**
```bash
sudo apt-get install -y mongodb
sudo systemctl start mongod
```

### Step 2: Backend Setup

```bash
cd backend

# Create .env file
copy .env.example .env

# Install packages
npm install

# Start backend
npm run dev
```

✅ Backend ready at: `http://localhost:5000`

### Step 3: Frontend Setup (New Terminal)

```bash
cd frontend

# Create .env file
copy .env.example .env

# Install packages
npm install

# Start frontend
npm run dev
```

✅ Frontend ready at: `http://localhost:5173`

## 📝 Default Test Accounts

### Admin Account
After running both servers, create these accounts through the app:

**Register as Admin:**
- Name: Shop Owner
- Email: admin@test.com
- Phone: 9876543210
- Password: admin123
- Account Type: Shop Owner

**Register as Customer:**
- Name: Test Customer
- Email: customer@test.com
- Phone: 9876543211
- Password: customer123
- Account Type: Customer

## 🎯 First Actions

1. **Login as Admin**
   - Enter email: `admin@test.com` + password: `admin123`
   - Click on Register first time

2. **Add Customer**
   - Register a customer account or select from list
   - Customer name will appear in admin dashboard

3. **Create Transaction**
   - Select a customer
   - Click "+ Add Transaction"
   - Enter amount (e.g., ₹500)
   - Select type: "Lent" or "Borrow"
   - Add notes if needed
   - Click "Add Transaction"

4. **View as Customer**
   - Logout
   - Login as customer
   - View your transactions and balance

## 🔧 Common Issues & Solutions

### Port Already in Use

**Backend Port 5000:**
```bash
# Windows
netstat -ano | findstr :5000
taskkill /PID <PID> /F

# Mac/Linux
lsof -i :5000
kill -9 <PID>
```

**Frontend Port 5173:**
Change in `frontend/vite.config.js`

### MongoDB Connection Error

```bash
# Check if MongoDB is running
# Windows: Task Manager -> Services -> MongoDB
# Mac: brew services list
# Linux: sudo systemctl status mongod

# If not running:
# Windows: Run MongoDB as admin
# Mac: brew services start mongodb-community
# Linux: sudo systemctl start mongod
```

### .env File Issues

Make sure you have:

**Backend .env:**
```
MONGODB_URI=mongodb://localhost:27017/khatabook
JWT_SECRET=test-secret-key
PORT=5000
FRONTEND_URL=http://localhost:5173
```

**Frontend .env:**
```
VITE_API_URL=http://localhost:5000/api
```

## 🎨 UI Walkthrough

### Admin Dashboard
```
┌─────────────────────────────────────────┐
│         Khatabook - Admin Panel         │
├─────────────────────────────────────────┤
│  📊 Total Lent  │  💸 Total Borrowed    │
│  ₹ 5,000        │  ₹ 2,000              │
├─────────────────────────────────────────┤
│  Customers      │  Customer Details     │
│  • Raj          │  Raj Singh            │
│  • Priya        │  Lent: ₹1000          │
│  • Amit         │  Borrow: ₹500         │
│                 │  [+ Add Transaction]  │
└─────────────────────────────────────────┘
```

### Customer Dashboard
```
┌──────────────────────────────────┐
│     You Owe (Udhar Diya)          │
│     ₹ 1,000                       │
├──────────────────────────────────┤
│  You'll Receive (Udhar Liya)      │
│     ₹ 500                         │
├──────────────────────────────────┤
│  Transactions                     │
│  • 01 Dec - Udhar Diya - ₹500     │
│  • 15 Dec - Udhar Liya - ₹300     │
└──────────────────────────────────┘
```

## 📱 Features Demo

### Create a Transaction
1. Admin dashboard → Select customer → + Add Transaction
2. Choose: Lent (Udhar Diya) or Borrow (Udhar Liya)
3. Enter amount (e.g., ₹500)
4. Add notes (optional)
5. Click "Add Transaction"

### Search Customer
1. Use search box in left panel
2. Search by name or phone number
3. Results update in real-time

### View Balance
1. Select customer from list
2. Balance shown in center panel
3. Three cards: Lent, Borrow, Net Balance

### Edit Transaction
1. Click on transaction in history
2. Click "Edit" button
3. Update amount, notes, status
4. Save changes

### Delete Customer
1. Select customer
2. Right-click or find delete option
3. Confirm deletion

## 🚀 Next Steps

- Read [Backend README](./backend/README.md) for API docs
- Read [Frontend README](./frontend/README.md) for component guide
- Check [Main README](./README.md) for full documentation

## 📞 Support

If you encounter issues:
1. Check the troubleshooting sections in README files
2. Verify .env files are correct
3. Ensure MongoDB is running
4. Check browser console for errors
5. Check terminal for backend errors

---

**🎉 You're all set! Start managing accounts with Khatabook!**
