# PlatformIOで開発

## インストール

- vscodeの拡張機能: PlatformIO IDE for VSCodeをインストール
- 最初のプロジェクトを作成すると必要なライブラリ等が`~/.platformio`配下にインストールされる

## `platformio`, `pio`コマンドにpathを通す

```bash
$ vi ~/.bashrc
alias platformio='${HOME}/.platformio/penv/bin/platformio'
alias pio='${HOME}/.platformio/penv/bin/pio'
$ source ~/.bashrc
$ pio --version
PlatformIO Core, version 6.1.18
$ platformio --version
PlatformIO Core, version 6.1.18
```

## プロジェクトの作成

- プロジェクトはデフォルトでは`${HOME}/Documents/PlatformIO/Projects`配下に作成される
- ディレクトリを作成し、そこにcdしてから`pio project --init -b hifive1-revb`を実行するとそこに作成される

```bash
$ platformio project init -h
Usage: platformio project init [OPTIONS]

Options:
  -d, --project-dir DIRECTORY
  -b, --board ID
  --ide [clion|codeblocks|eclipse|emacs|netbeans|qtcreator|sublimetext|vim|visualstudio|vscode]
  -e, --environment TEXT          Update existing environment
  -O, --project-option TEXT       A `name=value` pair
  --sample-code
  --no-install-dependencies
  --env-prefix TEXT
  -s, --silent
  -h, --help                      Show this message and exit.
$ mkdir test_project && test_project
$ pio project --init --b hifive1-revb
$ ls 
include  lib  platformio.ini  src  test
```

## ビルドと書き込み

- ボードをUSBに挿入してから以下のコマンドを実行する
- ビルド: `pio run`
- 書き込み: `pio run -t upload`

```bash
$ pio run
Processing hifive1-revb (platform: sifive; board: hifive1-revb; framework: freedom-e-sdk)
-----------------------------------------------------------------------------------------------------------------
Verbose mode can be enabled via `-v, --verbose` option
CONFIGURATION: https://docs.platformio.org/page/boards/sifive/hifive1-revb.html
PLATFORM: SiFive (5.2.0) > HiFive1 Rev B
HARDWARE: FE310 320MHz, 16KB RAM, 16MB Flash
DEBUG: Current (jlink) On-board (jlink, renode)
PACKAGES: 
 - framework-freedom-e-sdk @ 2.20050003.200818 (2005.0.3) 
 - toolchain-riscv @ 1.80300.190927 (8.3.0)
Installing Freedom E SDKs Python dependencies
Requirement already satisfied: pyparsing==2.4.5 in /Users/dspace/.platformio/penv/lib/python3.11/site-packages (2.4.5)
Requirement already satisfied: MarkupSafe==2.0.1 in /Users/dspace/.platformio/penv/lib/python3.11/site-packages (2.0.1)
Requirement already satisfied: Jinja2==2.10.1 in /Users/dspace/.platformio/penv/lib/python3.11/site-packages (2.10.1)
LDF: Library Dependency Finder -> https://bit.ly/configure-pio-ldf
LDF Modes: Finder ~ chain, Compatibility ~ soft
Found 0 compatible libraries
Scanning dependencies...
No dependencies
Building in release mode
Compiling .pio/build/hifive1-revb/src/led.o
Compiling .pio/build/hifive1-revb/src/utils.o
Compiling .pio/build/hifive1-revb/metal/atomic.o
Compiling .pio/build/hifive1-revb/metal/button.o
Compiling .pio/build/hifive1-revb/metal/cache.o
Compiling .pio/build/hifive1-revb/metal/clock.o
Compiling .pio/build/hifive1-revb/metal/cpu.o
Compiling .pio/build/hifive1-revb/metal/drivers/fixed-clock.o
Compiling .pio/build/hifive1-revb/metal/drivers/fixed-factor-clock.o
Compiling .pio/build/hifive1-revb/metal/drivers/inline.o
Compiling .pio/build/hifive1-revb/metal/drivers/riscv_clint0.o
Compiling .pio/build/hifive1-revb/metal/drivers/riscv_cpu.o
Compiling .pio/build/hifive1-revb/metal/drivers/riscv_plic0.o
Compiling .pio/build/hifive1-revb/metal/drivers/sifive_buserror0.o
Compiling .pio/build/hifive1-revb/metal/drivers/sifive_ccache0.o
Compiling .pio/build/hifive1-revb/metal/drivers/sifive_clic0.o
Compiling .pio/build/hifive1-revb/metal/drivers/sifive_fe310-g000_hfrosc.o
Compiling .pio/build/hifive1-revb/metal/drivers/sifive_fe310-g000_hfxosc.o
Compiling .pio/build/hifive1-revb/metal/drivers/sifive_fe310-g000_lfrosc.o
Compiling .pio/build/hifive1-revb/metal/drivers/sifive_fe310-g000_pll.o
Compiling .pio/build/hifive1-revb/metal/drivers/sifive_fe310-g000_prci.o
Compiling .pio/build/hifive1-revb/metal/drivers/sifive_fu540-c000_l2.o
Compiling .pio/build/hifive1-revb/metal/drivers/sifive_global-external-interrupts0.o
Compiling .pio/build/hifive1-revb/metal/drivers/sifive_gpio-buttons.o
Compiling .pio/build/hifive1-revb/metal/drivers/sifive_gpio-leds.o
Compiling .pio/build/hifive1-revb/metal/drivers/sifive_gpio-switches.o
Compiling .pio/build/hifive1-revb/metal/drivers/sifive_gpio0.o
Compiling .pio/build/hifive1-revb/metal/drivers/sifive_i2c0.o
Compiling .pio/build/hifive1-revb/metal/drivers/sifive_local-external-interrupts0.o
Compiling .pio/build/hifive1-revb/metal/drivers/sifive_pwm0.o
/Users/dspace/.platformio/packages/framework-freedom-e-sdk/freedom-metal/src/drivers/sifive_i2c0.c: In function '__metal_driver_sifive_i2c0_transfer':
/Users/dspace/.platformio/packages/framework-freedom-e-sdk/freedom-metal/src/drivers/sifive_i2c0.c:330:9: warning: 'ret' may be used uninitialized in this function [-Wmaybe-uninitialized]
     int ret;
         ^~~
Compiling .pio/build/hifive1-revb/metal/drivers/sifive_rtc0.o
Compiling .pio/build/hifive1-revb/metal/drivers/sifive_simuart0.o
Compiling .pio/build/hifive1-revb/metal/drivers/sifive_spi0.o
Compiling .pio/build/hifive1-revb/metal/drivers/sifive_test0.o
Compiling .pio/build/hifive1-revb/metal/drivers/sifive_trace.o
Compiling .pio/build/hifive1-revb/metal/drivers/sifive_uart0.o
Compiling .pio/build/hifive1-revb/metal/drivers/sifive_wdog0.o
Compiling .pio/build/hifive1-revb/metal/drivers/ucb_htif0.o
Compiling .pio/build/hifive1-revb/metal/entry.o
Compiling .pio/build/hifive1-revb/metal/gpio.o
Compiling .pio/build/hifive1-revb/metal/hpm.o
Compiling .pio/build/hifive1-revb/metal/i2c.o
Compiling .pio/build/hifive1-revb/metal/init.o
Compiling .pio/build/hifive1-revb/metal/interrupt.o
Compiling .pio/build/hifive1-revb/metal/led.o
Compiling .pio/build/hifive1-revb/metal/lock.o
Compiling .pio/build/hifive1-revb/metal/memory.o
Compiling .pio/build/hifive1-revb/metal/pmp.o
Compiling .pio/build/hifive1-revb/metal/privilege.o
Compiling .pio/build/hifive1-revb/metal/pwm.o
Compiling .pio/build/hifive1-revb/metal/rtc.o
Compiling .pio/build/hifive1-revb/metal/scrub.o
Compiling .pio/build/hifive1-revb/metal/shutdown.o
Compiling .pio/build/hifive1-revb/metal/spi.o
Compiling .pio/build/hifive1-revb/metal/switch.o
/Users/dspace/.platformio/packages/framework-freedom-e-sdk/freedom-metal/src/shutdown.c:15:9: note: #pragma message: There is no defined shutdown mechanism, metal_shutdown() will spin.
 #pragma message(                                                               \
         ^~~~~~~
Compiling .pio/build/hifive1-revb/metal/synchronize_harts.o
Compiling .pio/build/hifive1-revb/metal/time.o
Compiling .pio/build/hifive1-revb/metal/timer.o
Compiling .pio/build/hifive1-revb/metal/trap.o
Compiling .pio/build/hifive1-revb/metal/tty.o
Compiling .pio/build/hifive1-revb/metal/uart.o
Compiling .pio/build/hifive1-revb/metal/vector.o
Compiling .pio/build/hifive1-revb/metal/watchdog.o
Compiling .pio/build/hifive1-revb/metal-gloss/crt0.o
Compiling .pio/build/hifive1-revb/metal-gloss/nanosleep.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_access.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_chdir.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_chmod.o
Archiving .pio/build/hifive1-revb/libmetal.a
Compiling .pio/build/hifive1-revb/metal-gloss/sys_chown.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_clock_gettime.o
Indexing .pio/build/hifive1-revb/libmetal.a
Compiling .pio/build/hifive1-revb/metal-gloss/sys_close.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_execve.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_exit.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_faccessat.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_fork.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_fstat.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_fstatat.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_ftime.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_getcwd.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_getpid.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_gettimeofday.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_isatty.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_kill.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_link.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_lseek.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_lstat.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_open.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_openat.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_read.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_sbrk.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_stat.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_sysconf.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_times.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_unlink.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_utime.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_wait.o
Compiling .pio/build/hifive1-revb/metal-gloss/sys_write.o
Archiving .pio/build/hifive1-revb/libmetal-gloss.a
Indexing .pio/build/hifive1-revb/libmetal-gloss.a
Linking .pio/build/hifive1-revb/firmware.elf
Checking size .pio/build/hifive1-revb/firmware.elf
Advanced Memory Usage is available via "PlatformIO Home > Project Inspect"
RAM:   [===       ]  27.2% (used 4460 bytes from 16384 bytes)
Flash: [          ]   0.1% (used 11150 bytes from 16777216 bytes)
Building .pio/build/hifive1-revb/firmware.hex
========================================== [SUCCESS] Took 7.48 seconds ==========================================

$ pio run -t upload
Processing hifive1-revb (platform: sifive; board: hifive1-revb; framework: freedom-e-sdk)
-----------------------------------------------------------------------------------------------------------------
Tool Manager: Installing platformio/tool-openocd-riscv @ ~2.1000.0
Downloading  [####################################]  100%
Unpacking  [####################################]  100%
Tool Manager: tool-openocd-riscv@2.1000.20190927 has been installed!
Verbose mode can be enabled via `-v, --verbose` option
CONFIGURATION: https://docs.platformio.org/page/boards/sifive/hifive1-revb.html
PLATFORM: SiFive (5.2.0) > HiFive1 Rev B
HARDWARE: FE310 320MHz, 16KB RAM, 16MB Flash
DEBUG: Current (jlink) On-board (jlink, renode)
PACKAGES: 
 - framework-freedom-e-sdk @ 2.20050003.200818 (2005.0.3) 
 - tool-jlink @ 1.81206.0 (8.12.6) 
 - tool-openocd-riscv @ 2.1000.20190927 (10.0) 
 - toolchain-riscv @ 1.80300.190927 (8.3.0)
Installing Freedom E SDKs Python dependencies
Requirement already satisfied: pyparsing==2.4.5 in /Users/dspace/.platformio/penv/lib/python3.11/site-packages (2.4.5)
Requirement already satisfied: MarkupSafe==2.0.1 in /Users/dspace/.platformio/penv/lib/python3.11/site-packages (2.0.1)
Requirement already satisfied: Jinja2==2.10.1 in /Users/dspace/.platformio/penv/lib/python3.11/site-packages (2.10.1)
LDF: Library Dependency Finder -> https://bit.ly/configure-pio-ldf
LDF Modes: Finder ~ chain, Compatibility ~ soft
Found 0 compatible libraries
Scanning dependencies...
No dependencies
Building in release mode
Checking size .pio/build/hifive1-revb/firmware.elf
Advanced Memory Usage is available via "PlatformIO Home > Project Inspect"
RAM:   [===       ]  27.2% (used 4460 bytes from 16384 bytes)
Flash: [          ]   0.1% (used 11150 bytes from 16777216 bytes)
Configuring upload protocol...
AVAILABLE: jlink
CURRENT: upload_protocol = jlink
Uploading .pio/build/hifive1-revb/firmware.hex
SEGGER J-Link Commander V8.12f (Compiled Feb 12 2025 12:06:54)
DLL version V8.12f, compiled Feb 12 2025 12:05:43


J-Link Command File read successfully.
Processing script file...
J-Link>h
J-Link connection not established yet but required for command.
Connecting to J-Link via USB...O.K.
Firmware: J-Link OB-K22-SiFive compiled Nov  7 2022 16:21:52
Hardware version: V1.00
J-Link uptime (since boot): 0d 00h 00m 14s
S/N: 979017049
USB speed mode: Full speed (12 MBit/s)
VTref=3.300V
Target connection not established yet but required for command.
Device "FE310" selected.


Connecting to target via JTAG
ConfigTargetSettings() start
ConfigTargetSettings() end - Took 19us
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
pc = 20010E6A sp = 80000940 ra = 20010E46 
gp = 80000C88 tp = 800004C8 fp = 001BEE15 
t0 = 00000000 t1 = 00000000 t2 = 80000560 
t3 = 00000000 t4 = 00000000 t5 = 00000000 t6 = 00000000 
a0 = 001BEE15 a1 = 00680000 a2 = 20010358 a3 = FFFFFFFF 
a4 = 00200000 a5 = 001E847F a6 = 0000001F a7 = 00000000 
s1 = 02000080 s2 = 02000080 s3 = 00000000 s4 = 00000000 
s5 = 00000000 s6 = 00000000 s7 = 00000000 s8 = 00000000 
s9 = 00000000 s10 = 00000000 s11 = 00000000 
J-Link>loadfile .pio/build/hifive1-revb/firmware.hex
'loadfile': Performing implicit reset & halt of MCU.
Reset type: Normal (https://wiki.segger.com/J-Link_Reset_Strategies)
RISC-V: Performing reset via <ndmreset>
Downloading file [.pio/build/hifive1-revb/firmware.hex]...
Comparing flash   [100%] Done.
Erasing flash     [100%] Done.
Programming flash [100%] Done.
J-Link: Flash download: Bank 0 @ 0x20000000: 1 range affected (65536 bytes)
J-Link: Flash download: Total: 0.767s (Prepare: 0.000s, Compare: 0.318s, Erase: 0.139s, Program & Verify: 0.208s, Restore: 0.101s)
J-Link: Flash download: Program & Verify speed: 308 KB/s
O.K.
J-Link>r
Reset delay: 0 ms
Reset type: Normal (https://wiki.segger.com/J-Link_Reset_Strategies)
RISC-V: Performing reset via <ndmreset>
J-Link>q

Script processing completed.
```

## その他のコマンド

- インストール済みのボードを表示

```bash
$ pio boards --installed

Platform: sifive
============================================================================================================================
ID                     MCU    Frequency    Flash    RAM    Name
---------------------  -----  -----------  -------  -----  -------------------------
e310-arty              FE310  450MHz       16MB     256MB  Arty FPGA Dev Kit
hifive-unleashed       FU540  1500MHz      32MB     8GB    HiFive Unleashed
hifive1-revb           FE310  320MHz       16MB     16KB   HiFive1 Rev B
hifive1                FE310  320MHz       16MB     16KB   HiFive1
sparkfun_redboard_v    FE310  320MHz       16MB     16KB   SparkFun RED-V RedBoard
sparkfun_thing_plus_v  FE310  320MHz       16MB     16KB   SparkFun RED-V Thing Plus
```