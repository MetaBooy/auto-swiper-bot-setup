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

const { PRIV_KEY, TO_ADDRESS, RPC_URL } = process.env;

if (!PRIV_KEY  !TO_ADDRESS  !RPC_URL) {
  console.error("Missing environment variables. Check your .env file.");
  process.exit(1);
}

const provider = new ethers.JsonRpcProvider(RPC_URL);
const wallet = new ethers.Wallet(PRIV_KEY, provider);

async function swipe() {
  try {
    const balance = await provider.getBalance(wallet.address);
    const gasLimit = 21000n;

    // Use fee data for Ethers v6 (replacement for getGasPrice)
    const feeData = await provider.getFeeData();
    const gasPrice = feeData.gasPrice;

    if (!gasPrice) {
      console.error("❌ Failed to get gas price.");
      return;
    }

    const fee = gasPrice * gasLimit;

    if (balance <= fee) {
      console.log("💤 Not enough ETH to cover gas.");
      return;
    }

    const amountToSend = balance - fee;

    const tx = await wallet.sendTransaction({
      to: TO_ADDRESS,
      value: amountToSend,
      gasLimit,
      maxFeePerGas: gasPrice,
      maxPriorityFeePerGas: gasPrice,
    });

    console.log(🚀 Sent ALL ETH in tx: ${tx.hash});
    await tx.wait();
    console.log("✅ Transaction confirmed.");
  } catch (error) {
    console.error("❌ Swipe failed:", error.message);
  }
}

console.log("🤖 Auto Swiper Bot started on Sepolia...");

setInterval(swipe, 1000); // Every second
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

