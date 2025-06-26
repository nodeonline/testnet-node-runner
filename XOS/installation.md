## **XOS CHAIN SETUP VALIDATOR TESTNET**

<p align= "center">
<img src="https://avatars.githubusercontent.com/u/189432645?s=200&v=4" "width="250" height="150" /><b\>

##### api      : https://api.xos.nodeonline.xyz
##### rpc      : https://rpc.xos.nodeonline.xyz
##### explorer : https://explorer.nodeonline.xyz/xos-testnet




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
> change manual your "wallet","moniker","XOS_PORT" 
```
echo "export WALLET="wallet"" >> $HOME/.bash_profile
echo "export MONIKER="nodeonline"" >> $HOME/.bash_profile
echo "export XOS_PORT="37"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```


### # _Install GO,If you need_ 
```
ver="1.23.4"
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
rm -rf node
git clone https://github.com/xos-labs/node.git
cd node
make install
xosd version
```

### # _Initialize The Node_ 
```
xosd init $MONIKER --chain-id xos_1267-1
sed -i -e "s|^node *=.*|node = \"tcp://localhost:${XOS_PORT}657\"|" $HOME/.xosd/config/client.toml
sed -i -e "s|^keyring-backend *=.*|keyring-backend = \"os\"|" $HOME/.xosd/config/client.toml
sed -i -e "s|^chain-id *=.*|chain-id = \"xos_1267-1\"|" $HOME/.xosd/config/client.toml
```

### # _download genesis and addrbook_
```
curl -L https://ss.t.nodeonline.xyz/testnet/xos/genesis.json > $HOME/.xosd/config/genesis.json
curl -L https://ss.t.nodeonline.xyz/testnet/xos/addrbook.json > $HOME/.xosd/config/addrbook.json
```

### # _Configure Seeds and Peers_  jgn dulu di pake
```
seeds="29b3dbb76409b6e23d26d1ab84181454323b4c2c@194.238.28.132:11656"
PEERS="$(curl -sS https://rpc.xos.nodeonline.xyz/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}' | sed -z 's|\n|,|g;s|.$||')"
sed -i -e "s|^seeds *=.*|seeds = '"$SEEDS"'|; s|^persistent_peers *=.*|persistent_peers = '"$PEERS"'|" $HOME/.xosd/config/config.toml
```

### #_set custom ports in app.toml_
```
sed -i.bak -e "s%:1317%:${XOS_PORT}317%g;
s%:8080%:${XOS_PORT}080%g;
s%:9090%:${XOS_PORT}090%g;
s%:9091%:${XOS_PORT}091%g;
s%:8545%:${XOS_PORT}545%g;
s%:8546%:${XOS_PORT}546%g;
s%:6065%:${XOS_PORT}065%g" $HOME/.xosd/config/app.toml
```

### # _set custom ports in config.toml file_
```
sed -i.bak -e "s%:26658%:${XOS_PORT}658%g;
s%:26657%:${XOS_PORT}657%g;
s%:6060%:${XOS_PORT}060%g;
s%:26656%:${XOS_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${XOS_PORT}656\"%;
s%:26660%:${XOS_PORT}660%g" $HOME/.xosd/config/config.toml
```

### # _Set Minimum Gas Price, Enable Prometheus, and Disable the Indexer_ 
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.025axos\"|" $HOME/.xosd/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.xosd/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.xosd/config/config.toml
```

### # _Customize Pruning_ 
```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "17"|' \
  $HOME/.xosd/config/app.toml
```

### # _create service file_ 
```
sudo tee /etc/systemd/system/xosd.service > /dev/null <<EOF
[Unit]
Description=xos
After=network-online.target

[Service]
User=$USER
ExecStart=$(which xosd) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable xosd.service
```


### # _Download Latest Snapshot_
```
curl "https://ss.t.nodeonline.xyz/testnet/xos/ss.t.xos-latest.tar.lz4" | lz4 -dc - | tar -xf - -C "$HOME/.xosd"
```



### # _Start service and check the logs_ 
```
sudo systemctl restart xosd.service && sudo journalctl -u xosd.service -f --no-hostname -o cat
```

### wait until the node status becomes "false" with this command
```
xosd status 2>&1 | jq .sync_info
```

[continue validator setup](https://github.com/nodeonline/testnet-node-runner/blob/main/xos/cli%20cheatsheet.md)
