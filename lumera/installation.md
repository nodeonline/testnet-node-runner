## **LUMERA CHAIN SETUP VALIDATOR TESTNET**

<p align= "center">
<img src="https://github.com/nodeonline/testnet-node-runner/blob/main/lumera/logo-lumera.png" "width="250" height="150" /><b\>

##### api      : https://api.lumera.nodeonline.xyz
##### rpc      : https://rpc.lumera.nodeonline.xyz
##### explorer : https://explorer.nodeonline.xyz/lumera-testnet

### Hardware requirements
> Recommended Hardware: 8 Cores, 8GB RAM, 200GB of storage (NVME)

### # _Install dependencies Required_
```
sudo apt update && sudo apt upgrade -y
```
```
sudo apt-get install git curl build-essential make jq gcc snapd chrony lz4 tmux unzip bc -y
```

### # _set vars_
> change manual your "wallet","moniker"
```
echo "export WALLET="wallet"" >> $HOME/.bash_profile
echo "export MONIKER="moniker"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### # _Install GO,If you need_ 
```
ver="1.21.4"
cd $HOME
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile
source ~/.bash_profile
go version
```

### # _Download project binaries_
```
mkdir -p $HOME/.lumera/cosmovisor/genesis/bin
wget -O $HOME/.lumera/cosmovisor/genesis/bin/lumerad https://ss.t.nodeonline.xyz/testnet/lumera/lumerad
chmod +x $HOME/.lumera/cosmovisor/genesis/bin/lumerad
```
```
wget -O /usr/lib/libwasmvm.x86_64.so https://ss.t.nodeonline.xyz/testnet/lumera/libwasmvm.x86_64.so
```
### # _Create application symlinks_
```
ln -s $HOME/.lumera/cosmovisor/genesis $HOME/.lumera/cosmovisor/current -f
sudo ln -s $HOME/.lumera/cosmovisor/current/bin/lumerad /usr/local/bin/lumerad -f
```

# Install Cosmovisor and create a service

### # _Download and install Cosmovisor_
```
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.6.0
```


### # _Create service_
```
sudo tee /etc/systemd/system/lumera-testnet.service > /dev/null << EOF
[Unit]
Description=lumera node service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.lumera"
Environment="DAEMON_NAME=lumerad"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:$HOME/.lumera/cosmovisor/current/bin"

[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable lumera-testnet.service
```

### # _Initialize the node & Set node config_
```
lumerad init $MONIKER --chain-id lumera-testnet-1
sed -i -e "s|^node *=.*|node = \"tcp://localhost:16957\"|" $HOME/.lumera/config/client.toml
sed -i -e "s|^keyring-backend *=.*|keyring-backend = \"os\"|" $HOME/.lumera/config/client.toml
sed -i -e "s|^chain-id *=.*|chain-id = \"lumera-testnet-1\"|" $HOME/.lumera/config/client.toml
```

### # _Download genesis and addrbook_
```
curl -Ls https://ss.t.nodeonline.xyz/testnet/lumera/genesis.json > $HOME/.lumera/config/genesis.json
curl -Ls https://ss.t.nodeonline.xyz/testnet/lumera/addrbook.json > $HOME/.lumera/config/addrbook.json
```

### # _Configure Seeds and Peers_
```
seeds="2c7733576b4c131464f78384f854c842052656c6@194.238.28.132:16656"
PEERS="$(curl -sS https://rpc.lumera.nodeonline.xyz/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}' | sed -z 's|\n|,|g;s|.$||')"
sed -i -e "s|^seeds *=.*|seeds = '"$SEEDS"'|; s|^persistent_peers *=.*|persistent_peers = '"$PEERS"'|" $HOME/.lumera/config/config.toml
```

### # _set custom ports_
```
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:16958\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:16957\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:16960\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:16956\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":16966\"%" $HOME/.lumera/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:16917\"%; s%^address = \":8080\"%address = \":16980\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:16990\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:16991\"%; s%:8545%:16945%; s%:8546%:16946%; s%:6065%:16965%" $HOME/.lumera/config/app.toml
```

### # _Set minimum gas price_
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.025ulume\"|" $HOME/.lumera/config/app.toml
```

### # _Set pruning_
```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.lumera/config/app.toml
```

### # _Download latest chain snapshot_
```
curl -L https://ss.t.nodeonline.xyz/testnet/lumera/ss.t.lumera-latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.lumera
[[ -f $HOME/.lumera/data/upgrade-info.json ]] && cp $HOME/.lumera/data/upgrade-info.json $HOME/.lumera/cosmovisor/genesis/upgrade-info.json
```

### # _Start service and check the logs_
```
sudo systemctl start lumera-testnet.service && sudo journalctl -u lumera-testnet.service -f --no-hostname -o cat
```

### # _wait until the node status becomes "**false**" with this command_
```
lumerad status 2>&1 | jq .sync_info
```

next step validator setup
https://github.com/nodeonline/testnet-node-runner/blob/main/lumera/cli%20cheatsheet.md

