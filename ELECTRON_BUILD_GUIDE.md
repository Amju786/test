# Electron Desktop App Build Guide

## Overview

StockSync is now a fully functional Electron desktop application that can be built for Windows, macOS, and Linux.

## Prerequisites

- Node.js 18+ and Yarn
- Electron and electron-builder installed
- MongoDB (or use in-memory fallback)

## Development

### Run in Development Mode

```bash
# Start Next.js dev server and Electron
yarn electron:dev
```

This will:
1. Start Next.js development server on port 3000
2. Wait for server to be ready
3. Launch Electron window
4. Open DevTools automatically

### Development Features

- Hot reload enabled
- DevTools open by default
- Fast refresh for React components
- Console logging for debugging

## Building for Production

### Build for All Platforms

```bash
# Build Next.js and package Electron for all platforms
yarn electron:build
```

### Build for Specific Platform

```bash
# Windows
yarn electron:build:win

# macOS
yarn electron:build:mac

# Linux
yarn electron:build:linux
```

### Build Process

The build process:
1. **Builds Next.js** - Creates optimized production build with standalone output
2. **Packages Electron** - Creates platform-specific installers
3. **Outputs to `dist/`** - All build artifacts are in the `dist` directory

## Build Output

### Windows
- `StockSync-1.0.0-Setup.exe` - NSIS installer
- Installs to Program Files
- Creates desktop and start menu shortcuts

### macOS
- `StockSync-1.0.0.dmg` - Disk image
- Drag to Applications folder
- Code signed (if configured)

### Linux
- `StockSync-1.0.0.AppImage` - Portable AppImage
- No installation required
- Executable directly

## Configuration

### Electron Main Process

Location: `electron/main.js`

Key features:
- Automatic server management (production)
- Window management
- Menu bar
- Security settings
- External link handling

### Electron Builder Config

Location: `package.json` → `build` section

Customize:
- App ID
- Product name
- Icons
- Installer options
- Code signing

## Icons

Place app icons in `build/` directory:

- `icon.ico` - Windows icon (256x256)
- `icon.icns` - macOS icon (512x512 with multiple sizes)
- `icon.png` - Linux icon (512x512)

### Generate Icons

You can use tools like:
- [Electron Icon Maker](https://www.electron.build/icons)
- [Icon Generator](https://icon.kitchen/)

## Code Signing

### Windows

For code signing, add to `package.json`:

```json
"win": {
  "certificateFile": "path/to/certificate.pfx",
  "certificatePassword": "password"
}
```

### macOS

For code signing, add to `package.json`:

```json
"mac": {
  "identity": "Developer ID Application: Your Name"
}
```

## Environment Variables

Create `.env.local` for environment-specific config:

```env
MONGO_URL=mongodb://localhost:27017
DB_NAME=stocksync
NEXT_PUBLIC_BASE_URL=http://localhost:3000
```

This file will be included in the build.

## Troubleshooting

### Build Fails

1. **Check Next.js build**:
   ```bash
   yarn build
   ```

2. **Verify standalone output**:
   Check that `.next/standalone` exists

3. **Check electron-builder**:
   ```bash
   yarn electron-builder --help
   ```

### App Won't Start

1. **Check server logs**:
   - Look for errors in console
   - Verify MongoDB connection

2. **Check port availability**:
   - Ensure port 3000 is not in use
   - Change PORT in `electron/main.js` if needed

### Icons Not Showing

1. **Verify icon files exist** in `build/` directory
2. **Check icon formats**:
   - Windows: `.ico`
   - macOS: `.icns`
   - Linux: `.png`

### Production Server Issues

The standalone Next.js server should:
- Be in `.next/standalone/`
- Have `server.js` file
- Include all dependencies

If missing, check `next.config.js` has `output: 'standalone'`.

## File Structure

```
stocksync/
├── electron/
│   ├── main.js          # Main Electron process
│   ├── preload.js       # Preload script
│   └── assets/          # App icons
├── build/               # Build resources
│   ├── icon.ico
│   ├── icon.icns
│   ├── icon.png
│   └── entitlements.mac.plist
├── dist/                # Build output (generated)
├── .next/               # Next.js build (generated)
│   └── standalone/      # Standalone server
└── package.json         # Build configuration
```

## Distribution

### Windows

1. Build installer: `yarn electron:build:win`
2. Test installer on clean Windows machine
3. Distribute `StockSync-X.X.X-Setup.exe`

### macOS

1. Build DMG: `yarn electron:build:mac`
2. Test on clean macOS machine
3. Code sign if distributing outside App Store
4. Distribute `StockSync-X.X.X.dmg`

### Linux

1. Build AppImage: `yarn electron:build:linux`
2. Test on target Linux distribution
3. Distribute `StockSync-X.X.X.AppImage`

## Updates

For auto-updates, consider:
- [electron-updater](https://www.electron.build/auto-update)
- GitHub Releases
- Custom update server

## Security

The Electron app includes:
- ✅ Context isolation enabled
- ✅ Node integration disabled
- ✅ Web security enabled
- ✅ External link protection
- ✅ Secure preload script

## Performance

Optimizations:
- Standalone Next.js server
- Optimized webpack config
- Reduced file watching in dev
- Memory limits configured

## Support

For issues:
1. Check Electron logs in console
2. Check Next.js server logs
3. Verify MongoDB connection
4. Review build configuration

---

**Note**: First build may take longer as electron-builder downloads platform-specific tools.

