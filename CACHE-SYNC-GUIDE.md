# Geocode Cache Sync Guide

## Overview

Your festival map app has a **geocode cache** that stores all location lookups to avoid re-querying the API. This cache is automatically synced to your browser's **localStorage** and accumulates over time (currently 275+ entries).

To keep this data persistent across deployments and share it with future versions of the app, you can export it to `corrections.json` in your GitHub repository.

## How It Works

1. **App Runtime**: As users browse the map, all geocoding results are cached in memory and localStorage
2. **Auto Sync**: Every 30 seconds (and on page unload), the cache is synced to localStorage
3. **Manual Export**: When you want to save the cache to GitHub, you export it and commit to the repo
4. **Next Deployment**: The app loads the cached coordinates from GitHub, skipping re-geocoding

## How to Export Cache

### Option 1: Using the Export Helper (Easiest)

1. Open `get-cache.html` in your browser
2. Click **"📥 Export Cache"** button
3. Click **"💾 Download as corrections.json"** button
4. Replace `corrections.json` in your repo with the downloaded file
5. Commit and push to GitHub

```bash
# After downloading the file
git add corrections.json
git commit -m "Update geocode cache with 275+ entries"
git push
```

### Option 2: Using Browser Console

1. Open your festival map app
2. Open DevTools (F12 → Console tab)
3. Run this command:
   ```javascript
   exportCacheAsJSON()
   ```
4. The file will automatically download
5. Follow steps 4-5 above to commit to GitHub

### Option 3: Manual Copy-Paste

1. Open DevTools (F12 → Console tab)
2. Run:
   ```javascript
   console.log(JSON.stringify({
     overrides: coordinateOverrides,
     geocodeCache: geocodeCache,
     lastUpdated: new Date().toISOString(),
     version: "1.0",
     description: "Festival location corrections, geocoding cache, and coordinate overrides"
   }, null, 2))
   ```
3. Copy the output
4. Paste into `corrections.json` in your editor
5. Commit and push

## Cache Contents

The `geocodeCache` object stores:
- **Key**: Address/search query (e.g., "Rehov HaNasi 15 Pardes Hana")
- **Value**: `{ lat, lng, timestamp }`

Example:
```json
{
  "geocodeCache": {
    "Rehov HaNasi 15 Pardes Hana": {
      "lat": 32.475,
      "lng": 34.975,
      "timestamp": "2025-11-06T12:00:00Z"
    }
  }
}
```

## When to Sync

- **After testing**: Once you've verified all locations geocode correctly
- **Periodically**: Weekly or monthly to capture new events
- **Before major deployment**: To ensure coordinates are cached for fast load times

## What Gets Synced

The exported `corrections.json` includes:
1. **overrides**: Manually corrected coordinates (admin use)
2. **geocodeCache**: All successfully geocoded locations (275+ entries)
3. **lastUpdated**: Timestamp of last sync
4. **version**: Format version for compatibility

## FAQ

**Q: Will this slow down the app?**
A: No. Cached coordinates load instantly from GitHub on app startup, saving time.

**Q: Can I edit corrections.json manually?**
A: Yes! You can add or fix coordinates in the `overrides` section and the app will use them.

**Q: What if I make a mistake?**
A: Just revert the commit on GitHub or re-export from a working browser instance.

**Q: Does this work offline?**
A: Yes. The app uses cached data from localStorage even if GitHub is unavailable.

## Current Status

- **Browser Cache**: 275+ entries in localStorage ✅
- **GitHub File**: Empty (ready to be populated) ⏳
- **Next Step**: Export cache and commit to GitHub

Run `exportCacheAsJSON()` when ready to sync!
