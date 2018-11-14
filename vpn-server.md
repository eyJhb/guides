# VPN server
## Installing openvpn - pfSense setup

Using this guide for a basis for CA and installing OpenVPN, [here](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-openvpn-server-on-ubuntu-16-04).

```
sudo apt-get update
sudo apt-get install openvpn easy-rsa
cd ~
make-cadir openvpn-ca
cd openvpn-ca
vim vars -> change the buttom KEY_COUNTRY, etc.
#check openssl version, and symlink them
ln -s openssl-1.0.0.cnf openssl.cnf
source vars
./clean-all
./build-ca
./build-key-server server -> no challenge pw
./build-dh
sudo openvpn --genkey --secret keys/ta.key
./build-key client
cd keys
sudo cp ca.crt server.crt server.key ta.key dh2048.pem /etc/openvpn
#you can then copy an example config
gunzip -c /usr/share/doc/openvpn/examples/sample-config-files/server.conf.gz | sudo tee /etc/openvpn/server.conf
#remember to disable systemctl for openvpn if needed..
sudo systemctl disable openvpn
openvpn server.conf
```

Basic things that I edited can be seen below.
- [server2.conf](_media/server2.conf)
- [server2.conf.patch](_media/server2.conf.patch)

The patch could just be applied, and it should work.

READ BELOW! FORWARDING TRAFFIC WILL NOT WORK BEFORE IP_FORWARD AND MASQUERADE...

## iptables for port forwarding
If you want to port forward down the tunnel, use the information below...
Should be fairly straightforward. Remember to use tcpdump etc. to see what is happening.

```
#trying to setup portforwarding from openvpn server to openvpn client (pfsense)
- Server (Debian) WAN IP: x.x.x.x on eth0 - pptpd IP: y.y.y.1 on ppp0 - Client VPN IP: y.y.y.100
## Original
sysctl -w net.ipv4.ip_forward=1
iptables -t nat -I POSTROUTING -o eth0 -s y.y.y.0/24 -j MASQUERADE
iptables -t nat -A PREROUTING -d x.x.x.x -p tcp --dport 6000 -j DNAT --to-dest y.y.y.100:6000
iptables -t nat -A POSTROUTING -d y.y.y.100 -p tcp --dport 6000 -j SNAT --to-source y.y.y.1
#actually used code
## 85.104.89.241 -> external IP of VPN
## 2225 port
## 172.20.0.6 -> OpenVPN client
## 172.20.0.1 -> OpenVPN router
iptables -t nat -A PREROUTING -d 85.104.89.241 -p tcp --dport 2225 -j DNAT --to-dest 172.20.0.6:2225
iptables -t nat -A POSTROUTING -d 172.20.0.6 -p tcp --dport 2225 -j SNAT --to-source 172.20.0.1
```
