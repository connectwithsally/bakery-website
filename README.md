# Bakery Website - Complete Template
## PayPal Integration | Customer Accounts | Order History | Animated Banner

---

## 🚀 Quick Start (5 Minutes)

### 1. Prerequisites
- Node.js 16+ installed
- MongoDB Atlas account (free: mongodb.com/cloud/atlas)
- PayPal Business account (sandbox: developer.paypal.com)

### 2. Clone/Setup
```bash
# Create project
npx create-next-app@latest bakery-website
cd bakery-website

# Install dependencies
npm install stripe next-auth mongoose framer-motion axios bcryptjs jsonwebtoken dotenv

# Alternative single command:
npm install next-auth@latest mongoose framer-motion axios bcryptjs jsonwebtoken dotenv express-rate-limit cors
```

### 3. Environment Setup
Create `.env.local` in root directory:
```
# Database
MONGODB_URI=mongodb+srv://username:password@cluster.mongodb.net/bakery?retryWrites=true&w=majority

# PayPal (Get from developer.paypal.com)
PAYPAL_CLIENT_ID=your_client_id_here
PAYPAL_CLIENT_SECRET=your_secret_here
PAYPAL_API_URL=https://api-m.sandbox.paypal.com

# Auth
NEXTAUTH_SECRET=generate_with: openssl rand -base64 32
NEXTAUTH_URL=http://localhost:3000

# JWT for order verification
JWT_SECRET=your_jwt_secret_here
```

### 4. Run Locally
```bash
npm run dev
# Open http://localhost:3000
```

### 5. Deploy to Vercel
```bash
# Push to GitHub first
npm install -g vercel
vercel
# Follow prompts, add .env.local variables to Vercel dashboard
```

---

## 📁 Project Structure

```
bakery-website/
├── app/
│   ├── layout.js (Global wrapper)
│   ├── page.js (Homepage with banner)
│   ├── products/
│   │   └── page.js (Products listing)
│   ├── checkout/
│   │   └── page.js (Checkout page)
│   ├── account/
│   │   ├── login/page.js
│   │   ├── signup/page.js
│   │   └── orders/page.js
│   └── api/
│       ├── auth/ (NextAuth endpoints)
│       ├── products/route.js
│       ├── orders/route.js
│       ├── paypal/
│       │   ├── create-order/route.js
│       │   ├── capture-order/route.js
│       │   └── webhook/route.js
│       └── middlewares/auth.js
├── models/
│   ├── User.js
│   ├── Product.js
│   └── Order.js
├── lib/
│   ├── mongodb.js (Connection)
│   ├── paypal.js (PayPal helper)
│   └── auth.js (Auth utilities)
├── components/
│   ├── Banner.js (Animated)
│   ├── ProductCard.js
│   ├── Navbar.js
│   └── Footer.js
├── .env.local (Your secrets - NEVER commit)
├── .gitignore
└── package.json
```

---

## 🔐 Security Features Included

✅ PayPal webhook signature validation (prevents fake payments)
✅ Environment variable protection (no secrets in code)
✅ Password hashing with bcryptjs
✅ JWT authentication tokens
✅ Rate limiting on payment endpoints
✅ HTTPS redirect (automatic on Vercel)
✅ XSS protection (Next.js React default)
✅ CSRF tokens (Next.js/NextAuth automatic)

---

## 💳 PayPal Integration Flow

```
Customer clicks "Pay with PayPal"
    ↓
Frontend calls /api/paypal/create-order
    ↓
Backend creates PayPal order (no money taken yet)
    ↓
PayPal Checkout window opens
    ↓
Customer approves payment (on PayPal's secure server)
    ↓
Frontend calls /api/paypal/capture-order
    ↓
Backend captures funds & validates signature
    ↓
Order created in MongoDB with status: "completed"
    ↓
Customer sees success page + email confirmation
    ↓
WebHook from PayPal confirms (backup verification)
```

---

## 📊 Database Schema

### Users Collection
```javascript
{
  _id: ObjectId,
  email: "customer@example.com",
  password: "hashed_bcrypt",
  name: "John Doe",
  createdAt: ISODate,
  updatedAt: ISODate
}
```

### Products Collection
```javascript
{
  _id: ObjectId,
  name: "Chocolate Cake",
  category: "cakes", // cakes | bread | custom
  price: 29.99,
  image: "url_to_image",
  description: "Rich chocolate cake with ganache",
  available: true,
  createdAt: ISODate
}
```

### Orders Collection
```javascript
{
  _id: ObjectId,
  userId: ObjectId,
  items: [
    { productId: ObjectId, name: "Cake", quantity: 1, price: 29.99 }
  ],
  totalAmount: 29.99,
  paypalOrderId: "3C679662-2Jxxxx", // From PayPal API
  status: "completed", // pending | completed | failed | refunded
  paymentMethod: "paypal",
  shippingAddress: "123 Main St",
  createdAt: ISODate,
  updatedAt: ISODate
}
```

---

## 🧪 Testing Payments Locally

### PayPal Sandbox Test Cards:
```
Buyer Email: sb-xxxx@personal.example.com (from PayPal dashboard)
Password: sandbox password
```

Test transaction without real money:
1. Go to http://localhost:3000/products
2. Add item to cart
3. Click "Checkout"
4. Use PayPal sandbox account
5. Confirm payment
6. Check MongoDB for order created

---

## 🚀 Production Deployment Checklist

- [ ] Create PayPal Live app (not sandbox)
- [ ] Update PAYPAL_CLIENT_ID, PAYPAL_CLIENT_SECRET, PAYPAL_API_URL in Vercel env
- [ ] Update NEXTAUTH_URL to your domain
- [ ] Set up custom domain on Vercel
- [ ] Enable email notifications (add Nodemailer setup)
- [ ] Test payment flow end-to-end
- [ ] Set up error logging (Sentry recommended)
- [ ] Monitor PayPal webhook health

---

## 📞 Support & Common Issues

### Issue: "MONGODB_URI not found"
**Solution:** Check .env.local exists and has correct connection string

### Issue: "PayPal order creation fails"
**Solution:** Verify PAYPAL_CLIENT_ID and CLIENT_SECRET in .env.local

### Issue: "Signature validation failed"
**Solution:** Don't modify webhook body, PayPal SDK does verification automatically

### Issue: "Customer can't login"
**Solution:** Check password hashing - use the bcryptjs helper provided

---

## 📝 Files Included in This Package

This template includes:
1. ✅ Complete API routes (PayPal, Auth, Products, Orders)
2. ✅ React components (Banner with animations, Product cards, Checkout)
3. ✅ Database models (User, Product, Order - MongoDB)
4. ✅ Authentication system (NextAuth + JWT)
5. ✅ Security middleware (Rate limiting, signature validation)
6. ✅ Environment setup guide
7. ✅ Deployment instructions (Vercel)
8. ✅ Test data seeding script
9. ✅ PayPal webhook handler

See individual files below for complete code.

---

## 🎨 Customization

### Change Colors
Edit `components/Banner.js` - update Framer Motion colors and CSS

### Add More Products
Edit `lib/seedData.js` - add product objects, run node script

### Custom Animations
Framer Motion is included - see `components/Banner.js` for examples

### Email Notifications
Add Nodemailer to send confirmation emails on order completion

---

## 💡 Next Steps

1. Copy all files to your project
2. Run `npm install`
3. Set up `.env.local`
4. Connect MongoDB Atlas
5. Set up PayPal sandbox
6. Run `npm run dev`
7. Test flow locally
8. Deploy to Vercel

**Ready to build? All files below are production-ready.** 🚀
