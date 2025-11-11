# 🔧 Troubleshooting Guide

## Common Issues & Solutions

---

## 🚨 Authentication Issues

### 1. "Authentication error. Please check if the auth server is running"

**Cause:** Auth server not running or not accessible

**Solutions:**
```powershell
# Check if auth server is running:
curl http://localhost:3001/health

# If not running, start it:
cd d:\EZTK\server
npm run start:auth

# Or run both servers:
npm run dev:all
```

**Check console for errors:**
- MongoDB connection failed?
- Port 3001 already in use?
- Missing environment variables?

---

### 2. "Could not send DM. Please enable DMs from server members"

**Cause:** User has DMs disabled in Discord privacy settings

**Solution for User:**
1. Open Discord
2. Right-click on the server (TinyTap server)
3. Go to **Privacy Settings**
4. Enable **"Allow direct messages from server members"**
5. Try logging in again

---

### 3. "You need to join the Discord server first!"

**Cause:** User not in Discord server (ID: 1419161334872019007)

**Solution:**
1. Click the invite link: https://discord.gg/VJSbxYNfwS
2. Join the server
3. Try logging in again

**Alternative:** Check if bot can see user:
```powershell
# Test bot token:
curl -H "Authorization: Bot YOUR_BOT_TOKEN" https://discord.com/api/v10/guilds/1419161334872019007/members/USER_ID
```

---

### 4. Bot Not Sending DMs

**Cause:** Bot lacks required intents or permissions

**Solution:**
1. Go to https://discord.com/developers/applications
2. Select **tinytapbot**
3. Go to **Bot** section
4. Enable these intents:
   - ✅ **MESSAGE CONTENT INTENT**
   - ✅ **SERVER MEMBERS INTENT**
5. Click **Save Changes**
6. **Restart auth server**

---

### 5. "Invalid credentials" or OAuth fails

**Cause:** Discord credentials are wrong or expired

**Solution:**
1. Check `.env` file has correct values:
   - `DISCORD_CLIENT_ID`
   - `DISCORD_CLIENT_SECRET`
   - `DISCORD_BOT_TOKEN`
2. If they were compromised (posted publicly), **regenerate them**:
   - Go to https://discord.com/developers/applications
   - Bot section → **Reset Token**
   - OAuth2 section → **Reset Secret**
3. Update `.env` with new values
4. Restart servers

---

### 6. "No verification code found. Please login again"

**Cause:** Code expired (5-minute TTL) or MongoDB issue

**Solutions:**
- Code expired? Login again to get new code
- Check MongoDB connection:
  ```powershell
  mongo
  > use tinytap-auth
  > db.verifications.find()
  ```
- If empty, login process isn't saving codes
- Check auth server logs for errors

---

### 7. Block Timer Resets on Page Refresh

**Cause:** Frontend-only timer (not synced with backend)

**Expected Behavior:**
- Warning shown to users: "⚠️ If you refresh, the timer will restart!"
- Block persists on backend (MongoDB)
- User still blocked even if timer resets visually

**Not a bug - this is by design to prevent users from refreshing to bypass the timer.**

---

## 🗄️ Database Issues

### 8. "MongoDB connection error"

**Cause:** MongoDB not running or wrong connection string

**Solutions:**
```powershell
# Start MongoDB service:
net start MongoDB

# Or manually:
mongod --dbpath C:\data\db

# Test connection:
mongo
> show dbs
```

**Check `.env` connection strings:**
```env
MONGO_URI=mongodb://localhost:27017/tinytap-filehost
AUTH_MONGO_URI=mongodb://localhost:27017/tinytap-auth
```

**For MongoDB Atlas:**
```env
MONGO_URI=mongodb+srv://username:password@cluster.mongodb.net/tinytap-filehost
```

---

### 9. Verification Codes Not Expiring

**Cause:** TTL index not created on `createdAt` field

**Solution:**
```javascript
// In MongoDB shell:
use tinytap-auth;
db.verifications.createIndex({ "createdAt": 1 }, { expireAfterSeconds: 300 });
```

**Or restart auth server** - it should create the index automatically via Mongoose schema.

---

## 📁 File Upload Issues

### 10. "File too large"

**Cause:** File exceeds 5GB limit

**Solution:**
- Check file size before upload
- Limit set in `server.js` (Multer):
  ```javascript
  limits: { fileSize: 5 * 1024 * 1024 * 1024 } // 5GB
  ```
- Increase limit if needed (edit `server.js`)

---

### 11. Files Not Uploading

**Causes & Solutions:**

**File server not running:**
```powershell
curl http://localhost:3000/health
npm start
```

**Uploads folder missing:**
```powershell
cd d:\EZTK\server
mkdir uploads
```

**Permissions issue:**
- Check folder has write permissions

**CORS error:**
- Check `.env`: `CORS_ORIGIN=*`
- Or set specific origin: `CORS_ORIGIN=http://localhost:8080`

---

### 12. Short Links Not Working

**Cause:** MongoDB not saving file metadata

**Debug:**
```javascript
// Check MongoDB:
mongo
> use tinytap-filehost;
> db.files.find();
```

**If empty:**
- Check file server logs for errors
- Verify MongoDB connection
- Check Multer is saving files to `./uploads/`

---

## 🌐 Network Issues

### 13. "CORS policy: No 'Access-Control-Allow-Origin'"

**Cause:** CORS not configured properly

**Solution in `.env`:**
```env
CORS_ORIGIN=*
```

**Or allow specific domain:**
```env
CORS_ORIGIN=http://localhost:8080
```

**Restart servers after changing `.env`**

---

### 14. Port Already in Use

**Error:** "Port 3000 is already in use"

**Solutions:**
```powershell
# Find process using port:
netstat -ano | findstr :3000

# Kill process (replace PID):
taskkill /PID <PID> /F

# Or change port in .env:
PORT=3001
```

---

## 🎨 Frontend Issues

### 15. Login Button Not Working

**Causes:**
1. JavaScript error - check browser console (F12)
2. Auth server URL wrong - check `http://localhost:3001/auth/discord`
3. CORS issue - check network tab in DevTools

**Debug:**
```javascript
// Open browser console:
console.log('Testing Discord login...');
fetch('http://localhost:3001/health')
  .then(r => r.json())
  .then(console.log);
```

---

### 16. Verification Code Input Not Showing

**Cause:** OAuth callback not triggering properly

**Debug:**
1. Check URL after Discord authorization
2. Should have `?code=...`
3. Check browser console for errors
4. Verify `handleDiscordCallback()` is being called

---

### 17. User Avatar Not Showing

**Cause:** Discord user has no avatar or URL is wrong

**Expected Behavior:**
- Fallback SVG icon should appear
- Check `completeLogin()` function has fallback:
  ```javascript
  picture: userData.picture || 'data:image/svg+xml,...'
  ```

---

## 🤖 Discord Bot Issues

### 18. Bot Offline in Server

**Cause:** Bot token invalid or bot not invited

**Solutions:**
1. **Check bot token in `.env`**
2. **Invite bot to server:**
   ```
   https://discord.com/api/oauth2/authorize?client_id=1320627493894742106&permissions=2147485696&scope=bot
   ```
3. **Verify bot online:**
   - Open Discord
   - Check server member list
   - Bot should have green dot

---

### 19. Bot Can't Check Server Membership

**Cause:** Missing SERVER MEMBERS INTENT

**Solution:**
1. Go to https://discord.com/developers/applications
2. Select **tinytapbot** → **Bot**
3. Enable **SERVER MEMBERS INTENT** ✅
4. Click **Save Changes**
5. **Restart auth server**

---

## 🔍 Debugging Tips

### Enable Verbose Logging

**Add to `auth-server.js`:**
```javascript
// After MongoDB connection:
mongoose.set('debug', true);

// In sendDiscordDM():
console.log('[DM] Sending to:', userId);
console.log('[DM] Message:', message);

// In OAuth callback:
console.log('[OAUTH] User:', user);
console.log('[OAUTH] In server:', inServer);
console.log('[OAUTH] Code generated:', verificationCode);
```

---

### Check Environment Variables

```powershell
# In server directory:
cd d:\EZTK\server
node -e "require('dotenv').config(); console.log(process.env);"
```

**Verify all required variables are set:**
- `DISCORD_CLIENT_ID`
- `DISCORD_CLIENT_SECRET`
- `DISCORD_BOT_TOKEN`
- `DISCORD_SERVER_ID`
- `DISCORD_REDIRECT_URI`
- `AUTH_PORT`
- `MONGO_URI`
- `AUTH_MONGO_URI`

---

### Test Discord API Directly

```powershell
# Test OAuth endpoint:
curl https://discord.com/api/oauth2/authorize?client_id=YOUR_CLIENT_ID&redirect_uri=http://localhost:3001/auth/discord/callback&response_type=code&scope=identify%20email

# Test Bot API:
curl -H "Authorization: Bot YOUR_BOT_TOKEN" https://discord.com/api/v10/users/@me

# Test Server Membership:
curl -H "Authorization: Bot YOUR_BOT_TOKEN" https://discord.com/api/v10/guilds/1419161334872019007/members/USER_ID
```

---

### Monitor MongoDB in Real-Time

```javascript
// Open MongoDB shell:
mongo

// Switch to auth database:
use tinytap-auth;

// Watch for new verifications:
db.verifications.find().pretty();

// Watch collection changes:
var changeStream = db.verifications.watch();
changeStream.on("change", function(change) {
    print(JSON.stringify(change));
});
```

---

### Check Server Logs

**File Server:**
```powershell
cd d:\EZTK\server
npm start
# Watch for errors in console
```

**Auth Server:**
```powershell
cd d:\EZTK\server
npm run start:auth
# Watch for Discord API errors
```

---

## 🚨 Emergency Reset

### Full System Reset

**If everything is broken:**

1. **Stop all servers** (Ctrl+C in terminals)

2. **Clear MongoDB:**
   ```javascript
   mongo
   > use tinytap-auth;
   > db.verifications.deleteMany({});
   > use tinytap-filehost;
   > db.files.deleteMany({});
   ```

3. **Regenerate Discord credentials:**
   - Bot Token
   - OAuth Client Secret

4. **Update `.env`** with new credentials

5. **Reinstall dependencies:**
   ```powershell
   cd d:\EZTK\server
   rm -rf node_modules
   rm package-lock.json
   npm install
   ```

6. **Restart everything:**
   ```powershell
   npm run dev:all
   ```

7. **Test login flow again**

---

## 📞 Still Having Issues?

### Collect Debug Information:

1. **Server logs** (copy full console output)
2. **Browser console errors** (F12 → Console tab)
3. **Network errors** (F12 → Network tab)
4. **MongoDB state:**
   ```javascript
   db.verifications.find().pretty();
   ```
5. **Environment variables** (hide secrets!)
6. **Node.js version:** `node -v`
7. **npm version:** `npm -v`

### Ask for Help:
- Discord Server: https://discord.gg/VJSbxYNfwS
- Include debug information above
- Describe exact steps to reproduce issue

---

## ✅ Validation Checklist

Before reporting issues, verify:
- [ ] Both servers running (3000 and 3001)
- [ ] MongoDB connected
- [ ] `.env` file configured correctly
- [ ] Discord bot online in server
- [ ] Intents enabled (MESSAGE CONTENT, SERVER MEMBERS)
- [ ] OAuth redirect URI added in Discord Developer Portal
- [ ] User is in Discord server
- [ ] User has DMs enabled from server members
- [ ] No console errors in browser (F12)
- [ ] No errors in server terminal

---

**Most issues are configuration-related. Double-check environment variables! 🔐**
