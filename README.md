# Sai Eternal Foundation - Pipe Data Management System

A production-ready, mobile-first Firebase web application for managing pipe inventory, engineer assignments, daily progress reports (DPR), and performance targets.

---

## Features

### Core Modules
- **Dashboard** - Role-based overview with stats and charts
- **Inventory** - Pipe stock management with low stock alerts
- **Engineers** - Engineer profiles and performance tracking
- **Assignments** - Pipe assignment to engineers with usage tracking
- **DPR** - Daily Progress Report submission and viewing
- **Targets** - Monthly performance targets with progress tracking
- **Reports** - Analytics and exportable reports
- **Notifications** - Low stock and activity alerts

### User Roles
| Role | Permissions |
|------|-------------|
| **Admin** | Full access - create, read, update, delete all data |
| **Engineer** | View inventory, submit DPRs, view own assignments/targets |
| **Viewer** | Read-only access to all data |

### Mobile-First Design
- Responsive layout for all screen sizes
- Touch-friendly interface
- Bottom navigation for mobile
- Card-based table views on small screens
- Optimized for Android and iOS browsers

---

## Technical Stack

- **Frontend:** Vanilla JavaScript, CSS3, HTML5
- **Backend:** Firebase (Auth, Firestore)
- **Charts:** Chart.js 4.4.0
- **Icons:** Font Awesome 6.5.0
- **Fonts:** Inter, JetBrains Mono

---

## Architecture

### Modular Structure
```
App/
├── Auth/           # Authentication module
├── UI/             # UI utilities and components
├── DataService/    # Firebase data operations
├── Dashboard/      # Dashboard page logic
├── Inventory/      # Inventory management
├── Engineers/      # Engineer management
├── Assignments/    # Assignment management
├── DPR/            # DPR submission and viewing
├── Targets/        # Target management
├── Reports/        # Reporting and analytics
└── Notifications/  # Notification system
```

### State Management
- Centralized state object
- In-memory caching for small collections
- Optimistic updates for better UX

### Firebase Free Tier Optimization
- Limited queries with pagination
- Debounced search/filter inputs
- Local state updates after writes
- No unnecessary real-time listeners

---

## File Structure

```
sai-pipe-management/
├── index.html              # Main application file
├── firestore.rules         # Firestore security rules
├── firestore.indexes.json  # Firestore index configuration
├── FIRESTORE_SCHEMA.md     # Database schema documentation
├── SETUP_GUIDE.md          # Setup instructions
└── README.md               # This file
```

---

## Quick Start

### 1. Firebase Setup
```bash
# Install Firebase CLI
npm install -g firebase-tools

# Login
firebase login

# Initialize
firebase init
```

### 2. Deploy Security Rules
```bash
firebase deploy --only firestore:rules
```

### 3. Deploy Indexes
```bash
firebase deploy --only firestore:indexes
```

### 4. Deploy Hosting
```bash
firebase deploy --only hosting
```

See [SETUP_GUIDE.md](SETUP_GUIDE.md) for detailed instructions.

---

## Firestore Collections

| Collection | Purpose | Fields |
|------------|---------|--------|
| `users` | Auth role mapping | email, role, displayName |
| `engineers` | Engineer profiles | name, empId, phone, location, status |
| `inventory` | Pipe stock | pipeType, diameter, material, quantity |
| `assignments` | Pipe assignments | engineerId, pipeId, quantity, assignedUsed |
| `dprs` | Daily reports | engineerId, assignmentId, date, pipesUsed, meterLaid |
| `targets` | Monthly targets | engineerId, month, targetMeters, targetPipes |
| `settings` | App configuration | key-value pairs |

See [FIRESTORE_SCHEMA.md](FIRESTORE_SCHEMA.md) for complete schema.

---

## Security

### Authentication
- Firebase Auth with email/password
- Role-based access control
- Session persistence

### Firestore Rules
- Read/write permissions based on role
- Data validation on create/update
- No client-side security bypass

### Best Practices
- Strong passwords required
- 2FA recommended for admin accounts
- Regular security rule reviews

---

## Performance

### Optimizations
- Debounced search (150ms)
- Limited queries (100 records default)
- In-memory caching
- Chart instance reuse
- Mobile-optimized CSS

### Bundle Size
- No build step required
- CDN-loaded dependencies
- ~50KB gzipped application code

---

## Browser Support

| Browser | Version |
|---------|---------|
| Chrome | 90+ |
| Firefox | 88+ |
| Safari | 14+ |
| Edge | 90+ |
| Chrome Android | 90+ |
| Safari iOS | 14+ |

---

## Firebase Free Tier Limits

| Resource | Limit | Typical Usage |
|----------|-------|---------------|
| Reads | 50,000/day | ~2,000/day |
| Writes | 20,000/day | ~500/day |
| Deletes | 20,000/day | ~100/day |
| Storage | 1 GB | ~100 MB |
| Hosting | 10 GB/month | ~1 GB/month |

**Note:** Moderate usage (20 users, 200 DPRs/month) stays well within free tier.

---

## Customization

### Theme Colors
Edit CSS variables in `index.html`:
```css
:root {
  --primary-500: #14b8a6;  /* Main brand color */
  --accent-500: #f59e0b;   /* Accent color */
  --danger-500: #ef4444;   /* Error color */
  /* ... */
}
```

### Logo/Branding
Replace the water icon with your logo:
```html
<div class="login-logo">
  <img src="your-logo.png" alt="Logo">
</div>
```

### Custom Fields
Add custom fields to DPR form in the `DPR` module.

---

## Troubleshooting

### Common Issues

**Permission Denied**
- Check Firestore rules are deployed
- Verify user exists in `users` collection

**Index Errors**
- Deploy indexes: `firebase deploy --only firestore:indexes`
- Wait for indexes to build (can take several minutes)

**Charts Not Loading**
- Check internet connection (Chart.js loaded from CDN)
- Verify no ad blockers are blocking CDN

**Mobile Layout Issues**
- Clear browser cache
- Test on actual device (not just emulator)

---

## Development

### Local Testing
```bash
# Serve locally
firebase serve

# Or use any static server
npx serve .
```

### Making Changes
1. Edit `index.html`
2. Test locally
3. Deploy: `firebase deploy`

### Adding New Features
1. Add module to App controller
2. Create page section in HTML
3. Add navigation item
4. Update Firestore rules if needed

---

## Deployment Checklist

- [ ] Firebase project created
- [ ] Authentication enabled (Email/Password)
- [ ] Firestore database created
- [ ] Security rules deployed
- [ ] Indexes deployed
- [ ] Users created in Auth
- [ ] User roles mapped in Firestore
- [ ] Sample data added
- [ ] App deployed to hosting
- [ ] Login tested with all roles
- [ ] Mobile layout verified

---

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

---

## License

MIT License - See LICENSE file for details

---

## Credits

- **Framework:** Firebase
- **Icons:** Font Awesome
- **Charts:** Chart.js
- **Fonts:** Google Fonts (Inter, JetBrains Mono)

---

## Support

For issues and questions:
1. Check the troubleshooting section
2. Review Firebase documentation
3. Check browser console for errors

---

## Changelog

### v2.0.0 (2025)
- Complete mobile-first redesign
- Modular JavaScript architecture
- Firebase free tier optimizations
- Improved accessibility
- Dark/light theme toggle
- Bottom navigation for mobile
- Card-based mobile table views

### v1.0.0 (2024)
- Initial release
- Basic CRUD operations
- Role-based access
- Dashboard with charts
