# Database Schema Documentation

## MongoDB Collections

### 1. Users Collection

Stores user information for both admins and customers.

```javascript
{
  _id: ObjectId,                    // MongoDB auto-generated ID
  name: String,                     // User's full name (required)
  email: String,                    // Email address (unique, required)
  phone: String,                    // Phone number (unique, required)
  password: String,                 // Bcrypt hashed password (required)
  role: String,                     // 'admin' or 'customer' (default: 'customer')
  shopName: String,                 // Shop name (only for admin users)
  isActive: Boolean,                // Active status (default: true)
  createdAt: Date,                  // Creation timestamp
  updatedAt: Date                   // Last update timestamp
}
```

**Indexes:**
- email (unique)
- phone (unique)
- role
- createdAt (for sorting)

**Example Document:**
```json
{
  "_id": ObjectId("507f1f77bcf86cd799439011"),
  "name": "Raj Kumar",
  "email": "raj@shop.com",
  "phone": "+919876543210",
  "password": "$2a$10$...",
  "role": "admin",
  "shopName": "Raj General Store",
  "isActive": true,
  "createdAt": ISODate("2024-01-15T10:00:00Z"),
  "updatedAt": ISODate("2024-01-20T15:30:00Z")
}
```

---

### 2. Transactions Collection

Records all lent and borrowed transactions.

```javascript
{
  _id: ObjectId,                    // MongoDB auto-generated ID
  amount: Number,                   // Transaction amount (required, min: 0)
  type: String,                     // 'LENT' or 'BORROW' (required)
  description: String,              // Brief description (optional)
  notes: String,                    // Additional notes (optional)
  customer: ObjectId,               // Reference to User (required)
  admin: ObjectId,                  // Reference to User (admin) (required)
  date: Date,                       // Transaction date (default: now)
  paymentStatus: String,            // 'PENDING' or 'COMPLETED' (default: 'PENDING')
  dueDate: Date,                    // Due date for payment (optional)
  createdAt: Date,                  // Creation timestamp
  updatedAt: Date                   // Last update timestamp
}
```

**Indexes:**
- customer (for queries by customer)
- admin (for queries by admin)
- date (for sorting transactions)
- type (for filtering by transaction type)
- compound index: (customer, admin, date)

**Example Document:**
```json
{
  "_id": ObjectId("507f1f77bcf86cd799439012"),
  "amount": 500,
  "type": "LENT",
  "description": "Weekly grocery shopping",
  "notes": "First order from Raj",
  "customer": ObjectId("507f1f77bcf86cd799439013"),
  "admin": ObjectId("507f1f77bcf86cd799439011"),
  "date": ISODate("2024-01-15T10:00:00Z"),
  "paymentStatus": "PENDING",
  "dueDate": ISODate("2024-02-15T00:00:00Z"),
  "createdAt": ISODate("2024-01-15T10:00:00Z"),
  "updatedAt": ISODate("2024-01-20T15:30:00Z")
}
```

---

## Relationships

### User ←→ Transaction

**One-to-Many:**
- One User (as admin) can have many Transactions
- One User (as customer) can appear in many Transactions
- Each Transaction references both admin and customer

```
User (Admin)
  │
  └─→ [Transaction 1]
      ├─ customer: User (Customer)
      └─ admin: User (Admin)
  
  └─→ [Transaction 2]
      ├─ customer: User (Customer)
      └─ admin: User (Admin)
```

---

## Query Examples

### Find All Transactions for a Customer

```javascript
db.transactions.find({ customer: ObjectId("...") })
```

### Get Total Lent Amount by Admin

```javascript
db.transactions.aggregate([
  { $match: { admin: ObjectId("..."), type: "LENT" } },
  { $group: { _id: null, total: { $sum: "$amount" } } }
])
```

### Find Pending Transactions Older Than 30 Days

```javascript
db.transactions.find({
  paymentStatus: "PENDING",
  date: { $lt: new Date(Date.now() - 30 * 24 * 60 * 60 * 1000) }
})
```

### Get Transaction Count by Type

```javascript
db.transactions.aggregate([
  { $group: { _id: "$type", count: { $sum: 1 } } }
])
```

### Find Customer Details with Balance

```javascript
db.users.aggregate([
  { $match: { _id: ObjectId("...") } },
  {
    $lookup: {
      from: "transactions",
      let: { customerId: "$_id" },
      pipeline: [
        { $match: { $expr: { $eq: ["$customer", "$$customerId"] } } }
      ],
      as: "transactions"
    }
  }
])
```

---

## Data Validation

### User
- name: non-empty string, max 100 chars
- email: valid email format
- phone: valid phone format (e.g., +91 10-digit)
- password: minimum 6 characters (hashed)
- role: must be 'admin' or 'customer'

### Transaction
- amount: positive number
- type: must be 'LENT' or 'BORROW'
- customer: valid ObjectId
- admin: valid ObjectId
- paymentStatus: must be 'PENDING' or 'COMPLETED'

---

## Performance Optimization

### Recommended Indexes

```javascript
// User collection
db.users.createIndex({ email: 1 }, { unique: true })
db.users.createIndex({ phone: 1 }, { unique: true })
db.users.createIndex({ role: 1 })
db.users.createIndex({ createdAt: -1 })

// Transaction collection
db.transactions.createIndex({ customer: 1 })
db.transactions.createIndex({ admin: 1 })
db.transactions.createIndex({ date: -1 })
db.transactions.createIndex({ type: 1 })
db.transactions.createIndex({ customer: 1, admin: 1, date: -1 })
```

### Query Performance Tips

1. Always filter by customer/admin before other fields
2. Use date range queries efficiently
3. Limit results with pagination
4. Use aggregation pipeline for complex calculations
5. Denormalize if needed (e.g., cache totals)

---

## Backup & Recovery

### Backup Strategy

```bash
# Monthly full backup
mongodump --uri "mongodb://..." --out /backups/khatabook-$(date +%Y%m%d)

# Weekly incremental (optional)
mongodump --query '{"createdAt": {"$gt": <last_week>}}'
```

### Recovery

```bash
# Full restore
mongorestore --uri "mongodb://..." /backups/khatabook-20240115

# Specific collection
mongorestore --uri "mongodb://..." --collection transactions /backup/transactions.bson
```

---

## Data Export/Import

### Export to CSV

```bash
# Users
mongoexport --uri "mongodb://..." --collection users --out users.csv --csv --fields name,email,phone,role

# Transactions
mongoexport --uri "mongodb://..." --collection transactions --out transactions.csv --csv --fields customer,admin,amount,type,date
```

### Import from CSV

```bash
mongoimport --uri "mongodb://..." --collection users --type csv --headerline --file users.csv
```

---

## Archive & Cleanup

### Archive Old Transactions (After 2 Years)

```javascript
db.transactions.updateMany(
  { date: { $lt: new Date(new Date().setFullYear(new Date().getFullYear() - 2)) } },
  { $set: { archived: true } }
)
```

### Delete Inactive Users (Soft Delete)

```javascript
db.users.updateMany(
  { isActive: false, updatedAt: { $lt: new Date(Date.now() - 180 * 24 * 60 * 60 * 1000) } },
  { $set: { deleted: true } }
)
```

---

## Monitoring

### Check Database Size

```javascript
db.stats()  // Database stats
db.collection("transactions").stats()  // Collection stats
```

### Monitor Indexes

```javascript
db.transactions.getIndexes()
db.users.getIndexes()
```

### Find Slow Queries

```javascript
// Enable profiling
db.setProfilingLevel(1, { slowms: 100 })

// Get profiling data
db.system.profile.find({ millis: { $gt: 100 } }).pretty()
```

---

## Data Privacy & Security

- Passwords are hashed with bcryptjs (10 salt rounds)
- Sensitive data (passwords) is never returned in API responses
- Use MongoDB's encryption at rest (paid plans)
- Regular backups to prevent data loss
- Access control via JWT tokens
- API validation on all inputs

---

## Future Enhancements

- [ ] Add transaction categories
- [ ] Implement payment records
- [ ] Add invoice management
- [ ] Audit logging collection
- [ ] Customer credit score
- [ ] SMS/Email notification logs
- [ ] Monthly settlement records
- [ ] Batch transaction imports

---

**Database Documentation - Khatabook v1.0**
