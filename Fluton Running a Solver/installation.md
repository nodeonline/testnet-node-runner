## **Running a Solver Testnet**

<p align= "center">
<img src="https://github.com/nodeonline/testnet-node-runner/blob/main/Fluton%20Running%20a%20Solver/logomark-white.png" "width="250" height="150" /><b\>
  

### Hardware requirements
>  Recommended Hardware: 4 Cores, 8GB RAM, 200GB of storage (NVME)

### # _Update & Upgrade_
```
sudo apt update && sudo apt upgrade -y
```

### # _Open port 3001_
```
sudo ufw allow 3001
```

### # _Clone the Repository_
```
git clone https://github.com/fluton-io/fluton-relayer.git
cd fluton-relayer
```

### # _Install yarn 4.9.2_
```
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs
npm install -g yarn@4.9.2
yarn install
```

### # _Configure environment variables_
create .env file and fill in your values:

PRIVATE_KEY=your_private_key (with 0x)

INFURA_API_KEY=your_infura_api_key 

ONEINCH_API_KEY=your_oneinch_api_key (optional)

SUBMIT RPC AND WS_URL ONCHAIN (SEPOLIA,ARB,BASE)

```
nano .env
```
and copy 
```
PRIVATE_KEY=your_private_key

PORT=3001

BACKEND_URL=http://localhost:3000
ODOS_API_URL=https://api.odos.xyz
ONEINCH_API_URL=https://api.1inch.dev

INFURA_API_KEY=infura_api_key
ONEINCH_API_KEY=oneinch_api_key

SEPOLIA_RPC_URL=your_rpc_sepolia
SEPOLIA_WS_URL=your_ws_sepolia

ARBITRUM_SEPOLIA_RPC_URL=your_rpc_arb_sepolia
ARBITRUM_SEPOLIA_WS_URL=your_ws_arb_sepolia

BASE_SEPOLIA_RPC_URL=your_rpc_base_sepolia
BASE_SEPOLIA_WS_URL=your_ws_base_sepolia
```

> If it has been changed, press "CTRL + X" then "Y" to exit and save the file

> If you don't have an infusion fire, you can come [here](https://www.infura.io/) login with email > create API + RPC + WS_URL > copy to file .env


### # _Configure fees_
```
yarn generate-fee-schema
```
> select 0 for Odos




