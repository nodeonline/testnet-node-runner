# SONARIC AI NODE

<p align= "center">
<img src="https://github.com/Ariffadilah2017/testnet/blob/main/sonaric/logo.png" width="150" height="150" />

# specifications
<p align= "left">
<img src="https://github.com/Ariffadilah2017/testnet/blob/main/sonaric/Screenshot 2024-08-01 201432.png" width="350" height="200" />



## Install sonaric
```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/monk-io/sonaric-install/main/linux-install-sonaric.sh)"
```
>#### check your install

## check your install node 
```
sonaric node-info
```


>#### replace the `your-vps-ip` with your ip vps
#check your install node 
```
ssh -L 127.0.0.1:44003:127.0.0.1:44003 -L 127.0.0.1:44004:127.0.0.1:44004 -L 127.0.0.1:44005:127.0.0.1:44005 -L 127.0.0.1:44006:127.0.0.1:44006 user@your-vps-ip
```
submit password vps

## Change node nickname
```
sonaric node-rename -n my-node
```

## Restart service
```
systemctl restart sonaricd
```

## save your node.indentity
```
cat node.identity
```

# backup

## export node.indentity
```
sonaric identity-export -o node.identity
```
## import node.indentity
```
sonaric identity-import -i node.identity
```

## cek point
```
sonaric points
```




## cek your node in web https://tracker.sonaric.xyz/
>you can raed [here](https://docs.sonaric.xyz/installation/vps.html)


