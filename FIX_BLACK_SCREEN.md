# Fix: Black Screen in Production Build

## Problem

The installed EXE shows a black screen and doesn't load. This happens because:
1. Window loads before server is ready
2. Server might not be starting
3. No visual feedback during startup

## Solution Applied

### 1. Loading Screen
- Created `electron/loading.html` - Shows while server starts
- Displays progress and status messages
- Automatically redirects when server is ready

### 2. Server Startup Sequence
- Window only loads **after** server confirms it's ready
- Increased timeout to 60 seconds for production
- Better error messages if server fails

### 3. DevTools Enabled (Temporary)
- DevTools now open automatically in production builds
- This helps you see what's happening
- **Remove this before final release**

### 4. Better Error Handling
- Shows error page in window if load fails
- Console logging for debugging
- Error dialogs with details

## How to Rebuild

```bash
# Clean
rm -rf dist .next

# Rebuild
yarn electron:build

# Install and test
```

## What You'll See

1. **Loading Screen** appears first (shows "Starting application...")
2. **Status updates** as server starts
3. **Main app** loads automatically when ready
4. **DevTools** opens (for debugging - remove later)

## Debugging

If you still see a black screen:

1. **Check DevTools Console**:
   - Look for error messages
   - Check if server started
   - See network requests

2. **Check Terminal/Console** (if running from command line):
   - Look for server startup messages
   - Check for file path errors
   - See MongoDB/in-memory DB messages

3. **Common Issues**:
   - Server file not found → Check build includes `.next/standalone`
   - Port already in use → Change PORT in electron/main.js
   - MongoDB connection → Should use in-memory automatically

## Next Steps

1. **Rebuild**: `yarn electron:build`
2. **Install and run**
3. **Check DevTools** for any errors
4. **Report what you see** in console/DevTools

## Remove DevTools Before Release

In `electron/main.js`, find and comment out:
```javascript
// if (isProduction) {
//   mainWindow.webContents.openDevTools();
// }
```

The loading screen and server wait should fix the black screen issue!

