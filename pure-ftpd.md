# pure-ftpd
ftps ftp ssl tls pure ftpd 

## Installation
```
apt-get install pure-ftpd
cd /etc/pure-ftpd/conf
echo 2 > TLS # 0 only plain, 1 allow plain and tls, 2 force tls
echo HIGH > TLSCipherSuite
echo no > PAMAuthentication
echo no > UnixAuthentication
echo yes > NoAnonymous
echo "/etc/pure-ftpd/pureftpd.pdb" > PureDB # location of the pure db
ln -s /etc/pure-ftpd/conf/PureDB /etc/pure-ftpd/auth/50pure
# symlink conf and auth togehter, if not symlink it WILL NOT WORK 
systemctl status pure-ftpd.service # should look like ... (below)
# /usr/sbin/pure-ftpd -l puredb:/etc/pure-ftpd/pureftpd.pdb -J HIGH -u 1000 -8 UTF-8 -Y 2 -O clf:/var/log/pure-ftpd/transfer.log -E -B

#add user for virtual users
groupadd ftpgroup #group for ftpusers
#new user with group ftpgroup, homedir /dev/null, shell /etc and user ftpuser
useradd -g ftpgroup -d /dev/null -s /etc ftpuser 

#add virtual user - remember to create dir and chmod it
#create ftp_user under ftpuser with chroot /home/ftpusers/ftp_user
pure-pw useradd ftp_user -u ftpuser -d /home/ftpusers/ftp_user 

#make db
pure-pw mkdb

##setup tls
#generate cert - could use lets encrypt - symlink to /etc/ssl/private/pure-ftpd.pem
openssl req -x509 -nodes -days 7300 -newkey rsa:2048 -keyout /etc/ssl/private/pure-ftpd.pem -out /etc/ssl/private/pure-ftpd.pem

chmod 600 /etc/ssl/private/pure-ftpd.pem
systemctl restart pure-ftpd.service
```

To get it to work, outside network, do the following.

```
#first edit /etc/hosts, and add a alias for your domain, eg.
echo "ftp.domain.com 127.0.0.1" >> /etc/hosts
#add a bind file and specify passive ports, AND OPEN!
echo "ftp.domain.com,21" > /etc/pure-ftpd/conf/Bind
echo "29799 29899" > /etc/pure-ftpd/conf/PassivePortRange
```

# References
- https://download.pureftpd.org/pub/pure-ftpd/doc/README.Virtual-Users
- http://articlebin.michaelmilette.com/setting-up-pure-ftpd-in-ubuntu/
- https://www.howtoforge.com/how-to-configure-pureftpd-to-accept-tls-sessions-on-ubuntu-10.10
- https://www.howtoforge.com/tutorial/pureftpd-tls-on-centos/
- https://download.pureftpd.org/pub/pure-ftpd/doc/README.TLS
