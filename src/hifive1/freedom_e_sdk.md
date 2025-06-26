# Freedom E SDKの導入

[An Introduction to the HiFive1 Rev B and RISC-V](https://dev.iachieved.it/iachievedit/an-introduction-to-the-hifive-rev-b-and-risc-v/)を参考に
Freedom E SDKをMacに導入

## toolchainとOpenOCDのインストール

SiFiveのサイトにはもうtoolchainのダウンロードページは存在しないが、以下のURLからダウンロードできた。

```bash
$ wget https://static.dev.sifive.com/dev-tools/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-apple-darwin.tar.gz
$ tar xf riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-apple-darwin.tar.gz
$ cd riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-apple-darwin
$ ls
bin  include  lib  libexec  riscv64-unknown-elf  share
$ ls bin
riscv64-unknown-elf-addr2line  riscv64-unknown-elf-gcc-ar         riscv64-unknown-elf-ld.bfd
riscv64-unknown-elf-ar         riscv64-unknown-elf-gcc-nm         riscv64-unknown-elf-nm
riscv64-unknown-elf-as         riscv64-unknown-elf-gcc-ranlib     riscv64-unknown-elf-objcopy
riscv64-unknown-elf-c++        riscv64-unknown-elf-gcov           riscv64-unknown-elf-objdump
riscv64-unknown-elf-c++filt    riscv64-unknown-elf-gcov-dump      riscv64-unknown-elf-ranlib
riscv64-unknown-elf-cpp        riscv64-unknown-elf-gcov-tool      riscv64-unknown-elf-readelf
riscv64-unknown-elf-elfedit    riscv64-unknown-elf-gdb            riscv64-unknown-elf-size
riscv64-unknown-elf-g++        riscv64-unknown-elf-gdb-add-index  riscv64-unknown-elf-strings
riscv64-unknown-elf-gcc        riscv64-unknown-elf-gprof          riscv64-unknown-elf-strip
riscv64-unknown-elf-gcc-8.3.0  riscv64-unknown-elf-ld

$ wget https://static.dev.sifive.com/dev-tools/riscv-openocd-0.10.0-2019.08.2-x86_64-apple-darwin.tar.gz
$ tar xf riscv-openocd-0.10.0-2019.08.2-x86_64-apple-darwin.tar.gz
$ cd riscv-openocd-0.10.0-2019.08.2-x86_64-apple-darwin
$ ls
bin  include  lib  share
$ ls bin
libftdi1-config  libusb-config  openocd

$ vi ~/.bashrc
export RISCV_PATH=desired-untar-dir/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-apple-darwin
```

## Freedom E SDKのインストール

```bash
$ git clone -o upstream --recursive https://github.com/sifive/freedom-e-sdk.git
cd freedom-e-sdk/
$ ls
FreeRTOS-metal   LICENSE.MIT  bsp       docker                    freedom-metal     scl-metal
LICENSE          Makefile     debug.mk  freedom-devicetree-tools  release.mk        scripts
LICENSE.Apache2  README.md    doc       freedom-e-sdk.mk          requirements.txt  software
```

## J-Linkのインストール

```bash
$ wget https://www.segger.com/downloads/jlink/JLink_MacOSX.pkg
$ open JLink_MacOSX.pkg
$ ls /Applications/SEGGER/JLink_V844a/
DDConditionerExe  JLinkConfig.app          JLinkRTTViewer.app      JLinkXVCDServerExe     libQtGui.4.8.7.dylib
DevProExe         JLinkExe                 JLinkRegistration.app   JMem.app               libQtGui.4.8.dylib
Doc               JLinkGDBServer           JLinkRemoteServer       JRunExe                libQtGui.4.dylib
ETC               JLinkGDBServer.app       JLinkRemoteServer.app   JScope.app             libQtGui.dylib
Firmwares         JLinkGDBServerCLExe      JLinkRemoteServerCLExe  JTAGLoadExe            libjlinkarm.8.44.1.dylib
GDBServer         JLinkGUIServerExe        JLinkSTM32              Samples                libjlinkarm.8.dylib
JFlash.app        JLinkLicenseManager.app  JLinkSTM32Exe           Script                 libjlinkarm.dylib
JFlashLite.app    JLinkRTTClient           JLinkSWOViewer          libQtCore.4.8.7.dylib
JFlashSPI         JLinkRTTClientExe        JLinkSWOViewer.app      libQtCore.4.8.dylib
JFlashSPI.app     JLinkRTTLogger           JLinkSWOViewerCLExe     libQtCore.4.dylib
JFlashSPICLExe    JLinkRTTLoggerExe        JLinkUSBWebServerExe    libQtCore.dylib
```

## プログラムの作成

```bash
$ make PROGRAM=hello TARGET=sifive-hifive1-revb
/bin/sh: /Users/dspace/nuttx/hifive1/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-apple-darwin/bin/riscv64-elf--gcc: No such file or directory
(standard_in) 1: parse error
/Applications/Xcode.app/Contents/Developer/usr/bin/make -f scripts/virtualenv.mk virtualenv
python3 -m venv /Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv
. /Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/bin/activate && /Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/bin/python3 /Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/bin/pip install pip==20.0.1
Collecting pip==20.0.1
  Downloading pip-20.0.1-py2.py3-none-any.whl.metadata (3.4 kB)
Downloading pip-20.0.1-py2.py3-none-any.whl (1.5 MB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 1.5/1.5 MB 8.7 MB/s eta 0:00:00
Installing collected packages: pip
  Attempting uninstall: pip
    Found existing installation: pip 24.0
    Uninstalling pip-24.0:
      Successfully uninstalled pip-24.0
Successfully installed pip-20.0.1

[notice] A new release of pip is available: 20.0.1 -> 25.1.1
[notice] To update, run: pip install --upgrade pip
################################################################################
FREEDOM_E_SDK_PIP_CACHE not found, creating virtualenv from Python Package Index
You can pre-download Python packages by running 'make pip-cache'.
################################################################################
. /Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/bin/activate && /Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/bin/python3 /Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/bin/pip install pip==20.0.1
Traceback (most recent call last):
  File "/Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/bin/pip", line 5, in <module>
    from pip._internal.cli.main import main
  File "/Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/lib/python3.12/site-packages/pip/_internal/cli/main.py", line 10, in <module>
    from pip._internal.cli.autocompletion import autocomplete
  File "/Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/lib/python3.12/site-packages/pip/_internal/cli/autocompletion.py", line 9, in <module>
    from pip._internal.cli.main_parser import create_main_parser
  File "/Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/lib/python3.12/site-packages/pip/_internal/cli/main_parser.py", line 7, in <module>
    from pip._internal.cli import cmdoptions
  File "/Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/lib/python3.12/site-packages/pip/_internal/cli/cmdoptions.py", line 19, in <module>
    from distutils.util import strtobool
ModuleNotFoundError: No module named 'distutils'
make[1]: *** [/Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/.stamp] Error 1
make: *** [/Volumes/wdb/nuttx/hifive1/freedom-e-sdk/bsp/sifive-hifive1-revb/design.dts] Error 2
```

- 別に使っているtoolchainの環境変数CROSS_COMPILEの値がここで使う値と違っていた
- scripts/standalone.mkの`CROSS_COMPILE ?= riscv64-unknown-elf`を`CROSS_COMPILE := riscv64-unknown-elf`に変更

```bash
$ make PROGRAM=hello TARGET=sifive-hifive1-revb
/Applications/Xcode.app/Contents/Developer/usr/bin/make -f scripts/virtualenv.mk virtualenv
################################################################################
FREEDOM_E_SDK_PIP_CACHE not found, creating virtualenv from Python Package Index
You can pre-download Python packages by running 'make pip-cache'.
################################################################################
. /Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/bin/activate && /Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/bin/python3 /Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/bin/pip install pip==20.0.1
Traceback (most recent call last):
  File "/Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/bin/pip", line 5, in <module>
    from pip._internal.cli.main import main
  File "/Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/lib/python3.12/site-packages/pip/_internal/cli/main.py", line 10, in <module>
    from pip._internal.cli.autocompletion import autocomplete
  File "/Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/lib/python3.12/site-packages/pip/_internal/cli/autocompletion.py", line 9, in <module>
    from pip._internal.cli.main_parser import create_main_parser
  File "/Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/lib/python3.12/site-packages/pip/_internal/cli/main_parser.py", line 7, in <module>
    from pip._internal.cli import cmdoptions
  File "/Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/lib/python3.12/site-packages/pip/_internal/cli/cmdoptions.py", line 19, in <module>
    from distutils.util import strtobool
ModuleNotFoundError: No module named 'distutils'
make[1]: *** [/Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/.stamp] Error 1
make: *** [/Volumes/wdb/nuttx/hifive1/freedom-e-sdk/bsp/sifive-hifive1-revb/design.dts] Error 2
```

- `distutils`は現行pythonでは提供されなくなった。代わりに`setuptools`を使えとのこと。

```bash
$ brew install python-setuptools
$ make PROGRAM=hello TARGET=sifive-hifive1-revb
/Applications/Xcode.app/Contents/Developer/usr/bin/make -f scripts/virtualenv.mk virtualenv
################################################################################
FREEDOM_E_SDK_PIP_CACHE not found, creating virtualenv from Python Package Index
You can pre-download Python packages by running 'make pip-cache'.
################################################################################
. /Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/bin/activate && /Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/bin/python3 /Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/bin/pip install pip==20.0.1
Traceback (most recent call last):
  File "/Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/bin/pip", line 5, in <module>
    from pip._internal.cli.main import main
  File "/Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/lib/python3.12/site-packages/pip/_internal/cli/main.py", line 10, in <module>
    from pip._internal.cli.autocompletion import autocomplete
  File "/Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/lib/python3.12/site-packages/pip/_internal/cli/autocompletion.py", line 9, in <module>
    from pip._internal.cli.main_parser import create_main_parser
  File "/Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/lib/python3.12/site-packages/pip/_internal/cli/main_parser.py", line 7, in <module>
    from pip._internal.cli import cmdoptions
  File "/Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/lib/python3.12/site-packages/pip/_internal/cli/cmdoptions.py", line 19, in <module>
    from distutils.util import strtobool
ModuleNotFoundError: No module named 'distutils'
make[1]: *** [/Volumes/wdb/nuttx/hifive1/freedom-e-sdk/venv/.stamp] Error 1
make: *** [/Volumes/wdb/nuttx/hifive1/freedom-e-sdk/bsp/sifive-hifive1-revb/design.dts] Error 2
```

- 結局、python 3.12, 3.11ではエラーがなくならず、3.8まで下げたところうまく行った。

```bash
$ cd /usr/local/bin
$ ls
$ ls -l python*
lrwxr-xr-x 1 dspace wheel 43  6 24 16:38 python3 -> ../Cellar/python@3.11/3.11.9/bin/python3.11
lrwxr-xr-x 1 dspace wheel 50  6 24 16:39 python3-config -> ../Cellar/python@3.11/3.11.9/bin/python3.11-config
lrwxr-xr-x 1 dspace wheel 46  9  2  2024 python3.10 -> ../Cellar/python@3.10/3.10.14_1/bin/python3.10
lrwxr-xr-x 1 dspace wheel 53  9  2  2024 python3.10-config -> ../Cellar/python@3.10/3.10.14_1/bin/python3.10-config
lrwxr-xr-x 1 dspace wheel 43  6 24 16:23 python3.11 -> ../Cellar/python@3.11/3.11.9/bin/python3.11
lrwxr-xr-x 1 dspace wheel 50  6 24 16:23 python3.11-config -> ../Cellar/python@3.11/3.11.9/bin/python3.11-config
lrwxr-xr-x 1 dspace wheel 41  4  9  2024 python3.8 -> ../Cellar/python@3.8/3.8.19/bin/python3.8
lrwxr-xr-x 1 dspace wheel 48  4  9  2024 python3.8-config -> ../Cellar/python@3.8/3.8.19/bin/python3.8-config
lrwxr-xr-x 1 dspace wheel 43  9  2  2024 python3.9 -> ../Cellar/python@3.9/3.9.19_1/bin/python3.9
lrwxr-xr-x 1 dspace wheel 50  9  2  2024 python3.9-config -> ../Cellar/python@3.9/3.9.19_1/bin/python3.9-config
$ rm python3 python3-config
remove python3? y
remove python3-config? y
$ ln -s ../Cellar/python@3.8/3.8.19/bin/python3.8 python3
$ ln -s ../Cellar/python@3.8/3.8.19/bin/python3.8-config python3-config
$ ls -l python*
lrwxr-xr-x 1 dspace wheel 41  6 24 18:00 python3 -> ../Cellar/python@3.8/3.8.19/bin/python3.8
lrwxr-xr-x 1 dspace wheel 48  6 24 18:00 python3-config -> ../Cellar/python@3.8/3.8.19/bin/python3.8-config
lrwxr-xr-x 1 dspace wheel 46  9  2  2024 python3.10 -> ../Cellar/python@3.10/3.10.14_1/bin/python3.10
lrwxr-xr-x 1 dspace wheel 53  9  2  2024 python3.10-config -> ../Cellar/python@3.10/3.10.14_1/bin/python3.10-config
lrwxr-xr-x 1 dspace wheel 43  6 24 16:23 python3.11 -> ../Cellar/python@3.11/3.11.9/bin/python3.11
lrwxr-xr-x 1 dspace wheel 50  6 24 16:23 python3.11-config -> ../Cellar/python@3.11/3.11.9/bin/python3.11-config
lrwxr-xr-x 1 dspace wheel 41  4  9  2024 python3.8 -> ../Cellar/python@3.8/3.8.19/bin/python3.8
lrwxr-xr-x 1 dspace wheel 48  4  9  2024 python3.8-config -> ../Cellar/python@3.8/3.8.19/bin/python3.8-config
lrwxr-xr-x 1 dspace wheel 43  9  2  2024 python3.9 -> ../Cellar/python@3.9/3.9.19_1/bin/python3.9
lrwxr-xr-x 1 dspace wheel 50  9  2  2024 python3.9-config -> ../Cellar/python@3.9/3.9.19_1/bin/python3.9-config
$ python3 --version
Python 3.8.19
$ gmake PROGRAM=hello TARGET=sifive-hifive1-revb
...
${HOME}/nuttx/hifive1/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-apple-darwin/bin/riscv64-unknown-elf-size /Volumes/wdb/nuttx/hifive1/freedom-e-sdk/software/hello/debug/hello.elf
   text	   data	    bss	    dec	    hex	filename
  26588	   2804	   3236	  32628	   7f74	/Volumes/wdb/nuttx/hifive1/freedom-e-sdk/software/hello/debug/hello.elf
${HOME}/nuttx/hifive1/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-apple-darwin/bin/riscv64-unknown-elf-objcopy -O ihex /Volumes/wdb/nuttx/hifive1/freedom-e-sdk/software/hello/debug/hello.elf /Volumes/wdb/nuttx/hifive1/freedom-e-sdk/software/hello/debug/hello.hex
                                        // ボードをMacに接続
$ make PROGRAM=hello TARGET=sifive-hifive1-revb upload
scripts/upload --hex /Volumes/wdb/nuttx/hifive1/freedom-e-sdk/software/hello/debug/hello.hex --jlink JLinkExe
...
Downloading file [/Volumes/wdb/nuttx/hifive1/freedom-e-sdk/software/hello/debug/hello.hex]...
J-Link: Flash download: Bank 0 @ 0x20000000: 1 range affected (65536 bytes)
J-Link: Flash download: Total: 1.123s (Prepare: 0.000s, Compare: 0.319s, Erase: 0.142s, Program & Verify: 0.559s, Restore: 0.102s)
J-Link: Flash download: Program & Verify speed: 114 KB/s
O.K.
...
$ minicom
                                        // Resetボタンを押下
Welcome to minicom 2.8

OPTIONS: 
Compiled on Jan  4 2021, 00:04:46.
Port /dev/cu.usbmodem0009790170491, 18:04:37
Using character set conversion
                                                                        
Press Meta-Z for help on special keys                                   
                                                                        
Bench Clock Reset Complete                                              

ATE0-->ATE0
OK
AT+BLEINIT=0-->OK
AT+CWMODE=0-->OK

Hello, World!
```

## `make PROGRAM=hello TARGET=sifive-hifive1-revb upload`の内容

```bash
$ cat bsp/sifive-hifive1-revb/settings.mk
TARGET_TAGS = board jlink
$ cat Makefile
ifneq ($(filter jlink,$(TARGET_TAGS)),)         
upload: $(PROGRAM_HEX)
    scripts/upload --hex $(PROGRAM_HEX) --jlink $(SEGGER_JLINK_EXE)     // こちらが選択される
else
upload: $(PROGRAM_ELF)
    scripts/upload --elf $(PROGRAM_ELF) --openocd $(RISCV_OPENOCD) --gdb $(RISCV_GDB) --openocd-config bsp/$(TARGET)/openocd.cfg
endif
$ cat scripts/upload
if [ "$jlink" != "" ]
then
echo -e "loadfile $hex\nrnh\nexit" | $jlink -device FE310 -if JTAG -speed 4000 -jtagconf -1,-1 -autoconnect 1
```

### jlinkコマンドの内容

#### 情報源

- [J-Link Commander](https://kb.segger.com/J-Link_Commander#Command_line_options)
- [FAQ・テクニカルガイド: 「J-Link Commander」ツール](https://www.embitek.co.jp/technote/jlink/JLink_HowToUse_JLinkCommander.pdf)

#### コマンダーコマンド

| コマンド | 説明 |
|:---------|:-----|
| loadfile | データファイルをターゲットメモリにロードする |
| rnh | ターゲットを停止せずに、リセットする |
| exit | J-Link コマンダーを終了する |

#### コマンドラインオプション

| オプション | 説明 |
|:---------|:-----|
| -device <DeviceName> | ターゲットデバイスを選択する |
| -if <TargetInterface> | ターゲットインタフェースを構成する |
| -speed <InterfaceSpeed> | ターゲットインタフェース速度を構成する |
| -jtagconf <IRPre>,<DRPre> | ターゲットデバイスのJTAGスキャンを構成する。<br/>自動検出させるにはIRPre==-1, DRPre==-1を指定する（=> 最初の既知のデバイスが使用される） |
| -autoconnect <Value> | Value==1: J-Linkコマンダーを自動的にターゲットに接続させる。  |
