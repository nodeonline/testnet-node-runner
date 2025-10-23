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
<img src="" />

nano ~/infernet-container-starter/deploy/docker-compose.yaml
















