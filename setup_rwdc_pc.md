## tool類
apt : jupyter-notebook pip3
python-pip pandas sklearn
jupyter-notebook
chainer tensorflow-gpu pandas-ml
numpy scipy matplotlib statsmodels nltk seaborn ggplot datetime

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
sudo adduser rwdc-dthon
sudo adduser rwdc-dthon　student
```
name = rwdc**lastname**

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

ファイル名の番号は全て変えること
同じだとどれか1つしか実行されない
変な空白も注意
`cat -A filename`でチェック

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
最後は
`sudo systemctl restart polkit.service`


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
cryptsetup close crypt_root

sleep 5
```

## enable run the mount program
/etc/sudoers
```bash
%student ALL=(root) NOPASSWD:/usr/local/mount-benesse,/usr/local/umount-benesse
```

## create desktop application


## setup vpn server
[ここを参考に](https://qiita.com/noraworld/items/2fe6be489e1d93c748b8)
[ここも](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-openvpn-server-on-ubuntu-18-04)
* `systemctl start openvpn`だと起動するけど、　`systemctl status openvpn`が`exit`にはなったので,
`sudo openvpn server.conf`で起動  -> openvpn@serverが立ち上がり、`running`になった。
* フォワーディングとマスカレードの設定はしっかりやること。
* VPNサーバー側のローカルIPは`192.168.15.0/24`で、VPNは`10.8.0.0/24`
* VPN側のローカルPCにアクセスする場合は、ローカルPCのIPのゲートウェイをVPNサーバーのVPN側のIPに設定(10.8.0.1)

[ufwについて](https://linuxsalad.blogspot.com/2009/02/ufw.html)
[IPマスカレード](http://safe-linux.homeip.net/network/Gateway_Server-09.html)

## vpn client on linux

1. 上で作成したvpn.ovpnを、gnome-networkmanager(設定のやつ)で読み込む
1. `Network`タブのVPNで設定
1. `sudo apt install network-manager-openvpn-gnome`が必要

## vnc server
[ここ参考](https://qiita.com/_dakc_/items/81ca237dfc4cfec855d9)
以降のPCへのログインを　gnome-sessionで行うとカーソル等が動かなく成るのでxwaylandでログインするか、cuiログインすること。もしくは、vncサーバーを停止してからログイン

1. install vnc server
```bash
sudo apt install tigervnc-standalone-server tigervnc-xorg-extension tigervnc-common
```
1. config vnc server
```bash
vncpasswd
# readonly pass はnoでいい
```
2. start vnc server
systemctl で制御しようと思ったが、上手く行かなかったので、各ユーザでログインしてサーバー起動
ディスプレイIDは変えること

```bash
vncserver :1 -localhost no
```
