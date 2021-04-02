## [OpenOCD 主页](http://openocd.org/)  

## [OpenOCD 源码]( https://git.code.sf.net/p/openocd/code)  

### 如何编译OpenOCD源码

```bash
$ mkdir -p /build; cd /build
$ wget https://www.intra2net.com/en/developer/libftdi/download/libftdi1-1.5.tar.bz2
--2021-03-22 18:51:13--  https://www.intra2net.com/en/developer/libftdi/download/libftdi1-1.5.tar.bz2
Resolving www.intra2net.com (www.intra2net.com)... 62.75.181.28, 85.214.138.66
Connecting to www.intra2net.com (www.intra2net.com)|62.75.181.28|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 116297 (114K) [application/x-bzip2]
Saving to: ‘libftdi1-1.5.tar.bz2.1’

libftdi1-1.5.tar.bz2.1                   100%[=================================================================================>] 113.57K  35.5KB/s    in 3.2s

2021-03-22 18:51:18 (35.5 KB/s) - ‘libftdi1-1.5.tar.bz2.1’ saved [116297/116297]

```

下载OPENOCD软件包，并安装OPENOCD

```bash
$  git clone https://git.code.sf.net/p/openocd/code  openocd
Cloning into 'openocd'...
remote: Enumerating objects: 67709, done.
remote: Counting objects: 100% (67709/67709), done.
remote: Compressing objects: 100% (31407/31407), done.
remote: Total 67709 (delta 55694), reused 43998 (delta 36123)
Receiving objects: 100% (67709/67709), 15.26 MiB | 1.57 MiB/s, done.
Resolving deltas: 100% (55694/55694), done.
Updating files: 100% (1794/1794), done.

$ cd openocd
$ git checkout f342aa
M       jimtcl
Note: switching to 'f342aa'.
You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by switching back to a branch.
If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -c with the switch command. Example:
  git switch -c <new-branch-name>
Or undo this operation with:
  git switch -
Turn off this advice by setting config variable advice.detachedHead to false
HEAD is now at f342aac08 The openocd-0.11.0 release

$  ./bootstrap
+ aclocal --warnings=all
+ libtoolize --automake --copy
+ autoconf --warnings=all
+ autoheader --warnings=all
+ automake --warnings=all --gnu --add-missing --copy
configure.ac:27: installing './compile'
configure.ac:39: installing './config.guess'
configure.ac:39: installing './config.sub'
configure.ac:17: installing './install-sh'
configure.ac:17: installing './missing'
Makefile.am: installing './INSTALL'
Makefile.am: installing './depcomp'
Makefile.am:22: installing './mdate-sh'
Makefile.am:22: installing './texinfo.tex'
Setting up submodules
Submodule 'jimtcl' (https://github.com/msteveb/jimtcl.git) registered for path 'jimtcl'
Submodule 'src/jtag/drivers/libjaylink' (https://repo.or.cz/libjaylink.git) registered for path 'src/jtag/drivers/libjaylink'
Submodule 'tools/git2cl' (https://repo.or.cz/git2cl.git) registered for path 'tools/git2cl'
Cloning into '/build/openocd/jimtcl'...
Cloning into '/build/openocd/src/jtag/drivers/libjaylink'...
……
……
Bootstrap complete. Quick build instructions:
./configure ....

$ mkdir build;cd build
$ ../configure  --with-ftd2xx-linux-tardir=../../libftdi1-1.5 --enable-jlink --enable-ftdi --enable-stlink --enable-cmsis-dap
checking for makeinfo... makeinfo
checking for a BSD-compatible install... /usr/bin/install -c
checking whether build environment is sane... yes
checking for a thread-safe mkdir -p... /usr/bin/mkdir -p
checking for gawk... gawk
checking whether make sets $(MAKE)... yes
checking whether make supports nested variables... yes
checking for gcc... gcc
checking whether the C compiler works... yes
checking for C compiler default output file name... a.exe
checking for suffix of executables... .exe
checking whether we are cross compiling... no
checking for suffix of object files... o
……
……
=== configuring in jimtcl (/build/openocd/build/jimtcl)
configure: running /bin/sh ../../jimtcl/configure.gnu --disable-option-checking '--prefix=/usr/local'  '--with-ftd2xx-linux-tardir=../../libftdi1-1.5' '--enable-jlink' '--enable-ftdi' '--enable-stlink' '--enable-cmsis-dap' 'PKG_CONFIG_PATH=/usr/local/lib/pkgconfig' 'HIDAPI_LIBS=/usr/local/lib/libhidapi.a' --cache-file=/dev/null --srcdir=../../jimtcl
……
……

OpenOCD configuration summary
--------------------------------------------------
MPSSE mode of FTDI based devices        yes
ST-Link Programmer                      yes
TI ICDI JTAG Programmer                 yes (auto)
Keil ULINK JTAG Programmer              yes (auto)
Altera USB-Blaster II Compatible        yes (auto)
Bitbang mode of FT232R based devices    yes (auto)
Versaloon-Link JTAG Programmer          yes (auto)
TI XDS110 Debug Probe                   yes (auto)
CMSIS-DAP v2 Compliant Debugger         yes (auto)
OSBDM (JTAG only) Programmer            yes (auto)
eStick/opendous JTAG Programmer         yes (auto)
Raisonance RLink JTAG Programmer        yes (auto)
Andes JTAG Programmer                   yes (auto)
USBProg JTAG Programmer                 no
Olimex ARM-JTAG-EW Programmer           no
CMSIS-DAP Compliant Debugger            yes
Nu-Link Programmer                      yes (auto)
Cypress KitProg Programmer              yes (auto)
Altera USB-Blaster Compatible           no
ASIX Presto Adapter                     no
OpenJTAG Adapter                        no
Linux GPIO bitbang through libgpiod     no
SEGGER J-Link Programmer                yes
Use Capstone disassembly framework      no

$ make
$ make install
$ which openocd
/usr/local/bin/openocd

```

## [xpack-openocd-0.10.0-15](https://github.com/xpack-dev-tools/openocd-xpack/releases/download/v0.10.0-15/xpack-openocd-0.10.0-15-win32-x64.zip)  
## [xpack-openocd-0.11.0-1](https://github.com/xpack-dev-tools/openocd-xpack/releases/download/v0.11.0-1/xpack-openocd-0.11.0-1-win32-x64.zip)  

