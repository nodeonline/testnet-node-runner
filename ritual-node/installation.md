# <p align= "center">  **:candle:Installation Ritual Node**



## **:candle: Hardware Requirements**
<img src="https://github.com/nodeonline/testnet-node-runner/blob/82eb2b2656743b841fe9eb61bd5092b33780b29a/ritual-node/SPEK.png" />

## :candle: **Requirements**

- Private Key
- fee ETH_BASE $15-$25 (recommend $30)
- RPC_URL: https://mainnet.base.org/ or BASE MAINNET FROM ALCHEMY
- address registry: 0x3B1554f346DFe5c482Bb4BA31b880c1C18412170


-------------------------

## :candle: **Install dependencies Required**
```
sudo apt update && sudo apt upgrade -y
```
```
sudo apt-get install git curl build-essential make jq gcc snapd chrony lz4 tmux screen unzip bc -y
```

## :candle: **Open Ports**
```
sudo apt install ufw -y
sudo ufw allow 8545
sudo ufw allow 4000
sudo ufw allow 6379
sudo ufw allow 3000
sudo ufw allow 22
```
```
sudo ufw enable
```

## :candle: **Install Docker & Docker Compose**
```
sudo apt install docker.io
```
```
sudo curl -L "https://github.com/docker/compose/releases/download/v2.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
```
sudo chmod +x /usr/local/bin/docker-compose
```
```
DOCKER_CONFIG=${DOCKER_CONFIG:-$HOME/.docker} && mkdir -p $DOCKER_CONFIG/cli-plugins && curl -SL https://github.com/docker/compose/releases/download/v2.20.2/docker-compose-linux-x86_64 -o $DOCKER_CONFIG/cli-plugins/docker-compose && chmod +x $DOCKER_CONFIG/cli-plugins/docker-compose
```
check version
```
docker compose version
```

## :candle: **Cloning Repository**

```
git clone https://github.com/ritual-net/infernet-container-starter && cd infernet-container-starter
```

## :candle: **Running hello-world**

```
screen -S ritual
```
```
docker pull ritualnetwork/hello-world-infernet:latest
```
```
project=hello-world make deploy-container
```
> If successful, you can press "ctrl A + D" together

## :candle: **Node Configuration**

edit configuration, you have to change :
- replace Private Key (only 0x)
- replace RPC_URL: https://mainnet.base.org/ or BASE MAINNET FROM ALCHEMY
- replace address registry: 0x3B1554f346DFe5c482Bb4BA31b880c1C18412170
- replace 0 to 3, "trail_head_blocks": 3,

and also replace this part like this:

<img src="https://github.com/nodeonline/testnet-node-runner/blob/5c31f27e82e37d5040289f658ccb7a4e1092e663/ritual-node/edit_con.png" />

```
nano ~/infernet-container-starter/deploy/config.json
```
> If it has been changed, press "CTRL + X" then "Y" to exit and save the file

```
nano ~/infernet-container-starter/projects/hello-world/container/config.json
```
> If it has been changed, press "CTRL + X" then "Y" to exit and save the file

replace the registry address with this : 0x3B1554f346DFe5c482Bb4BA31b880c1C18412170
```
nano ~/infernet-container-starter/projects/hello-world/contracts/script/Deploy.s.sol
```

## :candle: **Edit Makefile**

+ replace sender's address with your private key (only 0x)
+ Change RPC_URL to https://mainnet.base.org/
```
nano ~/infernet-container-starter/projects/hello-world/contracts/Makefile
```
> If it has been changed, press "CTRL + X" then "Y" to exit and save the file


## :candle: **Edit Node Version**

replace it with version 1.4.0

<img src="https://github.com/nodeonline/testnet-node-runner/blob/d0f86ad450369c312b914346d0ec16a857fb8e79/ritual-node/edit_node_version.png" />

```
nano ~/infernet-container-starter/deploy/docker-compose.yaml
```
> If it has been changed, press "CTRL + X" then "Y" to exit and save the file


## :candle: **Initialize Configuration**

Restart Docker containers
```
docker compose -f ~/infernet-container-starter/deploy/docker-compose.yaml down && docker compose -f ~/infernet-container-starter/deploy/docker-compose.yaml up -d
```

check Docker
```
docker ps -a
```

## :candle: **Install Foundry**

before installing foundryup, we have to stop docker
```
docker compose -f ~/infernet-container-starter/deploy/docker-compose.yaml down
```
```
cd
```
```
mkdir foundry && cd foundry
```
```
curl -L https://foundry.paradigm.xyz | bash
```
```
source ~/.bashrc
```
```
foundryup
```

## :candle: **Install the forge-std library & Install the infernet-sdk**
```
cd ~/infernet-container-starter/projects/hello-world/contracts
```
```
rm -rf lib/forge-std lib/infernet-sdk
```
```
forge install foundry-rs/forge-std
```
```
forge install ritual-net/infernet-sdk
```
```
cd ../../../
```
next, restart Docker containers
```
docker compose -f ~/infernet-container-starter/deploy/docker-compose.yaml down && docker compose -f ~/infernet-container-starter/deploy/docker-compose.yaml up -d
```
check Docker
```
docker ps -a
```
> my docker
<img src="https://github.com/nodeonline/testnet-node-runner/blob/b64be1cbe7795ede76a6b58f11cbe2b358d3864b/ritual-node/cek_docker.png" />

check your logs infernet node
```
docker logs infernet-node
```
> logs normal
<img src="https://github.com/nodeonline/testnet-node-runner/blob/a2fdf92b0f68c423a319576f05f879aa776b872e/ritual-node/photo/logs-infernit-node.png" />


## :candle: **Deploy Consumer Contract**
```
cd ~/infernet-container-starter
```
```
project=hello-world make deploy-contracts
```
you have to save **contract address** result from deploy sayGM
<img src="https://github.com/nodeonline/testnet-node-runner/blob/7c66741b3735e247b1418dcfde8d352593be5bc4/ritual-node/photo/sayGM.png" />
If the transaction is successful ✅, then you have deployed sayGM. You can check the txhash yourself on :mag: [basescan](https://basescan.org/)


## :candle: **Call Contract**

see the contract address in the rectangle and save

<img src="https://github.com/nodeonline/testnet-node-runner/blob/25be5ec492cce692bfeff6e9c75344997cf91474/ritual-node/photo/call_contract.png" />
edit your CallContract.s.sol file by inserting the new contract address. The preconfigured address is SaysGM saysGm = SaysGM(0x13D69Cf7d6CE4218F646B759Dcf334D82c023d8e), change it to the address that was generated when calling SaysGM.


```
nano ~/infernet-container-starter/projects/hello-world/contracts/script/CallContract.s.sol
```
> If it has been changed, press "CTRL + X" then "Y" to exit and save the file

Deploy call contract
```
project=hello-world make call-contract
```
If the transaction is successful ✅, then you have deployed. You can check the txhash yourself on :mag: [basescan](https://basescan.org/)

# <p align= "center">  **:candle:Congratulations, your node is running:candle:**


check docker
```
docker ps -a
```
check logs docker
```
docker logs infernet-node
```
restart docker 
```
docker compose -f ~/infernet-container-starter/deploy/docker-compose.yaml down && docker compose -f ~/infernet-container-starter/deploy/docker-compose.yaml up -d
```

If there is anything missing or wrong please let me know:  
round_pushpin user id discord 917275641441816607 / @babangaip

gRitual fam happy to you :indonesia:	
