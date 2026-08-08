# ⚡ Quick Start - 10 Minutes to Live Shop

Follow these steps to get your bakery website running locally and deployed.

---

## 🎯 Step 1: Local Setup (5 minutes)

### 1.1 Install Node.js
- Download from https://nodejs.org (LTS version)
- Verify: `node --version` (should be 16+)

### 1.2 Create Project
```bash
# Create new Next.js app
npx create-next-app@latest bakery-website --typescript=no --tailwind=yes --eslint=yes

cd bakery-website

# Install dependencies
npm install next-auth@latest mongoose framer-motion axios bcryptjs jsonwebtoken dotenv
```

### 1.3 Copy Files
1. Copy all files from this template to your project
2. Replace placeholders in component files
3. Copy `.env.example` to `.env.local` and fill in values

### 1.4 Get Credentials

**Get MongoDB Connection String:**
1. Go to https://www.mongodb.com/cloud/atlas
2. Create free account → Create cluster
3. Security → Database Access → Add user
4. Security → Network Access → Add IP
5. Connect → Copy connection string
6. Paste in `.env.local` as `MONGODB_URI`

**Get PayPal Credentials:**
1. Go to https://developer.paypal.com/dashboard
2. Apps & Credentials → Sandbox tab
3. Copy Client ID → paste in `.env.local`
4. Copy Secret → paste in `.env.local`

### 1.5 Run Locally
```bash
npm run dev
# Visit http://localhost:3000
```

**Test Payment Flow:**
1. Click "Shop Now"
2. Add products to cart
3. Go to checkout
4. Fill in details
5. Click "Pay with PayPal"
6. Use PayPal sandbox account
7. Approve → See success page

---

## 🚀 Step 2: Deploy to Vercel (3 minutes)

### 2.1 Push to GitHub
```bash
git add .
git commit -m "Initial bakery website"
git push -u origin main
```

### 2.2 Deploy on Vercel
1. Go to https://vercel.com
2. Sign up with GitHub
3. Click "Import Project"
4. Select `bakery-website` repo
5. Click "Deploy"

### 2.3 Add Environment Variables
In Vercel dashboard:
1. Settings → Environment Variables
2. Add all variables from `.env.local`:
   - `MONGODB_URI`
   - `PAYPAL_CLIENT_ID`
   - `PAYPAL_CLIENT_SECRET`
   - `PAYPAL_API_URL=https://api-m.sandbox.paypal.com`
   - `NEXTAUTH_SECRET` (generate new)
   - `NEXTAUTH_URL=https://yourdomain.vercel.app`
   - `JWT_SECRET` (any random string)
   - `NEXT_PUBLIC_PAYPAL_CLIENT_ID`

3. Click Deploy → Wait 2 minutes

**Your site is now live!** Share the URL with your client.

---

## 📝 Step 3: Configure PayPal Webhook (2 minutes)

1. Go to https://developer.paypal.com/dashboard
2. Apps & Credentials → Sandbox
3. Webhooks → Add Webhook
4. URL: `https://yourdomain.vercel.app/api/paypal/webhook`
5. Select events: CHECKOUT.ORDER.COMPLETED, PAYMENT.CAPTURE.COMPLETED
6. Save
7. Copy Webhook ID
8. Add `PAYPAL_WEBHOOK_ID` to Vercel environment variables

---

## ✅ Testing Checklist

- [ ] Local dev server runs: `npm run dev`
- [ ] Products page loads
- [ ] Can add items to cart
- [ ] Checkout form appears
- [ ] PayPal button works
- [ ] Can approve payment with sandbox account
- [ ] Success page appears
- [ ] Order saved in MongoDB
- [ ] Deployed to Vercel successfully
- [ ] Live URL is accessible
- [ ] Can buy on live site

---

## 🎨 Customization (10 minutes)

### Change Bakery Info
**`components/Banner.js`**
- Change "Freshly Baked Goodness" to your bakery name
- Update description
- Modify colors (gradient-to-r from-orange-400 to-pink-400)

### Add Products
**`lib/seedData.js`** (create this file):
```javascript
// Add your products to MongoDB
const products = [
  {
    name: "Chocolate Cake",
    category: "cakes",
    price: 29.99,
    description: "Rich chocolate cake"
  },
  // Add more...
];
```

### Change Colors
Update Tailwind classes in components:
- `from-orange-400` → change color theme
- `text-orange-500` → accent color
- `bg-gradient-to-r` → gradient direction

### Add Logo
**`app/layout.js`**:
```javascript
<img src="/logo.png" alt="Logo" className="w-8 h-8" />
```
Put `logo.png` in `/public` folder

---

## 🔑 Environment Variables Reference

```
# Database
MONGODB_URI=mongodb+srv://user:pass@cluster.mongodb.net/bakery

# PayPal
PAYPAL_CLIENT_ID=AbCdEf...
PAYPAL_CLIENT_SECRET=XyZ123...
PAYPAL_API_URL=https://api-m.sandbox.paypal.com
PAYPAL_WEBHOOK_ID=12V56...

# Auth
NEXTAUTH_SECRET=random_secret_here
NEXTAUTH_URL=https://yourdomain.vercel.app

# JWT
JWT_SECRET=another_secret

# Public (safe to expose)
NEXT_PUBLIC_PAYPAL_CLIENT_ID=AbCdEf...
```

---

## 💡 Pro Tips

1. **Test locally first** - catch bugs before deploying
2. **Keep secrets safe** - never commit `.env.local`
3. **Monitor payments** - check PayPal dashboard daily
4. **Backup database** - enable MongoDB backups
5. **Use custom domain** - add in Vercel settings
6. **Enable email alerts** - get notified of errors

---

## 🚨 Common Issues & Fixes

| Issue | Fix |
|-------|-----|
| "MONGODB_URI not found" | Add to Vercel env vars, wait 60s, redeploy |
| "PayPal button doesn't work" | Check NEXT_PUBLIC_PAYPAL_CLIENT_ID is set |
| "Orders not saving" | Verify MongoDB connection in env vars |
| "Webhook fails" | Add PAYPAL_WEBHOOK_ID to env vars |
| "Payment shows pending" | Wait 30s, refresh page |

---

## 📞 Next Steps

1. ✅ Test your shop locally
2. ✅ Deploy to Vercel
3. ✅ Set up PayPal webhooks
4. ✅ Test live payment
5. ✅ Add your real products
6. ✅ Switch PayPal to production mode
7. ✅ Add custom domain
8. ✅ Share with customers
9. ✅ Monitor orders & payments
10. ✅ Scale as needed

---

## 📚 Full Documentation

- **README.md** - Complete project overview
- **DEPLOYMENT.md** - Detailed deployment guide
- **API Documentation** - All endpoints and webhooks

**Need help?** Check the comments in the code - every file is well documented!

Good luck! 🎉
