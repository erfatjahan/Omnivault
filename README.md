# Omnivault

Omnivault is a full-stack e-commerce platform that brings together a customer storefront,
an administration dashboard,and a REST API.Customers can discover products,manage their 
accounts,place orders,and pay through SSLCommerz,while administrators 
manage catalog and store operations.
## Project Overview
Omnivault is organized as three independently run applications:

- **Storefront (`frontend/`)** — The customer-facing shopping experience, located in`frontend/`.
- **Dashboard** -The administrative interface, located in `dashboard/`.
- **API server** —The Node.js service in `backend/`,responsible for authentication,product and
-  order workflows,payments,and PostgreSQL access.

The storefront and dashboard call the API over HTTP.The API persists commerce data in PostgreSQL and connects to Cloudinary and 
Google Gemini for media management and AI-assisted product search.
## Live Links
- **Customer storefront:** [omnivault-frontend-one.vercel.app](https://omnivault-frontend-one.vercel.app)
- **Admin dashboard:** [omnivault-dashboard.vercel.app](https://omnivault-dashboard.vercel.app)
## Key Features

- **Product catalog and discovery** — Browse product listings and details,and submit product reviews.
- **Accounts and authentication** — Register,sign in,manage a profile,and use password recovery and update workflows.
- **Cart, orders, and order history** — Place orders,view order history,cancel eligible orders,and create shareable Pay-for-Me payment requests.
- **SSLCommerz payments** — Initialize checkout and process success,failure,and cancellation callbacks.Configure merchant credentials and callback URLs for the environment in use.
- **Cloudinary media** — Store product images and user profile images in Cloudinary.
- **Gemini AI search** — Find products using AI-assisted search and recommendations.This feature requires a Gemini API key.
- **Administration** — Manage products,orders,and users;review dashboard statistics;and perform protected user-role updates.
- **PostgreSQL persistence** — Store account,catalog,order,review,payment,and shipping data in PostgreSQL.

The codebase also contains a Stripe webhook handler. SSLCommerz is the primary documented checkout flow.

## How It Works (Workflow)

1. **Browse:** Customers explore the catalog and product details in the storefront.Authenticated customers can use AI-assisted search and submit reviews.
2. **Create an order:** A customer signs in,prepares an order,and submits it through the API.
3. **Pay:** The API initializes an SSLCommerz session.The payment provider redirects the customer to the configured success,failure, or cancellation callback.
4. **Track:** Customers view their orders and can cancel eligible orders.A customer can also create a Pay-for-Me link for supported payment flows.
5. **Manage:** Administrators use the dashboard and protected API routes to manage products,orders,users,and store statistics.

```text
Customer storefront ─┐
                     ├── REST API ─── PostgreSQL
Admin dashboard ─────┘       ├─────── SSLCommerz
                             ├─────── Cloudinary
                             └─────── Gemini AI
```
## Tech Stack

| Area | Technologies |
| --- | --- |
| Storefront | React, Vite,Redux Toolkit,React Router,Tailwind CSS, Axios |
| Admin dashboard | React,Vite,Redux Toolkit,React Router,Tailwind CSS,Recharts |
| Backend | Node.js,Express 5,JavaScript ES modules |
| Database | PostgreSQL,`pg` |
| Authentication | JSON Web Tokens (JWT),cookies |
| Payments | SSLCommerz;Stripe webhook handler |
| Media storage | Cloudinary |
| AI search | Google Gemini via`@google/generative-ai` |
| Email | Nodemailer |


## Project Structure

The folder names below reflect the current repository.`frontend/` is the customer **frontend**;`backend/` is the API service.

```text
E_COMMERCE/
├── s_erver_frontend/          # Customer storefront (React + Vite)
│   ├── public/
│   ├── src/
│   └── package.json
├── dashboard/                  # Admin dashboard (React + Vite)
│   ├── public/
│   ├── src/
│   └── package.json
├── s_erver/                    # API server (Node.js + Express)
│   ├── controllers/
│   ├── database/
│   ├── middlewares/
│   ├── models/
│   ├── router/
│   ├── uploads/
│   ├── utils/
│   ├── app.js
│   ├── server.js
│   └── package.json
└── README.md
```

## Getting Started

### Prerequisites

- Node.js 18 or later and npm
- A PostgreSQL database
- Cloudinary credentials for image uploads
- A Gemini API key for AI-assisted search
- SSLCommerz merchant credentials for payment testing or live checkout

The project has no root-level npm workspace. Install and start each application from its own directory.

### 1. Configure and run the API

From the repository root, install the backend dependencies:

```powershell
cd s_erver
npm install
```

The API loads `config/config.env` relative to the `s_erver/` working directory. Create that file and add the settings required for your environment:

```powershell
New-Item -ItemType Directory -Force config
New-Item -ItemType File -Force config/config.env
```

Example `s_erver/config/config.env`:

```dotenv
PORT=4000
DATABASE_URL=your-postgresql-connection-string
JWT_SECRET_KEY=replace-with-a-long-random-secret

FRONTEND_URL=http://localhost:5173
CLIENT_URL=http://localhost:5173
DASHBOARD_URL=http://localhost:5174
BACKEND_URL=http://localhost:4000

CLOUDINARY_CLIENT_NAME=your-cloud-name
CLOUDINARY_CLIENT_API=your-cloudinary-api-key
CLOUDINARY_CLIENT_SECRET=your-cloudinary-api-secret
GEMINI_API_KEY=your-gemini-api-key

SSLCOMMERZ_STORE_ID=your-store-id
SSLCOMMERZ_STORE_PASSWORD=your-store-password
SSLCOMMERZ_IS_LIVE=false

JWT_EXPIRES_IN=30d
COOKIE_EXPIRES_IN=30
SMTP_MAIL=your-sender@example.com
SMTP_PASSWORD=your-email-provider-credential
```

Use your database provider's connection string and real provider credentials. Keep secrets out of source control. SSLCommerz sandbox mode is selected unless `SSLCOMMERZ_IS_LIVE=true`. Email settings are needed only for email workflows; Cloudinary and Gemini credentials are needed for their corresponding features.

Start the API:

```powershell
npm run dev
```

The API is available at `http://localhost:4000` with the example port. Check `http://localhost:4000/health` to verify it is responding. The server initializes its database tables during startup, so the configured PostgreSQL database must be reachable.

### 2. Run the customer storefront

Open a second terminal at the repository root:

```powershell
cd s_erver_frontend
npm install
npm run dev
```

Vite serves the storefront at `http://localhost:5173` by default.

### 3. Run the admin dashboard

Open a third terminal at the repository root:

```powershell
cd dashboard
npm install
npm run dev
```

The dashboard's Vite configuration uses `http://localhost:5174`.

Both frontends use `http://localhost:4000/api/v1` as their development API base URL. Their production builds use the API URL configured in each frontend's Axios client.

### Production builds

Build the frontends from their respective directories:

```powershell
cd s_erver_frontend
npm run build

cd ..\dashboard
npm run build
```

Run the backend in production from `s_erver/` with `npm start`, and provide its environment variables through the deployment platform.

## API Endpoints Overview

The API is mounted at `/api/v1`. Routes marked **Authenticated** require a valid session/token; **Admin** and **SuperAdmin** routes additionally require the corresponding role. SSLCommerz callback routes are called by the payment provider.

| Method | Endpoint | Access | Description |
| --- | --- | --- | --- |
| `GET` | `/health` | Public | Check API availability. |
| `POST` | `/api/v1/auth/register` | Public | Register an account. |
| `POST` | `/api/v1/auth/login` | Public | Sign in. |
| `GET` | `/api/v1/auth/me` | Authenticated | Get the current user's profile. |
| `POST` | `/api/v1/auth/password/forgot` | Public | Start password recovery. |
| `PUT` | `/api/v1/auth/password/reset/:token` | Public token route | Reset a password. |
| `PUT` | `/api/v1/auth/profile/update` | Authenticated | Update profile details. |
| `GET` | `/api/v1/product/` | Public | Fetch the product catalog. |
| `GET` | `/api/v1/product/singleProduct/:productId` | Public | Fetch product details. |
| `POST` | `/api/v1/product/ai-search` | Authenticated | Search for products with AI assistance. |
| `POST` | `/api/v1/product/admin/create` | Admin | Create a product. |
| `PUT` | `/api/v1/product/admin/update/:productId` | Admin | Update a product. |
| `POST` | `/api/v1/order/new` | Authenticated | Place an order. |
| `GET` | `/api/v1/order/my-orders` | Authenticated | List the current user's orders. |
| `PUT` | `/api/v1/order/cancel/:orderId` | Authenticated | Cancel an eligible order. |
| `GET` | `/api/v1/order/admin/orders` | Admin | List orders for administration. |
| `PUT` | `/api/v1/order/admin/order/:orderId` | Admin | Update order status. |
| `GET` | `/api/v1/admin/users` | Admin or SuperAdmin | List users. |
| `GET` | `/api/v1/admin/stats` | Admin or SuperAdmin | Fetch dashboard statistics. |
| `POST` | `/api/v1/admin/user/:id/send-role-otp` | SuperAdmin | Send an OTP for a role update. |
| `PUT` | `/api/v1/admin/user/:id/update-role` | SuperAdmin |Update a user's role with OTP verification. |
| `POST` | `/api/v1/payment/ssl-init` | Authenticated | Initialize SSLCommerz checkout. |
| `GET`, `POST` | `/api/v1/payment/ssl-success` | Provider callback | Handle successful payment. |
| `GET`, `POST` | `/api/v1/payment/ssl-fail` | Provider callback | Handle failed payment. |
| `GET`, `POST` | `/api/v1/payment/ssl-cancel` | Provider callback | Handle cancelled payment. |
| `POST` | `/api/v1/payment/webhook` | Stripe provider callback | Receive Stripe webhook events. |

This table highlights the primary routes. See the route modules in `s_erver/router/` for the complete route list, aliases, and access controls.
