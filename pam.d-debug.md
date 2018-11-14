# PAM Debug
This have some little tips and tricks, to debug pam.d when e.g. fidling with SSH TOTP

Backup syslog.conf
```
cp /etc/syslog.conf /etc/syslog.conf.bak
```

Edit `/etc/syslog.conf` and add the following somewhere (can be at the top)

```
*.debug /var/log/debug.log
```

Remember to touch the file if it doesn't exists
```
touch /var/log/debug.log
```

Now tail it!
```
tail -f /var/log/debug.log
```

## References
- https://chadmayfield.com/2016/06/15/enable-pam-debug-logging/
