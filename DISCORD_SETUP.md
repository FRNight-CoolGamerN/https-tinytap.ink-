# 🔐 Discord OAuth Setup Guide

## ⚠️ CRITICAL SECURITY WARNING

**Your Discord credentials posted in the chat are COMPROMISED!** You must regenerate them immediately before running this application.

---

## 📋 Prerequisites

1. Discord Account
2. Discord Server (already have: ID `1419161334872019007`)
3. Node.js and npm installed

---

## 🤖 Step 1: Regenerate Discord Bot Token

### **IMPORTANT: Your old bot token is compromised. Follow these steps:**

1. Go to [Discord Developer Portal](https://discord.com/developers/applications)
2. Click on your application: **tinytapbot**
3. Go to **Bot** section in left sidebar
4. Click **"Reset Token"** button
5. Copy the new token immediately (you can only see it once!)
6. Save it to `.env` file as `DISCORD_BOT_TOKEN`

---

## 🔑 Step 2: Regenerate OAuth2 Client Secret

### **IMPORTANT: Your old client secret is compromised:**

1. Stay in [Discord Developer Portal](https://discord.com/developers/applications)
2. Click on your application: **tinytapbot**
3. Go to **OAuth2 → General** section
4. Click **"Reset Secret"** button
5. Copy the new secret immediately
6. Save it to `.env` file as `DISCORD_CLIENT_SECRET`

---

## 🔧 Step 3: Configure OAuth2 Settings

1. In [Discord Developer Portal](https://discord.com/developers/applications)
2. Go to **OAuth2 → General**
3. Under **Redirects**, add:
   ```
   http://localhost:3001/auth/discord/callback
   ```
4. Click **"Save Changes"**

---

## 🔐 Step 4: Bot Permissions & Intents

### Required Intents (in Bot section):
- ✅ **MESSAGE CONTENT INTENT** (to send DMs)
- ✅ **SERVER MEMBERS INTENT** (to check membership)

### Required Bot Permissions:
- ✅ **Send Messages**
- ✅ **Send DMs**

---

## 📝 Step 5: Create `.env` File

Create `d:\EZTK\server\.env` with the following (use your NEW credentials):

```env
# MongoDB
MONGO_URI=mongodb://localhost:27017/tinytap-filehost

# File Server
PORT=3000

# Auth Server
AUTH_PORT=3001
AUTH_MONGO_URI=mongodb://localhost:27017/tinytap-auth

# Discord OAuth (USE NEW CREDENTIALS!)
DISCORD_CLIENT_ID=1320627493894742106
DISCORD_CLIENT_SECRET=<YOUR_NEW_CLIENT_SECRET_HERE>
DISCORD_BOT_TOKEN=<YOUR_NEW_BOT_TOKEN_HERE>
DISCORD_SERVER_ID=1419161334872019007
DISCORD_REDIRECT_URI=http://localhost:3001/auth/discord/callback
```

---

## 🚀 Step 6: Install Dependencies & Run

### Install packages:
```powershell
cd d:\EZTK\server
npm install
```

### Run both servers:
```powershell
npm run dev:all
```

This will start:
- File server on `http://localhost:3000`
- Auth server on `http://localhost:3001`

---

## 🧪 Step 7: Test the Flow

1. Open `d:\EZTK\tiktok-downloader.html` in browser
2. Click **"Sign in with Discord"**
3. Authorize the app on Discord
4. Check your Discord DMs for the 10-digit code
5. Enter the code on the website
6. Access granted! ✅

---

## 🔒 How It Works

### Authentication Flow:

1. **User clicks "Login with Discord"**
   - Frontend redirects to Discord OAuth page
   - User authorizes the app

2. **Discord Callback**
   - Auth server receives OAuth code
   - Exchanges code for user info
   - Checks if user is in Discord server (ID: `1419161334872019007`)

3. **Verification Code**
   - Generates random 10-digit code (e.g., `1234567890`)
   - Stores in MongoDB with 5-minute expiry
   - Bot sends code via Discord DM

4. **User Enters Code**
   - Frontend posts code to `/auth/verify`
   - Server validates code
   - Tracks failed attempts (10 max)

5. **Rate Limiting**
   - After 10 failed attempts: Account blocked for 10 minutes
   - Block timer shown on frontend
   - Auto-unblocks after timeout

6. **Success**
   - User granted access
   - Session stored in localStorage
   - Premium features unlocked

---

## 📊 MongoDB Collections

### `tinytap-auth` database:
- **verifications** collection:
  ```javascript
  {
    userId: "123456789012345678",
    code: "1234567890",
    attempts: 3,
    blockedUntil: null,
    createdAt: ISODate("2024-01-01T12:00:00Z") // Auto-deletes after 5 minutes
  }
  ```

---

## 🛠️ Troubleshooting

### "Not in server" error:
- Make sure the bot is in your server
- Invite bot with this link structure:
  ```
  https://discord.com/api/oauth2/authorize?client_id=1320627493894742106&permissions=2147485696&scope=bot
  ```

### DMs not sending:
- Check **MESSAGE CONTENT INTENT** is enabled
- Verify bot token is correct
- User must have DMs enabled from server members

### "Authentication error" on frontend:
- Make sure auth server is running on port 3001
- Check `.env` file has correct values
- Verify MongoDB is running

### Code expired:
- Codes expire after 5 minutes (MongoDB TTL)
- Click "Resend Code" to get a new one

---

## 🔐 Security Features

✅ **10-digit verification codes** (not guessable)  
✅ **5-minute code expiration** (MongoDB TTL)  
✅ **Rate limiting** (10 attempts = 10-minute block)  
✅ **Server membership required** (prevents unauthorized access)  
✅ **Discord bot DM delivery** (secure channel)  
✅ **Real-time block timer** (user feedback)

---

## 📞 Support

Discord Server: https://discord.gg/VJSbxYNfwS

---

## ✅ Checklist Before Running

- [ ] Regenerated Discord Bot Token
- [ ] Regenerated Discord OAuth Client Secret
- [ ] Created `.env` file with new credentials
- [ ] Added OAuth redirect URL in Discord Developer Portal
- [ ] Enabled MESSAGE CONTENT INTENT
- [ ] Enabled SERVER MEMBERS INTENT
- [ ] Bot is invited to Discord server
- [ ] MongoDB is running
- [ ] Ran `npm install` in server folder
- [ ] Ready to run `npm run dev:all`

---

**🚨 REMEMBER: NEVER share your bot token or client secret publicly again!**
