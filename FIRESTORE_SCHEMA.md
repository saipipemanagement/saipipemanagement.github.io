# Firestore Schema - Sai Eternal Foundation Pipe Data Management System

## Updated Schema (2026-04-10)

This document describes the Firestore database schema with the latest changes for the 2-step engineer login system.

---

## Collections

### 1. `engineers`

Stores engineer profiles and their login credentials.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Full name of the engineer |
| `empId` | string | Yes | Unique employee ID |
| `loginPassword` | string | Yes | Numeric password for second-step login (e.g., "123456") |
| `phone` | string | No | Phone number |
| `location` | string | No | Work location |
| `status` | string | Yes | "active" or "inactive" |
| `notes` | string | No | Additional notes |
| `createdAt` | timestamp | Auto | Creation timestamp |
| `updatedAt` | timestamp | Auto | Last update timestamp |

**Unique Constraint:** `empId` must be unique across all engineers.

**Password Validation:** `loginPassword` must contain only numeric characters (0-9).

---

### 2. `inventory`

Stores pipe stock information.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `pipeType` | string | Yes | Type of pipe (e.g., "SEF SSS JV", "SUEZ") |
| `diameter` | string | Yes | Pipe diameter in mm (e.g., "110", "160") |
| `material` | string | No | Pipe material (e.g., "UPVC", "GI") |
| `quantity` | number | Yes | Total quantity received |
| `notes` | string | No | Additional notes |
| `createdAt` | timestamp | Auto | Creation timestamp |
| `updatedAt` | timestamp | Auto | Last update timestamp |

---

### 3. `assignments`

Tracks pipe assignments to engineers.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `engineerId` | string | Yes | Reference to engineer document ID |
| `pipeId` | string | Yes | Reference to inventory document ID |
| `quantity` | number | Yes | Quantity assigned |
| `assignedUsed` | number | Yes | Quantity already used in DPRs (default: 0) |
| `notes` | string | No | Additional notes |
| `dateAssigned` | string | Yes | Assignment date (YYYY-MM-DD) |
| `createdAt` | timestamp | Auto | Creation timestamp |
| `updatedAt` | timestamp | Auto | Last update timestamp |

---

### 4. `dprs` (Daily Progress Reports)

Stores daily work progress submissions.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `engineerId` | string | Yes | Reference to engineer document ID |
| `assignmentId` | string | Yes | Reference to assignment document ID |
| `date` | string | Yes | DPR date (YYYY-MM-DD) |
| `pipeType` | string | Yes | Pipe type (auto-populated from assignment) |
| `diameter` | string | Yes | Pipe diameter (auto-populated from assignment) |
| `pipesUsed` | number | Yes | Number of pipes used |
| `pipesLeft` | number | Yes | Remaining pipes after this DPR |
| `meterLaid` | number | Yes | Meters of pipe laid |
| `totalJoints` | number | No | Number of joints made |
| `startJunction` | string | No | Starting junction point |
| `endJunction` | string | No | Ending junction point |
| `notes` | string | No | Additional notes |
| `createdAt` | timestamp | Auto | Creation timestamp |
| `updatedAt` | timestamp | Auto | Last update timestamp |

---

### 5. `targets`

Stores monthly performance targets per engineer.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `engineerId` | string | Yes | Reference to engineer document ID |
| `month` | string | Yes | Target month (YYYY-MM) |
| `targetMeters` | number | Yes | Target meters to lay |
| `targetPipes` | number | Yes | Target pipes to use |
| `createdAt` | timestamp | Auto | Creation timestamp |
| `updatedAt` | timestamp | Auto | Last update timestamp |

---

### 6. `notifications` (Optional)

Stores system notifications. This collection is optional - if it doesn't exist, notifications are generated dynamically from app data.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `title` | string | Yes | Notification title |
| `message` | string | Yes | Notification message |
| `type` | string | Yes | "info", "success", "warning", or "critical" |
| `read` | boolean | Yes | Whether notification has been read |
| `createdAt` | timestamp | Auto | Creation timestamp |

---

## Firestore Indexes

### Required Indexes

```json
{
  "indexes": [
    {
      "collectionGroup": "engineers",
      "queryScope": "COLLECTION",
      "fields": [
        { "fieldPath": "status", "order": "ASCENDING" },
        { "fieldPath": "name", "order": "ASCENDING" }
      ]
    },
    {
      "collectionGroup": "inventory",
      "queryScope": "COLLECTION",
      "fields": [
        { "fieldPath": "pipeType", "order": "ASCENDING" }
      ]
    },
    {
      "collectionGroup": "assignments",
      "queryScope": "COLLECTION",
      "fields": [
        { "fieldPath": "engineerId", "order": "ASCENDING" },
        { "fieldPath": "dateAssigned", "order": "DESCENDING" }
      ]
    },
    {
      "collectionGroup": "dprs",
      "queryScope": "COLLECTION",
      "fields": [
        { "fieldPath": "engineerId", "order": "ASCENDING" },
        { "fieldPath": "date", "order": "DESCENDING" }
      ]
    },
    {
      "collectionGroup": "dprs",
      "queryScope": "COLLECTION",
      "fields": [
        { "fieldPath": "date", "order": "DESCENDING" }
      ]
    },
    {
      "collectionGroup": "targets",
      "queryScope": "COLLECTION",
      "fields": [
        { "fieldPath": "engineerId", "order": "ASCENDING" },
        { "fieldPath": "month", "order": "DESCENDING" }
      ]
    },
    {
      "collectionGroup": "notifications",
      "queryScope": "COLLECTION",
      "fields": [
        { "fieldPath": "createdAt", "order": "DESCENDING" }
      ]
    }
  ],
  "fieldOverrides": []
}
```

---

## Firestore Security Rules

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    
    // Helper functions
    function isAuthenticated() {
      return request.auth != null;
    }
    
    function isAdmin() {
      return isAuthenticated() && 
        request.auth.token.email == 'saiadmin@saipipemanagement.app';
    }
    
    function isEngineer() {
      return isAuthenticated() && 
        request.auth.token.email == 'saiuser@saipipemanagement.app';
    }
    
    function isViewer() {
      return isAuthenticated() && 
        request.auth.token.email == 'viewer@saipipemanagement.app';
    }
    
    // Engineers collection
    match /engineers/{engineerId} {
      allow read: if isAuthenticated();
      allow create, update, delete: if isAdmin();
    }
    
    // Inventory collection
    match /inventory/{inventoryId} {
      allow read: if isAuthenticated();
      allow create, update, delete: if isAdmin();
    }
    
    // Assignments collection
    match /assignments/{assignmentId} {
      allow read: if isAuthenticated();
      allow create, update, delete: if isAdmin();
    }
    
    // DPRs collection
    match /dprs/{dprId} {
      allow read: if isAuthenticated();
      allow create: if isAdmin() || isEngineer();
      allow update, delete: if isAdmin();
    }
    
    // Targets collection
    match /targets/{targetId} {
      allow read: if isAuthenticated();
      allow create, update, delete: if isAdmin();
    }
    
    // Notifications collection (optional)
    match /notifications/{notificationId} {
      allow read: if isAuthenticated();
      allow create, update, delete: if isAdmin();
    }
  }
}
```

---

## Engineer Login Flow

### 2-Step Verification Process

1. **Step 1: Common Login**
   - Engineer enters `saiuser / user@saiEF`
   - Firebase Auth validates credentials
   - App loads engineer list from Firestore

2. **Step 2: Profile Selection**
   - Engineer sees list of all active engineers
   - Can search by name or employee ID
   - Selects their profile

3. **Step 3: Password Verification**
   - Engineer enters their personal numeric password
   - Password is validated against `loginPassword` field
   - Wrong password shows error and allows retry

4. **Step 4: Access Granted**
   - Engineer context is saved in session
   - App shows personalized dashboard
   - Engineer can only see their own data

---

## Data Migration Notes

### For Existing Engineers

If you have existing engineers without `loginPassword`, you need to:

1. Update each engineer document to add a `loginPassword` field
2. The password must be numeric only (e.g., "123456")
3. Communicate the password to each engineer securely

### Example Migration Script

```javascript
// Run this in Firebase Console or Cloud Function
const engineers = await db.collection('engineers').get();
const batch = db.batch();

engineers.forEach(doc => {
  const data = doc.data();
  if (!data.loginPassword) {
    // Generate a random 6-digit numeric password
    const randomPassword = Math.floor(100000 + Math.random() * 900000).toString();
    batch.update(doc.ref, { loginPassword: randomPassword });
    console.log(`Updated ${data.name}: ${randomPassword}`);
  }
});

await batch.commit();
```

---

## Summary of Changes

### New Fields
- `engineers.loginPassword` - Numeric password for 2-step verification

### New Features
- Engineer search in profile selection screen
- Password modal for second-step verification
- Numeric password validation
- Duplicate empId prevention
- Fallback notification generation from app data

### Fixed Issues
- Notification infinite loading
- Engineer add Firebase errors
- Report filter functionality
- Mobile responsiveness for all modals
- DPR mobile UI glitches
