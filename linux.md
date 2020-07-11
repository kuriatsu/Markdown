Literacy
===
---

## Missing Key

1. When updated

        sudo apt update

1. Error is

        the public key is not available: NO_PUBKEY <KEY>....

1. Add keys to ubuntu key manager

        sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys <KEY>

1. update again.        


## gccのversionを、g++やその他と付随させて変更

```bash
#confirm version
gcc --version

#confirm registration
sudo update-alternatives --list gcc

#if none, install and register gcc with other compiler as slave
sudo apt install gcc-* g++-*

sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-* <priority value> --slave /usr/bin/g++ g++ /usr/bin/g++-* --slave /usr/bin/gcov gcov /usr/bin/gcov-*

#change gcc and others incidentally
sudo update-alternatives --config gcc
There are 3 choices for the alternative gcc (providing /usr/bin/gcc).

  Selection    Path              Priority   Status
------------------------------------------------------------
* 0            /usr/bin/gcc-5     50        auto mode
  1            /usr/bin/gcc-4.9   49        manual mode
  2            /usr/bin/gcc-5     50        manual mode
  3            /usr/bin/gcc-7     30        manual mode

Press <enter> to keep the current choice[*], or type selection number:

```

## コンパイラをgccからclaugに変更

```bash
export CXX=
export C=
```
ダメならば

`CMakefile.txt`で
```c
set(CMAKE_CXX_COMPILER "clang++")
set(CMAKE_C_COMPILER "clang")
```

を追加

## sudo

always work as superuser
```bash
sudo -s
sudo -i
```

## Setting Fonts
1. Get font file
2. create file like `.fonts`
3. set fonts to OS
        fc-cache -vf ~/.fonts

## ffmpeg
```bash
#.avi to .mp4
ffmpeg -i infile.avi -acodec copy -vcodec copy outfile.mp4
#.oma to .mp3
ffmpeg -i "Car stories.oma" -codec:a libmp3lame -qscale:a 0 "Car stories.mp3"
#clop
ffmpeg -i infile.mp4 -vf crop=width:hight:start_x:start_y outfile.mp4

```

## VNC
### PC
* **VNC (Mocha VNC Lite)**

    Start server
```bash
sudo apt install x11vnc vnc-java
x11vnc -storepasswd <password>
x11vnc -forever -usepw -httpdir /usr/share/vnc-java/ #(optional -httpport 5901 -scale 385x210)　-
```
    Prepare iPad

    1. Install **Mocha VNC Lite**
    2. Add configuration
    3. Serect from `local workstations`
<image src="Picture/mocha_select.jpg">

    4. `servir Port` = 5901
    5. Do not set `VNC Password` (more stable)
<image src="Picture/mocha_set.jpg">
    6. Serect from `Connect`


* **VNC (Vino)**

    Master  
    1. search Vino
    2. start `Desctop Sharing`
    3. Open server
    <image src="Picture/vino.png">

    Child
    1. start `Remmina`
    2. click `+`
    3. set `Remote Desctop Preference`
    <image src="Picture/remmina.png">

* **tight VNC**

    server
    ```bash
    # install
    sudo apt install tightvncserver
    # run
    tightvnc
    ```

    cliant (smartphone)
    1. install VNC viewer

## network

### check ip addresses in the same network

```bash
# output search list
nmap -sP 192.168.1.*
```  


## multi display - setup sub touch display

Ubuntu may not support multi touch display. ONLY the specified device according to below process can be correctly used
```bash
# find which interface are used with each display. "eDP" means the built-in display
xinput list
# find device id of the input decice
xrandr
# specify relationship between device id and interface
xinput map-to-output <id> <port_name>
```

## gnome application .Desktop

* file location
    /var/lib/snapd/desktop/applications/

* icon location
    /snap/....

* bin location
    /snap/bin/...

## display id check
```bash
xrandr --listmonitors
```

## Thinkpad

### freeze double finger scrolling after suspend in kernel 5.0.0
`/etc/default/grub`
before
```bash
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
```
after
```bash
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash psmouse.synaptics_intertouch=0"
```
then
```bash
sudo update-grub
reboot
```

## error of apt
* `Temporary failure resolving 'archive.ubuntu.com`
```bash
echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf > /dev/null
```

## image processing

### compless image
```bash
# install
sudo apt install pngquant
# compless (overwrite)
pngquant -f --ext .png --quality=60 ./*.png
```

### resize image
```bash
convert -geometry "40%" from.png to.png
```

### create gif
```bash
convert -delay 10 -loop 0 *.png out.gif
```

## system
### check running UEFI mode or not
check file
```bash
ls /sys/dirmware/efi
```

## merge PDF

```bash
$ pdfunite 1.pdf 2.pdf ... output.pdf
```

## atom hydrogen
1. install hydrogen into atom
1. `pip3 install ipython ipykernel jupyter`
1. reboot atom
1. `python3 -m ipykernel install --user`
