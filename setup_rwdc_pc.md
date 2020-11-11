

## HDD  暗号化
```bash
sudo apt install cryptsetup
# keyfile 作成
sudo dd bs=512 count=4 if=/dev/urandom of=/root/keyfile
#暗号化、format
sudo cryptsetup luksFormat /dev/sdc /root/keyfile
#解凍
sudo cryptsetup open /dev/sdc crypt_root --key-file /root/keyfile
#マウント
sudo mkfs.ext4 -j /dev/mapper/crypt_root # 最初だけ
sudo mount /dev/mapper/crypt_root ~/Desctop/data
# アンマウント
sudo umount ~/Desctop/data
sudo cryptsetup close crypt_root
```

## auditd
```bash
sudo apt install auditd
sudo service auditd start
sudo systemctl enable auditd
```
## useradd
```bash
sudo groupadd student
sudo adduser rwdc-dthon student
```

## polkit
create bellow file

#### find the action with
```bash
pkaction list | grep network
```

#### Prevent network

/var/lib/polkit-1/localauthority/50-local.d/10-network-manager.pkla

```java
[Prevent Networking]
Identity=unix-group:student
Action=org.freedesktop.NetworkManager.*
ResultAny=no
ResultInactive=no
ResultActive=no
```

#### prevent mount external devices  
/var/lib/polkit-1/localauthority/50-local.d/10-automount.pkla

```java
[Prevent automount]
Identity=unix-group:student
Action=org.freedesktop.udisks2.filesystem-mount*
ResultAny=no
ResultInactive=no
ResultActive=no

[Prevent automount encrypted]
Identity=unix-group:student
Action=org.freedesktop.udisks2.encrypted*
ResultAny=no
ResultInactive=no
ResultActive=no


[Prevent power]
Identity=unix-group:student
Action=org.freedesktop.udisks2.power*
ResultAny=no
ResultInactive=no
ResultActive=no

[Prevent eject]
Identity=unix-group:student
Action=org.freedesktop.udisks2.eject*
ResultAny=no
ResultInactive=no
ResultActive=no
```

## create mount/unmount script
/usr/local/src/mount-benesse
```bash
#!/bin/bash

echo "disable network"
nmcli networking off

echo "decrypt hard disk"
cryptsetup open /dev/sdc crypt_root --key-file /root/keyfile

echo "mount hard disk"
#sudo mkfs.ext4 -j /dev/mapper/crypt_root #just after encrypted
mount /dev/mapper/crypt_root /mnt/benesse-data
if [ $? -ne 0 ]; then
    /usr/games/cowthink "Failed to mount hard disk, something wrong...."
    exit 1
else
    /usr/games/cowsay -f dragon "Success mount, have fun!!!!"
fi

ln -s /mnt/benesse-data/benesse ~/Desktop/benesse-data

sleep 5
```

/usr/local/src/umount-benesse
```bash
#!/bin/bash

unlink ~/Desktop/benesse-data

echo "Saving what you did"
filename=$(date "+%Y%m%d%H%M%S")
mkdir /mnt/benesse-data/log/$filename
cp /var/log/syslog /mnt/benesse-data/log/$filename/
cp /var/log/audit/audit.log /mnt/benesse-data/log/$filename/
cp ~/.bash_history /mnt/benesse-data/log/$filename/

echo "unmount"
umount /mnt/benesse-data
if [ $? -ne 0 ]; then
    /usr/games/cowthink "Unmount failed!!! Check the all applications do not access /mnt/benesse-data"
    exit 1
else
    /usr/games/cowsay "Unmount Successed. Thanks!!!"
fi

echo "close crypt root"
sudo cryptsetup close crypt_root

sleep 5
```

## create desktop application
