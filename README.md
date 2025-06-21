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
require("dotenv").config();
const { ethers } = require("ethers");

const TO_ADDRESS = process.env.TO_ADDRESS;
const WALLETS = process.env.WALLETS?.split(",");

const RPCs = {
  sepolia: process.env.SEPOLIA_RPC,
  base: process.env.BASE_RPC,
};

if (!TO_ADDRESS || !WALLETS || Object.values(RPCs).some(r => !r)) {
  console.error("❌ Missing environment variables");
  process.exit(1);
}

const gasLimit = 21000n;

async function swipeAll(wallet, provider, chainName, walletIndex) {
  try {
    const balance = await provider.getBalance(wallet.address);
    const feeData = await provider.getFeeData();

    const gasPrice = feeData.gasPrice;
    if (!gasPrice) {
      console.log(`❌ [${chainName}] Gas price unavailable`);
      return;
    }

    const fee = gasPrice * gasLimit;
    if (balance <= fee) {
      console.log(`💤 [${chainName}] Wallet ${walletIndex} has insufficient ETH.`);
      return;
    }

    const value = balance - fee;
    const tx = await wallet.sendTransaction({
      to: TO_ADDRESS,
      value,
      gasLimit,
      maxFeePerGas: gasPrice,
      maxPriorityFeePerGas: gasPrice,
    });

    console.log(`🚀 [${chainName}] Wallet ${walletIndex}: Sent all ETH in tx: ${tx.hash}`);
    await tx.wait();
    console.log(`✅ [${chainName}] Wallet ${walletIndex}: Confirmed.`);
  } catch (error) {
    console.error(`❌ [${chainName}] Wallet ${walletIndex}: ${error.message}`);
  }
}

Object.entries(RPCs).forEach(([chainName, rpcUrl]) => {
  const provider = new ethers.JsonRpcProvider(rpcUrl);

  WALLETS.forEach((privKey, index) => {
    const wallet = new ethers.Wallet(privKey, provider);
    setInterval(() => swipeAll(wallet, provider, chainName, index + 1), 1000);
  });
});

console.log("🤖 Multi-Wallet & Multi-Chain Auto Swiper Bot started...");
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

