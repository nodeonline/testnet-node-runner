### # _Install dependencies Required_
```
sudo apt update && sudo apt upgrade -y
```
```
sudo apt-get install git curl build-essential make jq gcc snapd chrony lz4 tmux unzip bc -y
```

















### # _set vars_
> change manual your "wallet","moniker","LUMERA_PORT" 
```
echo "export WALLET="wallet"" >> $HOME/.bash_profile
echo "export MONIKER="moniker"" >> $HOME/.bash_profile
echo "export LUMERA_CHAIN_ID="lumera-testnet-1"" >> $HOME/.bash_profile
echo "export LUMERA_PORT="20"" >> $HOME/.bash_profile
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

# Download project binaries
```
mkdir -p $HOME/.lumera/cosmovisor/genesis/bin
wget -O $HOME/.lumera/cosmovisor/genesis/bin/lumerad https://ss.t.nodeonline.xyz/testnet/lumera/lumerad
chmod +x $HOME/.lumera/cosmovisor/genesis/bin/lumerad
```
```
wget -O /usr/lib/libwasmvm.x86_64.so https://ss.t.nodeonline.xyz/testnet/lumera/libwasmvm.x86_64.so
```
# Create application symlinks
```
ln -s $HOME/.lumera/cosmovisor/genesis $HOME/.lumera/cosmovisor/current -f
sudo ln -s $HOME/.lumera/cosmovisor/current/bin/lumerad /usr/local/bin/lumerad -f
```

# Install Cosmovisor and create a service

# Download and install Cosmovisor
```
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.6.0
```


# Create service
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

# Initialize the node & Set node config
```
lumerad init $MONIKER --chain-id lumera-testnet-1
sed -i -e "s|^node *=.*|node = \"tcp://localhost:${LUMERA_PORT}957\"|" $HOME/.lumera/config/client.toml
sed -i -e "s|^keyring-backend *=.*|keyring-backend = \"os\"|" $HOME/.lumera/config/client.toml
sed -i -e "s|^chain-id *=.*|chain-id = \"lumera-testnet-1\"|" $HOME/.lumera/config/client.toml
```

# Download genesis and addrbook
```
curl -Ls https://ss.t.nodeonline.xyz/testnet/lumera/genesis.json > $HOME/.lumera/config/genesis.json
curl -Ls https://ss.t.nodeonline.xyz/testnet/lumera/addrbook.json > $HOME/.lumera/config/addrbook.json
```

# Configure Seeds and Peers
```
seeds="2c7733576b4c131464f78384f854c842052656c6@194.238.28.132:16656"
PEERS="$(curl -sS https://rpc.lumera.nodeonline.xyz/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}' | sed -z 's|\n|,|g;s|.$||')"
sed -i -e "s|^seeds *=.*|seeds = '"$SEEDS"'|; s|^persistent_peers *=.*|persistent_peers = '"$PEERS"'|" $HOME/.lumera/config/config.toml
```
















