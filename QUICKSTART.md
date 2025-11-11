# 🚀 Quick Start Guide

## ⚠️ CRITICAL FIRST STEP

**Your Discord credentials are COMPROMISED!**

Before doing ANYTHING else:
1. Go to https://discord.com/developers/applications
2. Select your app: **tinytapbot**
3. Go to **Bot** → Click **"Reset Token"** → Copy new token
4. Go to **OAuth2** → Click **"Reset Secret"** → Copy new secret
5. Save both to `.env` file (see step 2 below)

---

## 📋 5-Minute Setup

### Step 1: Install Dependencies
```powershell
cd d:\EZTK\server
npm install
```

### Step 2: Create `.env` File
Create `d:\EZTK\server\.env`:
```env
# MongoDB
MONGO_URI=mongodb://localhost:27017/tinytap-filehost

# Servers
PORT=3000
AUTH_PORT=3001
AUTH_MONGO_URI=mongodb://localhost:27017/tinytap-auth

# Discord OAuth (USE YOUR NEW CREDENTIALS!)
DISCORD_CLIENT_ID=1320627493894742106
DISCORD_CLIENT_SECRET=<YOUR_NEW_SECRET_HERE>
DISCORD_BOT_TOKEN=<YOUR_NEW_TOKEN_HERE>
DISCORD_SERVER_ID=1419161334872019007
DISCORD_REDIRECT_URI=http://localhost:3001/auth/discord/callback
```

### Step 3: Configure Discord Developer Portal
1. Go to https://discord.com/developers/applications
2. Select **tinytapbot**
3. Go to **OAuth2 → General**
4. Under **Redirects**, add: `http://localhost:3001/auth/discord/callback`
5. Click **Save Changes**
6. Go to **Bot** section
7. Enable **MESSAGE CONTENT INTENT** ✅
8. Enable **SERVER MEMBERS INTENT** ✅
9. Click **Save Changes**

### Step 4: Start MongoDB
```powershell
# If MongoDB is installed as a service:
net start MongoDB

# Or if using manual installation:
mongod --dbpath C:\data\db
```

### Step 5: Run Servers
```powershell
cd d:\EZTK\server
npm run dev:all
```

**You should see:**
```
✅ Connected to MongoDB (Files)
🚀 File Server running on http://localhost:3000

✅ Connected to MongoDB (Auth)
🚀 Discord Auth Server running on http://localhost:3001
📱 Discord OAuth configured
```

### Step 6: Test Login
1. Open `d:\EZTK\tiktok-downloader.html` in browser
2. Click **"Sign in with Discord"**
3. Authorize the app
4. Check your Discord DMs for 10-digit code
5. Enter code on website
6. ✅ **You're in!**

---

## 🎯 What Each Server Does

### File Server (Port 3000)
- Handles file uploads
- Generates short links (tinytap.ink/:code)
- Serves file downloads
- Manages file expiration

### Auth Server (Port 3001)
- Discord OAuth login
- Generates 10-digit codes
- Sends Discord DMs via bot
- Checks server membership
- Rate limiting (10 attempts max)
- Account blocking (10 minutes)

---

## 🔍 Quick Debug

### Check if servers are running:
```powershell
curl http://localhost:3000/health
curl http://localhost:3001/health
```

### Check MongoDB:
```powershell
mongo
> show dbs
> use tinytap-auth
> db.verifications.find()
```

### Check Discord bot is online:
- Open Discord
- Check bot status in server members list
- Should show online (green dot)

---

## 🐛 Common Errors

### "Could not send DM"
**Fix:** Enable DMs from server members in Discord Privacy Settings

### "Not in server"
**Fix:** Join https://discord.gg/VJSbxYNfwS

### "Authentication error. Please check if the auth server is running"
**Fix:** Run `npm run dev:all`

### "MongoDB connection error"
**Fix:** Start MongoDB service

---

## 📚 Full Documentation

- **Setup Guide:** `DISCORD_SETUP.md`
- **Testing Guide:** `TESTING_CHECKLIST.md`
- **Migration Details:** `MIGRATION_SUMMARY.md`

---

## ✅ Success Checklist

After setup, verify:
- [ ] Both servers running (3000 and 3001)
- [ ] MongoDB connected
- [ ] Discord bot online
- [ ] Login redirects to Discord
- [ ] Code received in DMs
- [ ] Code verification works
- [ ] User logged in successfully

---

## 🎉 You're Ready!

Your Discord OAuth system is now operational!

**Next steps:**
1. Test all features (use `TESTING_CHECKLIST.md`)
2. Customize Discord DM messages (in `auth-server.js`)
3. Add production environment variables
4. Deploy to production server

---

## 🔐 Security Reminder

**NEVER share these publicly:**
- Discord Bot Token
- Discord Client Secret
- MongoDB connection strings
- Any API keys

**Keep them in `.env` file (already in .gitignore)**

---

**Happy coding! 🚀**
