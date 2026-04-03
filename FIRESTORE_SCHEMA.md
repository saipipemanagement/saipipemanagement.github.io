# Firestore Schema Documentation

## Sai Eternal Foundation - Pipe Data Management System

---

## Collections Overview

| Collection | Purpose | Estimated Documents |
|------------|---------|---------------------|
| `users` | Authentication & role mapping | 10-50 |
| `engineers` | Engineer profiles | 10-100 |
| `inventory` | Pipe stock records | 20-200 |
| `assignments` | Pipe assignments to engineers | 50-500 |
| `dprs` | Daily Progress Reports | 100-5000 |
| `targets` | Monthly targets per engineer | 50-500 |
| `settings` | App configuration | 1-10 |

---

## Collection Details

### 1. `users`
Maps Firebase Auth users to roles.

```javascript
{
  uid: string,           // Firebase Auth UID (document ID)
  email: string,         // User email
  role: string,          // "admin" | "engineer" | "viewer"
  displayName: string,   // Display name
  createdAt: timestamp,
  updatedAt: timestamp
}
```

**Example:**
```javascript
{
  uid: "abc123def456",
  email: "saiadmin@saipipemanagement.app",
  role: "admin",
  displayName: "Administrator",
  createdAt: Timestamp,
  updatedAt: Timestamp
}
```

---

### 2. `engineers`
Engineer profile records.

```javascript
{
  id: string,            // Auto-generated document ID
  name: string,          // Full name (required)
  empId: string,         // Employee ID (required, unique)
  phone: string,         // Phone number (optional)
  location: string,      // Work location (optional)
  status: string,        // "active" | "inactive"
  notes: string,         // Additional notes (optional)
  createdAt: timestamp,
  updatedAt: timestamp
}
```

**Example:**
```javascript
{
  id: "eng_001",
  name: "Rajesh Kumar",
  empId: "EMP001",
  phone: "+91 98765 43210",
  location: "Zone A - North",
  status: "active",
  notes: "Senior engineer with 5+ years experience",
  createdAt: Timestamp,
  updatedAt: Timestamp
}
```

**Indexes:**
- `name` (ascending) - for alphabetical listing
- `status` (ascending) + `name` (ascending) - for filtering active engineers

---

### 3. `inventory`
Pipe stock inventory records.

```javascript
{
  id: string,            // Auto-generated document ID
  pipeType: string,      // e.g., "PVC", "DI", "HDPE" (required)
  diameter: string,      // e.g., "110", "160" in mm (required)
  material: string,      // e.g., "UPVC" (optional)
  quantity: number,      // Total quantity in stock (required)
  dateAdded: string,     // ISO date string YYYY-MM-DD
  notes: string,         // Additional notes (optional)
  createdAt: timestamp,
  updatedAt: timestamp
}
```

**Example:**
```javascript
{
  id: "inv_001",
  pipeType: "PVC",
  diameter: "110",
  material: "UPVC",
  quantity: 500,
  dateAdded: "2025-01-15",
  notes: "Grade A quality pipes",
  createdAt: Timestamp,
  updatedAt: Timestamp
}
```

**Indexes:**
- `pipeType` (ascending) - for grouping by type
- `dateAdded` (descending) - for recent items

---

### 4. `assignments`
Pipe assignments to engineers.

```javascript
{
  id: string,            // Auto-generated document ID
  engineerId: string,    // Reference to engineers collection (required)
  pipeId: string,        // Reference to inventory collection (required)
  quantity: number,      // Quantity assigned (required)
  assignedUsed: number,  // Quantity used from this assignment
  dateAssigned: string,  // ISO date string YYYY-MM-DD
  notes: string,         // Additional notes (optional)
  createdAt: timestamp,
  updatedAt: timestamp
}
```

**Example:**
```javascript
{
  id: "asgn_001",
  engineerId: "eng_001",
  pipeId: "inv_001",
  quantity: 50,
  assignedUsed: 25,
  dateAssigned: "2025-01-20",
  notes: "For Project Alpha",
  createdAt: Timestamp,
  updatedAt: Timestamp
}
```

**Indexes:**
- `engineerId` (ascending) - for engineer's assignments
- `pipeId` (ascending) - for pipe usage tracking
- `dateAssigned` (descending) - for recent assignments

---

### 5. `dprs` (Daily Progress Reports)
Field activity reports.

```javascript
{
  id: string,            // Auto-generated document ID
  engineerId: string,    // Reference to engineers collection (required)
  assignmentId: string,  // Reference to assignments collection (required)
  date: string,          // ISO date string YYYY-MM-DD (required)
  pipeType: string,      // Denormalized from inventory
  diameter: string,      // Denormalized from inventory
  pipesUsed: number,     // Number of pipes used (required)
  pipesLeft: number,     // Remaining pipes in assignment
  meterLaid: number,     // Meters of pipe laid (required)
  totalJoints: number,   // Number of joints made
  startJunction: string, // Starting point
  endJunction: string,   // Ending point
  fittings: array,       // [{ name: string, qty: number }]
  customFields: array,   // [{ key: string, value: string }]
  notes: string,         // Additional notes
  createdAt: timestamp,
  updatedAt: timestamp
}
```

**Example:**
```javascript
{
  id: "dpr_001",
  engineerId: "eng_001",
  assignmentId: "asgn_001",
  date: "2025-01-25",
  pipeType: "PVC",
  diameter: "110",
  pipesUsed: 10,
  pipesLeft: 15,
  meterLaid: 55.5,
  totalJoints: 8,
  startJunction: "Junction A",
  endJunction: "Junction B",
  fittings: [
    { name: "Elbow 90°", qty: 4 },
    { name: "Tee Joint", qty: 2 }
  ],
  customFields: [
    { key: "Weather", value: "Sunny" },
    { key: "Crew Size", value: "4" }
  ],
  notes: "Completed section 3 ahead of schedule",
  createdAt: Timestamp,
  updatedAt: Timestamp
}
```

**Indexes:**
- `engineerId` (ascending) + `date` (descending) - for engineer's DPR history
- `date` (descending) - for recent DPRs
- `assignmentId` (ascending) - for assignment tracking

---

### 6. `targets`
Monthly performance targets.

```javascript
{
  id: string,            // Auto-generated document ID
  engineerId: string,    // Reference to engineers collection (required)
  month: string,         // YYYY-MM format (required)
  targetMeters: number,  // Target meters to lay (required)
  targetPipes: number,   // Target pipes to use (required)
  createdAt: timestamp,
  updatedAt: timestamp
}
```

**Example:**
```javascript
{
  id: "tgt_001",
  engineerId: "eng_001",
  month: "2025-01",
  targetMeters: 500,
  targetPipes: 100,
  createdAt: Timestamp,
  updatedAt: Timestamp
}
```

**Indexes:**
- `engineerId` (ascending) + `month` (descending) - for engineer's targets
- `month` (descending) - for monthly reports

---

### 7. `settings`
Application settings.

```javascript
{
  id: string,            // Setting key (document ID)
  value: any,            // Setting value
  description: string,   // Description of the setting
  updatedAt: timestamp
}
```

**Example:**
```javascript
{
  id: "lowStockThreshold",
  value: 0.25,
  description: "Percentage threshold for low stock warning",
  updatedAt: Timestamp
}
```

---

## Data Relationships

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│    users    │────▶│  engineers  │◀────│ assignments │
└─────────────┘     └─────────────┘     └──────┬──────┘
                                                │
                       ┌─────────────┐          │
                       │  inventory  │◀─────────┘
                       └─────────────┘
                              │
                              ▼
                       ┌─────────────┐
                       │    dprs     │
                       └─────────────┘
                              │
                              ▼
                       ┌─────────────┐
                       │   targets   │
                       └─────────────┘
```

---

## Query Patterns

### 1. Dashboard Stats
```javascript
// Get all engineers (small collection)
query(collection(db, 'engineers'), orderBy('name'))

// Get all inventory (small collection)
query(collection(db, 'inventory'), orderBy('pipeType'))

// Get recent DPRs (limited)
query(collection(db, 'dprs'), orderBy('date', 'desc'), limit(100))
```

### 2. Engineer View
```javascript
// Get engineer's assignments
query(collection(db, 'assignments'), where('engineerId', '==', engId))

// Get engineer's DPRs
query(collection(db, 'dprs'), where('engineerId', '==', engId), orderBy('date', 'desc'))

// Get engineer's targets
query(collection(db, 'targets'), where('engineerId', '==', engId), orderBy('month', 'desc'))
```

### 3. Monthly Reports
```javascript
// Get DPRs for a month
query(collection(db, 'dprs'), where('date', '>=', '2025-01-01'), where('date', '<=', '2025-01-31'))

// Get targets for a month
query(collection(db, 'targets'), where('month', '==', '2025-01'))
```

---

## Data Validation Rules

### Required Fields by Collection

| Collection | Required Fields |
|------------|-----------------|
| `users` | `email`, `role` |
| `engineers` | `name`, `empId` |
| `inventory` | `pipeType`, `diameter`, `quantity` |
| `assignments` | `engineerId`, `pipeId`, `quantity` |
| `dprs` | `engineerId`, `assignmentId`, `date`, `pipesUsed`, `meterLaid` |
| `targets` | `engineerId`, `month`, `targetMeters`, `targetPipes` |

### Field Constraints

- `quantity`, `pipesUsed`, `targetPipes`: Integer ≥ 0
- `meterLaid`, `targetMeters`: Number ≥ 0
- `date`: ISO format YYYY-MM-DD
- `month`: YYYY-MM format
- `status`: "active" or "inactive"
- `role`: "admin", "engineer", or "viewer"

---

## Free Tier Optimization

### Read Optimization
1. **Limited queries**: Use `limit()` for recent data
2. **Pagination**: Use `startAfter()` for large collections
3. **Caching**: Cache small collections (engineers, inventory) in memory
4. **Denormalization**: Store frequently accessed fields in DPRs

### Write Optimization
1. **Batch writes**: Use `writeBatch()` for multiple operations
2. **Server timestamps**: Use `serverTimestamp()` for consistency
3. **Minimal updates**: Only update changed fields

### Estimated Usage
- **Light usage** (5 users, 50 DPRs/month): ~500 reads/day
- **Moderate usage** (20 users, 200 DPRs/month): ~2,000 reads/day
- **Free tier limit**: 50,000 reads/day

---

## Backup Strategy

1. **Daily exports**: Use Firebase Extensions for daily Firestore exports
2. **Critical data**: Export users, engineers, and inventory weekly
3. **Retention**: Keep 30 days of DPR data in hot storage, archive older data
