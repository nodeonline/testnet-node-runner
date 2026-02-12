## **FAIRBLOCK CHAIN SETUP VALIDATOR TESTNET**

<p align= "center">
<img src="https://github.com/nodeonline/testnet-node-runner/blob/main/Fairblock/Logo.png" "width="250" height="150" /><b\>



##### api      : https://api.fairblock.nodeonline.xyz
##### rpc      : https://rpc.fairblock.nodeonline.xyz
##### explorer : https://explorer.nodeonline.xyz/fairblock-testnet




### Hardware requirements
> Recommended Hardware: 4 Cores, 8GB RAM, 200GB of storage (NVME)




### # _Install dependencies Required_
```
sudo apt update && sudo apt upgrade -y
```
```
sudo apt-get install git curl build-essential make jq gcc snapd chrony lz4 tmux unzip bc -y
```


### # _set vars_
> change manual your "wallet","moniker","FAIRBLOCK_PORT" 
```
echo "export WALLET="wallet"" >> $HOME/.bash_profile
echo "export MONIKER="moniker"" >> $HOME/.bash_profile
echo "export FAIRBLOCK_CHAIN_ID="fairyring-testnet-1"" >> $HOME/.bash_profile
echo "export FAIRBLOCK_PORT="20"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```


### # _Install GO,If you need_ 
```
ver="1.22.5"
cd $HOME
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile
source ~/.bash_profile
go version
```

### # _Install binary_ 
```
cd $HOME
rm -rf fairyring
git clone https://github.com/Fairblock/fairyring.git
cd fairyring
git checkout v0.10.2
make install
fairyringd version
```

### # _Initialize The Node_ 
```
fairyringd init $MONIKER --chain-id fairyring-testnet-1
sed -i -e "s|^node *=.*|node = \"tcp://localhost:${FAIRBLOCK_PORT}657\"|" $HOME/.fairyring/config/client.toml
sed -i -e "s|^keyring-backend *=.*|keyring-backend = \"os\"|" $HOME/.fairyring/config/client.toml
sed -i -e "s|^chain-id *=.*|chain-id = \"fairyring-testnet-1\"|" $HOME/.fairyring/config/client.toml
```

### # _download genesis and addrbook_
```
curl -L https://ss.t.nodeonline.xyz/testnet/fairblock/genesis.json > $HOME/.fairyring/config/genesis.json (SOON)
curl -L https://ss.t.nodeonline.xyz/testnet/fairblock/addrbook.json > $HOME/.fairyring/config/addrbook.json (SOON)
```

### # _Configure Seeds and Peers_ 
```
seeds="29b3dbb76409b6e23d26d1ab84181454323b4c2c@194.238.28.132:11656"
PEERS="$(curl -sS https://rpc.fairblock.nodeonline.xyz/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}' | sed -z 's|\n|,|g;s|.$||')"
sed -i -e "s|^seeds *=.*|seeds = '"$SEEDS"'|; s|^persistent_peers *=.*|persistent_peers = '"$PEERS"'|" $HOME/.fairyring/config/config.toml
```

### #_set custom ports in app.toml_
```
sed -i.bak -e "s%:1317%:${FAIRBLOCK_PORT}317%g;
s%:8080%:${FAIRBLOCK_PORT}080%g;
s%:9090%:${FAIRBLOCK_PORT}090%g;
s%:9091%:${FAIRBLOCK_PORT}091%g;
s%:8545%:${FAIRBLOCK_PORT}545%g;
s%:8546%:${FAIRBLOCK_PORT}546%g;
s%:6065%:${FAIRBLOCK_PORT}065%g" $HOME/.fairyring/config/app.toml
```

### # _set custom ports in config.toml file_
```
sed -i.bak -e "s%:26658%:${FAIRBLOCK_PORT}658%g;
s%:26657%:${FAIRBLOCK_PORT}657%g;
s%:6060%:${FAIRBLOCK_PORT}060%g;
s%:26656%:${FAIRBLOCK_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${FAIRBLOCK_PORT}656\"%;
s%:26660%:${FAIRBLOCK_PORT}660%g" $HOME/.fairyring/config/config.toml
```

### # _Set Minimum Gas Price, Enable Prometheus, and Disable the Indexer_ 
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.25uodis\"|" $HOME/.fairyring/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.fairyring/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.fairyring/config/config.toml
```

### # _Customize Pruning_ 
```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "17"|' \
  $HOME/.fairyring/config/app.toml
```

### # _create service file_ 
```
sudo tee /etc/systemd/system/fairyringd.service > /dev/null <<EOF
[Unit]
Description=fairblock
After=network-online.target

[Service]
User=$USER
ExecStart=$(which fairyringd) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable fairyringd.service
```


### # _Download Latest Snapshot_
```
curl "https://ss.t.nodeonline.xyz/testnet/fairblock/ss.t.fairblock-latest.tar.lz4" | lz4 -dc - | tar -xf - -C "$HOME/.fairyring"
```



### # _Start service and check the logs_ 
```
sudo systemctl restart fairyringd.service && sudo journalctl -u fairyringd.service -f --no-hostname -o cat
```

### wait until the node status becomes "false" with this command
```
fairyringd status 2>&1 | jq .sync_info
```

[continue validator setup](https://github.com/nodeonline/testnet-node-runner/blob/main/fairblock/cli%20cheatsheet.md)



