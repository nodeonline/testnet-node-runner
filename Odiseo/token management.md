# Token Management

### # Withdraw Rewards
```
achillesd tx distribution withdraw-all-rewards --from $WALLET --chain-id ithaca-1 --gas-prices=0.25uodis --gas-adjustment=1.5 --gas=auto
```
### # Withdraw Rewards with Comission
```
achillesd tx distribution withdraw-rewards $(achillesd keys show $WALLET --bech val -a) --commission --from $WALLET --chain-id ithaca-1 --gas-prices=0.25uodis --gas-adjustment=1.5 --gas=auto
```
### # Delegate Token to your own validator
```
achillesd tx staking delegate $(achillesd keys show $WALLET --bech val -a) 1000000uodis --from $WALLET --chain-id ithaca-1 --gas-prices=0.25uodis --gas-adjustment=1.5 --gas=auto
```
### # Delegate Token to other validator
```
achillesd staking redelegate $(achillesd keys show $WALLET --bech val -a) <TO_VALOPER_ADDRESS> 1000000uodis --from $WALLET --chain-id ithaca-1 --gas-prices=0.25uodis --gas-adjustment=1.5 --gas=auto
```
### # Unbond Token from your validator
```
achillesd tx staking unbond $(achillesd keys show $WALLET --bech val -a) 1000000uodis --from $WALLET --chain-id ithaca-1 --gas-prices=0.25uodis --gas-adjustment=1.5 --gas=auto
```
### # Send Token to another wallet
```
achillesd tx bank send wallet <TO_WALLET_ADDRESS> 1000000uodis --from $WALLET --chain-id ithaca-1 --gas-prices=0.25uodis --
```

# Governance

### # View proposal
```
achillesd query gov proposal 1
```


### # Vote
> _change yes or no_
```
achillesd tx gov vote 1 yes --from $WALLET --chain-id ithaca-1  --gas auto --gas-adjustment 1.5 --fees 65000uodis -y
```

