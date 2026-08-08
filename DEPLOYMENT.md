# Bakery Website - Complete Deployment Guide

## 🚀 Deployment to Vercel (Recommended)

Vercel is perfect for Next.js apps and provides:
- ✅ Automatic HTTPS
- ✅ Global CDN
- ✅ Serverless functions
- ✅ Free tier available
- ✅ 1-click deploy from GitHub

---

## Step 1: Setup Prerequisites

### 1.1 Create GitHub Account & Repository
```bash
# Initialize git in your project
git init
git add .
git commit -m "Initial commit"

# Push to GitHub
git remote add origin https://github.com/yourusername/bakery-website.git
git branch -M main
git push -u origin main
```

### 1.2 Set Up MongoDB Atlas (Free)
1. Go to https://www.mongodb.com/cloud/atlas
2. Sign up for free account
3. Create a cluster (choose free tier)
4. In Security → Database Access, create a user with password
5. In Security → Network Access, add IP 0.0.0.0/0 (or your IP)
6. Click "Connect" → "Connect Your Application"
7. Copy connection string:
   ```
   mongodb+srv://username:password@cluster.mongodb.net/bakery?retryWrites=true&w=majority
   ```

### 1.3 Set Up PayPal Developer Account
1. Go to https://developer.paypal.com/dashboard
2. Sign up and log in
3. Create a sandbox account (for testing)
4. Go to Apps & Credentials
5. Copy your:
   - **Client ID**
   - **Secret**

---

## Step 2: Deploy to Vercel

### 2.1 Connect GitHub to Vercel
1. Go to https://vercel.com
2. Sign up with GitHub
3. Click "Import Project"
4. Select your `bakery-website` repository
5. Click "Import"

### 2.2 Add Environment Variables in Vercel
In your Vercel project dashboard:

1. Go to **Settings** → **Environment Variables**
2. Add each variable from `.env.example`:

```
MONGODB_URI = mongodb+srv://username:password@cluster.mongodb.net/bakery...
PAYPAL_CLIENT_ID = your_paypal_client_id
PAYPAL_CLIENT_SECRET = your_paypal_secret
PAYPAL_API_URL = https://api-m.sandbox.paypal.com (for testing)
PAYPAL_WEBHOOK_ID = your_webhook_id
NEXTAUTH_SECRET = (generate: openssl rand -base64 32)
NEXTAUTH_URL = https://yourdomain.vercel.app
JWT_SECRET = (any long random string)
NEXT_PUBLIC_PAYPAL_CLIENT_ID = your_paypal_client_id
```

### 2.3 Deploy
1. Click "Deploy"
2. Wait for deployment to complete
3. You'll get a URL like: `https://bakery-website.vercel.app`

---

## Step 3: Configure PayPal Webhooks

PayPal needs to notify your server of payments.

### 3.1 Set Up Webhook in PayPal Dashboard
1. Go to https://developer.paypal.com/dashboard
2. Click **Apps & Credentials** → **Sandbox** (for testing)
3. Under **Webhooks**, click **Add Webhook**
4. Webhook URL: `https://yourdomain.vercel.app/api/paypal/webhook`
5. Select events to listen to:
   - ✓ CHECKOUT.ORDER.COMPLETED
   - ✓ PAYMENT.CAPTURE.COMPLETED
   - ✓ PAYMENT.CAPTURE.DENIED
   - ✓ PAYMENT.CAPTURE.FAILED
   - ✓ PAYMENT.CAPTURE.REFUNDED
6. Click **Create Webhook**
7. Copy the **Webhook ID** and add to Vercel env variables

### 3.2 Test Webhook (Local)
```bash
# Install Stripe's webhook tester (works for all webhooks)
npm install -g stripe

# In your project directory
stripe listen --forward-to localhost:3000/api/paypal/webhook

# This gives you a signing secret - useful for testing
```

---

## Step 4: Connect Custom Domain (Optional)

### 4.1 In Vercel Dashboard
1. Go to **Settings** → **Domains**
2. Add your domain: `bakery.com`
3. Follow DNS setup instructions
4. Update `NEXTAUTH_URL` environment variable to: `https://bakery.com`

### 4.2 Update DNS Records
At your domain registrar (GoDaddy, Namecheap, etc.):
- Add CNAME record pointing to Vercel
- Instructions provided by Vercel

---

## Step 5: Test Payment Flow

### 5.1 Get Sandbox Test Accounts
1. In PayPal Developer Dashboard → **Sandbox**
2. Accounts section has pre-created buyer/seller accounts
3. Use buyer account for testing

### 5.2 Test Checkout Flow
1. Go to `https://yourdomain.vercel.app`
2. Browse products
3. Add to cart → Checkout
4. Fill in form details
5. Click "Pay with PayPal"
6. Use PayPal sandbox buyer account (no real money charged)
7. Should see success page
8. Check MongoDB to confirm order was created

### 5.3 Verify Database Records
```bash
# In MongoDB Atlas:
# Database → Collections → bakery → orders
# Should see your test order with status "completed"
```

---

## Step 6: Switch to Production (Go Live!)

⚠️ **DO NOT skip this** - you need real PayPal credentials

### 6.1 Create PayPal Live App
1. In PayPal Developer Dashboard → **Live** (not Sandbox)
2. Create new app
3. Copy **Live Client ID** and **Live Secret**

### 6.2 Update Environment Variables
In Vercel → Settings → Environment Variables:

```
PAYPAL_CLIENT_ID = your_LIVE_paypal_client_id
PAYPAL_CLIENT_SECRET = your_LIVE_paypal_secret
PAYPAL_API_URL = https://api-m.paypal.com (CHANGE from sandbox!)
NEXTAUTH_URL = https://yourdomain.com (or vercel app URL)
NODE_ENV = production
```

### 6.3 Set Up Live Webhook
In PayPal Developer → **Live** → Webhooks:
1. Add webhook: `https://yourdomain.com/api/paypal/webhook`
2. Select same events
3. Copy new **Webhook ID**
4. Update `PAYPAL_WEBHOOK_ID` in Vercel

### 6.4 Final Test
1. Make a real purchase (use real card or PayPal balance)
2. Verify:
   - Order appears in MongoDB
   - Order status is "completed"
   - Customer received email confirmation
   - Webhook was received

---

## 🔐 Security Checklist

Before going live, verify:

- [ ] `.env.local` is in `.gitignore` (never committed)
- [ ] `PAYPAL_CLIENT_SECRET` is NOT in any client-side code
- [ ] Webhook signature validation is enabled
- [ ] Rate limiting is active on payment endpoints
- [ ] HTTPS is enforced (automatic on Vercel)
- [ ] Database access restricted by IP or credentials
- [ ] Passwords hashed with bcryptjs
- [ ] JWT tokens have expiration
- [ ] Error messages don't leak sensitive info

---

## 📊 Monitoring & Maintenance

### Monitor PayPal Transactions
```bash
# In PayPal Developer Dashboard → Sandbox or Live
# Activity → Transactions
# Check for failed payments, disputes, refunds
```

### Monitor Database
```bash
# MongoDB Atlas Dashboard
# Charts → analyze order volume, payment success rate
```

### Monitor Website
```bash
# Vercel Analytics (automatic)
# Check:
# - Page load times
# - Error rates
# - Bandwidth usage
```

### Backup Database
```bash
# MongoDB Atlas → Backup section
# Enable automatic backups (recommended daily)
```

---

## 🆘 Troubleshooting

### Issue: "MONGODB_URI not found"
- Check Vercel environment variables are set
- Wait 60 seconds for changes to take effect
- Redeploy: `git push` → Vercel redeploys automatically

### Issue: "PayPal order creation fails"
- Verify PAYPAL_CLIENT_ID is correct
- Check you're using sandbox URL for testing
- Verify webhook ID is set (can be empty for now)

### Issue: "Webhook signature verification failed"
- Ensure webhook ID matches in PayPal dashboard
- Check raw webhook body isn't being modified
- Verify webhook URL is publicly accessible

### Issue: "Customer sees blank checkout page"
- Check browser console for errors (F12)
- Verify `NEXT_PUBLIC_PAYPAL_CLIENT_ID` is set
- Clear browser cache and try again

### Issue: "Orders not appearing in database"
- Check MongoDB connection string in env vars
- Verify database user has write permissions
- Check MongoDB network access includes your IP

---

## 📈 Performance Optimization

### Enable Image Optimization
- Bakery photos should be optimized for web
- Use Next.js Image component (already done)
- Compress images to <200KB each

### Enable Caching
```javascript
// next.config.js
module.exports = {
  images: {
    domains: ['via.placeholder.com'],
    formats: ['image/avif', 'image/webp'],
  },
  headers: async () => [
    {
      source: '/:path*',
      headers: [
        {
          key: 'Cache-Control',
          value: 'public, max-age=31536000, immutable',
        },
      ],
    },
  ],
};
```

### Monitor Performance
- Vercel provides real-time analytics
- Check Core Web Vitals in dashboard
- Optimize based on real user data

---

## 🎉 You're Live!

Congratulations! Your bakery website is now:
- ✅ Deployed to production
- ✅ Accepting real PayPal payments
- ✅ Storing orders in database
- ✅ Secure and HTTPS-enabled
- ✅ Available 24/7 globally

**Next steps:**
1. Share your website with customers
2. Monitor orders and payments
3. Collect feedback
4. Add email notifications for customers
5. Scale as business grows

---

## 📞 Support

- **Vercel Docs:** https://vercel.com/docs
- **PayPal Docs:** https://developer.paypal.com/docs
- **MongoDB Docs:** https://docs.mongodb.com
- **Next.js Docs:** https://nextjs.org/docs
