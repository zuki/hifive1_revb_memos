# 第7章 SiFiveソフトウェアの開発

FE310-G002のブートコードはHiFive1 Rev Bの外部SPI Flushメモリ（アドレス 
0x20010000）にジャンプします。FE310-G002が実行するプログラムを変更するには
デバッグ/プログラミングインタフェースを使用して、新しくコンパイルしたプログラムを
外部SPIフラッシュにフラッシュします。

SiFiveは以下の複数のソフトウェア開発フローをサポートしています。

- Freedom Studio（7.2章）はEclipseベースのIDEであり、開発に必要なすべてを1つの
  ダウンロードにバンドルしています。
- Freedom E SDK（7.3章）は、コマンドラインツールでソースコードをコンパイルできます。

これらのソフトウェア開発フローは同じツールセットをインストールしますが
バージョン、インストールパス、関連するソフトウェアライブラリやサンプルは
開発フローごとに異なります。

Arduino® IDEではHiFive1 Rev Bはサポートされていません。

## 7.1 サポートされている開発プラットフォーム

Freedom Studio IDEはLinux, macOS, Windowsをサポートしています。

Freedom E SDKはLinuxとmacOSをサポートしています。

## 7.2 Freedom Studio IDEを使ったソフトウェアの開発

省略

## 7.3 Freedom E SDKコマンドラインツールを使ったソフトウェアの開発

Freedom-E-SDKはSiFive Inc.が管理しているgithubリポジトリで、SiFiveのFreedomと
RISC-V Core IPプラットフォーム向けのソフトウェアの開発を簡単に始めることができます。
SDKは幅広いSiFiveコアコンプレックス、SoC、エミュレーション環境をサポートしています。
この節ではツールチェインのセットアップとSDKの設定方法について説明します。また、
サンプルプログラムをビルドし、SiFive Core IPデバイスに含まれているRTLテストベンチで
実行する方法を説明します。さらに、カスタムBSPをインポートし、カスタムBSPターゲットを
使用してプログラムをビルドする方法についても説明します。

この節ではSDKがLinuxまたはLinux類似環境で使用されることを想定しています。

### 7.3.1 Freedom E SDKのセットアップ

#### 必要条件

このSDKを使用するにはホストマシン上で次のソフトぅエアが利用可能である必要があります。

- GNU Make
- Git

#### ツールチェインの必要条件

プログラムのビルドとデバッグを行うにはホストマシンに以下のツールチェインが
インストールされている必要があります.

- RISC-V GNU Toolchain
- Segger J-Link OB debugger

ツールチェーンは[SiFiveのウェブサイト](https://www.sifive.com/boards)から
ダウンロードできます。ビルド済みツールはRISC-V 32ビットISAと64ビットISAを
サポートし、Linux、macOS、Windowsホストで動作するように慎重にパッケージ化
されています。

プラットフォーム用のツールチェインをダウンロードし、好みの場所に解凍してください。
そして、Freedom-E-SDKがツールを見つけられるようにRISCV_PATH環境変数を設定して
ください。例えば次のようにします。

```bash
$ cp riscv64-unknown-elf-gcc-<date>-<platform>.tar.gz desired-untar-dir
$ cd desired-untar-dir
$ tar -xvf riscv64-unknown-elf-gcc-<date>-<platform>.tar.gz
$ export RISCV_PATH=desired-untar-dir/riscv64-unknown-elf-gcc-<date>-<version>
```

Segger J-Link OBデバッガのインストールに関しては6.1節を参照してください。

#### Freedom E SDKリポジトリのクローン

Freedom E SDKリポジトリは次のコマンドでクローンできます。

```bash
$ git clone --recursive https://github.com/sifive/freedom-e-sdk.git
$ cd freedom-e-sdk
```

リポジトリに含まれているgit submodulesをクローンするために`--recursive`オプションは必須です。
最初に--recursiveオプションをつけなかった場合は次のコマンドを使ってsubmodulesを更新する
ことにより同じ事を行うことができます。

```bash
$ git submodule update --init --recursive
```

より詳しい情報は[https://github.com/sifive/freedom-e-sdk](https://github.com/sifive/freedom-e-sdk)を
参照してください。

### 7.3.2 Freedom Metal

Freedom MetalはSiFive社が開発したライブラリで、SiFive社のRISC-V IP、RISC-V FPGA評価イメージ、
開発ボードのすべてに対してポータブルなソフトウェアを記述するためのものです。Freedom Metal APIを
使って書かれたプログラムはすべてのSiFive RISC-Vターゲット用にビルドされ実行されることを意図して
います。このため、Freedom Metalはポータブルなテストやベアメタルアプリケーションプログラミングを
書くこと、および、オペレーティングシステムをRISC-Vに移植するためのハードウェア抽象化レイヤとして
適しています。

また、Freedom-E-SDKを使う方はFreedom Metalによって提供されるAPIにまだ改良が加えられていることに
注意する必要があります。そのため、Freedom E SDKのstable releaseが出るまではFreedom Metal APIは
ベータ版と考えるべきです。

- [Freedom Metalリポジトリ](https://github.com/sifive/freedom-metal)
- [Freedom Metal APIドキュメント](https://sifive.github.io/freedom-metal-docs/index.html)

### 7.3.3 Freedom E SDK標準BSP

Freedom Metal Compatibility Libraryレイヤはハードウェア抽象化レイヤを提供するためにボード
サポートパッケージ (BSP) ファイルを使用します。これらのbspファイルはFreedom- E-SDKのbsp
フォルダの下にあり、各ターゲットディレクトリ内に完全にカプセル化されています。

Freedom Metal用のHiFive1 Rev Bサポートファイルはすべて`bsp/sifive-hifive1b/`配下にあり、
次のように構成されています。

#### design.dts

ターゲットの DeviceTree記述です。このファイルはFreedom Metalライブラリをターゲット
デバイス用にパラメータ化するために使用されます。Freedom Metalのユーザがターゲットで
どのような機能と周辺機器が利用できるかを認識できるようにリファレンスとして含まれています。

#### metal.h

ターゲットデバイスをサポートするための構造体をインスタンス化するためにFreedom Metal
内部で使用されるFreedom Metalマシンヘッダです。

#### metal.lds

ターゲットデバイス用のリンカスクリプトです。

#### settings.mk

RISC-V GNU Toolchainへの`-march`引数と`-mabi`引数をセットするのに使用されます。

### 7.3.4 サンプルプログラム

`software`ディレクトリには以下のようなサンプルプログラムが含まれています。

#### hello

#### return-pass

#### return-fail

#### example-itim

#### software-interrupt

#### timer-interrupt

#### local-interrupt

#### example-pmp

#### example-spi

### 7.3.5 SDKの更新

SDKを最新版に更新するには次のコマンドを実行します。

```bash
$ git pull origin master
$ git submodule update --init --recursive
```

### 7.3.6 Freedom E SDKの使用法

#### サンプルのビルド

ベアメタルのRISC-Vプログラムをコンパイルするするには次のコマンドを実行します。

```bash
$ make BSP=metal [PROGRMA=hello] TARGET=sifive-hifive1-revb software
```

角括弧はmake呼び出しのオプションパラメータを示しています。これらのパラメータの
デフォルト値はsifive-hifive1ターゲット用のhelloサンプルをビルドするようにビルド
スクリプトに指示します。たとえば、`timer^interrupt`サンプルをビルドしたい場合は
代わりに次のコマンドを実行します。

```bash
$ make BSP=metal PROGRAM=timer-interrupt TARGET=sifive-hifive1-revb software
```

#### helloプログラムのターゲットボードへのアップロード

```bash
$ make BSP=metal PROGRMA=hello TARGET=sifive-hifive1-revb upload
```

#### helloプログラムのデバッグ

```bash
$ make BSP=metal PROGRMA=hello TARGET=sifive-hifive1-revb debug
```

#### helloプログラムビルドディレクトリのクリア

```bash
$ make BSP=metal PROGRMA=hello TARGET=sifive-hifive1-revb clean
```

#### 一連の操作の連続実行: helloプログラムをclean, build, uploadする

```bash
$ make BSP=metal PROGRAM=hello TARGET=sifive-hifive1-revb clean software upload
```

#### デバッグのコツ: プログラムアドレス、セクション、opcode, freedom-e-sdkのソースコードを見る

```bash
$ $RISCV_PATH/bin/riscv64-unknown-elf-objdump -dS software/hello/debug/hello.elf | less
$ $RISCV_PATH/bin/riscv64-unknown-elf-readelf -a software/hello/debug/hello.elf | less
```

#### スタンドアローンプロジェクトの作成

スタンドアローンターゲットを使用してプログラムをスタンドアローンプロジェクト
ディレクトリにエクスポートできます。出来上がったプロジェクトは特定のTARGETに
ロックされます。

STANDALONE_DESTは適切なプロジェクトディレクトリを指定するための必須引数です。

```bash
$ make standalone BSP=metal PROGRAM=hello TARGET=sifive-hifive1-revb STANDALONE_DEST=desired-standalone-dir
```

スタンドアローンプロジェクトを作成すると`make`とタイプするだけでそのプロジェクトを
コンパイルすることができます。

```bash
$ cd desired-standalone-dir
$ make
```

その他のオプションは以下を実行することで見ることができます。

```bash
$ make help
```