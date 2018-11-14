# SSH - TOTP (Time-based One-time Password algorithm)

## Installation

```
apt-get install libpam-oath oathtool
```

Config users in `/etc/users.oath`.
```
#generate secret seed, add user to users.oath, make readable only for root, unset key
KEY=$( head -c 1024 /dev/urandom | openssl sha1 | awk '{ print $2 }' )
echo "HOTP/T30/6 username - ${KEY}" >> /etc/users.oath
chmod 600 /etc/users.oath
unset KEY
```

Edit `/etc/pam.d/sshd`.
```
#comment include common-auth
# @include common-auth
#put this above instead - keep common-auth, to also ask for password.
auth requisite pam_oath.so usersfile=/etc/users.oath window=1
```

Params for pam_oath are explained [here](http://www.nongnu.org/oath-toolkit/pam_oath.html).

SSH server config `/etc/ssh/sshd_config`.
```
#enable pam use
UsePAM yes
#pam gets the challenges 
ChallengeResponseAuthentication yes
#disable passwords
PasswordAuthentication no
#require publickey and totp to login. Remove publickey, to only use pam file
AuthenticationMethods publickey,keyboard-interactive:pam
```

## Debug

### Generate keys with oathtool
To generate keys for OTP, remember to increase counter (is shown in `/etc/users.oath`). 
```
oathtool -v --counter x key
oathtool -v -c 0 00
```

For TOTP use
```
oathtool -v key
oathtool -v --totp 00
```

### Debug with SU
Edit `/etc/pam.d/su`.

```
auth requisite pam_oath.so debug usersfile=/etc/users.oath window=20
```

Now add a test user, and add it to `/etc/users.oath`, where we will be using OTP instead of TOTP.
```
HOTP test - 00
```

Generate keys using oahtool, and try to su to the user, eg.
```
oathtool -v -c 0 00
su test
<key>
<password>
```

Read debug output, if it does not work.

### Debug with SSHD

FIRST enable pam.d debugging - [here](pam.d-debug.md).
Edit `/etc/pam.d/sshd`.
```
auth requisite pam_oath.so debug usersfile=/etc/users.oath window=20
```

Now add a test user, and add it to `/etc/users.oath`.
```
HOTP/T30/6 test - 00
```

Generate keys using oahtool, and try to login to user with ssh, eg.
```
oathtool -v --totp 00
ssh test@xxx.yyy.zzz.ccc
<key>
<password>
```
Read debug output, if it does not work.


## Differences between require and requisite
- Difference between require, requisite found here - https://www.linux.com/news/understanding-pam
    - require will let the user continue, but still fail after all auth methods
    - requisite will fail the user instantly

## References
- https://www.linode.com/docs/security/use-one-time-passwords-for-two-factor-authentication-with-ssh-on-ubuntu-16-04-and-debian-8
- https://gist.github.com/andrewlkho/e9a8c996c4bc1df23cd2
