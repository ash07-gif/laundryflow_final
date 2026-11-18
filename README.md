# LaundryFlow 🧺⚡

> Modern, cloud-native Laundry & Dry-Cleaning Management System built with **React**, **FastAPI**, and **PostgreSQL**.

[![FastAPI](https://img.shields.io/badge/Backend-FastAPI-009688?style=flat&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com)
[![React](https://img.shields.io/badge/Frontend-React%2018%20%2B%20Vite-61DAFB?style=flat&logo=react&logoColor=black)](https://react.dev)
[![TypeScript](https://img.shields.io/badge/Language-TypeScript-3178C6?style=flat&logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![Tailwind CSS](https://img.shields.io/badge/Styling-TailwindCSS-06B6D4?style=flat&logo=tailwindcss&logoColor=white)](https://tailwindcss.com/)
[![PostgreSQL](https://img.shields.io/badge/Database-PostgreSQL-4169E1?style=flat&logo=postgresql&logoColor=white)](https://www.postgresql.org/)

---

## 📖 Table of Contents
- [Features](#-features)
- [Architecture](#-architecture)
- [Project Structure](#-project-structure)
- [Quick Start (Local Development)](#-quick-start-local-development)
- [Default Demo Credentials](#-default-demo-credentials)
- [API Endpoints](#-api-endpoints)
- [Production Deployment Guide](#-production-deployment-guide)
  - [Option A: Free Cloud PaaS (Neon + Render + Vercel)](#option-a-free-cloud-paas-neon--render--vercel-recommended)
  - [Option B: Docker Compose](#option-b-docker-compose)
  - [Option C: Full AWS Cloud Infrastructure](#option-c-full-aws-cloud-infrastructure)
- [Environment Variables](#-environment-variables)
- [License](#-license)

---

## ✨ Features

- 👤 **Role-Based Portals**:
  - **Customer Portal**: Order scheduling wizard, service selection, active order tracking with status steppers, and invoice downloads.
  - **Staff Portal**: Mobile-responsive dashboard for drivers/processors to scan QR codes, claim orders, and update statuses (`PICKUP_SCHEDULED` $\rightarrow$ `PROCESSING` $\rightarrow$ `DELIVERED`).
  - **Admin Portal**: Real-time revenue metrics, operational KPIs, active orders breakdown, and staff management.
- 📦 **Smart Order Wizard**: Dynamic pricing calculator based on selected garment types, item quantities, and turnaround speed.
- 🏷️ **QR Code Generation & Tracking**: Unique tracking codes generated for every order bag and laundry ticket.
- 💳 **Integrated Payments**: Support for online transactions and Cash on Delivery (COD).
- 🔐 **Robust Security**: Passwords hashed with `bcrypt`, stateless session authentication with signed `JWT` tokens, and CORS protection.
- 📱 **Mobile-First UI**: Crafted with React, Lucide Icons, and TailwindCSS for smooth responsive layouts.

---

## 🏛 Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                       Client Layer                          │
│        React + Vite + TypeScript + Tailwind CSS             │
└──────────────────────────────┬──────────────────────────────┘
                               │
                          REST / JSON
                               │
┌──────────────────────────────▼──────────────────────────────┐
│                       API Layer                             │
│                  FastAPI (Python 3.11)                      │
│        ├── JWT Authentication & RBAC Dependencies           │
│        ├── Pydantic Schemas & Data Validation               │
│        └── Service Modules (Pricing, QR, Invoices)          │
└──────────────────────────────┬──────────────────────────────┘
                               │
                         SQLAlchemy 2.0
                               │
┌──────────────────────────────▼──────────────────────────────┐
│                      Database Layer                         │
│            PostgreSQL (Production) / SQLite (Dev)           │
└─────────────────────────────────────────────────────────────┘
```

---

## 📂 Project Structure

```text
laundryflow/
├── backend/
│   ├── app/
│   │   ├── api/routes/        # Auth, Orders, Staff, Admin, Services, Payments
│   │   ├── core/              # Config, DB connection, JWT & password security
│   │   ├── models/            # SQLAlchemy ORM models
│   │   ├── schemas/           # Pydantic request/response schemas
│   │   ├── services/          # QR generation, pricing logic, invoices
│   │   └── main.py            # FastAPI entry point & CORS configuration
│   ├── requirements.txt       # Python dependencies
│   ├── seed.py                # Database seeder (creates default admin, staff & services)
│   └── Procfile               # PaaS process configuration
├── frontend/
│   ├── src/
│   │   ├── api/client.ts      # Axios API client with auth interceptors
│   │   ├── components/        # Shared navigation, modals, and status badges
│   │   ├── pages/             # Admin, Staff, Customer dashboards & OrderWizard
│   │   └── main.tsx           # React app mount
│   ├── .browserslistrc        # Build compatibility targeting
│   ├── vercel.json            # SPA rewrite rules for client routing
│   └── vite.config.ts         # Vite server configuration (Port 3000)
├── infrastructure/
│   └── aws/                   # EC2 setup scripts, Nginx configs, Systemd units
└── render.yaml                # Infrastructure-as-Code Blueprint for Render
```

---

## 🚀 Quick Start (Local Development)

### Prerequisites
- **Python 3.10+**
- **Node.js 18+** & npm

### 1. Backend Setup
```bash
cd backend

# Create and activate virtual environment
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Seed initial database (creates laundryflow.db with demo data)
python seed.py

# Start FastAPI server
uvicorn app.main:app --host 127.0.0.1 --port 8002 --reload
```
* Backend API root: `http://127.0.0.1:8002`
* Interactive API Documentation (Swagger): `http://127.0.0.1:8002/docs`

### 2. Frontend Setup
In a new terminal window:
```bash
cd frontend

# Install dependencies
npm install

# Start Vite dev server
npm run dev
```
* Frontend Web App: `http://localhost:3000`

---

## 🔑 Default Demo Credentials

The database seeder automatically creates the following accounts:

| Role | Email | Password | Access Level |
| :--- | :--- | :--- | :--- |
| **Admin** | `admin@laundryflow.com` | `admin123` | Full dashboard, revenue analytics, staff oversight |
| **Staff** | `staff@laundryflow.com` | `staff123` | Task list, order assignment, status updater |
| **Customer** | `customer@laundryflow.com` | `customer123` | Booking wizard, order history, invoice download |

---

## 📡 API Endpoints

| Category | Method | Endpoint | Description |
| :--- | :--- | :--- | :--- |
| **Auth** | `POST` | `/api/v1/auth/register` | Register a new customer account |
| | `POST` | `/api/v1/auth/login` | Authenticate & receive JWT bearer token |
| | `GET` | `/api/v1/auth/me` | Fetch currently logged-in user profile |
| **Orders** | `GET` | `/api/v1/orders/` | List orders (filtered by user role) |
| | `POST` | `/api/v1/orders/` | Place a new laundry order |
| | `GET` | `/api/v1/orders/{id}` | Retrieve specific order details |
| | `GET` | `/api/v1/orders/track/{code}`| Public order tracking by tracking code |
| **Staff** | `GET` | `/api/v1/staff/assigned` | List orders assigned to current staff member |
| | `POST` | `/api/v1/staff/update-status`| Update pickup, wash, or delivery progress |
| **Admin** | `GET` | `/api/v1/admin/analytics` | Fetch revenue totals, active order breakdown |
| | `GET` | `/api/v1/admin/users` | Manage registered customers & staff |
| **Services**| `GET` | `/api/v1/services/` | Get list of available wash & dry-clean tiers |

---

## 🌐 Production Deployment Guide

### Option A: Free Cloud PaaS (Neon + Render + Vercel) *(Recommended)*
*100% Free Tier, No Credit Card Required.*

#### 1. Free PostgreSQL Database ([Neon.tech](https://neon.tech))
1. Sign up on **Neon** using your GitHub account.
2. Create a project named `laundryflow`.
3. Copy the **Connection Details** string (`postgresql://...`).

#### 2. Backend on [Render](https://render.com)
1. In the Render Dashboard, click **New +** > **Web Service** (not Blueprint).
2. Connect your repository: `ash07-gif/laundryflow_final`.
3. Configure the service:
   - **Root Directory:** `backend`
   - **Runtime:** `Python 3`
   - **Build Command:** `pip install -r requirements.txt && python seed.py`
   - **Start Command:** `uvicorn app.main:app --host 0.0.0.0 --port $PORT`
   - **Instance Type:** `Free`
4. Under **Environment Variables**, add:
   - `DATABASE_URL`: *(Your Neon connection string)*
   - `JWT_SECRET_KEY`: *(Generate with `openssl rand -hex 32`)*
5. Click **Deploy Web Service** and copy your Render URL (e.g., `https://laundryflow-backend.onrender.com`).

#### 3. Frontend on [Vercel](https://vercel.com)
1. Click **Add New...** > **Project** and import `laundryflow_final`.
2. Configure settings:
   - **Root Directory:** Select `frontend`.
   - **Framework Preset:** `Vite`
3. Add Environment Variable:
   - **Name:** `VITE_API_BASE_URL`
   - **Value:** `https://<your-render-service>.onrender.com/api/v1`
4. Click **Deploy**.

---

### Option B: Docker Compose
Run the entire production-like stack (PostgreSQL + FastAPI + Vite) locally:
```bash
docker-compose up --build
```
- Frontend: `http://localhost:5173`
- Backend API Docs: `http://localhost:8000/docs`

---

### Option C: Full AWS Cloud Infrastructure
For enterprise deployments on Amazon Web Services:
- **EC2 Instance Setup:** See [`infrastructure/aws/ec2-setup.sh`](infrastructure/aws/ec2-setup.sh)
- **Nginx Reverse Proxy:** See [`infrastructure/aws/nginx.conf`](infrastructure/aws/nginx.conf)
- **Systemd Service Unit:** See [`infrastructure/aws/laundryflow.service`](infrastructure/aws/laundryflow.service)
- **S3 Asset Storage:** See [`infrastructure/aws/s3-setup.md`](infrastructure/aws/s3-setup.md)
- **CloudWatch Monitoring:** See [`infrastructure/aws/cloudwatch-setup.md`](infrastructure/aws/cloudwatch-setup.md)

---

## ⚙️ Environment Variables

### Backend (`backend/.env`)
| Variable | Description | Default |
| :--- | :--- | :--- |
| `DATABASE_URL` | SQLAlchemy database connection URI | `sqlite:///./laundryflow.db` |
| `JWT_SECRET_KEY` | Secret key used to sign JWT session tokens | `super-secret-key-for-dev-only` |
| `JWT_ALGORITHM` | Token encryption algorithm | `HS256` |
| `ACCESS_TOKEN_EXPIRE_MINUTES`| Token expiration lifetime in minutes | `1440` (24h) |
| `CORS_ORIGINS` | Permitted origins for CORS | `["*"]` |

### Frontend (`frontend/.env`)
| Variable | Description | Default |
| :--- | :--- | :--- |
| `VITE_API_BASE_URL` | Base URL of the backend REST API | `http://localhost:8002/api/v1` |

---

## 📄 License
This project is licensed under the [MIT License](LICENSE).
