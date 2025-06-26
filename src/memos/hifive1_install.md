# hifive1-revbでNuttx

[ドキュメント: hifive1-revb](https://nuttx.apache.org/docs/latest/platforms/risc-v/fe310/boards/hifive1-revb/index.html)に基づく

## QEMUで実行

### ツールのインストール

```bash
$ wget https://static.dev.sifive.com/dev-tools/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14.tar.gz
$ vi .bashrc
export PATH=$HOME/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14/bin:$PATH
$ wget https://www.segger.com/downloads/jlink/JLink_Linux_V844a_x86_64.deb
$ sudo apt install ./JLink_Linux_V844a_x86_64.deb
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
E: Unable to locate package JLink_Linux_V844a_x86_64.deb
$ sudo apt update
$ sudo apt install ./JLink_Linux_V844a_x86_64.deb
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Note, selecting 'jlink' instead of './JLink_Linux_V844a_x86_64.deb'
The following NEW packages will be installed:
  jlink
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 0 B/41.6 MB of archives.
After this operation, 111 MB of additional disk space will be used.
Get:1 /home/vagrant/nuttx/nuttx/JLink_Linux_V844a_x86_64.deb jlink amd64 8.44.1 [41.6 MB]
Selecting previously unselected package jlink.
(Reading database ... 414441 files and directories currently installed.)
Preparing to unpack .../JLink_Linux_V844a_x86_64.deb ...
Removing /opt/SEGGER/JLink ...
/opt/SEGGER/JLink not found (OK)
Unpacking jlink (8.44.1) ...
Setting up jlink (8.44.1) ...
Updating udev rules via udevadm...
OK
$ ls /opt/SEGGER/JLink_V844a/
DDConditionerExe  JFlashSPIExe            JLinkRegistrationExe    JLinkSTM32Exe         libjlinkarm.so             libQtGui.so
DevProExe         JLinkConfigExe          JLinkRemoteServer       JLinkSWOViewer        libjlinkarm.so.8           libQtGui.so.4
Doc               JLinkExe                JLinkRemoteServerCLExe  JLinkSWOViewerCLExe   libjlinkarm.so.8.44.1      libQtGui.so.4.8
ETC               JLinkGDBServer          JLinkRemoteServerExe    JLinkSWOViewerExe     libjlinkarm_x86.so         libQtGui.so.4.8.7
Firmwares         JLinkGDBServerCLExe     JLinkRTTClient          JLinkUSBWebServerExe  libjlinkarm_x86.so.8       Samples
GDBServer         JLinkGDBServerExe       JLinkRTTClientExe       JLinkXVCDServerExe    libjlinkarm_x86.so.8.44.1  Script
JFlashExe         JLinkGUIServerExe       JLinkRTTLogger          JMemExe               libQtCore.so               x86
JFlashLiteExe     JLinkLicenseManager     JLinkRTTLoggerExe       JRunExe               libQtCore.so.4
JFlashSPI_CL      JLinkLicenseManagerExe  JLinkRTTViewerExe       JScopeExe             libQtCore.so.4.8
JFlashSPICLExe    JLinkRegistration       JLinkSTM32              JTAGLoadExe           libQtCore.so.4.8.7
```

### nuttxの構成とビルド

```bash
$ mkdir ./nuttx; cd ./nuttx
$ git clone https://github.com/apache/nuttx.git nuttx
$ git clone https://github.com/apache/nuttx-apps.git apps
$ cd nuttx
$ make distclean
$ ./tools/configure.sh hifive1-revb:qemu
$ make
CC:  stdlib/lib_strtold.c stdlib/lib_strtold.c:42:10: fatal error: math.h: No such file or directory
   42 | #include <math.h>
      |          ^~~~~~~~
compilation terminated.
make[1]: *** [Makefile:156: bin/lib_strtold.o] Error 1
make: *** [tools/LibTargets.mk:196: libs/libc/libc.a] Error 2

$ vi libs/libc/stdlib/lib_strtold.c
// #include <math.h>	をコメントアウト

$ make
CC:  stdlib/lib_strtold.c stdlib/lib_strtold.c: In function 'strtox':
stdlib/lib_strtold.c:697:26: error: 'INFINITY' undeclared (first use in this function)
  697 |       return negative ? -INFINITY : INFINITY;
      |                          ^~~~~~~~
stdlib/lib_strtold.c:697:26: note: each undeclared identifier is reported only once for each function it appears in
stdlib/lib_strtold.c:709:14: error: 'NAN' undeclared (first use in this function)
  709 |       return NAN;
      |              ^~~
make[1]: *** [Makefile:156: bin/lib_strtold.o] Error 1
make: *** [tools/LibTargets.mk:196: libs/libc/libc.a] Error 2

$ vi libs/libc/stdlib/lib_strtold.c
// 以下を追加（libs/libm/include/math.h から）
# ifndef INFINITY
#  define INFINITY (__builtin_inff())
# endif

# ifndef NAN
#  define NAN (__builtin_nanf(""))
# endif

$ make

CPP:  /home/vagrant/nuttx/nuttx/boards/risc-v/fe310/hifive1-revb/scripts/ld.script-> /home/vagrant/nuttx/nuttx/boards/risc-v/fe310/hifive1-LD: nuttx                   
Memory region         Used Size  Region Size  %age Used
           flash:       77976 B         4 MB      1.86%
            sram:        4256 B        16 KB     25.98%
CP: nuttx.hex
CP: nuttx.bin

$ qemu-system-riscv32 -nographic -machine sifive_e -kernel ./nuttx

暴走
```


## 実機（ubuntuで作業）

```bash
$ make distclean
$ ./tools/configure.sh hifive1-revb:nsh
$ make
Create version.h
LN: platform/board to /home/vagrant/nuttx/apps/platform/dummy
Register: buttons
Register: hello
Register: nsh
Register: sh
Register: getprime
CPP:  /home/vagrant/nuttx/nuttx/boards/risc-v/fe310/hifive1-revb/scripts/ld.script-> /home/vagrant/nuttx/nuttx/boards/risc-v/fe310/hifive1-LD: nuttx                   
Memory region         Used Size  Region Size  %age Used
           flash:       77976 B         4 MB      1.86%
            sram:        4256 B        16 KB     25.98%
CP: nuttx.hex
CP: nuttx.bin

$ /opt/SEGGER/JLink_V844a/JLinkGDBServer -device FE310

$ riscv64-unknown-elf-gdb ./nuttx
(gdb) target extended-remote:2331
(gdb) load nuttx
(gdb) c

$ minicom -b 115200 /dev/ttyACM0
Bench Clock Reset Complete

ATE0--> Send Flag error: #0 #0 #0 #0 AT+BLEINIT=0-->AT+BLEINIT=0
OK
AT+CWMODE=0-->AT+CWMODE=0
OK

Current Version: NuttX  0.0.0 9f84695ef7 Jun 23 2025 09:05:14 risc-v
Assertion failed panic: at file: :0 task: Idle_Task process: Kernel 0x20010bbc  // <nx_start>の先頭アドレス : stack over flow
EPC: 20011162                                                                                 
A0: 80000b00 A1: 00000000 A2: 200200b8 A3: 00000000                                           
A4: 00000007 A5: 00000007 A6: 00000005 A7: 00000000                                           
T0: 800007c0 T1: 20010e3e T2: 800005d0 T3: 80000170
T4: 00000000 T5: 00000000 T6: 00000000
S0: 80001000 S1: 800007f0 S2: 80001000 S3: 00000000
S4: 00000000 S5: 200200b8 S6: 00000000 S7: 80001000
S8: 00000002 S9: 00000000 S10: 00000000 S11: 00000000
SP: 80000658 FP: 80001000 TP: 00000000 RA: 20011162
IRQ Stack:
  base: 0x800001c0
  size: 00001536
    sp: 0x80000658
0x80000638: 00000000 00000000 80000658 00000600 80001000 800007f0 80000658 20011264
0x80000658: 00000000 00000000 00000000 00000000 00000000 00000000 800007f0 80000b00
0x80000678: 200200b8 00000000 00000000 7474754e 00000058 00000000 00000000 00000000
0x80000698: 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000
0x800006b8: 302e3000 0000302e 00000000 00000000 00000000 66390000 39363438 37666535
0x800006d8: 6e754a20 20333220 35323032 3a393020 313a3530 00000034 00000000 00000000
0x800006f8: 00000000 00000000 73697200 00762d63 00000000 00000000 00000000 00000000
0x80000718: 00000000 00000000 00000000 00000000 00000000 00000000 00000001 00000000
0x80000738: 00000005 80001000 80001000 200142dc 00000000 00000000 00000000 20010e58
0x80000758: 00000000 00000000 00000000 20010304 00000000 00000000 00000000 800015fc
0x80000778: 00000000 00000000 00000000 80001680 00000005 200145ae 00000005 20010228
0x80000798: 00000000 00000000 00000000 800015fc 00000000 200145ae 800007c0 20010182
0x800007b8: 800015fc 200145a6 00000000 00000000 00000000 00000000 00000000 00000000
ERROR: Stack pointer 80001680 is not within the stack
User Stack:
  base: 0
  size: 00000000
0: 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000
   PID GROUP PRI POLICY   TYPE    NPX STATE   EVENT      SIGMASK          STACKBASE  STACKSIZE      USED   FILLED    COMMAND
  ----   --- --- -------- ------- --- ------- ---------- ---------------- 0x800001c0      1536      1536   100.0%!   irq


```

## HiFive1初期画面

```
$ vi ~/.minirc.dfl
pu port				/dev/cu.usbmodem0009790170491
$ minicom


Welcome to minicom 2.8

OPTIONS: 
Compiled on Jan  4 2021, 00:04:46.
Port /dev/cu.usbmodem0009790170491, 09:23:39
Using character set conversion

Press Meta-Z for help on special keys

Bench Clock Reset Complete

ATE0-->ATE0
OK
AT+BLEINIT=0-->OK
AT+CWMODE=0-->OK
                                                                        
                                                                        
                                                                        
                  SIFIVE, INC.                                          

           5555555555555555555555555
          5555                   5555
         5555                     5555
        5555                       5555
       5555       5555555555555555555555
      5555       555555555555555555555555
     5555                             5555
    5555                               5555
   5555                                 5555
  5555555555555555555555555555          55555
   55555           555555555           55555
     55555           55555           55555
       55555           5           55555
         55555                   55555
           55555               55555
             55555           55555
               55555       55555
                 55555   55555
                   555555555
                     55555
                       5


      Welcome to the SiFive HiFive1 Rev B!
```