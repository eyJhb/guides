# Luks - FDE (full disk encryption)
## Setting up /dev/sdb for encryption 
Used this link - [here](https://help.ubuntu.com/community/EncryptedFilesystemsOnRemovableStorage#Expert_setup_using_command_line_only).

The commands are as follows! (make sure that the disk is all free - gparted delete).

```
#install
sudo apt-get install cryptsetup
#partition disk
sudo fdisk /dev/sdb
w
sudo fdisk /dev/sdb
n
p
<enter>
<enter>
<enter>
p -> print the partion, default everything before
w
#prepare for encryption
sudo modprobe dm-crypt
sudo modprobe sha256
sudo modprobe aes_generic
#encrypt the disk
sudo cryptsetup --verify-passphrase luksFormat /dev/sdb1 -c aes -s 256 -h sha256
#open and create ext4 filesystem
sudo cryptsetup luksOpen /dev/sdb1 data
sudo mkfs -t ext4 -m 1 -O dir_index,filetype,sparse_super /dev/mapper/data
```

You now have a encrypted disk, that can be mounted as you wish.

## Automount encrypted disk
Used this page - [here](https://blog.tinned-software.net/automount-a-luks-encrypted-volume-on-system-start/).

```
#create folder for key
mkdir -p /etc/luks/
#generate a key
sudo dd if=/dev/urandom of=/etc/luks/disk-crypt-key bs=512 count=8
#add key to our luks crypt
sudo cryptsetup -v luksAddKey /dev/sdb1 /etc/luks/disk-crypt-key
#check it is enabled
sudo cryptsetup luksDump /dev/sdb1 | grep "Key Slot"
#check if the key works
sudo cryptsetup -v luksOpen /dev/sdb1 sdb1_crypt --key-file=/etc/luks/disk-crypt-key
#close our crypt
sudo cryptsetup -v luksClose sdb1_crypt
#setup automount - grep the uuid
sudo cryptsetup luksDump /dev/sdb1 | grep "UUID"
#add to /etc/crypttab
sdb1_crypt UUID=<uuid> /etc/luks/disk-crypt-key luks 
#test it works
sudo cryptdisks_start sdb1_crypt
#add to our fstab
/dev/mapper/sdb1_crypt /media/data ext4    defaults,errors=remount-ro   0       2
#try our fstab
sudo mount -a
#remember to chown!
```

Will properly need sudo throughout because of the key, as it should not be readable by users..
