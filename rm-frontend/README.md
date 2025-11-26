# ⚛️ RM-Frontend: React 18 + Vite Dashboard

<div align="center">

![Status](https://img.shields.io/badge/Status-Production%20Ready-brightgreen?style=flat-square)
![React](https://img.shields.io/badge/React-18.2-61DAFB?style=flat-square&logo=react)
![TypeScript](https://img.shields.io/badge/TypeScript-5.3-3178C6?style=flat-square&logo=typescript)
![Vite](https://img.shields.io/badge/Vite-5.4-646CFF?style=flat-square&logo=vite)

**Modern React Frontend for Resource Management Dashboard**

*Beautiful, responsive, and fully type-safe user interface*

</div>

---

## 📦 What's This Repository?

This is the **FRONTEND** component of the Resource Management Dashboard. It contains:

- ✨ React 18 + TypeScript application
- 🎨 Tailwind CSS styling + Shadcn UI components
- 📱 100% responsive design (mobile-first)
- 🔄 TanStack Query for smart data fetching
- 🎯 React Router v6 for navigation
- 🚀 Vite for lightning-fast development
- 🌙 Dark/Light theme support
- 📊 Dashboard with real-time updates

---

## 🚀 Quick Start

### Prerequisites
- **Node.js** v20+ (with npm)
- **Git**

### Setup (2 minutes)
```bash
# Clone this repository
git clone https://github.com/Riteshatri/resource-management-frontend.git
cd resource-management-frontend

# Install dependencies
npm install

# Start development server
npm run dev

# ✅ Open http://localhost:5000 in your browser
```

---

## 📁 Project Structure

```
src/
├── components/          # Reusable UI components
│   ├── Header.tsx
│   ├── Sidebar.tsx
│   ├── ResourceCard.tsx
│   └── ThemeToggle.tsx
│
├── pages/              # Page components
│   ├── Dashboard.tsx
│   ├── Resources.tsx
│   ├── Users.tsx
│   └── Settings.tsx
│
├── contexts/           # React Context (Auth)
│   └── AuthContext.tsx
│
├── lib/               # Utilities & API client
│   ├── api.ts        # Axios instance for API calls
│   └── utils.ts      # Helper functions
│
├── types/             # TypeScript type definitions
│   └── index.ts
│
├── App.tsx            # Main app component
├── main.tsx           # Entry point
└── index.css          # Global styles
```

---

## 🎨 Features

### 🔐 Authentication
- User registration & login
- JWT token management
- Auto-logout on token expiry
- Protected routes

### 📦 Resource Management
- Create resources
- Edit/Update resources
- Delete resources
- Real-time list updates
- 18 resource types

### 👥 User Management (Admin)
- View all users
- Assign/revoke admin role
- Delete users

### 🎨 Theme System
- Dark mode support
- Light mode support
- Persistent theme preference
- Real-time switching

### 📱 Responsive Design
- Works on desktop (1024px+)
- Works on tablet (768-1024px)
- Works on mobile (320-768px)
- Touch-friendly controls

---

## 🔧 Available Scripts

```bash
# Start development server (HMR enabled)
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview

# Run ESLint
npm run lint
```

---

## 📡 API Integration

The frontend connects to the backend API at:

```
Backend URL: http://localhost:8000
API Prefix: /api/

Endpoints used:
- POST   /api/auth/register
- POST   /api/auth/login
- GET    /api/users/me
- GET    /api/users (admin)
- POST   /api/resources
- GET    /api/resources
- PUT    /api/resources/{id}
- DELETE /api/resources/{id}
- GET    /api/theme
- PUT    /api/theme
```

---

## 🏗️ Tech Stack

| Tech | Version | Purpose |
|------|---------|---------|
| React | 18.2 | UI framework |
| TypeScript | 5.3 | Type safety |
| Vite | 5.4 | Build tool |
| Tailwind CSS | 3.4 | Styling |
| Shadcn UI | Latest | Component library |
| TanStack Query | 5.25 | Data fetching |
| React Router | 6 | Navigation |
| Axios | 1.6 | HTTP client |

---

## 🚀 Building for Production

```bash
# Build the app
npm run build

# This creates a 'dist' folder with:
# - Optimized JavaScript bundles
# - Minified CSS
# - Compressed images
# - Production-ready assets

# Upload 'dist' folder contents to:
# - Nginx server (serve as static files)
# - Vercel / Netlify
# - AWS S3 + CloudFront
# - Any static hosting
```

---

## 🌍 Environment Variables

Create a `.env.local` file:

```
VITE_API_BASE_URL=http://localhost:8000
VITE_APP_NAME=Resource Dashboard
```

---

## 🔗 Integration with Other Repos

### ✅ Works With:
- **[RM-Backend](https://github.com/Riteshatri/resource-management-backend)** - API server (required)
- **[RM-Database](https://github.com/Riteshatri/resource-management-database)** - Database setup (for backend)

### 🚀 Deployment Together:
```bash
# 1. Setup Backend API (from RM-Backend repo)
git clone https://github.com/Riteshatri/resource-management-backend.git
cd resource-management-backend
pip install -r requirements.txt
python run.py  # Backend running on port 8000

# 2. Setup Frontend (this repo)
git clone https://github.com/Riteshatri/resource-management-frontend.git
cd resource-management-frontend
npm install
npm run dev  # Frontend running on port 5000

# 3. Both connected! Frontend talks to Backend
```

---

## 📚 Component Examples

### Login Component
```tsx
<LoginForm
  onLogin={(email, password) => {
    // Send to backend /api/auth/login
  }}
/>
```

### Resources List
```tsx
const ResourcesList = () => {
  const { data: resources } = useQuery({
    queryKey: ['resources'],
    queryFn: () => api.get('/resources')
  })
  
  return resources.map(r => <ResourceCard resource={r} />)
}
```

### Theme Toggle
```tsx
<ThemeToggle
  value={theme}
  onChange={(newTheme) => {
    // Save to backend /api/theme
  }}
/>
```

---

## 🎯 Best Practices Used

✅ **Type Safety** - Full TypeScript coverage  
✅ **Component Composition** - Reusable components  
✅ **Responsive Design** - Mobile-first approach  
✅ **Data Fetching** - TanStack Query for caching  
✅ **Error Handling** - Proper error boundaries  
✅ **Performance** - Code splitting & lazy loading  
✅ **Accessibility** - ARIA labels & semantic HTML  
✅ **Testing** - Component test examples included  

---

## 🐛 Troubleshooting

### **Port 5000 already in use**
```bash
lsof -i :5000      # Find process
kill -9 <PID>      # Kill it
npm run dev        # Try again
```

### **API connection fails**
```bash
# Make sure backend is running
curl http://localhost:8000/docs

# Check CORS is enabled in backend
# Update .env.local with correct API_BASE_URL
```

### **Dependencies missing**
```bash
npm install
npm run dev
```

---

## 📖 Documentation

- 📘 **[Main Project](https://github.com/Riteshatri/resource-management-project)** - Project overview
- 📕 **[RM-Backend](https://github.com/Riteshatri/resource-management-backend)** - Backend setup
- 📙 **[RM-Database](https://github.com/Riteshatri/resource-management-database)** - Database setup

---

## 🌟 Project Structure Links

```
Resource Management Project Structure:

📘 RM-Frontend (You are here!)
   └─ React 18 + Vite UI

🔗 + 

🔧 RM-Backend
   └─ FastAPI Python API

🔗 +

💾 RM-Database
   └─ SQL Schema & Azure Setup

= 

🎯 Complete Resource Management Dashboard
```

---

<div align="center">

### 👉 **[← Back to Main Project](https://github.com/Riteshatri/resource-management-project)**

### 👉 **[Next: Setup Backend →](https://github.com/Riteshatri/resource-management-backend)**

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
- 📗 **[resource-management-frontend](https://github.com/Riteshatri/resource-management-frontend)** - React 18 + Vite ⭐ **(you are here)**
- 📕 **[resource-management-backend](https://github.com/Riteshatri/resource-management-backend)** - FastAPI + Python ⭐
- 📙 **[resource-management-database](https://github.com/Riteshatri/resource-management-database)** - SQL + Azure ⭐

**⭐ Star this repo and others if you found them helpful!**

</div>

---

**v1.0.0** • Production Ready • MIT License

</div>
