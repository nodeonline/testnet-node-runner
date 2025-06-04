# **CLI Cheatsheet**

## Create Wallet

### # Create New Wallet
> save your mnemonic phrase
```
lumerad keys add wallet
```
### # Recover Existing Keys Use
> submit your mnemonic phrase existing
```
lumerad keys add wallet --recover
```

### # List All Wallet
```
lumerad keys list
```

### # Delete Wallet
```
lumerad keys delete wallet
```
### # Export Wallet
```
lumerad keys export wallet
```
### # Import Key
```
lumerad keys import wallet.backup
```
### # Check Balance Wallet
```
lumerad q bank balances $(lumerad keys show wallet -a)
```

## # Setup Validator
check your node if sync continue to create validator
```
lumerad status 2>&1 | jq .sync_info
```
`"catching_up": false`

### # Creat Validator
>fill in your 'moniker' 'identity' 'details' 'website'
```
lumerad tx staking create-validator <(cat <<EOF
{
  "pubkey": $(lumerad comet show-validator),
  "amount": "1000000ulume",
  "moniker": "$MONIKER",
  "identity": "",
  "website": "",
  "details": "",
  "commission-rate": "0.05",
  "commission-max-rate": "0.20",
  "commission-max-change-rate": "0.05",
  "min-self-delegation": "1"
}
EOF
) \
--chain-id lumera-testnet-1 \
--from $WALLET \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.025ulume \
-y
```

### # Edit Existing Validator
```
lumerad tx staking edit-validator \
--new-moniker "$MONIKER" \
--identity "" \
--details "" \
--website "" \
--chain-id lumera-testnet-1 \
--commission-rate 0.05 \
--from $WALLET \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.025ulume \
-y
```

### # Validator info
```
lumerad status 2>&1 | jq .ValidatorInfo
```

### # Validator Details
```
lumerad q staking validator $(lumerad keys show $WALLET --bech val -a)
```

### # Unjail Validator
```
lumerad tx slashing unjail --from $WALLET --chain-id ithaca-1 --gas-prices=0.25ulume --gas-adjustment=1.5 --gas=auto
```

### # Check Jailed Reason
```
lumerad query slashing signing-info $(lumerad tendermint show-validator)
```

### # Check Validator key
```
[[ $(lumerad q staking validator $VALOPER_ADDRESS_LUMERA -oj | jq -r .consensus_pubkey.key) = $(lumerad status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```


# Service operations

### # Check logs
```
sudo journalctl -u lumerad -f
```
### # Sync info
```
lumerad status 2>&1 | jq .sync.info
```
### # Node info
```
lumerad status 2>&1 | jq .node_info
```
### # start service
```
sudo systemctl start lumerad
```
### # stop service
```
sudo systemctl stop lumerad
```
### # restart service
```
sudo systemctl restart lumerad
```
### # check service status
```
sudo systemctl status lumerad
```
### # reload services
```
sudo systemctl daemon-reload
```
### # enable Service
```
sudo systemctl enable lumerad
```
### # disable Service
```
sudo systemctl disable lumerad
```


# Delete Node
before deleting, it is best to backup the validator key first.
```
sudo systemctl stop lumerad
sudo systemctl disable lumerad
sudo rm /etc/systemd/system/lumera-testnet.service
sudo systemctl daemon-reload
rm -f $(which lumerad)
rm -rf .lumera
```
