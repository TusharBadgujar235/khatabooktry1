# API Testing Guide

## Using Postman or cURL

### 1. Register User

**POST** `http://localhost:5000/api/auth/register`

```json
{
  "name": "John Doe",
  "email": "john@test.com",
  "phone": "9876543210",
  "password": "password123",
  "role": "customer"
}
```

**Response:**
```json
{
  "success": true,
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "user": {
    "id": "...",
    "name": "John Doe",
    "email": "john@test.com",
    "role": "customer"
  }
}
```

### 2. Login

**POST** `http://localhost:5000/api/auth/login`

```json
{
  "email": "john@test.com",
  "password": "password123"
}
```

OR (with phone)

```json
{
  "phone": "9876543210",
  "password": "password123"
}
```

### 3. Get Profile

**GET** `http://localhost:5000/api/auth/profile`

**Headers:**
```
Authorization: Bearer <your_token>
```

### 4. Create Transaction (Admin Only)

**POST** `http://localhost:5000/api/transactions/create`

**Headers:**
```
Authorization: Bearer <admin_token>
Content-Type: application/json
```

**Body:**
```json
{
  "amount": 500,
  "type": "LENT",
  "customerId": "customer_id_here",
  "description": "Weekly groceries",
  "notes": "First order"
}
```

### 5. Get Admin Transactions

**GET** `http://localhost:5000/api/transactions/admin-transactions?page=1&limit=10`

**Headers:**
```
Authorization: Bearer <admin_token>
```

### 6. Get Customer Transactions

**GET** `http://localhost:5000/api/transactions/customer-transactions?page=1`

**Headers:**
```
Authorization: Bearer <customer_token>
```

### 7. Get Balance

**GET** `http://localhost:5000/api/transactions/balance/{customerId}`

**Headers:**
```
Authorization: Bearer <admin_token>
```

### 8. Get Customers List

**GET** `http://localhost:5000/api/customers/list?search=name&page=1&limit=10`

**Headers:**
```
Authorization: Bearer <admin_token>
```

### 9. Get Dashboard Stats

**GET** `http://localhost:5000/api/customers/stats`

**Headers:**
```
Authorization: Bearer <admin_token>
```

### 10. Update Transaction

**PUT** `http://localhost:5000/api/transactions/{transactionId}`

**Headers:**
```
Authorization: Bearer <admin_token>
Content-Type: application/json
```

**Body:**
```json
{
  "amount": 600,
  "notes": "Updated amount"
}
```

### 11. Delete Transaction

**DELETE** `http://localhost:5000/api/transactions/{transactionId}`

**Headers:**
```
Authorization: Bearer <admin_token>
```

## cURL Examples

### Register as Admin

```bash
curl -X POST http://localhost:5000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Admin User",
    "email": "admin@test.com",
    "phone": "9876543210",
    "password": "admin123",
    "role": "admin",
    "shopName": "My Shop"
  }'
```

### Login

```bash
curl -X POST http://localhost:5000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "admin@test.com",
    "password": "admin123"
  }'
```

### Get Profile

```bash
curl -X GET http://localhost:5000/api/auth/profile \
  -H "Authorization: Bearer YOUR_TOKEN_HERE"
```

### Create Transaction

```bash
curl -X POST http://localhost:5000/api/transactions/create \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_ADMIN_TOKEN" \
  -d '{
    "amount": 1000,
    "type": "LENT",
    "customerId": "CUSTOMER_ID_HERE",
    "description": "Monthly supplies",
    "notes": "October billing"
  }'
```

### Get Customers

```bash
curl -X GET "http://localhost:5000/api/customers/list?search=john&page=1" \
  -H "Authorization: Bearer YOUR_ADMIN_TOKEN"
```

## Postman Collection Template

Save as `khatabook.postman_collection.json`:

```json
{
  "info": {
    "name": "Khatabook API",
    "version": "1.0"
  },
  "item": [
    {
      "name": "Auth",
      "item": [
        {
          "name": "Register",
          "request": {
            "method": "POST",
            "header": [
              {
                "key": "Content-Type",
                "value": "application/json"
              }
            ],
            "url": {
              "raw": "{{base_url}}/auth/register",
              "host": ["{{base_url}}"],
              "path": ["auth", "register"]
            },
            "body": {
              "mode": "raw",
              "raw": "{\"name\": \"\", \"email\": \"\", \"phone\": \"\", \"password\": \"\"}"
            }
          }
        },
        {
          "name": "Login",
          "request": {
            "method": "POST",
            "header": [{"key": "Content-Type", "value": "application/json"}],
            "url": {"raw": "{{base_url}}/auth/login"},
            "body": {"mode": "raw", "raw": "{\"email\": \"\", \"password\": \"\"}"}
          }
        }
      ]
    }
  ],
  "variable": [
    {
      "key": "base_url",
      "value": "http://localhost:5000/api"
    },
    {
      "key": "token",
      "value": ""
    }
  ]
}
```

## Testing Checklist

- [ ] Register Admin account
- [ ] Register Customer account
- [ ] Login as Admin
- [ ] Login as Customer
- [ ] Create transaction (Admin)
- [ ] View transactions (Admin)
- [ ] View transactions (Customer)
- [ ] Update transaction (Admin)
- [ ] Delete transaction (Admin)
- [ ] Get balance (Admin)
- [ ] Get customer list (Admin)
- [ ] Get dashboard stats (Admin)

---

**Happy Testing! 🧪**
