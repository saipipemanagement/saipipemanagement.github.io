# Setup Guide

## Sai Eternal Foundation - Pipe Data Management System

---

## Prerequisites

- Firebase account (free tier sufficient)
- Modern web browser
- Text editor (for configuration)

---

## Step 1: Create Firebase Project

1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Click "Add project"
3. Enter project name: `saipipemanagement` (or your preferred name)
4. Disable Google Analytics (optional)
5. Click "Create project"

---

## Step 2: Enable Authentication

1. In Firebase Console, go to **Build > Authentication**
2. Click "Get started"
3. Enable **Email/Password** provider
4. Click "Save"

### Create User Accounts

Create these users in Firebase Authentication:

| Username | Email | Password | Role |
|----------|-------|----------|------|
| saiadmin | saiadmin@saipipemanagement.app | YourSecurePassword123 | admin |
| saiuser | saiuser@saipipemanagement.app | YourSecurePassword123 | engineer |
| viewer | viewer@saipipemanagement.app | YourSecurePassword123 | viewer |

**To create users:**
1. Go to **Authentication > Users**
2. Click "Add user"
3. Enter email and password
4. Click "Add user"

---

## Step 3: Setup Firestore Database

1. Go to **Build > Firestore Database**
2. Click "Create database"
3. Choose **Start in production mode**
4. Select region closest to your users (e.g., `asia-south1` for India)
5. Click "Enable"

### Create Users Collection

Create a `users` collection with documents mapping Auth UIDs to roles:

**Collection:** `users`

**Document 1:**
- Document ID: (Firebase Auth UID of saiadmin)
- Fields:
  - `email`: "saiadmin@saipipemanagement.app"
  - `role`: "admin"
  - `displayName`: "Administrator"

**Document 2:**
- Document ID: (Firebase Auth UID of saiuser)
- Fields:
  - `email`: "saiuser@saipipemanagement.app"
  - `role`: "engineer"
  - `displayName`: "Engineer User"

**Document 3:**
- Document ID: (Firebase Auth UID of viewer)
- Fields:
  - `email`: "viewer@saipipemanagement.app"
  - `role`: "viewer"
  - `displayName`: "Viewer User"

---

## Step 4: Deploy Security Rules

1. Go to **Firestore Database > Rules**
2. Copy contents from `firestore.rules` file
3. Paste into the rules editor
4. Click "Publish"

---

## Step 5: Create Firestore Indexes

Create these composite indexes for optimal query performance:

### Index 1: DPRs by Engineer and Date
- **Collection:** `dprs`
- **Fields:**
  1. `engineerId` (Ascending)
  2. `date` (Descending)

### Index 2: DPRs by Date
- **Collection:** `dprs`
- **Fields:**
  1. `date` (Descending)

### Index 3: Assignments by Engineer
- **Collection:** `assignments`
- **Fields:**
  1. `engineerId` (Ascending)
  2. `dateAssigned` (Descending)

### Index 4: Targets by Engineer and Month
- **Collection:** `targets`
- **Fields:**
  1. `engineerId` (Ascending)
  2. `month` (Descending)

**To create indexes:**
1. Go to **Firestore Database > Indexes**
2. Click "Add index"
3. Select collection and fields
4. Click "Create index"

---

## Step 6: Update Firebase Config

1. In Firebase Console, click the gear icon ⚙️ > **Project settings**
2. Scroll to "Your apps" section
3. Click the web app icon `</>`
4. Copy the Firebase config object
5. Open `index.html` in a text editor
6. Find the `firebaseConfig` object and replace with your config:

```javascript
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_PROJECT_ID.firebasestorage.app",
  messagingSenderId: "YOUR_SENDER_ID",
  appId: "YOUR_APP_ID",
  measurementId: "YOUR_MEASUREMENT_ID"
};
```

---

## Step 7: Deploy the Application

### Option A: Firebase Hosting (Recommended)

1. Install Firebase CLI:
```bash
npm install -g firebase-tools
```

2. Login to Firebase:
```bash
firebase login
```

3. Initialize project:
```bash
firebase init hosting
```
- Select your project
- Set public directory: `.` (current directory)
- Configure as single-page app: `No`
- Overwrite index.html: `No`

4. Deploy:
```bash
firebase deploy
```

5. Your app will be live at: `https://YOUR_PROJECT_ID.web.app`

### Option B: Any Static Hosting

Upload the `index.html` file to any static hosting service:
- Netlify
- Vercel
- GitHub Pages
- AWS S3
- Any web server

---

## Step 8: Initial Data Setup

### Add Sample Engineers

In Firestore Console, add documents to `engineers` collection:

**Sample Engineer 1:**
```json
{
  "name": "Rajesh Kumar",
  "empId": "EMP001",
  "phone": "+91 98765 43210",
  "location": "Zone A - North",
  "status": "active",
  "notes": "Senior engineer",
  "createdAt": "2025-01-01T00:00:00.000Z",
  "updatedAt": "2025-01-01T00:00:00.000Z"
}
```

**Sample Engineer 2:**
```json
{
  "name": "Suresh Patel",
  "empId": "EMP002",
  "phone": "+91 98765 43211",
  "location": "Zone B - South",
  "status": "active",
  "notes": "Field supervisor",
  "createdAt": "2025-01-01T00:00:00.000Z",
  "updatedAt": "2025-01-01T00:00:00.000Z"
}
```

### Add Sample Inventory

Add documents to `inventory` collection:

**Sample Inventory 1:**
```json
{
  "pipeType": "PVC",
  "diameter": "110",
  "material": "UPVC",
  "quantity": 500,
  "dateAdded": "2025-01-15",
  "notes": "Grade A quality",
  "createdAt": "2025-01-15T00:00:00.000Z",
  "updatedAt": "2025-01-15T00:00:00.000Z"
}
```

**Sample Inventory 2:**
```json
{
  "pipeType": "DI",
  "diameter": "150",
  "material": "Ductile Iron",
  "quantity": 200,
  "dateAdded": "2025-01-15",
  "notes": "Heavy duty",
  "createdAt": "2025-01-15T00:00:00.000Z",
  "updatedAt": "2025-01-15T00:00:00.000Z"
}
```

---

## Step 9: Test the Application

1. Open your deployed app URL
2. Login with credentials:
   - **Admin:** saiadmin / YourSecurePassword123
   - **Engineer:** saiuser / YourSecurePassword123
   - **Viewer:** viewer / YourSecurePassword123

3. Verify:
   - Dashboard loads with stats
   - Navigation works
   - Can add/view data based on role

---

## Troubleshooting

### "Permission denied" error
- Check Firestore security rules are deployed
- Verify user document exists in `users` collection
- Ensure Auth UID matches document ID

### "Index not found" error
- Create required indexes in Firestore Console
- Check Indexes tab for any failed indexes

### Charts not displaying
- Verify Chart.js CDN is accessible
- Check browser console for errors

### Mobile layout issues
- Ensure viewport meta tag is present
- Test on actual mobile device

---

## Security Best Practices

1. **Change default passwords** immediately after setup
2. **Use strong passwords** (min 12 characters, mixed case, numbers, symbols)
3. **Enable 2FA** for Firebase account
4. **Regular backups** of Firestore data
5. **Monitor usage** in Firebase Console
6. **Review security rules** periodically

---

## Free Tier Limits

| Resource | Free Tier | Typical Usage |
|----------|-----------|---------------|
| Reads | 50,000/day | ~2,000/day |
| Writes | 20,000/day | ~500/day |
| Deletes | 20,000/day | ~100/day |
| Storage | 1 GB | ~100 MB |
| Hosting | 10 GB/month | ~1 GB/month |

**Note:** With moderate usage (20 users, 200 DPRs/month), you should stay well within free tier limits.

---

## Next Steps

1. **Train users** on the system
2. **Set up regular backups**
3. **Monitor usage** in Firebase Console
4. **Add more engineers** and inventory
5. **Create targets** for each month
6. **Start submitting DPRs**

---

## Support

For issues or questions:
1. Check browser console for errors
2. Verify Firebase configuration
3. Review Firestore security rules
4. Check Firebase status page
