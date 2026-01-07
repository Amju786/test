# MongoDB Setup Guide

## Quick Setup Options

### Option 1: MongoDB Atlas (Recommended - Cloud, Free Tier Available)

1. **Sign up for MongoDB Atlas** (Free):
   - Go to https://www.mongodb.com/cloud/atlas/register
   - Create a free account (M0 Free Tier)

2. **Create a Cluster**:
   - Click "Build a Database"
   - Choose FREE (M0) tier
   - Select a cloud provider and region
   - Click "Create"

3. **Set up Database Access**:
   - Go to "Database Access" in the left menu
   - Click "Add New Database User"
   - Choose "Password" authentication
   - Create username and password (save these!)
   - Set privileges to "Atlas admin" or "Read and write to any database"
   - Click "Add User"

4. **Set up Network Access**:
   - Go to "Network Access" in the left menu
   - Click "Add IP Address"
   - Click "Allow Access from Anywhere" (for development) or add your IP
   - Click "Confirm"

5. **Get Connection String**:
   - Go to "Database" → Click "Connect"
   - Choose "Connect your application"
   - Copy the connection string (looks like: `mongodb+srv://username:password@cluster.mongodb.net/`)
   - Replace `<password>` with your actual password
   - Replace `<dbname>` with `stocksync` or leave it to use default

6. **Update `.env.local`**:
   ```env
   MONGO_URL=mongodb+srv://yourusername:yourpassword@cluster.mongodb.net/stocksync?retryWrites=true&w=majority
   DB_NAME=stocksync
   NEXT_PUBLIC_BASE_URL=http://localhost:3000
   ```

### Option 2: Local MongoDB Installation

#### Windows:
1. **Download MongoDB Community Server**:
   - Go to https://www.mongodb.com/try/download/community
   - Select Windows, MSI package
   - Download and run the installer

2. **Install MongoDB**:
   - Run the installer
   - Choose "Complete" installation
   - Install as a Windows Service (recommended)
   - Install MongoDB Compass (GUI tool - optional but helpful)

3. **Start MongoDB**:
   - MongoDB should start automatically as a Windows service
   - Or manually start: Open Services → Find "MongoDB" → Start

4. **Verify Installation**:
   ```powershell
   # Open PowerShell and run:
   mongod --version
   ```

5. **Your `.env.local` should already be correct**:
   ```env
   MONGO_URL=mongodb://localhost:27017
   DB_NAME=stocksync
   NEXT_PUBLIC_BASE_URL=http://localhost:3000
   ```

#### macOS:
```bash
# Using Homebrew
brew tap mongodb/brew
brew install mongodb-community
brew services start mongodb-community
```

#### Linux:
```bash
# Ubuntu/Debian
sudo apt-get install -y mongodb
sudo systemctl start mongodb
sudo systemctl enable mongodb
```

## After MongoDB is Running

1. **Initialize Default Accounts** (Optional):
   ```bash
   python setup_default_accounts.py
   ```

2. **Restart Your Next.js Server**:
   ```bash
   npm run dev
   ```

3. **Test Login**:
   - Use demo accounts:
     - Admin: `admin@stocksync.com` / `admin123`
     - User: `john@example.com` / `user123`
   - Or register a new account

## Troubleshooting

### Connection Refused Error
- **MongoDB not running**: Start MongoDB service
- **Wrong port**: Check if MongoDB is on port 27017
- **Firewall blocking**: Allow MongoDB through firewall

### Authentication Failed (Atlas)
- Check username/password in connection string
- Ensure IP is whitelisted in Network Access
- Verify database user has correct permissions

### Connection Timeout
- Check internet connection (for Atlas)
- Verify MongoDB service is running (for local)
- Check firewall settings

