# 📝 Code Style Guide & Best Practices

## JavaScript/Node.js Style Guide

### File Naming
```javascript
// ✅ Good
authController.js
userService.js
authRoutes.js
CustomerList.jsx

// ❌ Avoid
auth-controller.js (use camelCase for JS)
userservice.js (lack of clarity)
AUTH.js (all caps for files)
```

### Variable Naming
```javascript
// ✅ Good
const userData = {};
const isLoading = false;
const fetchUserData = async () => {};
const MAX_RETRIES = 3;

// ❌ Avoid
const u = {};          // too short
const is_loading = false; // snake_case
const fetch = async () => {}; // conflicts with fetch API
const maxRetries = 3;  // should be UPPER_CASE constant
```

### Function Naming
```javascript
// ✅ Good
async function fetchUser() {}
const getUserById = (id) => {}
const handleClick = () => {}

// ❌ Avoid
async function getUser() {} // ambiguous
const get_user_by_id = () => {} // snake_case
function onClick() {} // should start with "handle" for handlers
```

### Comments & Documentation
```javascript
// ✅ Good
/**
 * Authenticates user with email and password
 * @param {string} email - User email
 * @param {string} password - User password
 * @returns {Promise<Object>} User and token
 */
const login = async (email, password) => {
  // Validate inputs
  if (!email || !password) {
    throw new Error('Email and password required');
  }
  
  // Make API call
  return await api.post('/auth/login', { email, password });
};

// ❌ Avoid
// Login function
const login = async (email, password) => {
  // check email
  if (!email) throw new Error('Email required');
  // rest of code
};
```

### Error Handling
```javascript
// ✅ Good
try {
  const user = await fetchUser(userId);
  return { success: true, user };
} catch (error) {
  console.error('Error fetching user:', error);
  return { success: false, message: 'Failed to fetch user' };
}

// ❌ Avoid
const user = await fetchUser(userId); // No error handling
try {
  // code
} catch (e) {
  // Silent failure
}
```

---

## React Component Style Guide

### Component Structure
```jsx
// ✅ Good
import { useState, useEffect } from 'react';
import { Input, Button } from '../common/UIComponents';
import { userService } from '../../services/authService';

export default function UserProfile() {
  // 1. State
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  // 2. Effects
  useEffect(() => {
    fetchUser();
  }, []);

  // 3. Functions
  const fetchUser = async () => {
    try {
      const response = await userService.getProfile();
      setUser(response.data);
    } catch (error) {
      console.error('Error:', error);
    } finally {
      setLoading(false);
    }
  };

  // 4. Render
  if (loading) return <LoadingSpinner />;
  if (!user) return <Error message="User not found" />;

  return (
    <div className="p-4">
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  );
}

// ❌ Avoid
export default function UserProfile() {
  // Mixed order
  const fetchUser = async () => { /* ... */ };
  const [user, setUser] = useState(null);
  
  useEffect(() => {
    fetchUser();
  }, []);

  return <div>...</div>;
}
```

### Props & Prop Types
```jsx
// ✅ Good
function Button({ 
  children, 
  variant = 'primary', 
  onClick,
  disabled = false,
  className = '',
  ...props 
}) {
  return (
    <button
      className={`btn btn-${variant} ${className}`}
      onClick={onClick}
      disabled={disabled}
      {...props}
    >
      {children}
    </button>
  );
}

// ❌ Avoid
function Button(props) { // No destructuring
  return <button {...props}>{props.children}</button>;
}

function Input(email, phone, password, ...) { // Too many props
  return <input />;
}
```

### Custom Hooks
```jsx
// ✅ Good
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await fetch(url);
        setData(response.data);
      } catch (err) {
        setError(err);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, [url]);

  return { data, loading, error };
}

// Usage
const { data, loading, error } = useFetch('/api/users');
```

---

## MongoDB & Mongoose Best Practices

### Schema Definition
```javascript
// ✅ Good
const userSchema = new mongoose.Schema(
  {
    name: {
      type: String,
      required: [true, 'Name is required'],
      trim: true,
      minlength: [2, 'Name must be at least 2 characters'],
    },
    email: {
      type: String,
      required: [true, 'Email is required'],
      unique: true,
      lowercase: true,
      match: [/^[^\s@]+@[^\s@]+\.[^\s@]+$/, 'Please provide valid email'],
    },
    password: {
      type: String,
      required: [true, 'Password is required'],
      minlength: [6, 'Password must be at least 6 characters'],
      select: false, // Don't return password by default
    },
  },
  { timestamps: true }
);

// ❌ Avoid
const userSchema = new mongoose.Schema({
  name: String,
  email: String,
  password: String,
});
```

### Query Best Practices
```javascript
// ✅ Good
// Use lean() for read-only queries
const users = await User.find({ isActive: true }).lean();

// Use select for specific fields
const users = await User.find().select('name email phone');

// Use indexes for frequent queries
userSchema.index({ email: 1 });

// ❌ Avoid
// Getting all fields when you need only few
const user = await User.findById(id); // Gets password too

// No index on frequently queried fields
const users = await User.find({ email: email }); // Slow
```

---

## Express.js Best Practices

### Route Organization
```javascript
// ✅ Good
// routes/authRoutes.js
import { authenticate, authorize } from '../middleware/auth';

router.post('/register', validate, register);
router.post('/login', validate, login);
router.get('/profile', authenticate, getProfile);

// ❌ Avoid
// Mixing routes in server.js
app.post('/register', register);
app.post('/api/auth/login', login);
app.get('/profile', getProfile);
```

### Middleware Order
```javascript
// ✅ Good
app.use(cors());
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// Routes
app.use('/api/auth', authRoutes);
app.use('/api/users', authenticateToken, userRoutes);

// Error handling
app.use((err, req, res, next) => {
  res.status(500).json({ error: err.message });
});

// ❌ Avoid
app.use(authRoutes); // Before auth middleware
app.use(express.json()); // After routes
```

### Error Handling
```javascript
// ✅ Good
export const createUser = async (req, res) => {
  try {
    const { email, password } = req.body;

    if (!email || !password) {
      return res.status(400).json({
        success: false,
        message: 'Email and password required',
      });
    }

    const user = new User({ email, password });
    await user.save();

    res.status(201).json({
      success: true,
      message: 'User created',
      user,
    });
  } catch (error) {
    console.error('Create user error:', error);
    res.status(500).json({
      success: false,
      message: 'Error creating user',
    });
  }
};

// ❌ Avoid
export const createUser = async (req, res) => {
  const user = new User(req.body);
  await user.save(); // No error handling
  res.json(user);
};
```

---

## CSS & Tailwind Best Practices

### Class Organization
```jsx
// ✅ Good - Organized by category
<div className="
  /* Layout */
  flex flex-col
  /* Spacing */
  p-4 mb-4
  /* Sizing */
  w-full max-w-md
  /* Colors */
  bg-white text-gray-800
  /* Effects */
  shadow-md rounded-lg
  /* States */
  hover:shadow-lg transition
">
  Content
</div>

// ❌ Avoid - Random order
<div className="p-4 w-full mb-4 rounded-lg shadow-md bg-white hover:shadow-lg text-gray-800 flex flex-col max-w-md transition">
```

### Responsive Design
```jsx
// ✅ Good - Mobile first
<div className="
  grid
  grid-cols-1         /* Mobile */
  md:grid-cols-2      /* Tablet */
  lg:grid-cols-3      /* Desktop */
  gap-4
">
  {/* Cards */}
</div>

// ❌ Avoid - Desktop first
<div className="
  grid-cols-3
  lg:grid-cols-2
  md:grid-cols-1
">
```

### Custom Classes
```jsx
// ✅ Good - Use globals for repeated patterns
// index.css
@layer components {
  .btn-primary {
    @apply px-4 py-2 bg-blue-600 text-white rounded-lg
           hover:bg-blue-700 transition-colors;
  }
}

// Then in components
<button className="btn-primary">Click me</button>

// ❌ Avoid - Repeated utility classes
<button className="px-4 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition-colors">
  Click me
</button>
```

---

## Git Commit Messages

### Format
```
<type>(<scope>): <subject>

<body>

<footer>
```

### Types
- **feat**: A new feature
- **fix**: A bug fix
- **docs**: Documentation only
- **style**: Changes that don't affect code meaning
- **refactor**: Code change that neither fixes nor adds feature
- **perf**: Code change that improves performance
- **test**: Adding or updating tests

### Examples
```bash
# ✅ Good
git commit -m "feat(auth): add JWT token validation"
git commit -m "fix(dashboard): correct balance calculation"
git commit -m "docs(README): update setup instructions"
git commit -m "refactor(api): extract common validation logic"

# ❌ Avoid
git commit -m "fixed stuff"
git commit -m "updates"
git commit -m "WIP"
git commit -m "asdflkj"
```

---

## Testing Best Practices

### Test Structure
```javascript
// ✅ Good
describe('User Authentication', () => {
  let testUser;

  beforeEach(() => {
    testUser = { email: 'test@test.com', password: 'password123' };
  });

  it('should register a new user', async () => {
    const response = await api.post('/api/auth/register', testUser);
    expect(response.status).toBe(201);
    expect(response.body.user.email).toBe(testUser.email);
  });

  it('should not register with invalid email', async () => {
    testUser.email = 'invalid-email';
    const response = await api.post('/api/auth/register', testUser);
    expect(response.status).toBe(400);
  });

  afterEach(async () => {
    await User.deleteMany({});
  });
});
```

---

## Documentation Standards

### File Header
```javascript
/**
 * @file User Authentication Controller
 * @description Handles user registration, login, and profile endpoints
 * @requires mongoose
 * @requires jsonwebtoken
 * @author Team
 * @version 1.0.0
 */
```

### Function Documentation
```javascript
/**
 * Authenticates user and returns JWT token
 * 
 * @async
 * @function login
 * @param {string} email - User email address
 * @param {string} password - User password (will be hashed)
 * @returns {Promise<Object>} Object with success status, token, and user
 * @throws {Error} If credentials are invalid
 * 
 * @example
 * const result = await login('user@example.com', 'password123');
 * // { success: true, token: '...', user: {...} }
 */
async function login(email, password) {
  // Implementation
}
```

---

## Performance Tips

### Frontend
- Use React.memo for components that don't change often
- Lazy load components with React.lazy()
- Debounce search inputs
- Use pagination for large lists
- Minimize bundle size

### Backend
- Use database indexes
- Implement caching
- Optimize queries with select()
- Use lean() for read-only queries
- Implement rate limiting

### Database
- Create indexes on frequently queried fields
- Use pagination
- Archive old data
- Regular backups
- Monitor query performance

---

## Code Review Checklist

Before submitting code:
- [ ] Code follows style guide
- [ ] No console.log statements
- [ ] No hardcoded values
- [ ] Error handling implemented
- [ ] Comments added for complex logic
- [ ] No commented-out code
- [ ] Tests passing (if applicable)
- [ ] No security vulnerabilities
- [ ] Performance optimized
- [ ] Documentation updated

---

**Happy Coding! 🚀**
