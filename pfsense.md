# pfSense
## pfSense under VirtualBox 
To get pfSense to run under `Virtualbox`, use `freebsd x64`.
Use `NAT` as `adapter 1`, and create a `host-only` adapter for network two.

The host only network must `not` have `DHCP enabled` (pfSense will take care of that)
and the address of the network MUST NOT be `xxx.xxx.xxx.1`, but rather `xxx.xxx.xxx.2`.
For example `192.168.57.2`, that should get everything up and running!

## Setup VPN client
This will not cover the basic configuration, really quick.

### Add certs for connecting
First we need to add the certs to connect!
```
System -> Cert. Manager -> CAs -> Add
  - just enter the certificate data - ca.crt

System -> Cert. Manager -> Certificates -> Add
  - Enter client.crt (extra data before begin included) first, and client.key
```

### Add OpenVPN client
```
VPN -> OpenVPN -> Clients -> Add
    - server/host
    - server/port
    - username/password blank
    - Use a TLS key (do not generate)
    - Peer Certificate Authority (from previous step)
    - Client Certificate (from previous step)
    - Encrytion Algorithm (math server)
    - Auth digest algorithm (math server)
    - Gateway creation - both
    - Verbosity level - 5 (you properly made an error)
```

### The rest
Interfaces
```
Interfaces -> Interface Assignments
    - Add interface
Interfaces -> OPT1 (the one just added)
    - Enable
    - Description - ifvpn
    - IPv4 Configuration type - DHCP
```

NAT
```
Firewall -> NAT -> Output
    - Manual Outbound NAT rule generation
    - Copy all rules with the dobble page,
      and change Interface to IFVPN -> save
```

Start the OpenVPN
```
Status -> OpenVPN -> Start
    - Check if dashboard if IP is given
```

Now everything should work!

### Port forwarding
Create a forward as usual using `NAT -> Port Forwarding`, and use `ifvpn` as interface.

### Block all other traffic - floating rules
This progress will tag ALL outgoing traffic from LAN (IPv4 and IPv6) with a tag, so a floating rule can drop it.

```
Firewall -> Rules -> LAN 
    - Apply on each default allo lan to any rule
    - Display advanced -> Tag -> NO_WAN_EGRESS
Firewall -> Rules -> Floating -> Add
    - Action         - Reject
    - Interface      - WAN
    - Direction      - out
    - Address Family - IPv4+IPv6
    - Protocol       - Any
    - Tagged         - NO_WAN_EGRESS
```

Should now block all traffic, unless it is through the VPN.

### White list traffic
To allow some traffic to bypass the VPN; eg. intranet traffic or dr.dk, we need to create an alias, and some rules.

```
Firewall -> Aliases -> Add
    - name        - vpn-bypass
    - description - Hosts that bypass VPN
    - IP/FQDN     - ip/hostname
```

Now add firewall LAN rules

```
Firewall -> Rules -> LAN -> Add
    - action      - pass
    - interface   - LAN
    - protocol    - any
    - destination - single host or alias (name of alias)
    - gateway     - WAN gateway (NOT VPN!)
Firewall -> Rules -> Floating -> Add
    - action         - pass
    - quick          - true
    - interface      - wan
    - address family - IPv4+IPv6
    - protocol       - any
    - destination    - single host or alias (name of alias)
    - description    - VPN bypass
```

Save and reload! pfSense might have to restart...


## Debug help with rules
```
Diagnostics -> Packet Capture
    - See what packets come in and out
Firewall -> Rules -> Enable logging of packets
    - Can then be seen in status -> system logs -> firewall
    - used to debug the above rules where NAT translation failed...
```

## Backup script
```
#!/bin/bash
username="backup"
password="password"
router_ip="https://192.168.1.1"
backup_location="/tmp"

csrf=$(wget -qO- --keep-session-cookies --save-cookies cookies.txt \
  --no-check-certificate $router_ip/diag_backup.php \
  | grep "name='__csrf_magic'" | sed 's/.*value="\(.*\)".*/\1/')

csrf=$(wget -qO- --keep-session-cookies --load-cookies cookies.txt \
  --save-cookies cookies.txt --no-check-certificate \
  --post-data "login=Login&usernamefld=$username&passwordfld=$password&__csrf_magic=$csrf" \
  $router_ip/diag_backup.php  | grep "name='__csrf_magic'" \
  | sed 's/.*value="\(.*\)".*/\1/')

wget --keep-session-cookies --load-cookies cookies.txt --no-check-certificate \
  --post-data "download=download&donotbackuprrd=yes&__csrf_magic=$csrf" \
  $router_ip/diag_backup.php -O $backup_location/pfsense-`date +%Y-%m-%d`.xml

rm cookies.txt
```
