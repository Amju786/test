# Fix: Server File Not Found in Electron Build

## Problem

After building the Electron app, you get the error:
```
Failed to start application server: Server file not found
```

## Solution Applied

I've updated the Electron main process to:

1. **Better Path Resolution**: Tries multiple possible paths where the standalone server might be located
2. **Debug Logging**: Shows exactly where it's looking and what it finds
3. **Alternative Path Detection**: Automatically finds the server even if electron-builder places it differently
4. **Updated electron-builder Config**: Added `extraResources` to ensure standalone folder is included

## What Changed

### 1. Enhanced Path Detection (`electron/main.js`)

The app now checks multiple locations:
- `process.resourcesPath/.next/standalone`
- `process.resourcesPath/app/.next/standalone`
- `app.getAppPath()/.next/standalone`
- And more fallback paths

### 2. Updated Build Configuration (`package.json`)

Added `extraResources` to ensure the standalone folder is properly included in the build.

## How to Rebuild

1. **Clean previous build**:
   ```bash
   rm -rf dist .next
   ```

2. **Rebuild**:
   ```bash
   yarn electron:build
   ```

3. **Test the built app**:
   - The app will now show detailed logs about where it's looking for the server
   - If it still fails, check the console output for the exact paths it tried

## Debugging

If the issue persists, the app will now log:
- All paths it tried
- Directory contents of `process.resourcesPath`
- Exact error messages

Check the console/terminal output when running the built app to see these debug messages.

## Verification

After rebuilding, the app should:
1. Find the server file automatically
2. Start the Next.js server
3. Load the application window

If you still see errors, the debug logs will show exactly where the server file should be, helping you identify if it's a build configuration issue.

