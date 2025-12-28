# Deployment Guide

## 🚀 Production Deployment

### Part 1: Backend Deployment (Render.com)

#### Step 1: Prepare Backend

1. **Update package.json** for production:
   ```json
   {
     "engines": {
       "node": "18.x"
     }
   }
   ```

2. **Create .env.production**:
   ```
   MONGODB_URI=your_mongodb_atlas_uri
   JWT_SECRET=super_secret_key_change_this
   JWT_EXPIRE=7d
   PORT=10000
   NODE_ENV=production
   FRONTEND_URL=https://your-frontend-domain.com
   ```

#### Step 2: Deploy to Render

1. Go to [render.com](https://render.com)
2. Sign up/Login
3. Click "New" → "Web Service"
4. Connect GitHub repository
5. Configure:
   - **Name**: khatabook-backend
   - **Environment**: Node
   - **Build Command**: `npm install`
   - **Start Command**: `npm start`
   - **Instance Type**: Free (or Starter)

6. Add Environment Variables:
   - Add all values from .env.production

7. Click "Deploy"

**Your backend will be available at**: `https://khatabook-backend.onrender.com`

#### Step 3: Configure MongoDB Atlas

1. Go to [mongodb.com/cloud/atlas](https://www.mongodb.com/cloud/atlas)
2. Create free account
3. Create cluster (free tier available)
4. Get connection string: `mongodb+srv://user:password@cluster.mongodb.net/khatabook`
5. Add to Render environment variables

---

### Part 2: Frontend Deployment (Vercel)

#### Step 1: Prepare Frontend

1. **Update .env.production**:
   ```
   VITE_API_URL=https://khatabook-backend.onrender.com/api
   ```

2. **Build locally to test**:
   ```bash
   npm run build
   npm run preview
   ```

#### Step 2: Deploy to Vercel

1. Go to [vercel.com](https://vercel.com)
2. Sign up/Login with GitHub
3. Click "New Project"
4. Select your GitHub repository
5. Configure:
   - **Framework**: Vite
   - **Root Directory**: ./frontend

6. Add Environment Variables:
   ```
   VITE_API_URL=https://khatabook-backend.onrender.com/api
   ```

7. Click "Deploy"

**Your frontend will be available at**: `https://khatabook.vercel.app`

---

### Part 3: Alternative - Netlify Frontend

1. Go to [netlify.com](https://netlify.com)
2. Click "Add new site" → "Import an existing project"
3. Connect GitHub
4. Configure:
   - **Build command**: `npm run build`
   - **Publish directory**: `dist`

5. Add environment variables in Netlify dashboard
6. Deploy

---

### Part 4: Database Backup & Maintenance

#### MongoDB Atlas Backup

1. In MongoDB Atlas console:
   - Go to "Backup" section
   - Set up automated backups (daily)
   - Configure retention policy

#### Regular Maintenance

```bash
# Monitor database performance
# Check connection limits
# Review security settings
```

---

## 🔒 Production Security Checklist

- [ ] Change all default passwords
- [ ] Set strong JWT_SECRET (64+ characters)
- [ ] Enable HTTPS (automatic on Vercel/Render)
- [ ] Configure CORS properly
- [ ] Set secure environment variables
- [ ] Enable MongoDB IP whitelist
- [ ] Use strong database credentials
- [ ] Enable 2FA on all accounts
- [ ] Regular security updates
- [ ] Monitor for errors and issues

---

## 📊 Monitoring & Logging

### Render Backend Monitoring

1. Go to service dashboard
2. Check "Logs" section
3. Monitor for errors
4. Set up alerts for failures

### Vercel Frontend Monitoring

1. Dashboard → Analytics
2. Monitor build times
3. Check error logs
4. Review performance metrics

### Application Logging

Add logging service for production:

```bash
npm install winston
```

```javascript
// backend/src/config/logger.js
import winston from 'winston';

const logger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  transports: [
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' })
  ]
});

export default logger;
```

---

## 🔄 CI/CD Pipeline

### GitHub Actions (Optional)

Create `.github/workflows/deploy.yml`:

```yaml
name: Deploy to Production

on:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v2
        with:
          node-version: '18'
      - run: cd backend && npm install && npm test
      - run: cd frontend && npm install && npm run build

  deploy:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Deploy Backend
        # Deploy to Render/Heroku
      - name: Deploy Frontend
        # Deploy to Vercel/Netlify
```

---

## 💾 Database Migration

### From Local to Production

```bash
# Export local database
mongodump --uri "mongodb://localhost:27017/khatabook" --out ./backup

# Import to MongoDB Atlas
mongorestore --uri "mongodb+srv://user:pass@cluster.mongodb.net/khatabook" ./backup/khatabook
```

---

## 🐛 Troubleshooting Production

### Backend Issues

**502 Bad Gateway**
- Check Render logs
- Verify MongoDB connection
- Check environment variables

**Slow Performance**
- Check MongoDB indexes
- Monitor database load
- Optimize API queries

### Frontend Issues

**Blank Page**
- Check browser console
- Verify API_URL
- Check CORS headers

**Build Failures**
- Check build logs
- Verify dependencies
- Check Node version

---

## 📈 Scaling

### As Traffic Grows

1. **Backend**:
   - Upgrade from free to paid plan
   - Increase instance size
   - Add caching (Redis)
   - Optimize database queries

2. **Frontend**:
   - Enable CDN
   - Implement image optimization
   - Code splitting
   - Lazy loading

3. **Database**:
   - Upgrade cluster
   - Enable sharding
   - Optimize indexes
   - Archive old data

---

## 📞 Support & Resources

- Render Docs: https://render.com/docs
- Vercel Docs: https://vercel.com/docs
- MongoDB Atlas: https://docs.atlas.mongodb.com
- Node.js Best Practices: https://nodejs.org/en/docs/guides/

---

## 🎯 Quick Reference

| Service | URL | Status Page |
|---------|-----|------------|
| Backend (Render) | https://status.render.com | - |
| Frontend (Vercel) | https://www.vercel-status.com | - |
| Database (MongoDB) | https://status.mongodb.com | - |

---

**Happy Deploying! 🚀**
