# **CLI Cheatsheet**

## Create Wallet

### # Create New Wallet
> save your mnemonic phrase
```
tacchaind  keys add wallet
```
### # Recover Existing Keys Use
> submit your mnemonic phrase existing
```
tacchaind  keys add wallet --recover
```

### # List All Wallet
```
tacchaind  keys list
```

### # Delete Wallet
```
tacchaind  keys delete wallet
```
### # Export Wallet
```
tacchaind  keys export wallet
```
### # Import Key
```
tacchaind  keys import wallet.backup
```
### # Check Balance Wallet
```
tacchaind  q bank balances $(tacchaind  keys show wallet -a)
```

## # Setup Validator
check your node if sync continue to create validator
```
tacchaind  status 2>&1 | jq .sync_info
```
`"catching_up": false`


### # Creat Validator
>fill in your 'moniker' 'identity' 'details' 'website'
```
tacchaind  tx staking create-validator <(cat <<EOF
{
  "pubkey": $(tacchaind tendermint show-validator),
  "amount": "1000000utac",
  "moniker": "YOUR_MONIKER",
  "identity": "YOUR_INDENTIRY",
  "website": "YOUR_WEB",
  "details": "YOUR_DETAIL",
  "commission-rate": "0.05",
  "commission-max-rate": "0.20",
  "commission-max-change-rate": "0.05",
  "min-self-delegation": "1"
}
EOF
) \
--chain-id tacchain_2391-1 \
--from $WALLET \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.025utac \
-y
```

### # Edit Existing Validator
```
tacchaind  tx staking edit-validator \
--moniker": "YOUR_MONIKER",
--identity": "YOUR_INDENTIRY",
--website": "YOUR_WEB",
--details": "YOUR_DETAIL",
--chain-id tacchain_2391-1 \
--commission-rate 0.05 \
--from $WALLET \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.025utac \
-y
```

### # Validator info
```
tacchaind  status 2>&1 | jq .ValidatorInfo
```

### # Validator Details
```
tacchaind  q staking validator $(tacchaind  keys show $WALLET --bech val -a)
```

### # Unjail Validator
```
tacchaind tx slashing unjail --from wallet --chain-id tacchain_2391-1 --gas auto --gas-adjustment 1.5 --gas-prices 100000000000utac -y
```

### # Check Jailed Reason
```
tacchaind  query slashing signing-info $(tacchaind  tendermint show-validator)
```

### # Check Validator key
```
[[ $(tacchaind  q staking validator $VALOPER_ADDRESS_TAC -oj | jq -r .consensus_pubkey.key) = $(tacchaind  status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```


# Service operations

### # Check logs
```
sudo journalctl -u tacchaind -f -o cat
```
### # Sync info
```
tacchaind  status 2>&1 | jq .SyncInfo
```
### # Node info
```
tacchaind  status 2>&1 | jq .NodeInfo
```
### # start service
```
sudo systemctl start tacchaind 
```
### # stop service
```
sudo systemctl stop tacchaind 
```
### # restart service
```
sudo systemctl restart tacchaind 
```
### # check service status
```
sudo systemctl status tacchaind 
```
### # reload services
```
sudo systemctl daemon-reload
```
### # enable Service
```
sudo systemctl enable tacchaind 
```
### # disable Service
```
sudo systemctl disable tacchaind 
```


# Delete Node
before deleting, it is best to backup the validator key first.
```
sudo systemctl stop tacchaind 
sudo systemctl disable tacchaind 
sudo rm /etc/systemd/system/tacchaind.service
sudo systemctl daemon-reload
rm -f $(which tacchaind )
rm -rf .tacchaind
```
