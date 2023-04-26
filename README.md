# yocto-pi-zero

Yocto repository based on ["Ready, Set, Yocto!" for Raspberry Pi Zero](https://github.com/jynik/ready-set-yocto)
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
- MACHINE ??= "qemux86" --[to]--> MACHINE ??= "raspberrypi0-wifi"
- Enable the UART: `ENABLE_UART = "1"`
- <optional> Enable U_Boot: `RPI_USE_U_BOOT = "1"` 

Edit `conf/bblayers.conf` so that the BBLAYERS variable includes the following:
```
BBLAYERS ?= " \
  <path/to/repo>/poky/meta \
  <path/to/repo>/poky/meta-poky \
  <path/to/repo>/poky/meta-yocto-bsp \
  <path/to/repo>/meta-openembedded/meta-oe \
  <path/to/repo>/meta-openembedded/meta-python \
  <path/to/repo>/meta-openembedded/meta-networking \
  <path/to/repo>/meta-openembedded/meta-multimedia \
  <path/to/repo>/meta-openembedded/meta-filesystems \
  <path/to/repo>/meta-raspberrypi \
  "
```

## Raspberry Pi Kernel
The raspberry pi kernel can be difficult to fetch from the url. It is
recommended to have a local copy of the repo and modify the path in the recipie
to point at the local version.

A file `local-raspberry-pi-kernel.patch` contains the modifications needed to point at the local repo. To apply the changes, do:
```bash
cd meta-raspberrypi
git apply ../local-raspberry-pi-kernel.patch
```

By default, the raspberry pi kernel is cloned as a submodule in
downloads/rpi-linux-kernel. You will need to update the absolute paths in the
files modified by the patch above to work on your local system.

## Build minimal image
This will take a while:
```
bitbake core-image-minimal
```

The output images will be in `builds/rpi0/tmp/deploy/images/`

## Copy core-image-minimal to an SD Card
with dd:
```
sudo dd if=./tmp/deploy/images/core-image-minimal-raspberrypi0.ext3 of=<path/to/sdcard> bs=4M && sync
```

or using bmaptool
```
sudo bmaptool copy tmp/deploy/images/core-image-minimal-raspberrypi0.wic.bz2 <path/to/sdcard>
```

unmount the sd card!!

