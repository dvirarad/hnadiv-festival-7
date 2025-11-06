# Phase 2 Setup Guide - Backend & Admin Panel

Welcome to Phase 2! This guide walks you through setting up and running the backend server and admin dashboard.

## What's New in Phase 2

✅ **Admin Dashboard** - Web UI to manage and verify coordinates
✅ **Backend API** - REST endpoints for corrections management
✅ **Authentication** - Password-protected admin access
✅ **File-based Database** - Persistent storage with JSON
✅ **Coordinate Verification** - Mark coordinates as accurate
✅ **Statistics Dashboard** - Track verified vs pending coordinates

## Directory Structure

Your project now has this structure (both sides of the git repo):

```
hnadiv-festival-7/
├── web-app/
│   ├── index.html              (user-facing map)
│   ├── admin.html              ← NEW: admin dashboard
│   ├── corrections.json        (coordinate cache)
│   ├── tests.html
│   ├── get-cache.html
│   └── ... (other frontend files)
│
└── backend/                    ← NEW: backend server folder
    ├── server.js               (Express server - 500+ lines)
    ├── package.json            (dependencies)
    ├── .env.example            (configuration)
    ├── .gitignore              (exclude secrets)
    ├── Procfile                (Heroku config)
    ├── README.md               (API docs)
    └── data/
        └── corrections.json    (persistent storage)
```

## Installation

### Step 1: Navigate to Backend Folder

```bash
cd /Users/arad/dev/hnadiv-festival-7/backend
```

### Step 2: Install Dependencies

```bash
npm install
```

This installs:
- `express` - Web server framework
- `cors` - Handle cross-origin requests
- `dotenv` - Load environment variables
- `body-parser` - Parse JSON requests

### Step 3: Create `.env` File

```bash
cp .env.example .env
```

Edit `.env` and set your admin password:

```env
PORT=3000
ADMIN_PASSWORD=your_secure_password_here
API_TOKEN=
```

**Security note:** Choose a strong password!

## Running the Server

### Development Mode

```bash
npm start
```

Output should look like:

```
╔════════════════════════════════════════╗
║   Festival Map Backend Server          ║
║   Version 1.0                          ║
╚════════════════════════════════════════╝

🚀 Server running at http://localhost:3000

📍 Available endpoints:
  ...

🔐 Admin Panel: http://localhost:3000/admin
💼 Default password: your_secure_password_here
```

## Accessing the Applications

Once the server is running:

### User Map
- **URL**: http://localhost:3000
- **Features**: Browse events, search, filter, add favorites
- **No login required**

### Admin Panel
- **URL**: http://localhost:3000/admin
- **Features**: Manage coordinates, verify accuracy, export data
- **Login**: Use password from your `.env` file

### API Documentation
- **Base URL**: http://localhost:3000/api/
- **Auth Required**: Most endpoints (except /health)
- **See**: `backend/README.md` for full API docs

## Admin Panel Workflow

### First Time Setup

1. Open http://localhost:3000/admin
2. Login with your password
3. You'll see a dashboard with:
   - Statistics: Total coordinates, verified count, pending count
   - Filter controls: Search by location, filter by status
   - Coordinate table: All 275+ cached locations

### Verifying Coordinates

1. **Review a location**:
   - Look at latitude/longitude
   - If incorrect, click "✏️" to edit

2. **Edit coordinate**:
   - Update lat/lng with correct values
   - Add notes (optional) - e.g., "Verified with Google Maps"
   - Check "Mark as Verified"
   - Click "Save"

3. **Export data**:
   - Click "📥 Export Data" button
   - File downloads as `corrections.json`
   - This file is ready to commit to GitHub

### Batch Operations

**Search for location**:
- Type in search box at top
- Table filters in real-time

**Filter by status**:
- Select "Verified Only" or "Pending Only"
- See only coordinates matching your selection

**View errors**:
- Check geocoding errors in statistics
- Review which locations failed to geocode

## API Usage Examples

### Login

```bash
curl -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"password":"your_password"}'

# Response:
# {
#   "success": true,
#   "sessionId": "abc123...",
#   "expiresAt": 1234567890
# }
```

Save the `sessionId` for next requests.

### Get All Coordinates

```bash
curl http://localhost:3000/api/corrections \
  -H "x-session-id: abc123..."

# Returns: all 275+ cached and overridden coordinates
```

### Update a Coordinate

```bash
curl -X PUT http://localhost:3000/api/corrections/Rehov%20HaNasi%2015 \
  -H "Content-Type: application/json" \
  -H "x-session-id: abc123..." \
  -d '{
    "lat": 32.475,
    "lng": 34.975,
    "verified": true,
    "notes": "Verified with map"
  }'
```

### Get Statistics

```bash
curl http://localhost:3000/api/stats \
  -H "x-session-id: abc123..."

# Returns: { total, verified, pending, errors, lastUpdated }
```

## Data Files

### corrections.json Structure

```json
{
  "overrides": {
    "Location Name": {
      "lat": 32.475,
      "lng": 34.975,
      "timestamp": "2025-11-06T12:00:00Z",
      "notes": "Manually verified"
    }
  },
  "geocodeCache": {
    "Location Name": {
      "lat": 32.475,
      "lng": 34.975,
      "timestamp": "2025-11-06T12:00:00Z"
    }
  },
  "verified": {
    "Location Name": true
  },
  "errors": [
    {
      "query": "Unknown Place",
      "error": "No results found",
      "timestamp": "2025-11-06T12:00:00Z"
    }
  ],
  "lastUpdated": "2025-11-06T12:00:00Z",
  "version": "1.0"
}
```

## Deployment

### Local Testing

```bash
npm start
# Open http://localhost:3000
# Test the app and admin panel
```

### Deploy to Heroku

1. Install Heroku CLI from https://devcenter.heroku.com/articles/heroku-cli

2. Create and deploy:
```bash
cd backend
heroku create your-app-name
heroku config:set ADMIN_PASSWORD=your_secure_password
git push heroku main
```

3. Access your deployed app:
- **App**: https://your-app-name.herokuapp.com
- **Admin**: https://your-app-name.herokuapp.com/admin

### Deploy to Railway

1. Sign up at https://railway.app
2. Connect your GitHub repo
3. Railway auto-detects Node.js and deploys
4. Set environment variables in Railway dashboard

## Troubleshooting

### "Cannot find module 'express'"

```bash
npm install
```

### Port 3000 already in use

```bash
PORT=3001 npm start
```

### Admin panel shows blank

1. Check server is running: `npm start`
2. Check browser console: F12 → Console tab
3. Verify password is correct
4. Try clearing session: `localStorage.clear()`

### Coordinates not saving

1. Verify backend is running
2. Check backend console for errors
3. Ensure `backend/data/` directory exists
4. Check file permissions on `corrections.json`

### API returns 401 Unauthorized

1. You need a valid session ID
2. Login first to get `sessionId`
3. Include header: `x-session-id: <sessionId>`

## Next Steps

### Immediate (Manual)

1. ✅ Set up backend locally
2. ✅ Access admin panel
3. ✅ Verify some coordinates
4. ✅ Export updated data
5. ✅ Commit to GitHub

### Phase 3 (Future)

- [ ] GitHub API integration (auto-push)
- [ ] Database backend (optional)
- [ ] Multi-user authentication
- [ ] Audit history / verification log
- [ ] CSV import/export
- [ ] Automated notifications

## Documentation

- **Backend API**: `backend/README.md`
- **Frontend Testing**: `TESTING.md`
- **Cache Management**: `CACHE-SYNC-GUIDE.md`
- **Main Project**: `../../README.md`

## Getting Help

If you run into issues:

1. Check the troubleshooting section above
2. Review error messages in console (F12)
3. Check backend logs: `npm start` should show errors
4. Review API documentation in `backend/README.md`

## Key Files

| File | Purpose |
|------|---------|
| `server.js` | Main Express server (all API endpoints) |
| `admin.html` | Admin dashboard UI |
| `package.json` | Dependencies and scripts |
| `.env` | Your configuration (password, port) |
| `data/corrections.json` | Persistent storage |

## Remember

- **Passwords**: Keep `.env` file secure, never commit it
- **Data**: `corrections.json` is your source of truth for coordinates
- **Backups**: Export data regularly before major changes
- **Commits**: Always commit `corrections.json` updates to GitHub

---

**Need help?** Check `backend/README.md` for detailed API documentation.

Happy managing! 🎯
