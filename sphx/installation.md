## **SPINX CHAIN SETUP VALIDATOR TESTNET**

<p align= "center">
<img src="https://github.com/nodeonline/testnet-node-runner/blob/main/sphx/logo-sphx.jpg" "width="250" height="150" /><b\>

##### api      : https://api.sphx.nodeonline.xyz
##### rpc      : https://rpc.sphx.nodeonline.xyz
##### explorer : https://explorer.nodeonline.xyz/sphx-testnet




### Hardware requirements
> Recommended Hardware: 2 Cores, 8GB RAM, 200GB of storage (NVME)




### # _Install dependencies Required_
```
sudo apt update && sudo apt upgrade -y
```
```
sudo apt-get install git curl build-essential make jq gcc snapd chrony lz4 tmux unzip bc -y
```


### # _set vars_
> change manual your "wallet","moniker","SPHX_PORT" 
```
echo "export WALLET="wallet"" >> $HOME/.bash_profile
echo "export MONIKER="moniker"" >> $HOME/.bash_profile
echo "export SPHX_PORT="20"" >> $HOME/.bash_profile
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
rm -rf sphx-dev
wget https://github.com/sphx-dev/infra-chain/releases/download/v0.0.5/sphx
chmod +x sphx
mv sphx ~/go/bin
sphxd version #check_versionn
```

### # _Initialize The Node_ 
```
sphxd init $MONIKER --chain-id sphx-testnet
sed -i -e "s|^node *=.*|node = \"tcp://localhost:${SPHX_PORT}657\"|" $HOME/.sphxd/config/client.toml
sed -i -e "s|^keyring-backend *=.*|keyring-backend = \"os\"|" $HOME/.sphxd/config/client.toml
sed -i -e "s|^chain-id *=.*|chain-id = \"sphx-testnet\"|" $HOME/.sphxd/config/client.toml
```

### # _download genesis and addrbook_
```
curl -L https://ss.t.nodeonline.xyz/testnet/sphx/genesis.json > $HOME/.sphxd/config/genesis.json
curl -L https://ss.t.nodeonline.xyz/testnet/sphx/addrbook.json > $HOME/.sphxd/config/addrbook.json
```

### # _Configure Seeds and Peers_ 
```
seeds="29b3dbb76409b6e23d26d1ab84181454323b4c2c@194.238.28.132:11656"
PEERS="$(curl -sS https://rpc.sphx.nodeonline.xyz/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}' | sed -z 's|\n|,|g;s|.$||')"
sed -i -e "s|^seeds *=.*|seeds = '"$SEEDS"'|; s|^persistent_peers *=.*|persistent_peers = '"$PEERS"'|" $HOME/.sphxd/config/config.toml
```

### #_set custom ports in app.toml_
```
sed -i.bak -e "s%:1317%:${SPHX_PORT}317%g;
s%:8080%:${SPHX_PORT}080%g;
s%:9090%:${SPHX_PORT}090%g;
s%:9091%:${SPHX_PORT}091%g;
s%:8545%:${SPHX_PORT}545%g;
s%:8546%:${SPHX_PORT}546%g;
s%:6065%:${SPHX_PORT}065%g" $HOME/.sphxd/config/app.toml
```

### # _set custom ports in config.toml file_
```
sed -i.bak -e "s%:26658%:${SPHX_PORT}658%g;
s%:26657%:${SPHX_PORT}657%g;
s%:6060%:${SPHX_PORT}060%g;
s%:26656%:${SPHX_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${SPHX_PORT}656\"%;
s%:26660%:${SPHX_PORT}660%g" $HOME/.sphxd/config/config.toml
```

### # _Set Minimum Gas Price, Enable Prometheus, and Disable the Indexer_ 
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.025uodis\"|" $HOME/.sphxd/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.sphxd/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.sphxd/config/config.toml
```

### # _Customize Pruning_ 
```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "17"|' \
  $HOME/.sphxd/config/app.toml
```

### # _create service file_ 
```
sudo tee /etc/systemd/system/sphxd.service > /dev/null <<EOF
[Unit]
Description=sphx
After=network-online.target

[Service]
User=$USER
ExecStart=$(which sphxd) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```


### # _Download Latest Snapshot_
```
curl "https://ss.t.nodeonline.xyz/testnet/sphx/ss.t.sphx-latest.tar.lz4" | lz4 -dc - | tar -xf - -C "$HOME/.sphxd"
```



### # _Start service and check the logs_ 
```
sudo systemctl daemon-reload
sudo systemctl enable sphxd.service
sudo systemctl restart sphxd.service && sudo journalctl -u sphxd.service -f --no-hostname -o cat
```

### wait until the node status becomes "false" with this command
```
sphxd status 2>&1 | jq .sync_info
```

[continue validator setup](https://github.com/nodeonline/testnet-node-runner/blob/main/sphx/cli%20cheatsheet.md)

