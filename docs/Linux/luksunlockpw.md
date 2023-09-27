# luks\:unlock:password

This is a quick and dirty way to automatically unlock your luks volume at boot.[^1]
[^1]: source: [https://michael-prokop.at/blog/2023/03/22/automatically-unlocking-a-luks-encrypted-root-filesystem-during-boot/](https://michael-prokop.at/blog/2023/03/22/automatically-unlocking-a-luks-encrypted-root-filesystem-during-boot/)

!!! warning

    This is considered insecure for most situations, there is a much more secure method to auto-unlock using a TPM2 device. You will find those instructions [here](./luksunlocktpm2.md).

!!! note

    This article assumes you already have a luks encrypted volume and would like to have it automatically unlock at boot with no user intervention.

``` shell title="create unlock script"
$ cat << EOF | sudo tee /etc/initramfs-tools/unlock.sh
#!/bin/sh
echo -n "provide_the_actual_password_here and keep the double-quotes"
EOF
```

``` shell title="secure access to the password"
$ sudo chmod 700 /etc/initramfs-tools/unlock.sh && sudo chown root:root /etc/initramfs-tools/unlock.sh
```

Add ```keyscript=/etc/initramfs-tools/unlock.sh``` to your crypttab file, as shown below.

```shell title="add the script to the crypttab file"
$ sudo nano /etc/crypttab
md1_crypt UUID=77246138-b666-4151-b01c-5a12db54b28b none luks,discard,keyscript=/etc/initramfs-tools/unlock.sh
```

```shell title="update your initramfs"
$ sudo update-initramfs -k all -u
```
