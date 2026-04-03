<div align="center">
  <h1>Sai Eternal Foundation - Pipe Data Management System</h1>
  <p>A production-ready, mobile-first Firebase web application for managing pipe inventory, engineer assignments, daily progress reports (DPR), and performance targets.</p>

  <p>
    <img src="https://img.shields.io/badge/version-2.1.0-blue.svg?cacheSeconds=2592000" alt="Version" />
    <img src="https://img.shields.io/badge/HTML5-E34F26?style=flat&logo=html5&logoColor=white" alt="HTML5" />
    <img src="https://img.shields.io/badge/CSS3-1572B6?style=flat&logo=css3&logoColor=white" alt="CSS3" />
    <img src="https://img.shields.io/badge/JavaScript-F7DF1E?style=flat&logo=javascript&logoColor=black" alt="JavaScript" />
    <img src="https://img.shields.io/badge/Firebase-FFCA28?style=flat&logo=firebase&logoColor=black" alt="Firebase" />
    <img src="https://img.shields.io/badge/License-MIT-green.svg" alt="License: MIT" />
  </p>
</div>

---

## 🌟 Key Features

### Core Modules
- 📊 **Dashboard** - Role-based overview with real-time stats and interactive charts.
- 📦 **Inventory** - Comprehensive pipe stock management with automated low stock alerts.
- 👷 **Engineers** - Secure engineer profile management with individual, secure login generation.
- 📋 **Assignments** - Track pipe assignments to field engineers with dynamic usage tracking.
- 📝 **DPR (Daily Progress Report)** - Streamlined field activity logging and pipe usage records.
- 🎯 **Targets** - Set and monitor monthly performance targets per engineer.
- 📈 **Reports** - Detailed analytics and one-click CSV exportable reports.
- 🔔 **Notifications** - Real-time alerts for low inventory and recent field activity.

### 📱 Mobile-First Design
- Fully responsive layout adapting perfectly to all screen sizes.
- Touch-friendly interface with optimized tap targets.
- Persistent bottom navigation bar for mobile users.
- Card-based data views on small screens replacing traditional tables.
- Optimized for Chrome (Android) and Safari (iOS).

---

## 🔐 Authentication & Roles

The system employs a secure, role-based access control (RBAC) mechanism. As of v2.1.0, engineers are provisioned with individual, secure login credentials.

### Default Credentials

| Role | Username / ID | Password | Access Level |
| :--- | :--- | :--- | :--- |
| **👑 Admin** | `saiadmin` | `SaiAdmin@12` | Full Control. Create/Manage users, view all data, set targets. |
| **👁️ Viewer** | `viewer` | `Sai@viewer` | Read-only. Access to view dashboards, reports, and overall data. |
| **👷 Master Engineer** | `saiuser` | `user@saiEF` | *(Legacy Shared Account)* Shared login that prompts for profile selection. |

> **Note:** For new engineers, the Admin will create an individual profile and assign a specific Email and Password during creation. Engineers can then log in securely using those exact credentials, bypassing the legacy profile selection screen.

---

## 🛠️ Technical Stack

- **Frontend:** Vanilla JavaScript (ES6+), CSS3 (Custom Properties), HTML5
- **Backend & Database:** Google Firebase (Authentication, Firestore Database)
- **Data Visualization:** Chart.js 4.4.0
- **Iconography:** Font Awesome 6.5.0
- **Typography:** Inter (Sans), JetBrains Mono (Monospace)

---

## 🏗️ System Architecture

### Modular Structure

```text
App/
├── Auth/           # Authentication & session management (Individual & Legacy mapping)
├── UI/             # DOM manipulation, theme handling, and modals
├── DataService/    # Optimized Firestore CRUD operations & caching
├── Dashboard/      # Top-level metrics and recent activity feeds
├── Inventory/      # Stock tracking and variance calculation
├── Engineers/      # Profile management and secure Auth provisioning
├── Assignments/    # Logistics and pipe dispatching
├── DPR/            # Field reporting and data validation
├── Targets/        # Performance goals and metric tracking
├── Reports/        # Data aggregation and CSV generation
└── Notifications/  # In-app alert system for stock/activity
```

### State Management & Performance
- **Centralized State Object:** Single source of truth for UI rendering.
- **Firebase Free Tier Optimization:** Aggressive in-memory caching minimizes read operations. Data is loaded selectively (e.g., last 100 records for the dashboard) with full loads only triggered for reporting/exports.
- **Optimistic UI Updates:** Instant UI feedback before database confirmation for a snappy user experience.
- **Debounced Inputs:** Search bars are debounced (150ms) to prevent rapid-fire database queries.

---

## 📁 Repository Structure

```text
sai-pipe-management/
├── index.html              # Main application entry point (Single Page App)
├── firestore.rules         # Firestore security & validation rules
├── firestore.indexes.json  # Pre-configured composite index definitions
├── FIRESTORE_SCHEMA.md     # Detailed database schema and relationships
├── SETUP_GUIDE.md          # Step-by-step installation instructions
└── README.md               # Project documentation (You are here)
```

---

## 🚀 Quick Start Guide

### 1. Firebase CLI Setup
```bash
# Install Firebase CLI globally
npm install -g firebase-tools

# Login to your Google Account
firebase login

# Initialize project within the directory
firebase init
```

### 2. Deploy Security & Indexes
```bash
# Deploy Firestore security rules
firebase deploy --only firestore:rules

# Deploy composite indexes (Required for complex sorting/filtering)
firebase deploy --only firestore:indexes
```

### 3. Deploy Application
```bash
# Deploy the web app to Firebase Hosting
firebase deploy --only hosting
```

*For comprehensive setup details, including connecting your specific Firebase project, see `SETUP_GUIDE.md`.*

---

## 🛡️ Security Architecture

### Authentication Upgrades (v2.1.0)
- **Individual Engineer Accounts:** Admins now provision unique Email/Password combinations for every new engineer directly from the UI.
- **Secondary Auth Instance:** The system uses a secondary Firebase app instance during user creation, ensuring the Admin remains securely logged in while provisioning new accounts.
- **Smart Login Fallback:** The login screen intuitively handles both new secure email logins and legacy username mappings.

### Firestore Rules
- Strict read/write permissions mapped to the authenticated user's assigned role.
- Server-side data validation ensures data integrity regardless of the client.
- Complete prevention of client-side security bypasses.

---

## 🌐 Browser Compatibility

| Browser | Minimum Version | Note |
| :--- | :--- | :--- |
| **Google Chrome** | 90+ | Recommended |
| **Mozilla Firefox** | 88+ | Fully Supported |
| **Apple Safari** | 14+ | Fully Supported |
| **Microsoft Edge** | 90+ | Fully Supported |
| **Chrome (Android)** | 90+ | Mobile Optimized |
| **Safari (iOS)** | 14+ | Mobile Optimized |

---

## 📊 Firebase Limits & Usage (Free Tier)

This application is highly optimized to comfortably run on Firebase's "Spark" (Free) tier for small to medium operations.

| Resource | Free Tier Limit | Typical App Usage (Estimated) |
| :--- | :--- | :--- |
| **Document Reads** | 50,000 / day | ~2,000 / day |
| **Document Writes** | 20,000 / day | ~500 / day |
| **Document Deletes** | 20,000 / day | ~100 / day |
| **Storage** | 1 GB | ~10 MB (Data only, no media) |
| **Hosting** | 10 GB / month | ~1 GB / month |

---

## 🎨 Customization

### Theme Colors
Easily rebrand the application by modifying the CSS variables located at the top of the `<style>` block in `index.html`:

```css
:root {
  --primary-500: #14b8a6;  /* Primary Brand Color (Teal) */
  --accent-500: #f59e0b;   /* Accent Color (Amber) */
  --danger-500: #ef4444;   /* Error/Critical Color (Red) */
  /* ... */
}
```

### Logo Replacement
To replace the default Font Awesome water icon (`fa-water`), search for the `.login-logo` or `.sidebar-logo` classes in `index.html` and replace the inner `<i>` tag with an `<img>` tag pointing to your logo.

---

## 📝 Changelog

### v2.1.0 (Current)
- 🔒 **Security Major Upgrade:** Implemented individual, secure credential generation for Engineers.
- ⚙️ **Dual Auth System:** Added secondary Firebase initialization to allow Admins to create users seamlessly.
- 🚀 **Smart Routing:** Individual engineers bypass the legacy "Select Profile" screen completely.

### v2.0.0
- 📱 Complete mobile-first UI/UX redesign.
- 🧩 Transitioned to a modular JavaScript architecture within a Single Page App.
- ⚡ Aggressive Firebase read optimizations (caching & limits).
- 🌓 Implemented Dark/Light mode toggle.
- 📱 Introduced persistent mobile bottom navigation and card-based data views.

### v1.0.0
- Initial deployment.
- Basic CRUD for inventory, engineers, and DPRs.
- Hardcoded, shared role-based access.

---

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

---

## 💬 Support

For issues, questions, or deployment help:
1. Consult the Troubleshooting section in the setup guides.
2. Review the official [Firebase Documentation](https://firebase.google.com/docs).
3. Inspect your browser's Developer Console (F12) for specific runtime errors.
