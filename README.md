# yocto-pi-zero

Yocto repository based on ["Ready, Set, Yocto!" for Raspberry Pi Zero](https://gist.github.com/jynik/dca2b85a04cd48b2e3a9fcb92fa51a65)
## Setup
Install [nix](https://nixos.org/download.html#download-nix)

Run 
```bash
git submodule update --init
nix-shell
```

## Enter build environment
If you are starting from scratch, remove the directory builds/rpi0 (`rm -r builds/rpi0`)

Run the following to enter the build environment:
```bash
source <path/to/repo>/poky/oe-init-build-env <path/to/repo>/builds/rpi0
```

This will drop you into the `rpi0` directory. You need to make some changes to
the configuration if you haven't set it up yet.

Edit `conf/local.conf` with the following changes:
- MACHINE ??= "qemux86" --[to]--> MACHINE ??= "raspberrypi0"

Edit `conf/bblayers.conf` so that the BBLAYERS variable includes the following:
```
BBLAYERS ?= " \
  <path/to/repo>/poky/meta \
  <path/to/repo>/poky/meta-poky \
  <path/to/repo>/poky/meta-yocto-bsp \
  <path/to/repo>/meta-raspberrypi \
  <path/to/repo>/meta-openembedded/meta-oe \
  <path/to/repo>/meta-openembedded/meta-python \
  <path/to/repo>/meta-openembedded/meta-networking \
  <path/to/repo>/meta-openembedded/meta-multimedia \
  <path/to/repo>/meta-openembedded/meta-filesystems \
  "
```

## Build minimal image
This will take a while:
```
bitbake core-image-minimal
```

## Copy core-image-minimal to an SD Card
```
sudo dd if=./tmp/deploy/images/core-image-minimal-raspberrypi0.rpi-sdimg of=<path/to/sdcard> bs=4M && sync
```

## Enable RPi UART
Mount the SD card and find a file in the mount called `config.txt`

Add the following lines to `config.txt`:
```
enable_uart=1
dtoverlay=pi3-disable-bt
```

Save and Unmount the SD Card


