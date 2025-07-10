# **CLI Cheatsheet**

## Create Wallet

### # Create New Wallet
> save your mnemonic phrase
```
 xosd keys add wallet
```
### # Recover Existing Keys Use
> Change 'wallet' to your wallet name
```
xosd keys add wallet --recover
```
> Change wallet to your wallet name
### # Export PK to PK EVM
```
xosd keys unsafe-export-eth-key wallet
```
### # List All Wallet
```
 xosd keys list
```

### # Delete Wallet
```
xosd keys delete wallet
```
### # Export Wallet
```
xosd keys export wallet
```
### # Import Key
```
xosd keys import wallet.backup
```
### # Check Balance Wallet
```
xosd q bank balances $(xosd keys show wallet -a)
```

## # Setup Validator
check your node if sync continue to create validator
```
xosd status 2>&1 | jq .sync_info
```
`"catching_up": false`

### # Creat Validator
>fill in your 'moniker' 'identity' 'details' 'website'
```
xosd tx staking create-validator \
--amount=10000000000000000000axos \
--pubkey=$(xosd tendermint show-validator) \
--moniker="$MONIKER" \
--identity="" \
--details="" \
--website="" \
--chain-id=xos_1267-1 \
--commission-rate=0.05 \
--commission-max-rate=0.2 \
--commission-max-change-rate=0.05 \
--min-self-delegation=1 \
--gas-prices 14000000000000000axos \
--gas=auto \
--gas-adjustment=1.5 \
--from $WALLET \
-y
```

### # Edit Existing Validator
```
xosd tx staking edit-validator \
--new-moniker "moniker" \
--chain-id xos_1267-1 \
--gas auto \
--gas-adjustment 1.5 \
--gas-prices 14000000000000000axos \
--from $WALLET \
-y
```

### # Validator info
```
xosd status 2>&1 | jq .ValidatorInfo
```

### # Validator Details
```
xosd q staking validator $(xosd keys show $WALLET --bech val -a)
```

### # Unjail Validator
```
xosd tx slashing unjail --from $WALLET --chain-id xos_1267-1 --gas-prices=0.25axos --gas-adjustment=1.5 --gas=auto
```

### # Check Jailed Reason
```
xosd query slashing signing-info $(xosd tendermint show-validator)
```

### # Check Validator key
```
[[ $(xosd q staking validator $VALOPER_ADDRESS_XOS -oj | jq -r .consensus_pubkey.key) = $(xosd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```



### # Delegate Token to your own validator
```
xosd tx staking delegate $(xosd keys show wallet --bech val -a) 300000000000000000axos --from wallet --chain-id xos_1267-1 --fees 14000000000000000axos
```


# Service operations

Check logs
```
sudo journalctl -u xosd.service -f --no-hostname -o cat
```
Sync info
```
xosd status 2>&1 | jq .Sync_Info
```
Node info
```
xosd status 2>&1 | jq .NodeInfo
```


Start service
```
sudo systemctl start xosd
```
Stop service
```
sudo systemctl stop xosd
```
Restart service
```
sudo systemctl restart xosd
```
Check service status
```
sudo systemctl status xosd
```
Reload services
```
sudo systemctl daemon-reload
```
Enable Service
```
sudo systemctl enable xosd
```
Disable Service
```
sudo systemctl disable xosd
```


# Delete Node
Before deleting, it is best to backup the validator key first.
```
sudo systemctl stop xosd
sudo systemctl disable xosd
sudo rm /etc/systemd/system/xosd.service
sudo systemctl daemon-reload
rm -f $(which xosd)
rm -rf .xosd
```
