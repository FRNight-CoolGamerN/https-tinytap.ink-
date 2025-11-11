# 📚 TinyTap.ink - Complete Documentation Index

## 🎯 What is TinyTap.ink?

**TinyTap.ink** is a 3-in-1 web toolkit featuring:
- 📱 **TikTok Video Downloader** - Download TikTok videos without watermark
- 🎥 **YouTube Downloader & Search** - Download YouTube videos and search for content
- 📂 **File Hosting with QR Codes** - Upload files, get short links and QR codes

**Authentication:** Secure Discord OAuth 2.0 with 10-digit DM verification codes

---

## 📖 Documentation Quick Links

### 🚀 Getting Started
- **[QUICKSTART.md](QUICKSTART.md)** - 5-minute setup guide (START HERE!)
- **[DISCORD_SETUP.md](DISCORD_SETUP.md)** - Complete Discord OAuth configuration

### 🏗️ Architecture & Design
- **[ARCHITECTURE.md](ARCHITECTURE.md)** - System architecture diagrams and data flow
- **[MIGRATION_SUMMARY.md](MIGRATION_SUMMARY.md)** - What changed from Google to Discord OAuth

### 🧪 Testing & Troubleshooting
- **[TESTING_CHECKLIST.md](TESTING_CHECKLIST.md)** - Complete testing scenarios
- **[TROUBLESHOOTING.md](TROUBLESHOOTING.md)** - Common issues and solutions

### 📁 Server Documentation
- **[server/README.md](server/README.md)** - Backend setup and configuration

---

## 🗂️ File Structure

```
d:\EZTK\
│
├── tiktok-downloader.html          # Frontend (2287 lines)
│   ├── TikTok Downloader Tab
│   ├── YouTube Downloader Tab (with search)
│   ├── File Hosting Tab (with QR codes)
│   └── Discord OAuth Login System
│
├── server\
│   ├── server.js                   # File hosting backend (port 3000)
│   ├── auth-server.js              # Discord OAuth backend (port 3001)
│   ├── package.json                # Dependencies & scripts
│   ├── .env.example                # Environment template
│   ├── .env                        # Your credentials (CREATE THIS!)
│   └── uploads\                    # Uploaded files storage
│
├── QUICKSTART.md                   # 5-minute setup guide
├── DISCORD_SETUP.md                # Discord OAuth setup
├── TESTING_CHECKLIST.md            # Testing guide
├── TROUBLESHOOTING.md              # Debugging guide
├── ARCHITECTURE.md                 # System design
├── MIGRATION_SUMMARY.md            # Change log
└── README.md                       # This file
```

---

## ⚡ Quick Start (3 Steps)

### 1️⃣ Install Dependencies
```powershell
cd d:\EZTK\server
npm install
```

### 2️⃣ Configure Discord & .env
Follow **[DISCORD_SETUP.md](DISCORD_SETUP.md)** to:
- Regenerate Discord bot token (old one compromised!)
- Regenerate OAuth client secret (old one compromised!)
- Create `.env` file with credentials

### 3️⃣ Run Servers
```powershell
npm run dev:all
```

**Then open:** `d:\EZTK\tiktok-downloader.html` in browser

---

## 🔑 Key Features

### Discord OAuth Authentication
- ✅ OAuth 2.0 login via Discord
- ✅ 10-digit verification codes (not standard 6-digit)
- ✅ Codes delivered via Discord bot DMs
- ✅ Server membership requirement
- ✅ Rate limiting: 10 attempts → 10-minute block
- ✅ Code expiration: 5 minutes
- ✅ Real-time block countdown timer

### Content Downloading
- 📱 **TikTok:** Dual API system (tiklydown.me + tikwm.com fallback)
- 🎥 **YouTube:** RapidAPI YouTube MP4 Downloader
- 🔍 **YouTube Search:** YouTube Data API v3 integration

### File Hosting
- 📂 Upload files up to 5GB
- 🔗 Generate short links (tinytap.ink/:code)
- 📱 QR code generation for file links
- ⏰ Auto-expiration after 7 days
- 🗃️ User file management

---

## 🗄️ Technology Stack

| Layer | Technology |
|-------|-----------|
| **Frontend** | HTML5, CSS3, Vanilla JavaScript |
| **Backend** | Node.js, Express.js |
| **Database** | MongoDB (Mongoose) |
| **Authentication** | Discord OAuth 2.0 + Bot API |
| **File Upload** | Multer |
| **Short Links** | nanoid |
| **QR Codes** | QR Server API |
| **Cron Jobs** | node-cron |
| **APIs** | TikTok, YouTube, Discord |

---

## 🔐 Security Features

1. **Discord OAuth 2.0** - Secure token exchange
2. **Server Membership Check** - Must be in Discord server
3. **10-Digit Codes** - Random codes (1000000000-9999999999)
4. **DM Delivery** - Codes sent via secure Discord bot
5. **5-Minute Expiration** - MongoDB TTL index
6. **Rate Limiting** - Max 10 attempts per user
7. **Account Blocking** - 10-minute blocks on failure
8. **Real-time Feedback** - Discord DM notifications

---

## 📊 System Architecture

```
┌─────────────────────────────────────────┐
│        Frontend (HTML/CSS/JS)           │
│  • TikTok/YouTube Downloaders           │
│  • File Hosting with QR Codes           │
│  • Discord OAuth Login UI               │
└─────────────────────────────────────────┘
              ↓ HTTP Requests
┌─────────────────────────────────────────┐
│         Backend Servers                 │
│  ┌────────────┐    ┌────────────┐      │
│  │ File Server│    │Auth Server │      │
│  │  (3000)    │    │  (3001)    │      │
│  └────────────┘    └────────────┘      │
└─────────────────────────────────────────┘
              ↓
┌─────────────────────────────────────────┐
│           MongoDB                       │
│  • tinytap-filehost (files)             │
│  • tinytap-auth (verifications)         │
└─────────────────────────────────────────┘
              ↓
┌─────────────────────────────────────────┐
│         Discord API                     │
│  • OAuth 2.0                            │
│  • Bot API (DMs)                        │
│  • Server Membership                    │
└─────────────────────────────────────────┘
```

**See [ARCHITECTURE.md](ARCHITECTURE.md) for detailed diagrams**

---

## 🎯 Discord OAuth Flow

1. User clicks "Login with Discord"
2. Redirects to Discord OAuth authorization
3. User authorizes app
4. Backend receives OAuth code
5. Exchanges code for access token
6. Gets user info from Discord API
7. Checks if user is in Discord server
8. Generates 10-digit verification code
9. Stores code in MongoDB (5-min expiry)
10. Bot sends code via Discord DM
11. User enters code on website
12. Backend verifies code
13. Tracks attempts (max 10)
14. On success: User logged in ✅
15. On 10 failures: Account blocked for 10 minutes 🚫

**See [ARCHITECTURE.md](ARCHITECTURE.md) for flow diagram**

---

## 🗄️ Database Schemas

### Files Collection
```javascript
{
  userId: "123456789012345678",
  filename: "document.pdf",
  shortCode: "abc123xyz",
  size: 1048576,
  uploadDate: ISODate("2024-01-01"),
  expiresAt: ISODate("2024-01-08")
}
```

### Verifications Collection
```javascript
{
  userId: "123456789012345678",
  code: "1234567890", // 10 digits
  attempts: 3,
  blockedUntil: null, // or Date if blocked
  createdAt: ISODate("2024-01-01") // TTL: 5 min
}
```

---

## 🌐 API Endpoints

### File Server (localhost:3000)
- `POST /upload` - Upload file
- `GET /:shortCode` - Download file
- `GET /files/:userId` - List user files
- `DELETE /file/:shortCode` - Delete file
- `GET /health` - Health check

### Auth Server (localhost:3001)
- `GET /auth/discord` - Start OAuth
- `POST /auth/discord/callback` - OAuth callback
- `POST /auth/verify` - Verify code
- `POST /auth/resend` - Resend code
- `GET /health` - Health check

---

## 📋 Environment Variables

Required in `server/.env`:

```env
# MongoDB
MONGO_URI=mongodb://localhost:27017/tinytap-filehost
AUTH_MONGO_URI=mongodb://localhost:27017/tinytap-auth

# Servers
PORT=3000
AUTH_PORT=3001

# Discord OAuth
DISCORD_CLIENT_ID=your_client_id
DISCORD_CLIENT_SECRET=your_client_secret
DISCORD_BOT_TOKEN=your_bot_token
DISCORD_SERVER_ID=1419161334872019007
DISCORD_REDIRECT_URI=http://localhost:3001/auth/discord/callback

# File Hosting
MAX_FILE_SIZE=5368709120
UPLOAD_DIR=./uploads
CORS_ORIGIN=*
```

**⚠️ See [DISCORD_SETUP.md](DISCORD_SETUP.md) for credential regeneration!**

---

## 🧪 Testing

### Run All Tests
Follow **[TESTING_CHECKLIST.md](TESTING_CHECKLIST.md)** for:
- ✅ Successful login flow
- ✅ Non-member rejection
- ✅ Wrong code tracking (1-9 attempts)
- ✅ Account blocking (10 attempts)
- ✅ Block timer countdown
- ✅ Code expiration (5 minutes)
- ✅ Resend code functionality
- ✅ File upload/download

### Quick Health Check
```powershell
curl http://localhost:3000/health
curl http://localhost:3001/health
```

---

## 🐛 Troubleshooting

### Common Issues:

1. **"Authentication error"** → Auth server not running
   - Solution: `npm run start:auth`

2. **"Could not send DM"** → User has DMs disabled
   - Solution: Enable DMs from server members in Discord

3. **"Not in server"** → User not in Discord server
   - Solution: Join https://discord.gg/VJSbxYNfwS

4. **Bot offline** → Missing intents or wrong token
   - Solution: Enable MESSAGE CONTENT INTENT and SERVER MEMBERS INTENT

5. **MongoDB error** → MongoDB not running
   - Solution: `net start MongoDB` or `mongod --dbpath C:\data\db`

**See [TROUBLESHOOTING.md](TROUBLESHOOTING.md) for full guide**

---

## 🚀 Deployment

### Development
```powershell
npm run dev:all
```

### Production
```powershell
npm start              # File server
npm run start:auth     # Auth server
```

### Environment Setup
1. Update `.env` with production values
2. Change `DISCORD_REDIRECT_URI` to production URL
3. Update OAuth redirect in Discord Developer Portal
4. Use MongoDB Atlas for cloud database
5. Set up reverse proxy (nginx) for SSL/TLS

---

## 📦 npm Scripts

| Command | Description |
|---------|-------------|
| `npm start` | Run file server only |
| `npm run start:auth` | Run auth server only |
| `npm run dev:all` | Run both servers concurrently |

---

## 🔍 Monitoring

### Check Server Status
```powershell
# Health checks
curl http://localhost:3000/health
curl http://localhost:3001/health

# MongoDB status
mongo
> show dbs
> use tinytap-auth
> db.verifications.find()
```

### View Logs
- **File Server:** Check console output from `npm start`
- **Auth Server:** Check console output from `npm run start:auth`
- **MongoDB:** Check MongoDB logs

---

## 🎓 Learning Resources

### Discord API
- [Discord Developer Portal](https://discord.com/developers/applications)
- [Discord OAuth2 Documentation](https://discord.com/developers/docs/topics/oauth2)
- [Discord Bot API](https://discord.com/developers/docs/resources/user)

### MongoDB
- [MongoDB Documentation](https://docs.mongodb.com/)
- [Mongoose Guide](https://mongoosejs.com/docs/guide.html)
- [TTL Indexes](https://docs.mongodb.com/manual/core/index-ttl/)

### Node.js
- [Express.js Guide](https://expressjs.com/en/guide/routing.html)
- [Multer Documentation](https://github.com/expressjs/multer)
- [Axios Documentation](https://axios-http.com/docs/intro)

---

## 🤝 Contributing

### Report Issues
1. Check [TROUBLESHOOTING.md](TROUBLESHOOTING.md) first
2. Collect debug information:
   - Server logs
   - Browser console errors
   - MongoDB state
3. Post in Discord server: https://discord.gg/VJSbxYNfwS

### Feature Requests
- Suggest ideas in Discord server
- Explain use case and benefits

---

## 📞 Support

- **Discord Server:** https://discord.gg/VJSbxYNfwS
- **Documentation:** All `.md` files in this directory
- **Issues:** Check TROUBLESHOOTING.md first

---

## ⚠️ Security Warnings

### NEVER Share These:
- ❌ Discord Bot Token
- ❌ Discord Client Secret
- ❌ MongoDB connection strings
- ❌ API keys

### If Compromised:
1. **Immediately regenerate** credentials
2. Update `.env` file
3. Restart all servers
4. Review access logs

**Your credentials were compromised in this chat - regenerate them NOW!**

See [DISCORD_SETUP.md](DISCORD_SETUP.md) for regeneration steps.

---

## 📄 License

MIT License (or your chosen license)

---

## 🎉 Credits

- **Discord API** - Authentication system
- **MongoDB** - Database
- **Node.js** - Backend runtime
- **Express.js** - Web framework
- **TikTok APIs** - tiklydown.me, tikwm.com
- **YouTube APIs** - RapidAPI, YouTube Data API v3
- **QR Server API** - QR code generation

---

## 📈 Changelog

### v2.0.0 - Discord OAuth Migration
- ✅ Replaced Google OAuth with Discord OAuth
- ✅ Added 10-digit DM verification codes
- ✅ Implemented rate limiting (10 attempts)
- ✅ Added account blocking (10 minutes)
- ✅ Server membership requirement
- ✅ Real-time block countdown timer
- ✅ Discord bot DM notifications

### v1.0.0 - Initial Release
- ✅ TikTok downloader
- ✅ YouTube downloader + search
- ✅ File hosting with QR codes
- ✅ Google OAuth authentication

---

## 🎯 Next Steps

1. **Setup:** Follow [QUICKSTART.md](QUICKSTART.md)
2. **Configure:** Complete [DISCORD_SETUP.md](DISCORD_SETUP.md)
3. **Test:** Use [TESTING_CHECKLIST.md](TESTING_CHECKLIST.md)
4. **Deploy:** Set up production environment
5. **Monitor:** Check logs and MongoDB regularly

---

**Built with ❤️ for the TinyTap.ink community**

**Happy coding! 🚀**
