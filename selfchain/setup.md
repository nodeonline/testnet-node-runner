# Self Chain guide
<p align= "center">
<img src="https://github.com/Ariffadilah2017/testnet/blob/main/selfchain/gambar/2.jpeg" width="350" height="250" />

  >you can raed [here](https://blog.selfchain.xyz/self-chain-incentivized-testnet-2/)

# specifications
+ Ubuntu 22
+ CPU 4 core
+ Ram 8 Gb
+ ssd 160 Gb



### Install dependencies Required
```
sudo apt update && sudo apt upgrade -y
```
```
sudo apt-get install git curl build-essential make jq gcc snapd chrony lz4 tmux unzip bc -y
```

### Install GO
```
ver="1.20.4"
cd $HOME
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile
source ~/.bash_profile
go version
```
### Node Installation
```
# Install selfchain through repository
cd $HOME
rm -rf selfchain
git clone https://github.com/chain4energy/c4e-chain.git selfchain
cd selfchain
git checkout 0.2.2
make install
```
>#### replace the `moniker` with the name you want
```
selfchaind init moniker --chain-in self-dev-1
```
```
selfchaind config chain-id self-dev-1
selfchaind config keyring-backend file
```
### Custom Port
```
PORT=30
selfchaind config node tcp://localhost:${PORT}657
```
```
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${PORT}060\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${PORT}660\"%" $HOME/.selfchain/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${PORT}317\"%; s%^address = \":8080\"%address = \":${PORT}080\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${PORT}090\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${PORT}091\"%; s%^address = \"0.0.0.0:8545\"%address = \"0.0.0.0:${PORT}545\"%; s%^ws-address = \"0.0.0.0:8546\"%ws-address = \"0.0.0.0:${PORT}546\"%" $HOME/.selfchain/config/app.toml
```


### Add Genesis File and Addrbook
```
curl -Ls https://raw.githubusercontent.com/hotcrosscom/selfchain-genesis/main/networks/devnet/genesis.json > $HOME/.selfchain/config/genesis.json
curl -Ls https://snapshots.indonode.net/selfchain/addrbook.json > $HOME/.selfchain/config/addrbook.json
```

### Configure Seeds and Peers
```
PEERS="$(curl -sS https://rpc.selfchain-t.indonode.net/net_info | jq -r '.result.peers[] | "(.node_info.id)@(.remote_ip):(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}' | sed -z 's|
|,|g;s|.$||')"
sed -i -e "s|^persistent_peers *=.*|persistent_peers = "$PEERS"|" $HOME/.selfchain/config/config.toml
```
### pruning and Indexer
```
PRUNING="custom"
PRUNING_KEEP_RECENT="100"
PRUNING_INTERVAL="19"

sed -i -e "s/^pruning *=.*/pruning = "$PRUNING"/" $HOME/.selfchain/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = "$PRUNING_KEEP_RECENT"/" $HOME/.selfchain/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = "$PRUNING_INTERVAL"/" $HOME/.selfchain/config/app.toml
sed -i 's|^prometheus *=.*|prometheus = true|' $HOME/.selfchain/config/config.toml
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.selfchain/config/config.toml
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = "0.005uself"/" $HOME/.selfchain/config/app.toml
```
### Set Service file
```
sudo tee /etc/systemd/system/selfchaind.service > /dev/null <<EOF
[Unit]
Description=selfchain testnet node
After=network-online.target
[Service]
User=$USER
ExecStart=$(which selfchaind) start
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable selfchaind
```
### Download Latest Snapshot

```
curl -L undefined | tar -Ilz4 -xf - -C $HOME/.selfchain
[[ -f $HOME/.selfchain/data/upgrade-info.json ]] && cp $HOME/.selfchain/data/upgrade-info.json $HOME/.selfchain/cosmovisor/genesis/upgrade-info.json
```
### Start the Node
```
sudo systemctl restart selfchaind && sudo journalctl -fu selfchaind -o cat
```


