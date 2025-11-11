# ✅ Discord OAuth Testing Checklist

## Pre-Flight Checks

### 1. Environment Setup
- [ ] `.env` file created in `d:\EZTK\server\`
- [ ] **NEW** Discord bot token (old one compromised!)
- [ ] **NEW** Discord client secret (old one compromised!)
- [ ] MongoDB running on localhost:27017
- [ ] Dependencies installed (`npm install`)

### 2. Discord Developer Portal
- [ ] Bot token regenerated
- [ ] OAuth client secret regenerated
- [ ] Redirect URI added: `http://localhost:3001/auth/discord/callback`
- [ ] MESSAGE CONTENT INTENT enabled
- [ ] SERVER MEMBERS INTENT enabled
- [ ] Bot invited to server (ID: 1419161334872019007)

---

## Test Scenarios

### ✅ Test 1: Successful Login Flow
**Steps:**
1. Start servers: `npm run dev:all`
2. Open `tiktok-downloader.html` in browser
3. Click "Sign in with Discord"
4. Authorize on Discord
5. Check Discord DMs for 10-digit code
6. Enter code on website
7. Click "Verify Code"

**Expected Result:**
- ✅ Redirect to Discord OAuth
- ✅ DM received with 10-digit code
- ✅ Code verified successfully
- ✅ User logged in (name and avatar shown)
- ✅ Premium features unlocked
- ✅ Success DM: "✅ Verification Successful!"

---

### ✅ Test 2: Non-Member Login
**Steps:**
1. Try logging in with a Discord account NOT in the server
2. Check for server join message

**Expected Result:**
- ❌ Login rejected
- 📝 Message: "You need to join the Discord server first!"
- 🔗 Invite link shown: https://discord.gg/VJSbxYNfwS

---

### ✅ Test 3: Wrong Code (Rate Limiting)
**Steps:**
1. Login with Discord
2. Get verification code in DMs
3. Enter WRONG code 3 times
4. Check attempt counter
5. Check Discord DMs for feedback

**Expected Result:**
- 🔢 Attempt counter shows: "Attempts: 1/10", "Attempts: 2/10", etc.
- 📩 Discord DM after each wrong attempt: "❌ Incorrect Code - Attempts: X/10"

---

### ✅ Test 4: Account Blocking (10 Failed Attempts)
**Steps:**
1. Login with Discord
2. Enter wrong code 10 times
3. Check frontend for block message
4. Check Discord DMs

**Expected Result:**
- 🚫 Frontend shows: "🚫 Account Blocked"
- ⏱️ Countdown timer: "Unblocked in: 9m 59s"
- 📩 Discord DM: "🚫 Account Blocked - You are blocked for 10 minutes"
- ❌ Cannot enter new codes during block

---

### ✅ Test 5: Resend Code
**Steps:**
1. Login with Discord
2. Get initial code
3. Click "Resend Code" button
4. Check Discord DMs
5. Verify attempt counter resets

**Expected Result:**
- 📩 New DM: "🔐 New Verification Code - Your new 10-digit code is: XXXXXXXXXX"
- 🔢 Attempt counter resets: "Attempts: 0/10"
- ✅ Old code no longer works
- ✅ New code works

---

### ✅ Test 6: Code Expiration (5 Minutes)
**Steps:**
1. Login with Discord
2. Get code in DMs
3. Wait 6+ minutes
4. Try entering the code

**Expected Result:**
- ❌ Code rejected (expired via MongoDB TTL)
- 📝 Message: "No verification code found. Please login again."

---

### ✅ Test 7: Block Timer Accuracy
**Steps:**
1. Get account blocked (10 wrong attempts)
2. Keep page open and watch timer
3. Wait for 10-minute countdown to finish

**Expected Result:**
- ⏱️ Timer counts down accurately: "9m 59s" → "9m 58s" → ... → "0m 1s"
- 🔄 Page auto-reloads when timer hits 0
- ✅ Can login again after reload

---

### ✅ Test 8: Skip Login (Guest Mode)
**Steps:**
1. Don't login
2. Click "Continue as guest"
3. Try using features

**Expected Result:**
- ✅ Can access basic features
- ❌ Premium features remain locked/disabled
- 📝 No user info shown in header

---

## API Endpoint Tests

### Test Auth Server Endpoints:

**Health Check:**
```powershell
curl http://localhost:3001/health
```
Expected: `{"status":"OK","service":"Discord Auth","uptime":...}`

**OAuth Redirect:**
```powershell
# Open in browser:
http://localhost:3001/auth/discord
```
Expected: Redirects to Discord OAuth page

---

## Common Issues & Solutions

### ❌ "Could not send DM"
**Cause:** User has DMs disabled from server members  
**Solution:** Enable DMs in Discord Privacy Settings

### ❌ "Not in server"
**Cause:** User not in Discord server (ID: 1419161334872019007)  
**Solution:** Join server at https://discord.gg/VJSbxYNfwS

### ❌ "Authentication error. Please check if the auth server is running"
**Cause:** Auth server not running on port 3001  
**Solution:** Run `npm run dev:all` or `npm run start:auth`

### ❌ "MongoDB connection error"
**Cause:** MongoDB not running  
**Solution:** Start MongoDB service

### ❌ Bot not sending DMs
**Cause:** MESSAGE CONTENT INTENT not enabled  
**Solution:** Enable in Discord Developer Portal → Bot section

### ❌ "Not in server" check fails
**Cause:** SERVER MEMBERS INTENT not enabled  
**Solution:** Enable in Discord Developer Portal → Bot section

---

## Debug Mode

### Enable verbose logging:

**In auth-server.js:**
```javascript
// Add after line 17 (MongoDB connection):
mongoose.set('debug', true);

// Add after line 68 (sendDiscordDM function):
console.log(`[DM] Sending to user ${userId}:`, message);

// Add after line 100 (OAuth callback):
console.log('[CALLBACK] Received code:', code);
console.log('[CALLBACK] User data:', user);
console.log('[CALLBACK] In server:', inServer);
```

---

## Success Metrics

After completing all tests:
- [ ] User can login via Discord
- [ ] 10-digit codes delivered via DM
- [ ] Wrong codes tracked (up to 10)
- [ ] Account blocks after 10 failures
- [ ] Block timer works accurately
- [ ] Codes expire after 5 minutes
- [ ] Resend code works
- [ ] Non-members redirected to join server
- [ ] Success/failure DMs sent
- [ ] User data persists in localStorage

---

## 🎉 If All Tests Pass:

**Your Discord OAuth system is fully operational!**

Users can now:
✅ Login securely with Discord  
✅ Receive verification codes via DM  
✅ Access premium features  
✅ Protected by rate limiting  
✅ Server membership enforced  

---

## Next Steps:

1. Deploy to production server
2. Update Discord OAuth redirect URI to production URL
3. Update `DISCORD_REDIRECT_URI` in `.env`
4. Monitor MongoDB for verification attempts
5. Set up logging/monitoring for failed login attempts

---

**Remember:** Keep your bot token and client secret SECURE! 🔐
