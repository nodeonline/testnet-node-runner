# Guide install pingpong node

Update Package & install docker 

```
sudo apt update && sudo apt install -y apt-transport-https ca-certificates curl software-properties-common && curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - && sudo add-a>
```


```
cd $HOME && wget https://pingpong-build.s3.ap-southeast-1.amazonaws.com/linux/latest/PINGPONG && chmod +x PINGPONG
```

### create screen

```
screen -S pingpong
```

registration and klik your devices [pingpong](https://harvester.pingpong.build/devices)


### submit your your_device_id & run

```
./PINGPONG --key your_device_id
```
##### `example`
```
./PINGPONG --key 5780482930c5adcb695681e37438916015e90ab18e0e7a2e55eaf01707bbcbdb
```


> cek again your devices [pingpong](https://harvester.pingpong.build/devices)

