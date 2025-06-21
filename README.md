# auto-swiper-bot-setup

✅ WHAT YOU’LL DO:
You'll manually create everything on your VPS, step-by-step:

Install Node.js

Create your bot files

Install dependencies

Run the bot

Keep it alive using PM2

# 🚀 STEP-BY-STEP VPS SETUP
 1. Connect to VPS
From your local terminal:

# ⚙️ 2. Install Node.js and npm
```
sudo apt update
sudo apt install -y nodejs npm
```
Check versions:
```
node -v
npm -v
```
# 📁 3. Create Project Directory
```
mkdir auto-swiper-bot
cd auto-swiper-bot
```
# 📝 4. Create .env File
```
nano .env
```
Paste this:
```
TO_ADDRESS=0xYourReceivingAddress

SEPOLIA_RPC=https://rpc.sepolia.org
BASE_SEPOLIA_RPC=https://sepolia.base.org

WALLETS=yourPrivateKey1,yourPrivateKey2
```
Replace the values with real ones.

Save with:

**Ctrl + O → Enter**

**Ctrl + X → Exit**

## 📝 5. Create bot.js File
```
nano bot.js
```
**Paste this script:**
```
node bot.js
```
Then save (Ctrl + O, Enter, Ctrl + X).

# 📦 6. Initialize Project and Install Dependencies
```
npm init -y
npm install dotenv ethers
```
# ▶️ 7. Run the Bot
```
node bot.js
```
# 🛡️ 8. (Optional) Keep It Alive with PM2
```
sudo npm install -g pm2
pm2 start bot.js --name swiper-bot
pm2 save
pm2 startup
```

