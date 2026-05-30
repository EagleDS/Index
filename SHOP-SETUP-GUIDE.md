# Eagle DS Shop — Setup Guide

## What you have

- shop.html — public shop page, all 21 products, in your brand
- eagle-ds-functions.js — Firebase Cloud Function, handles Stripe webhooks, fires PO emails
- functions-package.json — npm dependencies for the function

## Steps to go live

### 1. Stripe — Create products (30 min)

For each product in your shop:

1. Go to stripe.com → Products → Add product
1. Name it exactly as listed in the shop
1. Set the price in NZD
1. Under Metadata, note the Price ID (starts with price_…)
1. Update SKU_MAP in eagle-ds-functions.js — replace PRICE_ID_… with real IDs

### 2. Stripe — Configure checkout

1. Settings → Checkout → enable Afterpay/Clearpay
1. Settings → Checkout → Shipping → add New Zealand flat rate
1. Settings → Checkout → collect shipping address: required
1. Settings → Checkout → collect customer name: required

### 3. Stripe — Payment links

For each product, create a Payment Link:

1. Products → click product → Create payment link
1. Copy the URL (buy.stripe.com/…)
1. In shop.html, find the product’s “stripe” field and replace STRIPE_XXX with the real URL suffix

### 4. Deploy Firebase Function

1. Install Firebase CLI: npm install -g firebase-tools
1. firebase login
1. In your functions folder: npm install
1. Set secrets:
   firebase functions:secrets:set STRIPE_SECRET_KEY
   firebase functions:secrets:set STRIPE_WEBHOOK_SECRET
   firebase functions:secrets:set RESEND_API_KEY
1. firebase deploy –only functions
1. Copy the function URL from the output

### 5. Stripe webhook

1. Stripe Dashboard → Developers → Webhooks → Add endpoint
1. Endpoint URL: your Firebase function URL
1. Events: checkout.session.completed
1. Copy the signing secret → set as STRIPE_WEBHOOK_SECRET

### 6. Morgan Sports dealer account

Contact Morgan Sports and confirm:

- Your dealer account allows ad-hoc email orders
- A default payment card is on file for settlement
- [orders@morgansports.com.au](mailto:orders@morgansports.com.au) is the correct order intake email

### 7. Deploy shop.html

Drop shop.html into your GitHub repo. Done.

## Adding new products later

1. Add to Stripe
1. Add to SKU_MAP in eagle-ds-functions.js
1. Add to PRODUCTS array in shop.html
1. Redeploy function + push shop.html