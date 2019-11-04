Mbed
===

## install requirement

```bash
apt-get update
apt-get install -y python2.7 python-pip git mercurial
# not good
# apt-get install -y gcc-arm-none-eabi
# good. Install arm compiler from gcc-arm-toolchain website "https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads" (latest one didn't work well)
wget https://developer.arm.com/-/media/Files/downloads/gnu-rm/8-2018q4/gcc-arm-none-eabi-8-2018-q4-major-linux.tar.bz2?revision=d830f9dd-cd4f-406d-8672-cca9210dd220?product=GNU%20Arm%20Embedded%20Toolchain,64-bit,,Linux,8-2018-q4-major
tar -xvf /PATH/TO/gcc-arm-none-eabi-***tar.bz2
mv /PATH/TO/gcc-arm-none-eabi-*** /usr/local/gcc-arm-none-eabi-***

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
mbed config -G GCC_ARM_PATH "/usr/local/gcc-arm-none-eabi-***/bin"
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
