# 🔧 RM-Backend: FastAPI + Python REST API

<div align="center">

![Status](https://img.shields.io/badge/Status-Production%20Ready-brightgreen?style=flat-square)
![Python](https://img.shields.io/badge/Python-3.11%2B-3776AB?style=flat-square&logo=python)
![FastAPI](https://img.shields.io/badge/FastAPI-0.104-009688?style=flat-square&logo=fastapi)
![SQLAlchemy](https://img.shields.io/badge/SQLAlchemy-2.0-CC0000?style=flat-square&logo=database)

**Production-Ready REST API for Resource Management**

*Secure, scalable, and fully documented backend*

</div>

---

## 📦 What's This Repository?

This is the **BACKEND** component of the Resource Management Dashboard. It contains:

- ⚙️ FastAPI REST API server
- 🔐 JWT authentication system
- 👥 Role-based access control (Admin/User)
- 💾 SQLAlchemy ORM for database
- 📊 SQLite (dev) or Azure SQL (prod)
- 🛡️ Security best practices
- 📖 Auto-generated API documentation
- 🚀 Production deployment ready

---

## 🚀 Quick Start

### Prerequisites
- **Python** 3.11+
- **pip** (Python package manager)
- **Git**

### Setup (5 minutes)
```bash
# Clone this repository
git clone https://github.com/Riteshatri/resource-management-backend.git
cd resource-management-backend

# Create virtual environment
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Start server
python run.py

# ✅ Backend running at http://localhost:8000
# 📖 API docs at http://localhost:8000/docs
```

---

## 📁 Project Structure

```
backend/
├── app/
│   ├── api/
│   │   ├── auth.py         # Authentication endpoints
│   │   ├── users.py        # User management endpoints
│   │   ├── resources.py    # Resource CRUD endpoints
│   │   └── theme.py        # Theme endpoints
│   │
│   ├── core/
│   │   └── config.py       # Configuration settings
│   │
│   ├── db/
│   │   ├── database.py     # Database connection
│   │   ├── models.py       # SQLAlchemy models
│   │   └── super_user_seed.py  # Admin user setup
│   │
│   ├── models/
│   │   ├── user.py         # User SQLAlchemy model
│   │   ├── resource.py     # Resource SQLAlchemy model
│   │   └── theme.py        # Theme SQLAlchemy model
│   │
│   ├── schemas/
│   │   ├── user.py         # User Pydantic schemas
│   │   ├── resource.py     # Resource Pydantic schemas
│   │   └── theme.py        # Theme Pydantic schemas
│   │
│   ├── main.py            # FastAPI app instance
│   └── __init__.py
│
├── data/
│   └── app.db             # SQLite database (gitignored)
│
├── .env.example           # Environment variables template
├── requirements.txt       # Python dependencies
├── run.py                # Server startup script
└── README.md             # This file
```

---

## 🎨 Features

### 🔐 Authentication
- User registration
- User login with JWT tokens
- Token refresh mechanism
- Auto-logout on expiry (30 min default)

### 👥 User Management
- User registration
- User profiles
- Admin user management
- Role assignment (Admin/User)
- Protected admin account

### 📦 Resource Management
- Create resources
- Read all resources
- Update resources
- Delete resources
- 18 resource types supported
- Real-time updates

### 🎨 Theme Management
- Save theme preferences
- Dark/Light mode
- Custom colors

### 🛡️ Security
- JWT authentication
- Bcrypt password hashing
- CORS protection
- SQL injection prevention
- Role-based access control

---

## 🔧 API Endpoints

### Authentication

**POST** `/api/auth/register`
```json
Request:
{
  "email": "user@example.com",
  "password": "SecurePass123"
}

Response:
{
  "access_token": "eyJhbGc...",
  "token_type": "bearer",
  "user": { "id": "...", "email": "...", "role": "user" }
}
```

**POST** `/api/auth/login`
```json
Request:
{
  "email": "user@example.com",
  "password": "SecurePass123"
}

Response: Same as register
```

### Users

**GET** `/api/users/me` (Get current user)
```
Headers: Authorization: Bearer <token>

Response:
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "email": "user@example.com",
  "role": "user"
}
```

**GET** `/api/users` (Admin only - Get all users)
```
Headers: Authorization: Bearer <admin_token>

Response: [{ user1 }, { user2 }, ...]
```

**PUT** `/api/users/{user_id}/role` (Admin only)
```json
Request:
{
  "role": "admin"
}

Response: { updated user }
```

**DELETE** `/api/users/{user_id}` (Admin only)
```
Response: { "success": true }
```

### Resources

**GET** `/api/resources` (List user's resources)
```
Headers: Authorization: Bearer <token>

Response: [
  {
    "id": 1,
    "title": "Web Server",
    "resource_name": "web-01",
    "icon": "server",
    "status": "Running",
    "region": "East US",
    "created_at": "2025-11-25T10:30:00Z"
  }
]
```

**POST** `/api/resources` (Create resource)
```json
Request:
{
  "icon": "server",
  "title": "API Server",
  "resource_name": "api-01",
  "description": "Production API",
  "status": "Running",
  "region": "East US"
}

Response: { created resource object }
```

**PUT** `/api/resources/{resource_id}` (Update resource)
```json
Request:
{
  "status": "Stopped"
}

Response: { updated resource }
```

**DELETE** `/api/resources/{resource_id}` (Delete resource)
```
Response: { "success": true }
```

### Theme

**GET** `/api/theme` (Get theme preference)
```
Response: { "mode": "dark", "primary_color": "#3B82F6" }
```

**PUT** `/api/theme` (Save theme preference)
```json
Request:
{
  "mode": "light",
  "primary_color": "#EF4444"
}

Response: { updated theme }
```

---

## 📖 API Documentation

Auto-generated interactive docs:

- **Swagger UI**: http://localhost:8000/docs
- **ReDoc**: http://localhost:8000/redoc

Open these in your browser to explore and test all endpoints!

---

## ⚙️ Configuration

### Development Setup

Create `.env` file (or use environment variables):

```bash
APP_ENV=development
SECRET_KEY=dev-key-any-random-string
PORT=8000
CORS_ALLOW_ORIGINS=*
LOG_LEVEL=INFO
```

### Production Setup (Azure SQL)

```bash
APP_ENV=production
SECRET_KEY=your-super-secret-key-min-32-chars
PORT=8000
UVICORN_WORKERS=4

# Azure SQL
AZURE_SQL_SERVER=your-server.database.windows.net
AZURE_SQL_DATABASE=resource_dashboard
AZURE_SQL_USERNAME=sqladmin
AZURE_SQL_PASSWORD=YourPassword123!

CORS_ALLOW_ORIGINS=https://yourdomain.com,https://www.yourdomain.com
ACCESS_TOKEN_EXPIRE_MINUTES=30
```

---

## 🗄️ Database

### Development (SQLite)
- Auto-created at `backend/data/app.db`
- Zero configuration needed
- Perfect for local development

### Production (Azure SQL)
- See [RM-Database](https://github.com/Riteshatri/resource-management-database) for setup

### Database Tables

```sql
users
├── id (UUID)
├── email (unique)
├── hashed_password
├── display_name
├── role (admin/user)
└── created_at

resources
├── id (Integer)
├── user_id (FK → users)
├── icon
├── title
├── resource_name
├── description
├── status
├── region
└── created_at

themes
├── id
├── user_id (FK → users)
├── mode (light/dark)
├── primary_color
└── updated_at

audit_logs
├── id
├── user_id (FK → users)
├── action
├── timestamp
└── details
```

---

## 🚀 Deployment

### Local Development
```bash
python run.py
```

### Azure VM (Production)
```bash
# 1. SSH into VM
ssh azureuser@<VM_IP>

# 2. Setup environment
git clone https://github.com/Riteshatri/resource-management-backend.git
cd resource-management-backend
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# 3. Configure .env with Azure SQL
cp .env.example .env
nano .env  # Edit with your values

# 4. Start with systemd
sudo systemctl start resource-dashboard
sudo systemctl status resource-dashboard
```

See [RM-Database](https://github.com/Riteshatri/resource-management-database) for complete Azure setup.

---

## 🔧 Available Commands

```bash
# Development server (auto-reload)
python run.py

# Production server (4 workers)
APP_ENV=production UVICORN_WORKERS=4 python run.py

# Create admin user
python app/db/super_user_seed.py

# Interactive shell
python -m ipython
```

---

## 🏗️ Tech Stack

| Tech | Version | Purpose |
|------|---------|---------|
| FastAPI | 0.104 | Web framework |
| Uvicorn | 0.24 | ASGI server |
| SQLAlchemy | 2.0 | ORM |
| Pydantic | 2.0 | Data validation |
| python-jose | Latest | JWT tokens |
| bcrypt | Latest | Password hashing |
| passlib | Latest | Password utilities |

---

## 🔗 Integration with Other Repos

### ✅ Works With:
- **[RM-Frontend](https://github.com/Riteshatri/resource-management-frontend)** - React UI (required)
- **[RM-Database](https://github.com/Riteshatri/resource-management-database)** - Database setup

### 🚀 Complete Setup:
```bash
# 1. Start this backend
git clone https://github.com/Riteshatri/resource-management-backend.git
cd resource-management-backend
pip install -r requirements.txt
python run.py

# 2. Start frontend (in another terminal)
git clone https://github.com/Riteshatri/resource-management-frontend.git
cd resource-management-frontend
npm install
npm run dev

# 3. Open http://localhost:5000
```

---

## 🐛 Troubleshooting

### Port 8000 already in use
```bash
lsof -i :8000      # Find process
kill -9 <PID>      # Kill it
python run.py      # Try again
```

### Dependencies not found
```bash
source venv/bin/activate
pip install -r requirements.txt
```

### Database error
```bash
rm backend/data/app.db
python run.py  # Recreates database
```

### Azure SQL connection fails
```bash
# Test connection
sqlcmd -S server.database.windows.net \
       -U user -P password \
       -Q "SELECT 1"

# Check .env credentials
cat .env | grep AZURE
```

---

## 📚 Code Examples

### Creating a Resource (Python)
```python
from app.schemas import ResourceCreate
from app.models import Resource

# In API endpoint
resource = Resource(**resource_data.dict())
db.add(resource)
db.commit()
```

### JWT Token
```python
from app.core.config import settings
from datetime import datetime, timedelta

access_token_expires = timedelta(
    minutes=settings.ACCESS_TOKEN_EXPIRE_MINUTES
)
```

### Password Hashing
```python
from passlib.context import CryptContext

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")
hashed = pwd_context.hash(password)
```

---

## 🌟 Project Structure Links

```
Resource Management Project Structure:

📘 RM-Frontend
   └─ React 18 + Vite UI

🔗 +

🔧 RM-Backend (You are here!)
   └─ FastAPI Python API

🔗 +

💾 RM-Database
   └─ SQL Schema & Azure Setup

= 

🎯 Complete Resource Management Dashboard
```

---

## 📖 Documentation

- 📘 **[Main Project](https://github.com/Riteshatri/resource-management-project)** - Project overview
- 📗 **[RM-Frontend](https://github.com/Riteshatri/resource-management-frontend)** - Frontend setup
- 📙 **[RM-Database](https://github.com/Riteshatri/resource-management-database)** - Database setup

---

<div align="center">

### 👉 **[← Back to Main Project](https://github.com/Riteshatri/resource-management-project)**

### 👉 **[Next: Setup Database →](https://github.com/Riteshatri/resource-management-database)**

---

## ⭐ **Found This Helpful? Give it a Star!**

---

<div align="center" style="background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); border-radius: 12px; padding: 30px; margin: 25px 0;">

## 👨‍💻 **Author**

### **Ritesh Sharma**

🎯 *Full-Stack Developer | Cloud Architect | DevOps Engineer*

**Tech:** Azure | Terraform | CI/CD | Cloud Automation | React | FastAPI | Database Design

<div style="margin: 20px 0; display: flex; gap: 15px; justify-content: center;">

**[🔗 LINKEDIN](https://www.linkedin.com/in/riteshatri)** | **[🐙 GITHUB](https://github.com/Riteshatri)**

</div>

### **All 4 Repositories:**

- 📘 **[resource-management-project](https://github.com/Riteshatri/resource-management-project)** - Main showcase ⭐
- 📗 **[resource-management-frontend](https://github.com/Riteshatri/resource-management-frontend)** - React 18 + Vite ⭐
- 📕 **[resource-management-backend](https://github.com/Riteshatri/resource-management-backend)** - FastAPI + Python ⭐ **(you are here)**
- 📙 **[resource-management-database](https://github.com/Riteshatri/resource-management-database)** - SQL + Azure ⭐

**⭐ Star this repo and others if you found them helpful!**

</div>

---

**v1.0.0** • Production Ready • MIT License

</div>
