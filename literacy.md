Literacy
===
---

# system

## update error

### Missing Key

1. When updated

        sudo apt update

1. Error is

        the public key is not available: NO_PUBKEY <KEY>....

1. Add keys to ubuntu key manager

        sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys <KEY>

1. update again.        

### GNU version change

#### gccのversionを、g++やその他と付随させて変更

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

#### コンパイラをgccからclaugに変更

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

## file

### compress / extract

#### options meaning

|option|description
|:-:   |:--        
|c     | create    
|v     | verbose (display status)
|f     |filename (designate file name)
|x     | extract
|z     | gz
|j     | bz2
|J     | xz

#### tar.gz

```bash
#compress
tar -zcvf <name>.tar.gz (directory)
#extract
tar -zxvf <name>.tar.gz
```

#### tar

```bash
#compress
tar -cvf <name>.tar (directory)
#extract
tar -xvf <name>.tar
```
#### zip

```bash
#compress
zip -r <name>.zip (directory)
#extract
unzip <name>.zip #ordinary
unar <name>.zip #compatible with files with Japanese created in Mac

```

## SHELL

### sudo

```bash
# always work as superuser
sudo -s
sudo -i
```

## trouble shooting
### bash crash after `source devel/setup.bash`
1. `etc/environment`
2. sprit line at
        PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:..." JAVA_HOME="/usr/lib/jvm/java-8-oracle/jre/bin/java"
    to
        PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games"
        JAVA_HOME="/usr/lib/jvm/java-8-oracle/jre/bin/java"

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


## LaTeX
### set picture with .png not .eps
PNG images do not contain Bounding Box Information, so they are not be able to shown correctly in some tex documents.
The one method is generate Bounding Box Information file(.bb).

    ebb picture.png

Add edit package include line at Tex configuration part

    \usepackage[]{graphicx} -> \usepackage[dvipdfm]{graphicx}


Then you can use .png files normally.

## network

### check ip addresses in the same network

```bash
# output search list
nmap -sP 192.168.1.*
```  


## multi display

### setup sub touch display
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
