# 🚀 Nexacart — Vercel + MongoDB Atlas Deployment Guide

## Architecture
- **Web Server**: Vercel (serverless Python)
- **Database**: MongoDB Atlas (cloud, free tier)
- **Images**: Cloudinary (cloud image storage, free tier)

---

## Step 1 — Set up MongoDB Atlas (Free)

1. Go to **https://www.mongodb.com/atlas** → Sign up free
2. Create a **Free Cluster** (M0, any region)
3. **Database Access** → Add user → set username + password
4. **Network Access** → Add IP Address → **Allow Access from Anywhere** (0.0.0.0/0)
5. Click **Connect** → **Drivers** → copy the connection string:
   ```
   mongodb+srv://<username>:<password>@cluster0.xxxxx.mongodb.net/?retryWrites=true&w=majority
   ```
6. Replace `<username>` and `<password>` with your credentials

---

## Step 2 — Push to GitHub

```bash
cd market
git init
git add .
git commit -m "Nexacart v1.0 — MongoDB + Cloudinary"
```

Create a repo on github.com → New repository → name it `nexacart` → no README.

```bash
git remote add origin https://github.com/YOUR_USERNAME/nexacart.git
git branch -M main
git push -u origin main
```

---

## Step 3 — Deploy on Vercel

1. Go to **https://vercel.com** → Sign up with GitHub
2. **Add New Project** → Import your `nexacart` repo
3. **Framework Preset**: Other
4. **Root Directory**: leave blank (project root)
5. Click **Environment Variables** and add all of these:

| Variable | Value |
|----------|-------|
| `SECRET_KEY` | A long random string (click Generate) |
| `MONGO_URI` | Your MongoDB Atlas connection string |
| `MONGO_DB` | `nexacart` |
| `ADMIN_SECRET` | Your admin panel password |
| `MERCHANT_UPI_ID` | Your UPI ID (e.g. `yourname@upi`) |
| `MERCHANT_NAME` | `Nexacart` |
| `STRIPE_PUBLISHABLE_KEY` | From stripe.com (optional) |
| `STRIPE_SECRET_KEY` | From stripe.com (optional) |
| `ANTHROPIC_API_KEY` | From console.anthropic.com (optional, for AI chat) |

6. Click **Deploy** ✅

Your app will be live at: `https://nexacart.vercel.app`

---

## Step 4 — First-Time Setup After Deployment

1. Visit `https://nexacart.vercel.app/register`
2. Select **Admin** account type
3. Create your admin account
4. Visit `https://nexacart.vercel.app/admin/login` and enter your `ADMIN_SECRET`

> **Note:** Products are auto-seeded in MongoDB on first startup. No manual setup needed.

---

## How Images Work (MongoDB GridFS)

When admin uploads a product image in **Edit Product** or **Add Product**:
1. Image is stored directly inside **MongoDB GridFS** (binary storage in your database)
2. The image key is saved to the MongoDB product document
3. Images are served via `/img/<product_id>/<slot>` Flask route
4. **No external service needed** — one MongoDB Atlas account handles everything
5. Works perfectly on Vercel — no filesystem required

---

## Updating Your Deployed App

```bash
git add .
git commit -m "Your change description"
git push
```
Vercel automatically redeploys on every push. ✅

---

## Vercel Free Tier Limits

| Limit | Free Tier |
|-------|-----------|
| Serverless function duration | 10 seconds |
| Bandwidth | 100 GB/month |
| Deployments | Unlimited |
| Custom domain | ✅ Free |

## MongoDB Atlas Free Tier Limits

| Limit | M0 Free |
|-------|---------|
| Storage | 512 MB |
| RAM | Shared |
| Connections | 500 |

512 MB is enough for ~500,000 products and millions of orders.
