# iproute2
## Changing metric
```
#get information
sudo route -n
sudo route add -net default gw 172.17.192.1 netmask 0.0.0.0 dev wlan0 metric 1
```
