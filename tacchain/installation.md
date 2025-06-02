## **TAC CHAIN SETUP VALIDATOR TESTNET**

<p align= "center">
<img src="https://github.com/nodeonline/testnet-node-runner/blob/main/Tacchain node/logo-Tacchain node.jpg" "width="250" height="150" /><b\>

##### api      : https://api.tac.nodeonline.xyz
##### rpc      : https://rpc.tac.nodeonline.xyz
##### explorer : https://explorer.nodeonline.xyz/tacchain-testnet




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
> change manual your "wallet","moniker","TAC_PORT" 
```
echo "export WALLET="wallet"" >> $HOME/.bash_profile
echo "export MONIKER="your_moniker"" >> $HOME/.bash_profile
echo "export TAC_PORT="18"" >> $HOME/.bash_profile
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
rm -rf tacchain
git clone https://github.com/TacBuild/tacchain.git
cd tacchain
git checkout v0.0.10
make install
```

### # _Initialize The Node_ 
```
tacchaind init $MONIKER --chain-id tacchain_2391-1
sed -i -e "s|^node *=.*|node = \"tcp://localhost:${TAC_PORT}657\"|" $HOME/.tacchaind/config/client.toml
sed -i -e "s|^keyring-backend *=.*|keyring-backend = \"os\"|" $HOME/.tacchaind/config/client.toml
sed -i -e "s|^chain-id *=.*|chain-id = \"tacchain_2391-1\"|" $HOME/.tacchaind/config/client.toml
```

### # _download genesis and addrbook_
```
curl -L https://ss.t.nodeonline.xyz/testnet/tac/genesis.json > $HOME/.tacchaind/config/genesis.json
curl -L https://ss.t.nodeonline.xyz/testnet/tac/addrbook.json > $HOME/.tacchaind/config/addrbook.json
```

### # _Configure Seeds and Peers_ 
```
seeds="c7800679d89a11215d8d4086aa3a6de4286986a5@194.238.28.132:18656"
PEERS="$(curl -sS https://rpc.tac.nodeonline.xyz/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}' | sed -z 's|\n|,|g;s|.$||')"
sed -i -e "s|^seeds *=.*|seeds = '"$SEEDS"'|; s|^persistent_peers *=.*|persistent_peers = '"$PEERS"'|" $HOME/.tacchaind/config/config.toml
```

### #_set custom ports in app.toml_
```
sed -i.bak -e "s%:1317%:${TAC_PORT}317%g;
s%:8080%:${TAC_PORT}080%g;
s%:9090%:${TAC_PORT}090%g;
s%:9091%:${TAC_PORT}091%g;
s%:8545%:${TAC_PORT}545%g;
s%:8546%:${TAC_PORT}546%g;
s%:6065%:${TAC_PORT}065%g" $HOME/.tacchaind/config/app.toml
```

### # _set custom ports in config.toml file_
```
sed -i.bak -e "s%:26658%:${TAC_PORT}658%g;
s%:26657%:${TAC_PORT}657%g;
s%:6060%:${TAC_PORT}060%g;
s%:26656%:${TAC_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${TAC_PORT}656\"%;
s%:26660%:${TAC_PORT}660%g" $HOME/.tacchaind/config/config.toml
```

### # _Set Minimum Gas Price, Enable Prometheus, and Disable the Indexer_ 
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.25utac\"|" $HOME/.tacchaind/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.tacchaind/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.tacchaind/config/config.toml
```

### # _Customize Pruning_ 
```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "17"|' \
  $HOME/.tacchaind/config/app.toml
```

### # _create service file_ 
```
sudo tee /etc/systemd/system/tacchaind.service > /dev/null <<EOF
[Unit]
Description=Tacchain node
After=network-online.target

[Service]
User=$USER
ExecStart=$(which tacchaind) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable tacchaind.service
```


### # _Download Latest Snapshot_
```
curl "https://ss.t.nodeonline.xyz/testnet/tac/ss.t.tacchain-latest.tar.lz4" | lz4 -dc - | tar -xf - -C "$HOME/.tacchaind"
```



### # _Start service and check the logs_ 
```
sudo systemctl restart tacchaind.service && sudo journalctl -u tacchaind.service -f --no-hostname -o cat
```

### wait until the node status becomes "false" with this command
```
tacchaind status 2>&1 | jq .sync_info
```

[next setup validator](https://github.com/nodeonline/testnet-node-runner/blob/main/tacchain/cli%20cheatset.md)



