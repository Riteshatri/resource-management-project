# 💾 RM-Database: SQL Schema & Azure Setup

<div align="center">

![Status](https://img.shields.io/badge/Status-Production%20Ready-brightgreen?style=flat-square)
![Database](https://img.shields.io/badge/Database-Azure%20SQL-0078D4?style=flat-square&logo=microsoftazure)
![SQL](https://img.shields.io/badge/SQL-T--SQL-CC2927?style=flat-square&logo=microsoftsqlserver)

**Database Schema & Azure SQL Setup for Resource Management**

*Complete database setup, migrations, and deployment guides*

</div>

---

## 📦 What's This Repository?

This is the **DATABASE** component of the Resource Management Dashboard. It contains:

- 💾 Complete SQL database schema
- 📊 SQLite initialization scripts
- 🗄️ Azure SQL setup guide (step-by-step)
- 🔄 Data migration scripts
- 📈 Performance optimization queries
- 🔐 Security best practices
- 📋 Backup & recovery procedures
- 🌱 Sample data (seed files)

---

## 🚀 Quick Start

### Option 1: SQLite (Development)
```bash
# SQLite auto-creates when you run the backend
git clone https://github.com/Riteshatri/resource-management-backend.git
cd resource-management-backend
python run.py

# Database created at: backend/data/app.db
```

### Option 2: Azure SQL (Production) - 10 Minutes

#### Step 1: Create Azure Resources
```bash
# Set variables
RESOURCE_GROUP="rmd-prod"
LOCATION="eastus"
SQL_SERVER="rmd-sql-$(date +%s)"

# Create resource group
az group create \
  --name $RESOURCE_GROUP \
  --location $LOCATION

# Create SQL Server
az sql server create \
  --name $SQL_SERVER \
  --resource-group $RESOURCE_GROUP \
  --admin-user sqladmin \
  --admin-password YourSecurePassword123!

# Create SQL Database
az sql db create \
  --resource-group $RESOURCE_GROUP \
  --server $SQL_SERVER \
  --name resource_dashboard \
  --edition Standard
```

#### Step 2: Allow Your IP
```bash
# Get your public IP
MY_IP=$(curl -s https://api.ipify.org)

# Add firewall rule
az sql server firewall-rule create \
  --resource-group $RESOURCE_GROUP \
  --server $SQL_SERVER \
  --name AllowMyIP \
  --start-ip-address $MY_IP \
  --end-ip-address $MY_IP
```

#### Step 3: Import Database Schema

```bash
# Get connection string
SERVER=$(az sql server show \
  --resource-group $RESOURCE_GROUP \
  --name $SQL_SERVER \
  --query fullyQualifiedDomainName \
  --output tsv)

# Connect with sqlcmd
sqlcmd -S $SERVER -U sqladmin -P YourSecurePassword123! -d resource_dashboard

# Then run schema.sql from this repository
```

#### Step 4: Update Backend Configuration

Create `.env` file in backend:

```bash
APP_ENV=production
PORT=8000
UVICORN_WORKERS=4

AZURE_SQL_SERVER=$SERVER.database.windows.net
AZURE_SQL_DATABASE=resource_dashboard
AZURE_SQL_USERNAME=sqladmin
AZURE_SQL_PASSWORD=YourSecurePassword123!

SECRET_KEY=your-super-secret-key-32-characters-minimum
CORS_ALLOW_ORIGINS=https://yourdomain.com
ACCESS_TOKEN_EXPIRE_MINUTES=30
```

---

## 📊 Database Schema

### Table: users

```sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    email VARCHAR(255) UNIQUE NOT NULL,
    hashed_password VARCHAR(255) NOT NULL,
    display_name VARCHAR(100),
    tagline VARCHAR(200),
    bio TEXT,
    avatar_url VARCHAR(500),
    role VARCHAR(20) DEFAULT 'user', -- 'admin' or 'user'
    is_protected BOOLEAN DEFAULT FALSE, -- Cannot be deleted
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_users_email ON users(email);
```

### Table: resources

```sql
CREATE TABLE resources (
    id SERIAL PRIMARY KEY,
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    icon VARCHAR(50) NOT NULL,
    title VARCHAR(100) NOT NULL,
    resource_name VARCHAR(100) NOT NULL,
    description TEXT,
    status VARCHAR(20) DEFAULT 'Running', -- Running, Stopped, Pending
    region VARCHAR(50), -- East US, West US, etc.
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_resources_user_id ON resources(user_id);
CREATE INDEX idx_resources_status ON resources(status);
```

### Table: themes

```sql
CREATE TABLE themes (
    id SERIAL PRIMARY KEY,
    user_id UUID NOT NULL UNIQUE REFERENCES users(id) ON DELETE CASCADE,
    mode VARCHAR(20) DEFAULT 'light', -- 'light' or 'dark'
    primary_color VARCHAR(20) DEFAULT '#3B82F6',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Table: audit_logs

```sql
CREATE TABLE audit_logs (
    id SERIAL PRIMARY KEY,
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    action VARCHAR(50) NOT NULL, -- 'login', 'create_resource', etc.
    target_id VARCHAR(100), -- Resource ID or User ID
    details TEXT,
    ip_address VARCHAR(45),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_audit_logs_user_id ON audit_logs(user_id);
CREATE INDEX idx_audit_logs_created_at ON audit_logs(created_at);
```

---

## 📁 Repository Contents

```
database/
├── schema.sql              # Complete database schema
├── seed.sql                # Sample data
├── migrations/
│   ├── 001_initial.sql
│   ├── 002_add_audit_logs.sql
│   └── 003_optimize_indexes.sql
├── azure/
│   ├── setup.sh            # Azure SQL setup script
│   ├── connection-test.sql # Test connection
│   └── backup.sh           # Backup procedures
├── sqlite/
│   ├── init.sql            # SQLite initialization
│   └── to_azure_migrate.sql# Migration script
└── README.md               # This file
```

---

## 🔄 Database Workflow

### Development (SQLite)
```
1. Developer starts backend
2. SQLite auto-creates database at backend/data/app.db
3. Development continues locally
4. All data stored in single file
```

### Production (Azure SQL)
```
1. Create Azure SQL Server
2. Run schema.sql
3. Configure backend .env
4. Backend connects to Azure SQL
5. Production data stored in cloud
```

### Migration Path
```
Development (SQLite)
        ↓
Test & Verify
        ↓
Export data (if needed)
        ↓
Production (Azure SQL)
        ↓
Continuous backups
```

---

## 🔐 Security Best Practices

### 1. User Passwords
```sql
-- Passwords are hashed with bcrypt (10 rounds)
-- NEVER store plain text passwords
UPDATE users SET hashed_password = '<hashed>' WHERE id = '...';
```

### 2. Protected Admin User
```sql
-- Default admin cannot be deleted
INSERT INTO users (email, hashed_password, role, is_protected)
VALUES ('ritesh@apka.bhai', '<hashed>', 'admin', TRUE);

-- Even admin deletion is prevented by app logic
```

### 3. Audit Logging
```sql
-- Every important action logged
INSERT INTO audit_logs (user_id, action, details)
VALUES ('user-id', 'create_resource', 'Created resource: web-01');
```

### 4. SQL Injection Prevention
- All queries use parameterized statements (SQLAlchemy ORM)
- No raw SQL with user input
- Prepared statements throughout

---

## 📊 Database Queries

### Get User Resources
```sql
SELECT id, title, resource_name, status, region, created_at
FROM resources
WHERE user_id = 'user-uuid'
ORDER BY created_at DESC;
```

### Get All Users (Admin)
```sql
SELECT id, email, display_name, role, created_at
FROM users
ORDER BY created_at DESC;
```

### Get User Activity
```sql
SELECT user_id, action, details, created_at
FROM audit_logs
WHERE user_id = 'user-uuid'
ORDER BY created_at DESC
LIMIT 50;
```

### Count Resources by Status
```sql
SELECT status, COUNT(*) as count
FROM resources
WHERE user_id = 'user-uuid'
GROUP BY status;
```

---

## 🔧 Maintenance & Operations

### Backup Azure SQL Database
```bash
# Full backup
az sql db backup create \
  --resource-group $RESOURCE_GROUP \
  --server $SQL_SERVER \
  --name resource_dashboard

# View backups
az sql db backup show \
  --resource-group $RESOURCE_GROUP \
  --server $SQL_SERVER \
  --name resource_dashboard
```

### Monitor Database Performance
```sql
-- Check slow queries
SELECT * FROM sys.dm_exec_query_stats
ORDER BY total_elapsed_time DESC
LIMIT 10;

-- Check index fragmentation
SELECT * FROM sys.dm_db_index_physical_stats
WHERE avg_fragmentation_in_percent > 10;
```

### Database Cleanup
```sql
-- Delete old audit logs (older than 30 days)
DELETE FROM audit_logs
WHERE created_at < NOW() - INTERVAL '30 days';

-- Remove orphaned data
DELETE FROM resources
WHERE user_id NOT IN (SELECT id FROM users);
```

---

## 🚀 Scaling the Database

### For Small Scale (< 1,000 users)
```
Azure SQL Database - Standard Edition
├─ Cost: ~$15-30/month
├─ Performance: Good for most use cases
└─ No scaling needed
```

### For Medium Scale (1,000 - 100,000 users)
```
Azure SQL Database - Premium Edition
├─ Cost: ~$100-500/month
├─ Performance: Excellent
└─ Add read replicas if needed
```

### For Large Scale (> 100,000 users)
```
Azure SQL Managed Instance
├─ Cost: ~$500-2000/month
├─ Performance: Enterprise-grade
└─ Auto-scaling & failover
```

---

## 🔗 Integration with Other Repos

### ✅ Works With:
- **[RM-Backend](https://github.com/Riteshatri/resource-management-backend)** - Queries this database
- **[RM-Frontend](https://github.com/Riteshatri/resource-management-frontend)** - Gets data through backend

### 🚀 Complete Setup:

#### Development:
```bash
# 1. Start backend (SQLite auto-creates)
git clone https://github.com/Riteshatri/resource-management-backend.git
cd resource-management-backend
pip install -r requirements.txt
python run.py

# 2. Database created automatically at backend/data/app.db
```

#### Production:
```bash
# 1. Create Azure SQL (this repo's setup scripts)
# 2. Configure backend with .env pointing to Azure
# 3. Backend connects and uses cloud database
```

---

## 📈 Monitoring & Alerts

### Setup Azure Monitoring
```bash
# Enable metrics
az sql server metrics create \
  --resource-group $RESOURCE_GROUP \
  --server $SQL_SERVER

# Set alerts
az monitor metrics alert create \
  --name DatabaseCPUAlert \
  --resource-group $RESOURCE_GROUP \
  --condition "avg cpu_percent > 80"
```

---

## 🌟 Project Structure Links

```
Resource Management Project Structure:

📘 RM-Frontend
   └─ React 18 + Vite UI

🔗 +

🔧 RM-Backend
   └─ FastAPI Python API

🔗 +

💾 RM-Database (You are here!)
   └─ SQL Schema & Azure Setup

= 

🎯 Complete Resource Management Dashboard
```

---

## 📖 Files Reference

| File | Purpose |
|------|---------|
| `schema.sql` | Complete database schema |
| `seed.sql` | Sample data for testing |
| `azure/setup.sh` | Automated Azure setup |
| `azure/backup.sh` | Backup procedures |
| `migrations/` | Schema version control |
| `sqlite/init.sql` | SQLite setup |

---

## 📚 Documentation

- 📘 **[Main Project](https://github.com/Riteshatri/resource-management-project)** - Project overview
- 📗 **[RM-Frontend](https://github.com/Riteshatri/resource-management-frontend)** - Frontend setup
- 📕 **[RM-Backend](https://github.com/Riteshatri/resource-management-backend)** - Backend setup

---

## 🐛 Troubleshooting

### Can't connect to Azure SQL
```bash
# Test connection
sqlcmd -S server.database.windows.net \
       -U user -P password \
       -Q "SELECT 1"

# Check firewall
az sql server firewall-rule list \
  --resource-group $RESOURCE_GROUP \
  --server $SQL_SERVER
```

### Database locked error
```sql
-- Kill active connections
KILL <session_id>;

-- View active sessions
SELECT * FROM sys.dm_exec_sessions
WHERE database_id = DB_ID('resource_dashboard');
```

---

<div align="center">

### 👉 **[← Back to Main Project](https://github.com/Riteshatri/resource-management-project)**

### 👉 **[Setup Backend](https://github.com/Riteshatri/resource-management-backend)**

### 👉 **[Setup Frontend](https://github.com/Riteshatri/resource-management-frontend)**

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
- 📕 **[resource-management-backend](https://github.com/Riteshatri/resource-management-backend)** - FastAPI + Python ⭐
- 📙 **[resource-management-database](https://github.com/Riteshatri/resource-management-database)** - SQL + Azure ⭐ **(you are here)**

**⭐ Star this repo and others if you found them helpful!**

</div>

---

**v1.0.0** • Production Ready • MIT License

</div>
