# luks\:extend:lvm:disk

How to resize a LUKS encrypted LVM volume (including root)

``` shell title="find your crypt volume"
$ cat /etc/crypttab
sda5_crypt UUID=3d378ba9-3588-43a5-8640-a0874fa5a26e none luks,discard,keyscript=/etc/initramfs-tools/unlock.sh
$ fdisk -l | grep sda5_crypt
Disk /dev/mapper/sda5_crypt: 63.51 GiB, 68189945856 bytes, 133183488 sectors
```

``` shell title="extend with cryptsetup"
$ cryptsetup resize sda5_crypt
```

``` shell title="extend lvm physical volume"
$ pvresize /dev/mapper/sda5_crypt
```

``` shell title="find your lvm logical volume"
$ df -h -T | grep root
/dev/mapper/debian--vg-root ext4   32G   32G   0G  100% /
```

``` shell title="extend your lvm logical volume & filesystem"
$ lvresize -l +100%FREE /dev/mapper/debian--vg-root
$
$ resize2fs -p /dev/mapper/debian--vg-root (if ext4) or xfs_growfs /dev/mapper/debian--vg-root (if xfs)
```

``` shell title="verify extension"
$ df -h -T | grep root
/dev/mapper/debian--vg-root ext4       62G   28G   32G  47% /
```
