# RuiJie-gzhu
Bypass Ruijie v6.84 based on Openwrt

**Great Appreciation for [ysc3839](https://github.com/ysc3839/openwrt-minieap/tree/gzhu) and [Lean](https://github.com/coolsnowwolf/lede)**

***

[Preparation](#preparation)

[Build](#build)

[Usage](#usage)


## Preparation

+ `A router that can be flashed and run Openwrt`


+ `Ubuntu 64bit (Ubuntu 18 LTS x64 recommend)`


+ `Conditions to bypass GFW globally`


+ If you use `VMware Workstation Pro` to build, please make sure it has at least **50GB** of storage space *(20GB as default)*


**My Compiling Environment**
+ VMware Workstation 16 Pro 
`Ver: 16.0.0 build-16894299`

+ Ubuntu image
`ubuntu-18.04.5-desktop-amd64`

***
## Build

DO **NOT** USE `ROOT USER` TO CONFIGURE OR MAKE!!!
DO **NOT** USE `ROOT USER` TO CONFIGURE OR MAKE!!!
DO **NOT** USE `ROOT USER` TO CONFIGURE OR MAKE!!!

**1. Prepare your compilation environment**

**2. Download the required packages**
```bash
cd ~ # or the PATH you want in /home/

sudo apt-get update

sudo apt-get -y install build-essential asciidoc binutils bzip2 gawk gettext git libncurses5-dev libz-dev patch python3.5 python2.7 unzip zlib1g-dev lib32gcc1 libc6-dev-i386 subversion flex uglifyjs git-core gcc-multilib p7zip p7zip-full msmtp libssl-dev texinfo libglib2.0-dev xmlto qemu-utils upx libelf-dev autoconf automake libtool autopoint device-tree-compiler g++-multilib antlr3 gperf wget swig rsync
```

**DO NOT USE `sudo` or `root user` IN THE FOLLOWING STEPS**

**3. Get the source code of `Openwrt` from github**

```bash
git clone https://github.com/coolsnowwolf/lede
```


**4. Enter the file directory of `lede` and get all the latest package definitions and install symlinks of all of them**


NOTE: If you would like to use some plug-ins such as `S*R-PLUS`, you should uncomment the line with the word `helloword` (the last line usually) before running `./scripts/feeds update -a`

```bash
cd ./lede/
./scripts/feeds update -a
./scripts/feeds install -a
```


**5. Integrate the the source code of `Mnieap` & `Luci-Minieap` into `Openwrt`**

```bash
git clone https://github.com/ysc3839/openwrt-minieap.git -b gzhu package/minieap
git clone https://github.com/ysc3839/luci-proto-minieap.git package/luci-proto-minieap
```
NOTE：Notice the `gzhu` branch

**6. Configure and Choose the packages you want**

```bash
make menuconfig
```


**NOTE:** 
+ Choose the correct `Target System`,`Subtarget` and `Target Profile` for your router 
+ In order to bypass the Ruijie, you should choose `minieap` in section `Network` and `luci-proto-minieap` in section `LuCI` -> `Protocols`

[More information for the other packages](https://www.right.com.cn/forum/thread-344825-1-1.html)


**7. Dowload some required library**
*(Run with bypassing GFW globally)*

```bash
make -j8 download V=s
```


**8. Compile**
*(Run with bypassing GFW globally)*

```bash
make -j1 V=s # `-j1` single thread is recommended for the first compilation
```
*Usually takes 2 ~ 3 hours to compile for the first time*

**9. Wait then Find your firmware and packages in `$YOURPATH(~ as default)/lede/bin/targets`**

***

## Usage

#### Login the Openwrt

+ Default login IP address : 192.168.1.1
+ Default login password : password

#### Configure the Openwrt and Minieap

1. Click `Network`->`Interface`->`LAN` to change the default IP address for logging in Openwrt (192.168.1.1 as default)


2. Make sure `WAN` runs `DHCP` protocol    
Create a new interface with name `$whateveruwant`  
Click and modify the new interface you create  


3. Switch to `MINIEAP` client  
Type in your account and password (same as ruijie client) and choose `rjv3` in `Packages plug-in`  
Click `RJv3 plug-in setting`  
Choose `Ruijie private` and `Twice certification` in `EAP Broadcast Address` and `DHCP Mode`  
Click `Physical settings`  
Choose the `VLAN` that runs `DHCP` (eth0.2 as default)  


4. Save and Apply


5. Test the Internet


## Supplement


+ Compile not the first time

```bash
cd lede
git pull
./scripts/feeds update -a && ./scripts/feeds install -a
make defconfig
make -j8 download
make -j$(($(nproc) + 1)) V=s
```

+ remake menuconfig

```bash
rm -rf ./tmp && rm -rf .config
make menuconfig
make -j$(($(nproc) + 1)) V=s
```