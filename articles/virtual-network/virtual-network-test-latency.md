---
title: Azure Virtual Network で Azure Virtual Machine のネットワーク待機時間をテストする | Microsoft Docs
description: 仮想ネットワーク上の Azure 仮想マシン間のネットワーク待機時間をテストする方法について学ぶ
services: virtual-network
documentationcenter: na
author: steveesp
manager: Marina Lipshteyn
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: steveesp
ms.openlocfilehash: 7dc8aac730fdf46cab47a3297b8c001cb0b8e314
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99226407"
---
# <a name="test-vm-network-latency"></a>VM ネットワークの待ち時間のテスト

最も正確な結果を得るには、Azure 仮想マシン (VM) のネットワーク待機時間を、測定用に設計されたツールで測定します。 SockPerf (Linux 用) や latte.exe (Windows 用) などの一般で利用されているツールは、アプリケーションの待機時間など、他の種類の待機時間を除外しながら、ネットワーク待機時間を分離します。 これらのツールは、アプリケーションのパフォーマンスに影響するネットワークトラフィックの種類 (伝送制御プロトコル [TCP] および ユーザー データグラム プロトコル [UDP] トラフィック) に重点を置いています。 

Ping などのその他の一般的な接続ツールでは、待機時間を測定できますが、実際のワークロードで使用されるネットワーク トラフィックをその結果が表していない場合があります。 これらのツールのほとんどは、インターネット制御メッセージ プロトコル (ICMP) を使用するため、アプリケーションのトラフィックとは異なる方法で処理でき、その結果は TCP および UDP を使用するワークロードには適用されない可能性があります。 

ほとんどのアプリケーションで使用されるプロトコルの正確なネットワーク待機時間をテストするには、SockPerf (Linux 用) および latte.exe (Windows 用) を利用することで、最も関連性の高い結果が生成されます。 この記事では、どちらのツールについても説明します。

## <a name="overview"></a>概要

2 つの VM (1 つは送信側、もう 1 つは受信側) を使用して、双方向の通信チャネルを作成します。 このアプローチでは、双方向でパケットを送受信し、ラウンド トリップ時間 (RTT) を測定できます。

これらのアプローチは、2 つの VM 間または 2 台の物理コンピューター間のネットワーク待機時間を測定するために使用できます。 待機時間の測定は、次のシナリオで役に立ちます。

- デプロイされた VM 間のネットワーク待機時間のベンチマークを確立します。
- 関連する変更が以下の項目に対して行われた後のネットワーク待機時間の変更の影響を比較する
  - 構成の変更を含むオペレーティング システム (OS) またはネットワーク スタック ソフトウェア。
  - VM デプロイ方法 (可用性ゾーンまたは近接通信配置グループ (PPG) へのデプロイなど)。
  - 高速ネットワークやサイズ変更などの VM のプロパティ。
  - 仮想ネットワーク (ルーティングやフィルターの変更など)。

### <a name="tools-for-testing"></a>テスト用のツール
待機時間は、次の 2 つの異なるツールオプションを使用して測定できます。

* Windows ベースのシステムの場合: [latte.exe (Windows)](https://github.com/microsoft/latte/releases/download/v0/latte.exe)
* Linux ベースのシステムの場合: [SockPerf (linux)](https://github.com/mellanox/sockperf)

これらのツールを使用すると、アプリケーションによって使用されておらず、パフォーマンスに影響を与えない ICMP (Ping) や他の種類のパケットではなく、TCP または UDP ペイロードの配信時間だけが測定されます。

### <a name="tips-for-creating-an-optimal-vm-configuration"></a>最適な VM 構成作成のヒント

VM 構成を作成するときは、次の推奨事項に留意してください。
- 最新バージョンの Windows または Linux を使用する。
- 最良の結果を得るため、高速ネットワークを有効にする。
- [Azure 近接通信配置グループ](../virtual-machines/co-location.md)を使用して VM をデプロイする。
- 通常、大規模な VM は小規模の VM よりもパフォーマンスが優れています。

### <a name="tips-for-analysis"></a>分析に関するヒント

テスト結果を分析する際は、次の推奨事項に留意してください。

- デプロイ、構成、最適化が完了したらすぐにベースラインを確立する。
- 変更を制御し、新しい結果を常にベースラインと比較するか、テストごとに比較する。
- 変更が見つかった場合、または計画されるたびにテストを繰り返す。


## <a name="test-vms-that-are-running-windows"></a>Windows を実行している VM をテストする

### <a name="get-latteexe-onto-the-vms"></a>latte.exe を VM に読み込む

[最新バージョンの Latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b) をダウンロードします。

latte.exe は、別のフォルダー (*c:\tools* など) に入れることを検討してください。

### <a name="allow-latteexe-through-windows-defender-firewall"></a>latte.exe が Windows Defender ファイアウォールを通過することを許可する

*受信側* で、Windows Defender ファイアウォールの許可ルールを作成して、latte.exe トラフィックの到着を許可します。 特定の TCP ポートの受信を許可するのではなく、名前によってlatte.exe プログラム全体を許可する方が簡単です。

次のコマンドを実行して、latte.exe が Windows Defender ファイアウォールを通過することを許可します。

```cmd
netsh advfirewall firewall add rule program=<path>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

たとえば、latte.exe を *c:tools* フォルダーにコピーした場合、このコマンドは次のようになります。

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>待機時間テストを実行する

* *受信側* で、latte.exe を起動します (PowerShell からではなく、コマンド ウィンドウから実行します)。

    ```cmd
    latte -a <Receiver IP address>:<port> -i <iterations>
    ```

    代表的な結果を返すには、65,000 前後のイテレーションが十分な長さです。

    使用可能な任意のポート番号で構いません。

    VM に IP アドレス 10.0.0.4 がある場合、コマンドは次のようになります。

    `latte -a 10.0.0.4:5005 -i 65100`

* *送信側* で、latte.exe を起動します (PowerShell からではなく、コマンド ウィンドウから実行します)。

    ```cmd
    latte -c -a <Receiver IP address>:<port> -i <iterations>
    ```

    結果として得られるコマンドは、これが *クライアント* (*送信側*) であることを示す &nbsp;*-c* が追加されている点を除き、受信側と同じです。

    `latte -c -a 10.0.0.4:5005 -i 65100`

結果を待ちます。 VM 同士の距離によっては、テストの完了までに数分かかる場合があります。 長いテストを実行する前に、成功についてテストするために少数のイテレーションで開始することを検討してください。

## <a name="test-vms-that-are-running-linux"></a>Linux を実行している VM をテストする

Linux を実行している VM をテストするには、[SockPerf](https://github.com/mellanox/sockperf) を使用します。

### <a name="install-sockperf-on-the-vms"></a>SockPerf を VM にインストールする

Linux VM の *送信側* と *受信側* の両方で、次のコマンドを実行して、VM 上に SockPerf を準備します。 一般向けのディストリビューションのためのコマンドが用意されています。

#### <a name="for-red-hat-enterprise-linux-rhelcentos"></a>Red Hat Enterprise Linux (RHEL)/CentOS へのアクセス

次のコマンドを実行します。

```bash
#RHEL/CentOS - Install Git and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu"></a>Ubuntu の場合

次のコマンドを実行します。

```bash
#Ubuntu - Install Git and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros"></a>すべてのディストリビューションの場合

次の手順に従って SockPerf をコピーし、コンパイルしてインストールします。

```bash
#Bash - all distros

#From bash command line (assumes Git is installed)
git clone https://github.com/mellanox/sockperf
cd sockperf/
./autogen.sh
./configure --prefix=

#make is slower, may take several minutes
make

#make install is fast
sudo make install
```

### <a name="run-sockperf-on-the-vms"></a>SockPerf を VM で実行する

SockPerf のインストールが完了すると、VM は待機時間テストを実行する準備が整います。 

まず、*受信側* で SockPerf を起動します。

使用可能な任意のポート番号で構いません。 この例では、ポート 12345 を使用します。

```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```

サーバーがリッスンしているので、クライアントは、リッスンしているポート (この場合は 12345) でサーバーへのパケットの送信を開始できます。

次の例に示すように、代表的な結果が返されるには約 100 秒あれば十分です。

```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

結果を待ちます。 VM 同士がどれだけ離れているかによって、イテレーションの数は異なります。 長いテストを実行する前に、まずは約 5 秒の短いテストから開始して成功するかどうかをテストすることを検討してください。

この SockPerf の例では、一般的な平均サイズのパケットの 350 バイトのメッセージ サイズが使用されています。 サイズは、お客様の VM で実行されるワークロードをより正確に表すために、より高いまたは低いサイズに調整できます。


## <a name="next-steps"></a>次のステップ
* [Azure 近接通信配置グループ](../virtual-machines/co-location.md)を使用して待機時間を改善する。
* シナリオに合わせて [VM のネットワークを最適化](../virtual-network/virtual-network-optimize-network-bandwidth.md)する方法について学ぶ。
* [仮想マシンに帯域幅が割り当てられる方法](../virtual-network/virtual-machine-network-throughput.md)に関するページを参照してください。
* 詳細については、[Azure Virtual Network のよくある質問](../virtual-network/virtual-networks-faq.md)に関するページを参照してください。