# **CLI Cheatsheet**

## Create Wallet

### # Create New Wallet
> save your mnemonic phrase
```
 achillesd keys add wallet
```
### # Recover Existing Keys Use
> Change 'wallet' to your wallet name
```
### # achillesd keys add wallet --recover
```
> Change wallet to your wallet name

### # List All Wallet
```
 achillesd keys list
```

### # Delete Wallet
```
achillesd keys delete wallet
```
### # Export Wallet
```
achillesd keys export wallet
```
### # Import Key
```
achillesd keys import wallet.backup
```
### # Check Balance Wallet
```
achillesd q bank balances $(achillesd keys show wallet -a)
```

## # Setup Validator
check your node if sync continue to create validator
```
achillesd status 2>&1 | jq .sync_info
```
`"catching_up": false`

### # Creat Validator
>fill in your 'moniker' 'identity' 'details' 'website'
```
achillesd tx staking create-validator \
--amount=1000000uodis \
--pubkey=$(achillesd tendermint show-validator) \
--moniker="$MONIKER" \
--identity="" \
--details="" \
--website="" \
--chain-id=ithaca-1 \
--commission-rate=0.05 \
--commission-max-rate=0.2 \
--commission-max-change-rate=0.05 \
--min-self-delegation=1 \
--gas-prices 0.25uodis \
--gas=auto \
--gas-adjustment=1.5 \
--from $WALLET \
-y
```

### # Edit Existing Validator
```
achillesd tx staking edit-validator \
--new-moniker "$MONIKER" \
--identity "" \
--details "" \
--chain-id ithaca-1 \
--commission-rate 0.1 \
--gas auto --gas-adjustment 1.5 \
--gas-prices 0.25uodis \
--from $WALLET \
-y
```

### # Validator info
```
achillesd status 2>&1 | jq .ValidatorInfo
```

### # Validator Details
```
achillesd q staking validator $(achillesd keys show $WALLET --bech val -a)
```

### # Unjail Validator
```
achillesd tx slashing unjail --from $WALLET --chain-id ithaca-1 --gas-prices=0.25uodis --gas-adjustment=1.5 --gas=auto
```

### # Check Jailed Reason
```
achillesd query slashing signing-info $(achillesd tendermint show-validator)
```

### # Check Validator key
```
[[ $(achillesd q staking validator $VALOPER_ADDRESS_odiseo -oj | jq -r .consensus_pubkey.key) = $(achillesd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```


# Service operations

Check logs
```
sudo journalctl -u achillesd -f
```
Sync info
```
achillesd status 2>&1 | jq .SyncInfo
```
Node info
```
achillesd status 2>&1 | jq .NodeInfo
```


Start service
```
sudo systemctl start achillesd
```
Stop service
```
sudo systemctl stop achillesd
```
Restart service
```
sudo systemctl restart achillesd
```
Check service status
```
sudo systemctl status achillesd
```
Reload services
```
sudo systemctl daemon-reload
```
Enable Service
```
sudo systemctl enable achillesd
```
Disable Service
```
sudo systemctl disable achillesd
```


# Delete Node
Before deleting, it is best to backup the validator key first.
```
sudo systemctl stop achillesd
sudo systemctl disable achillesd
sudo rm /etc/systemd/system/achillesd.service
sudo systemctl daemon-reload
rm -f $(which achillesd)
rm -rf .achilles
```
