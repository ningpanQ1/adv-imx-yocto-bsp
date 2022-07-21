Advantech imx yocto bsp Repo Manifest README

This repo is used to download manifests for Advantech imx yocto bsp releases.

Supported boards
----------------
i.MX8MM Series
- EAMB9918 A1

## Host PC Requirement
**RAM： >= 16GB**
**DISK  >= 128GB**

Specific instructions will reside in READMEs in each branch.
```
$ sudo apt-get install gawk wget git-core diffstat unzip texinfo gcc-multilib \
build-essential chrpath socat cpio python python3 python3-pip python3-pexpect \
xz-utils debianutils iputils-ping python3-git python3-jinja2 libegl1-mesa libsdl1.2-dev \
pylint3 xterm rsync curl
```

The branch will be based on the release type Linux or Android with release manifests in each branch tied to the base releases.
    
For example for Advantech imx Linux Yocto Project releases the branches will be <Yocto Project release> so zeus with
all manifests tied to releases on zeus in this branch.    
To use this manifest repo, the 'repo' tool must be isntalled first.
```
$: mkdir ~/bin
$: curl http://commondatastorage.googleapis.com/git-repo-downloads/repo  > ~/bin/repo
$: chmod a+x ~/bin/repo
$: PATH=${PATH}:~/bin
```

To execute 
```
$: mkdir <release>
$: cd <release>
$: repo init -u https://github.com/Advantech-IIoT/adv-imx-yocto-bsp -b <branch name> [ -m <release manifest>]
$: repo sync
```

Each branch will have detailed READMEs describing exact syntax.

Examples    
To download the NXP 5.10.72-2.2.0 release
```
repo init -u https://github.com/Advantech-IIoT/adv-imx-yocto-bsp  -b hardknott -m adv-imx-hardknott-1.0.0.xml
```

Setup the build folder for a BSP release:
-----------------------------------------
Note: The remaining instructions are for setting up a BSP release only. For setting   
up a demo, please see adv-imx-yocto-bsp/README-<demo> for further instructions.   
```
$: [MACHINE=<machine>] [DISTRO=fsl-imx-<backend>] source ./imx-setup-release.sh -b <build path>
```
<machine> defaults to imx8mmeamb9918a1    
<backend>   Graphics backend type   
xwayland    Wayland with X11 support - default distro   
wayland     Wayland   
fb          Framebuffer (not supported for mx8)   

Note if the poky community distro is used then build breaks will happen with some
components using our meta-fsl-bsp-release layer.    

Examples:   
- Setup for XWayland.
```
$: MACHINE=imx8mmeamb9918a1 DISTRO=fsl-imx-xwayland source ./imx-setup-release.sh -b eamb9918a1
```

To use an existing Yocto build directory:
```
$: source setup-environment <build path>
```

Build an image:
---------------
```
$ bitbake <image recipe>
```
Some image recipes:   
imx-image-core - core image with basic graphics and no multimedia   
imx-image-multimedia - image with multimedia and graphics   
imx-image-full - image with multimedia and machine learning and Qt    
swupdate-image - advantech OTA recovery initrd image    

When finished the building, the target file is generated in tmp/deploy/images/${MACHINE}/   
|image|filename|
|---|---|
|uboot image| imx-boot |
|kernel image|Image|
|kernel dtb|xxx.dtb|
|rootfs with compressed package|imx-image-full-xxx|
|ota recovery initrd|swupdate-image-xxx|

Build the SDK Installer
-----------------------
To build the SDK installer for a standard SDK and populate the SDK image, use the following command form. Be sure to replace image with an image (e.g. “imx-image-full”):						
```
  $ bitbake image -c populate_sdk	
```							
You can do the same for the extensible SDK using this command form:						
```
  $ bitbake image -c populate_sdk_ext
```	

Build a SDCard image:  
---------------
After building a image according to the above instructions ,you can burn the sdcard img file to your microSD card.
```
$ bitbake <sdcard image recipe>
```
sdcard image recipe:   
mk-sdcard-image : 
 
When finished the building
```
$ cd  tmp/deploy/images/${MACHINE}/mk-sdcard-image/scripts
$ sudo ./mkimg-linux.sh
    
```
the target file is generated in tmp/deploy/images/${MACHINE}/mk-sdcard-image/out/${SDimgfile}     
    
```
$sudo dd if=${SDimgfile} of=${SDpartition} bs=4096
```
Examples:
```
$sudo dd if=eamb9918-sdcard_1.0.0.img of=/dev/sde bs=4096
```

Note: You should check which partition is your microSD card
firstly and you can format your microSD card before this step to avoid compatibility
issue.
    
If your PC is Windows:    
Use the Rufus or Raspberry Pi Imager provided by Raspberry Pi to burn the img
file to your microSD card. You can refer to below link for the usage of the tools.    
Raspberry Pi Imager:    
https://www.raspberrypi.com/news/raspberry-pi-imager-imaging-utility/    
Rufus:    
https://rufus.ie/en/
