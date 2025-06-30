# macでnuttx

## Nuttxのバージョンによる実行成否まとめ

| バージョン | 成否 |
|:-----------|:-----|
| release/9.0 | OK |
| release/10.0 | OK |
| release/11.0 | OK |
| release/12.0 | NG |
| master | NG |

```bash
$ git fetch
$ git checkout -b releases/9.0 origin/releases/9.0
```

### 注意点

- release/10.0からrelease/11.0にupgradeした場合、以下の修正が必要だった。

```bash
$ make distclean
/Volumes/wdb/hifive1/nuttx_old/nuttx/tools/../Make.defs:38: /Volumes/wdb/hifive1/nuttx_old/nuttx/tools/../arch/risc-v/src/rv32im/Toolchain.defs: No such file or directory
make: *** No rule to make target `/Volumes/wdb/hifive1/nuttx_old/nuttx/tools/../arch/risc-v/src/rv32im/Toolchain.defs'.  Stop.
/Volumes/wdb/hifive1/nuttx_old/nuttx/Make.defs:38: /Volumes/wdb/hifive1/nuttx_old/nuttx/arch/risc-v/src/rv32im/Toolchain.defs: No such file or directory
make: *** No rule to make target `/Volumes/wdb/hifive1/nuttx_old/nuttx/arch/risc-v/src/rv32im/Toolchain.defs'.  Stop
$ vi Make.defs
- include $(TOPDIR)/arch/risc-v/src/rv32im/Toolchain.defs
+ include $(TOPDIR)/arch/risc-v/src/common/Toolchain.defs
```

- release/12.0: nx_start()を呼び出した時点でストール

```bash
ATE0-->ATE0
OK
AT+BLEINIT=0-->OK
AT+CWMODE=0-->OK

ABC         // ここでだんまり : 最新版と一緒
```

### 成功した場合の実行例

```bash
// バージョンは11.0から正しく設定される: この例はmasterの.versionが残った段階で9.0を実行した際のもの
NuttShell (NSH) NuttX-12.9.0
nsh> ls
nsh: ls: missing required argument(s)
nsh> ls .
nsh: ls: stat failed: Invalid argument
nsh> ls /
/:
 dev/
 proc/
nsh> ls /proc
/proc:
 0/
 1/
 meminfo
 fs/
 self/
 uptime
 version
nsh> cat /proc/0/stack
StackBase:  0x0x800011e0
StackSize:  1536
StackUsed:  1516
nsh> cat /proc/0/status
Name:       Idle Task
Type:       Kthread
State:      Ready
Flags:      N--
Priority:   0
Scheduler:  SCHED_FIFO
SigMask:    00000000
nsh> cat/proc/1/status
nsh: cat/proc/1/status: command not found
nsh> ls /proc/1
/proc/1:
 status
 cmdline
 stack
 group/
nsh> cat /proc/1/status
Name:       init
Type:       Task
State:      Running
Flags:      ---
Priority:   100
Scheduler:  SCHED_FIFO
SigMask:    00000000
nsh> cat /proc/1/cmdline
init
nsh> ls /proc/1/group
/proc/1/group:
 status
 fd
nsh> ?
help usage:  help [-v] [<cmd>]

  ?       echo    help    ls      mh      mv      ps      uname
  cat     free    kill    mb      mount   mw      sleep   usleep

Builtin Apps:
  buttons   getprime  hello     nsh
nsh> getprime
Set thread priority to 1
Set thread policy to SCHED_FIFO
Start thread #0
thread #1 started, looking for primes < 10000, doing 10 run(s)
thread #1 finished, found 1230 primes, last one was 9973
Done
getprime took 1280 msec
nsh> hello
Hello, World!!
nsh> uname -a
NuttX 11.0.0 d32555f3e0 Jun 29 2025 11:02:10 risc-v hifive1-revb
nsh> ps
  PID GROUP PRI POLICY   TYPE    NPX STATE    EVENT     SIGMASK  COMMAND
    0     0   0 FIFO     Kthread N-- Ready              00000000 Idle Task
    1     1 100 RR       Task    --- Running            00000000 nsh_main
nsh>
```
## 実行の環境と手順

### ビルド環境

```bash
$ cat ~/.bashrc
export RISCV_PATH=${HOME}/hifive1/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-apple-darwin
export TARGET=sifive-hifive1-revb
export RISCV_OPENOCD_PATH=${HOME}/hifive1/riscv-openocd-0.10.0-2019.08.2-x86_64-apple-darwin
export PATH=${HOME}/hifive1/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-apple-darwin/bin:$PATH
```

### minicomの設定

```bash
$ cat ~/.minirc.dfl
pu addcarreturn     Yes
pu baudrate			115200
pu bits				8
pu parity			N
pu stopbits			1
pu rtscts			No
pu xonxoff			No
pu port				/dev/cu.usbmodem0009790170491
```

### nuttx.binの書き込み

```bash
$ cd nuttx
$ upload.sh               // これで書き込み

$ cat ~/bin/upload.sh
#/bin/sh
echo "loadfile ./nuttx.bin 0x20010000 noreset" | JLinkExe -device FE310 -if JTAG -speed 4000 -jtagconf -1,-1 -autoconnect 1
$ which JLinkExe
/usr/local/bin/JLinkExe
$ ls -l /usr/local/bin/JLinkExe
lrwxr-xr-x 1 root wheel 41  6 24 10:43 /usr/local/bin/JLinkExe -> /Applications/SEGGER/JLink_V844a/JLinkExe
```

# current masterが動かない件

## nuttxをインストール

```bash
$ mkdir hifive1 && cd hifive1
$ git clone git@github.com:zuki/nuttx.git nuttx
$ cd nuttx
$ git remote add upstream https://github.com/apache/nuttx.git
$ git pull upstream master
$ git checkout -b hifive1
$ cd ..
$ git clone git@github.com:zuki/nuttx-apps.git apps
$ cd apps
$ git remote add upstream https://github.com/apache/nuttx-apps.git
$ git pull upstream master
$ git checkout -b hifive1
$ cd ../nuttx
$ vi ~/.bashrc
export RISCV_PATH=/Users/dspace/hifive1/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-apple-darwin
export TARGET=sifive-hifive1-revb
export RISCV_OPENOCD_PATH=/Users/dspace/hifive1/riscv-openocd-0.10.0-2019.08.2-x86_64-apple-darwin
export PATH=/Users/dspace/hifive1/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-apple-darwin/bin:$PATH
$ source ~/.bashrc
```

[ドキュメント](https://nuttx.apache.org/docs/latest/platforms/risc-v/fe310/boards/hifive1-revb/index.html)の
方法に従い、QEMUと実機で実行

## QEMUで実行

```bash
$ cp -r boards/risc-v/fe310/hifive1-revb/configs/nsh boards/risc-v/fe310/hifive1-revb/configs/qemu
$ vi boards/risc-v/fe310/hifive1-revb/configs/qemu
-CONFIG_ARCH_CHIP_FE310_G002=y
+CONFIG_ARCH_CHIP_FE310_QEMU=y
$ make distclean
NuttX has not been configured!
To configure the project:
  tools/configure.sh <config>
For a list of available configurations:
  tools/configure.sh -L
$ ./tools/configure.sh hifive1-revb:qemu
$ make
Memory region         Used Size  Region Size  %age Used
           flash:       77916 B         4 MB      1.86%
            sram:        4256 B        16 KB     25.98%
CP: nuttx.hex
CP: nuttx.bin

$ qemu-system-riscv32 -nographic -machine sifive_e -kernel ./nuttx

NuttShell (NSH)
nsh> ls
/:
 dev/
 proc/
nsh> cat /proc/meminfo
      total       used       free    maxused    maxfree  nused  nfree name
      10588       4524       6064       4720       6064     19      1 Umem
nsh>            // Cntl-x-a
nsh> QEMU: Terminated
```

## 実機で実行

- ビルド

```bash
$ make distclean
$ ./tools/configure.sh hifive1-revb:nsh
$ make
Memory region         Used Size  Region Size  %age Used
           flash:       77908 B         4 MB      1.86%
            sram:        4384 B        16 KB     26.76%
CP: nuttx.hex
CP: nuttx.bin
```

### terminal 1での実行: minicon

```bash
$ minicom

Send Flag Timed Out Busy. Giving Up.
 Send Flag error: #0 #0 #0 #0 AT+BLEINIT=0-->Send Flag Timed Out Busy. Giving U.
 Send Flag error: #0 #0 #0 #0 AT+CWMODE=0-->Send Flag Timed Out Busy. Giving Up.
 Send Flag error: #0 #0 #0 #0
---- HiFive1 Rev B WiFi Demo --------
* UART: 115200 bps
* SPI: 80 KHz
* CPU: 320 MHz
Greetings!
Enter SSID: Current Version: NuttX  0.0.0 4270235bfa Jun 26 2025 09:45:16 risc-v
Assertion failed panic: at file: :0 task: Idle_Task process: Kernel 0x20010bbc
EPC: 20011162
A0: 80000b00 A1: 00000000 A2: 200200b8 A3: 00000000
A4: 00000007 A5: 00000007 A6: 0000001f A7: 00000000
T0: 800007c0 T1: 20010e3e T2: 80002b90 T3: 00000000
T4: 00000000 T5: 00000000 T6: 00000000
S0: 80001000 S1: 800007f0 S2: 80001000 S3: 00000000
S4: 00000000 S5: 200200b8 S6: 00000000 S7: 80001000
S8: 00000002 S9: 00000000 S10: 00000000 S11: 00000000
SP: 80000658 FP: 80001000 TP: 800012e8 RA: 20011162
IRQ Stack:
  base: 0x800001c0
  size: 00001536
    sp: 0x80000658
0x80000638: 00000000 00000000 80000658 00000600 80001000 800007f0 80000658 20014
0x80000658: 00000000 00000000 00000000 00000000 00000000 00000000 800007f0 80000
0x80000678: 200200b8 00000000 00000000 7474754e 00000058 00000000 00000000 00000
0x80000698: 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000
0x800006b8: 302e3000 0000302e 00000000 00000000 00000000 32340000 33323037 61665
0x800006d8: 6e754a20 20363220 35323032 3a393020 313a3534 00000036 00000000 00000
0x800006f8: 00000000 00000000 73697200 00762d63 00000000 00000000 00000000 00000
0x80000718: 00000000 00000000 00000000 00000000 00000000 00000000 00000001 00000
0x80000738: 00000005 80001000 80001000 200142dc 00000000 00000000 00000000 20018
0x80000758: 00000000 00000000 00000000 20010304 00000000 00000000 00000000 8000c
0x80000778: 00000000 00000000 00000000 80001680 00000005 200145ae 00000005 20018
0x80000798: 00000000 00000000 00000000 800015fc 00000000 200145ae 800007c0 20012
0x800007b8: 800015fc 200145a6 00000000 00000000 00000000 00000000 00000000 00000
ERROR: Stack pointer 80001680 is not within the stack
User Stack:
  base: 0
  size: 00000000
0: 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000
   PID GROUP PRI POLICY   TYPE    NPX STATE   EVENT      SIGMASK          STACKD
  ----   --- --- -------- ------- --- ------- ---------- ---------------- 0x800q
```

### terminal 2での実行: GDBサーバの実行

```bash
$ JLinkGDBServer -device FE310
SEGGER J-Link GDB Server V8.44a Command Line Version

JLinkARM.dll V8.44a (DLL compiled Jun 18 2025 16:28:17)

Command line: -device FE310
-----GDB Server start settings-----
GDBInit file:                  none
GDB Server Listening port:     2331
SWO raw output listening port: 2332
Terminal I/O port:             2333
Accept remote connection:      yes
Generate logfile:              off
Verify download:               off
Init regs on start:            off
Silent mode:                   off
Single run mode:               off
Target connection timeout:     0 ms
------J-Link related settings------
J-Link Host interface:         USB
J-Link script:                 none
J-Link settings file:          none
------Target related settings------
Target device:                 FE310
Target device parameters:      none
Target interface:              JTAG
Target interface speed:        4000kHz
Target endian:                 little

Connecting to J-Link...
J-Link is connected.
Firmware: J-Link OB-K22-SiFive compiled Nov  7 2022 16:21:52
Hardware: V1.00
S/N: 979017049
Checking target voltage...
Target voltage: 3.30 V
Listening on TCP/IP port 2331
Connecting to target...

J-Link found 1 JTAG device, Total IRLen = 5
JTAG ID: 0x20000913 (RISC-V)
Halting core...
RISC-V RV32 detected. Using RV32 register set for communication with GDB
Core implements no FPU
Connected to target
Waiting for GDB connection...Connected to 0000:0000:0000:0000:0000:0000:0000:0001
GDB client (conn. 10) requested target.xml from GDB Server
Reading common registers: Read register 'zero' (4 bytes) from hardware: 0x00000000
Read register 'ra' (4 bytes) from hardware: 0x3C160120
Read register 'sp' (4 bytes) from hardware: 0x002F0080
Read register 'gp' (4 bytes) from hardware: 0xA01A0080
Read register 'tp' (4 bytes) from hardware: 0xE8120080
Read register 't0' (4 bytes) from hardware: 0x04000000
Read register 't1' (4 bytes) from hardware: 0x00180000
Read register 't2' (4 bytes) from hardware: 0x902B0080
Read register 'fp' (4 bytes) from hardware: 0x202F0080
Read register 's1' (4 bytes) from hardware: 0x80000002
Read register 'a0' (4 bytes) from hardware: 0x00000080
Read register 'a1' (4 bytes) from hardware: 0x00040000
Read register 'a2' (4 bytes) from hardware: 0x0C000000
Read register 'a3' (4 bytes) from hardware: 0x0C000000
Read register 'a4' (4 bytes) from hardware: 0xBD000080
Read register 'a5' (4 bytes) from hardware: 0xBD000080
Read register 'a6' (4 bytes) from hardware: 0x1F000000
Read register 'a7' (4 bytes) from hardware: 0x00000000
Read register 's2' (4 bytes) from hardware: 0x80000002
Read register 's3' (4 bytes) from hardware: 0x00000000
Read register 's4' (4 bytes) from hardware: 0x00000000
Read register 's5' (4 bytes) from hardware: 0x00000000
Read register 's6' (4 bytes) from hardware: 0x00000000
Read register 's7' (4 bytes) from hardware: 0x00000000
Read register 's8' (4 bytes) from hardware: 0x00000000
Read register 's9' (4 bytes) from hardware: 0x00000000
Read register 's10' (4 bytes) from hardware: 0x00000000
Read register 's11' (4 bytes) from hardware: 0x00000000
Read register 't3' (4 bytes) from hardware: 0x00000000
Read register 't4' (4 bytes) from hardware: 0x00000000
Read register 't5' (4 bytes) from hardware: 0x00000000
Read register 't6' (4 bytes) from hardware: 0x00000000
Read register 'pc' (4 bytes) from hardware: 0x6A1F0120
Reading 64 bytes @ address 0x80002F00
Downloading 15952 bytes @ address 0x20010000
Downloading 15904 bytes @ address 0x20013E50
Downloading 16016 bytes @ address 0x20017C70
Downloading 16032 bytes @ address 0x2001BB00
Downloading 13564 bytes @ address 0x2001F9A0
Downloading 440 bytes @ address 0x20022E9C
Writing register 'pc' = 0x20010000
Starting target CPU...
```

### terminal 3での実行: gdbの実行

```bash
~/hifive1/nuttx$ riscv64-unknown-elf-gdb ./nuttx
GNU gdb (SiFive GDB 8.3.0-2019.08.0) 8.3
Copyright (C) 2019 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "--host=x86_64-apple-darwin17.7.0 --target=riscv64-unknown-elf".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<https://github.com/sifive/freedom-tools/issues>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
/Users/dspace/.gdbinit:2: Error in sourced command file:
No symbol table is loaded.  Use the "file" command.
Reading symbols from ./nuttx...
(gdb) target extended-remote:2331
Remote debugging using :2331
spawn_execattrs (pid=33554560, attr=0x80002f20) at task/task_spawnparms.c:189
189	      param.sched_priority = attr->priority;
(gdb) load nuttx                                          // 実機への書き込み
Loading section .text, size 0x12e9c lma 0x20010000
Loading section .data, size 0x1b8 lma 0x20022e9c
Start address 0x20010000, load size 77908
Transfer rate: 25360 KB/sec, 12984 bytes/write.
(gdb) c
Continuing.
```

## JLinkExでnuttx.binを直接書き込む

```bash
$ echo "loadfile nuttx.bin 0x20010000 noreset" | JLinkExe -device FE310 -if JTAG -speed 4000 -jtagconf -1,-1 -autoconnect 1
SEGGER J-Link Commander V8.44a (Compiled Jun 18 2025 16:29:08)
DLL version V8.44a, compiled Jun 18 2025 16:28:17

Connecting to J-Link via USB...O.K.
Firmware: J-Link OB-K22-SiFive compiled Nov  7 2022 16:21:52
Hardware version: V1.00
J-Link uptime (since boot): 0d 00h 20m 13s
S/N: 979017049
USB speed mode: Full speed (12 MBit/s)
VTref=3.300V
Device "FE310" selected.


Connecting to target via JTAG
ConfigTargetSettings() start
ConfigTargetSettings() end - Took 23us
TotalIRLen = 5, IRPrint = 0x01
JTAG chain detection found 1 devices:
 #0 Id: 0x20000913, IRLen: 05, Unknown device
Assuming RISC-V TAP with DTM setup
Debug architecture:
  RISC-V debug: 0.13
  AddrBits: 7
  DataBits: 32
  IdleClks: 5
Memory access:
  Via system bus: No
  Via ProgBuf: Yes (16 ProgBuf entries)
  Via abstract command (AAM): May be tried as last resort
DataBuf: 1 entries
  autoexec[0] implemented: Yes
Detected: RV32 core
RISC-V: The connected J-Link (S/N 979017049) uses an old firmware module V0 with known problems / limitations.
Temp. halting CPU for for feature detection...
HW instruction/data BPs: 8
Support set/clr BPs while running: No
HW data BPs trigger before execution of inst
CSR access via abs. commands: No
Compressed instruction support: Yes
Feature detection done. Restarting core...
BG memory access support: No
Memory zones:
  Zone: "Default" Description: Default access mode
RISC-V identified.
'loadfile': Skipping reset & halt of MCU before download.
Downloading file [nuttx.bin]...
J-Link: Flash download: Bank 0 @ 0x20000000: Skipped. Contents already match
O.K.
dspace@mini:~/hifive1/nuttx$ minicom


Welcome to minicom 2.8

OPTIONS:
Compiled on Jan  4 2021, 00:04:46.
Port /dev/cu.usbmodem0009790170491, 09:53:13
Using character set conversion
```

- 実行

```bash
Press Meta-Z for help on special keys

Bench Clock Reset Complete

ATE0-->ATE0
OK
AT+BLEINIT=0-->OK
AT+CWMODE=0-->OK

Current Version: NuttX  0.0.0 4270235bfa Jun 26 2025 09:45:16 risc-v
Assertion failed panic: at file: :0 task: Idle_Task process: Kernel 0x20010bbc
EPC: 20011162
A0: 80000b00 A1: 00000000 A2: 200200b8 A3: 00000000
A4: 00000007 A5: 00000007 A6: 00000005 A7: 00000000
T0: 800007c0 T1: 20010e3e T2: 800005d0 T3: 80000170
T4: 00000000 T5: 00000000 T6: 00000000
S0: 80001000 S1: 800007f0 S2: 80001000 S3: 00000000
S4: 00000000 S5: 200200b8 S6: 00000000 S7: 80001000
S8: 00000002 S9: 00000000 S10: 00000000 S11: 00000000
SP: 80000658 FP: 80001000 TP: 00000000 RA: 20011162
tcbstack: base: 0, size: 0, top: 0, sp: 80000658
IRQ Stack:
  base: 0x800001c0                      // intstack_base    intstack_top: 0x800007C0
  size: 00001536 (0x600)                // instack_size
    sp: 0x80000658                      // (sp >= intstac_base && sp < intstack_top) => intstack_sp = 0x80000658
0x80000638: 00000000 00000000 80000658 00000600 80001000 800007f0 80000658 20011264
0x80000658: 00000000 00000000 00000000 00000000 00000000 00000000 800007f0 80000b00
0x80000678: 200200b8 00000000 00000000 7474754e 00000058 00000000 00000000 00000000
0x80000698: 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000
0x800006b8: 302e3000 0000302e 00000000 00000000 00000000 32340000 33323037 61666235
0x800006d8: 6e754a20 20363220 35323032 3a393020 313a3534 00000036 00000000 00000000
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
   PID GROUP PRI POLICY   TYPE    NPX STATE   EVENT      SIGMASK          STACKBASE  STACKSIZE      USED   FILLED    COMD
  ----   --- --- -------- ------- --- ------- ---------- ---------------- 0x800001c0      1536      1536   100.0%!   irq
```

- スタックサイズを増やす

```bash
$ make menuconfig
RTOS Features -> Stack and heap information
$ make
$ echo "loadfile nuttx.bin 0x20010000 noreset" | JLinkExe -device FE310 -if JTAG -speed 4000 -jtagconf -1,-1 -autoconnect 1
...
Downloading file [nuttx.bin]...
J-Link: Flash download: Bank 0 @ 0x20000000: 1 range affected (131072 bytes)
J-Link: Flash download: Total: 1.564s (Prepare: 0.002s, Compare: 0.236s, Erase: 0.265s, Program & Verify: 0.953s, Restore: 0.106s)
J-Link: Flash download: Program & Verify speed: 134 KB/s
O.K.
$ minicom

ABC ERROR irq: 5    // CONFIG_DEBUG_FEATURESをon  システム未登録の irg=5 を受信
Current Version: NuttX  0.0.0 4270235bfa Jun 26 2025 10:31:34 risc-v
Assertion failed panic: at file: :0 task: Idle_Task process: Kernel 0x20010bbc
EPC: 20011162
A0: 80000b00 A1: 00000000 A2: 200200b8 A3: 00000000
A4: 00000007 A5: 00000007 A6: 00000005 A7: 00000000
T0: 800007c0 T1: 20010e3e T2: 800005d0 T3: 80000170
T4: 00000000 T5: 00000000 T6: 00000000
S0: 80001000 S1: 800007f0 S2: 80001000 S3: 00000000
S4: 00000000 S5: 200200b8 S6: 00000000 S7: 80001000
S8: 00000002 S9: 00000000 S10: 00000000 S11: 00000000
SP: 80000658 FP: 80001000 TP: 00000000 RA: 20011162

tcbstack: base: 0, size: 0, top: 0, sp: 80000658

IRQ Stack:
  base: 0x800001c0    // _edata = _sbss : これは _ebss = 0x80001120 でないとおかしい?
  size: 00001536
    sp: 0x80000658    //

0x80000638: 00000000 00000000 80000658 00000600 80001000 800007f0 80000658 20011264
0x80000658: 00000000 00000000 00000000 00000000 00000000 00000000 800007f0 80000b00
0x80000678: 200200b8 00000000 00000000 7474754e 00000058 00000000 00000000 00000000
0x80000698: 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000
0x800006b8: 302e3000 0000302e 00000000 00000000 00000000 32340000 33323037 61666235
0x800006d8: 6e754a20 20363220 35323032 3a303120 333a3133 00000034 00000000 00000000
0x800006f8: 00000000 00000000 73697200 00762d63 00000000 00000000 00000000 00000000
0x80000718: 00000000 00000000 00000000 00000000 00000000 00000000 00000001 00000000
0x80000738: 00000005 80001000 80001000 200142dc 00000000 00000000 00000000 20010e58
0x80000758: 00000000 00000000 00000000 20010304 00000000 00000000 00000000 800017fc
0x80000778: 00000000 00000000 00000000 80001880 00000005 200145ae 00000005 20010228
0x80000798: 00000000 00000000 00000000 800017fc 00000000 200145ae 800007c0 20010182
0x800007b8: 800017fc 200145a6 00000000 00000000 00000000 00000000 00000000 00000000

ERROR: Stack pointer 80001880 is not within the stack
User Stack:
  base: 0
  size: 00000000

0: 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000
   PID GROUP PRI POLICY   TYPE    NPX STATE   EVENT      SIGMASK          STACKBASE  STACKSIZE      USED   FILLED    COMD
  ----   --- --- -------- ------- --- ------- ---------- ---------------- 0x800001c0      1536      1536   100.0%!   irq

ERROR irq: 5
```

- このデバッグ出力は`sched/misc/assert.c`, `arch/risc-v/src/common/riscv_checkstack.c`で出力している。
- tcbstack_base/sizeがセットされていないためuser stackが0となっている （これは今のところ問題ではない）
- この出力の出元は`sched/irq/irq_unexpectedsr.c#int irq_unexpected_isr()`で処理関数が未定義のirq = 5 が発生
    ```c
    {
      UNUSED(context);
      UNUSED(arg);

      up_irq_save();
      _err("ERROR irq: %d\n", irq);
      PANIC();                                // このPANIC()が出元
      return OK; /* Won't get here */
    }
  ```

## riscv_exception_common.Sを修正して実行

```diff
$ git diff arch/risc-v/src/common/riscv_exception_common.S
@@ -345,8 +346,12 @@ riscv_jump_to_user:
  ****************************************************************************/

 /* Total required interrupt stack size */
-
+/* 1530 * */
+#ifdef CONFIG_SMP_NCPUS
 #define STACK_ALLOC_SIZE (INT_STACK_SIZE * CONFIG_SMP_NCPUS)
+#else
+#define STACK_ALLOC_SIZE  INT_STACK_SIZE
+#endif
```

- ESP32-SOLO-1の無効化が失敗

```bash
ATE0-->ATE0
P
Send Flag Timed Out Busy. Giving Up.
Receive Length Timed Out Busy
Send Flag Timed Out Busy. Giving Up.
Receive Length Timed Out Busy
...
```

- 無効化に使われているATコマンド解説

```bash
ATE0          : echoをオフ
AT+BLEINIT=0  : Bluetooth LEを初期化しない
AT+CWMODE=0   : Wi-Fi RFを無効にする
```
