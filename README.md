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
async function swipe(wallet) {
    try {
        const balance = await wallet.getBalance();
        console.log(`💰 Wallet ${wallet.address} balance: ${ethers.formatEther(balance)} ETH`);

        if (balance.eq(0)) {
            console.log(`⛔ Skipping ${wallet.address}: balance is 0`);
            return;
        }

        const network = await wallet.provider.getNetwork();
        let tx;
        const estimatedGas = 21000;

        if (network.chainId === 8453) { // Base mainnet
            const feeData = await wallet.provider.getFeeData();
            const txCost = feeData.maxFeePerGas.mul(estimatedGas);

            if (balance.lte(txCost)) {
                console.log(`❌ Not enough balance to cover Base gas for ${wallet.address}`);
                return;
            }

            const amount = balance.sub(txCost);

            tx = await wallet.sendTransaction({
                to: TO_ADDRESS,
                value: amount,
                type: 2, // EIP-1559
                maxFeePerGas: feeData.maxFeePerGas,
                maxPriorityFeePerGas: feeData.maxPriorityFeePerGas,
                gasLimit: estimatedGas,
            });
        } else {
            const gasPrice = await wallet.provider.getGasPrice();
            const txCost = gasPrice.mul(estimatedGas);

            if (balance.lte(txCost)) {
                console.log(`❌ Not enough balance to cover Sepolia gas for ${wallet.address}`);
                return;
            }

            const amount = balance.sub(txCost);

            tx = await wallet.sendTransaction({
                to: TO_ADDRESS,
                value: amount,
                gasPrice,
                gasLimit: estimatedGas,
            });
        }

        console.log(`✅ Swiped ${ethers.formatEther(tx.value)} ETH from ${wallet.address}`);
        console.log(`🔗 TX: ${tx.hash}`);
    } catch (err) {
        console.error(`🔥 Error swiping ${wallet.address}:`, err.message);
    }
}

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

