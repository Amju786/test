# Quick Start: Electron Desktop App

## 🚀 Quick Commands

### Development

```bash
# Run Electron app in development mode
yarn electron:dev
```

This starts:
- Next.js dev server (port 3000)
- Electron window with DevTools

### Building

```bash
# Build for your current platform
yarn electron:build

# Build for specific platform
yarn electron:build:win    # Windows
yarn electron:build:mac    # macOS
yarn electron:build:linux  # Linux
```

## 📋 Before Building

1. **Create App Icons** (required):
   - Place `icon.ico` in `build/` (Windows)
   - Place `icon.icns` in `build/` (macOS)
   - Place `icon.png` in `build/` (Linux)
   
   See `build/README.md` for details.

2. **Configure Environment**:
   - Create `.env.local` with MongoDB URL
   - Or use in-memory database (no setup needed)

## 🎯 What Gets Built

- **Windows**: `StockSync-1.0.0-Setup.exe` installer
- **macOS**: `StockSync-1.0.0.dmg` disk image
- **Linux**: `StockSync-1.0.0.AppImage` portable app

All outputs go to `dist/` directory.

## 🔧 Troubleshooting

### "Server failed to start"
- Check MongoDB is running (or use in-memory mode)
- Verify port 3000 is available
- Check `.env.local` configuration

### "Icons not found"
- Create icon files in `build/` directory
- See `build/README.md` for icon requirements

### Build takes too long
- First build downloads platform tools (one-time)
- Subsequent builds are faster

## 📖 Full Documentation

See `ELECTRON_BUILD_GUIDE.md` for complete details.

