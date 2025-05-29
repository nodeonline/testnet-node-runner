## **ODISEO CHAIN SETUP VALIDATOR TESTNET**

<p align= "center">
<img src="https://github.com/nodeonline/testnet-node-runner/blob/main/Odiseo/logo-odiseo.jpg" "width="250" height="150" /><b\>

##### api      : https://api.odiseo.nodeonline.xyz
##### rpc      : https://rpc.odiseo.nodeonline.xyz
##### explorer : https://explorer.nodeonline.xyz/odiseo-testnet




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
> change manual your "wallet","moniker","ODISEO_PORT" 
```
echo "export WALLET="wallet"" >> $HOME/.bash_profile
echo "export MONIKER="moniker"" >> $HOME/.bash_profile
echo "export ODISEO_CHAIN_ID="ithaca-1"" >> $HOME/.bash_profile
echo "export ODISEO_PORT="20"" >> $HOME/.bash_profile
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

### # _Install binary_ 
```
cd $HOME
rm -rf Achilles
git clone https://github.com/daodiseomoney/Achilles.git
cd Achilles/achilles
git checkout v1.0.1
make install
achillesd version #check_versionn
```

### # _Initialize The Node_ 
```
achillesd init $MONIKER --chain-id ithaca-1
achillesd config node tcp://localhost:${ODISEO_PORT}657
achillesd config keyring-backend os
achillesd config chain-id ithaca-1
```

### # _download genesis and addrbook_
```
curl -L https://ss.t.nodeonline.xyz/testnet/odiseo/genesis.json > $HOME/.achilles/config/genesis.json
curl -L https://ss.t.nodeonline.xyz/testnet/odiseo/addrbook.json > $HOME/.achilles/config/addrbook.json
```

### # _Configure Seeds and Peers_ 
```
seeds="29b3dbb76409b6e23d26d1ab84181454323b4c2c@194.238.28.132:11656"
PEERS="$(curl -sS https://rpc.odiseo.nodeonline.xyz/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}' | sed -z 's|\n|,|g;s|.$||')"
sed -i -e "s|^seeds *=.*|seeds = '"$SEEDS"'|; s|^persistent_peers *=.*|persistent_peers = '"$PEERS"'|" $HOME/.achilles/config/config.toml
```

### #_set custom ports in app.toml_
```
sed -i.bak -e "s%:1317%:${ODISEO_PORT}317%g;
s%:8080%:${ODISEO_PORT}080%g;
s%:9090%:${ODISEO_PORT}090%g;
s%:9091%:${ODISEO_PORT}091%g;
s%:8545%:${ODISEO_PORT}545%g;
s%:8546%:${ODISEO_PORT}546%g;
s%:6065%:${ODISEO_PORT}065%g" $HOME/.achilles/config/app.toml
```

### # _set custom ports in config.toml file_
```
sed -i.bak -e "s%:26658%:${ODISEO_PORT}658%g;
s%:26657%:${ODISEO_PORT}657%g;
s%:6060%:${ODISEO_PORT}060%g;
s%:26656%:${ODISEO_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${ODISEO_PORT}656\"%;
s%:26660%:${ODISEO_PORT}660%g" $HOME/.achilles/config/config.toml
```

### # _Set Minimum Gas Price, Enable Prometheus, and Disable the Indexer_ 
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.25uodis\"|" $HOME/.achilles/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.achilles/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.achilles/config/config.toml
```

### # _Customize Pruning_ 
```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "17"|' \
  $HOME/.achilles/config/app.toml
```

### # _create service file_ 
```
sudo tee /etc/systemd/system/achillesd.service > /dev/null <<EOF
[Unit]
Description=odiseo
After=network-online.target

[Service]
User=$USER
ExecStart=$(which achillesd) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable achillesd.service
```


### # _Download Latest Snapshot_
```
curl "https://ss.t.nodeonline.xyz/testnet/odiseo/ss.t.odiseo-latest.tar.lz4" | lz4 -dc - | tar -xf - -C "$HOME/.achilles"
```



### # _Start service and check the logs_ 
```
sudo systemctl restart achillesd.service && sudo journalctl -u achillesd.service -f --no-hostname -o cat
```

### wait until the node status becomes "false" with this command
```
achillesd status 2>&1 | jq .sync_info
```

[continue validator setup](https://github.com/nodeonline/testnet-node-runner/blob/main/Odiseo/cli%20cheatsheet.md)


