Mbed
===

## install requirement

```bash
apt-get update
apt-get install -y python2.7 python-pip git mercurial
apt-get install -y gcc-arm-none-eabi
pip2 install --upgrade pip
apt-get clean

# install mbed-cli
pip2 install mbed-cli jsonschema
```

## check device
```bash
mbed detect
```
## set environment

```bash
# set compiler
mbed toolchain -G GCC_ARM
# set target (change according to detected devices)
mbed target LPC1768
```

## update mbed-os
```bash
cd mbed-os
mbed sync
mbed update master
```
## compile
```bash
mbed detect
mbed compile # optional -m LPC1768 -t GCC_ARM
cp /path/to/binary /path/to/Mbed
```
