# Crontabs
Should explain very little about crontabs

## Basics
A crontab is in the form of `m h dom mon dow command`, which translates to

```
minute
hour
day of month
month
day of week
```

Remember to use full paths instead of ones in your `$PATH`, as they may not be available, find which using `which echo`.

## Run every x minute
```
crontab -e
*/5 * * * * /bin/bash /root/backup.sh
```

## Redirect output to tty
Get current tty by using `tty`, (`/dev/pts/0`), then do the following.

```
crontab -e 
*/5 * * * * /bin/bash /root/backup.sh > /dev/pts/0
```
