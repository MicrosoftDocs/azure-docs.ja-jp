---
title: Microsoft Azure Virtual Network への VPN スループットを検証する
description: この記事は、オンプレミスのリソースから Azure 仮想マシンへのネットワーク スループットを検証する際に役立ちます。
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
manager: dcscontentpm
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 09/02/2020
ms.author: radwiv
ms.reviewer: chadmat;genli
ms.openlocfilehash: 2d5b51e8cfbfcb5f771e9da524231f8ddfc40a9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94660935"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>仮想ネットワークへの VPN スループットを検証する方法

VPN ゲートウェイ接続を使用すると、Azure 内の Virtual Network とオンプレミスの IT インフラストラクチャ間の安全なクロスプレミス接続を確立できます。

この記事では、オンプレミスのリソースから Azure 仮想マシン (VM) へのネットワーク スループットを検証する方法を示します。

> [!NOTE]
> この記事の目的は、一般的な問題の診断と修正に役立つことです。 次の情報を使用して問題を解決できない場合は、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

## <a name="overview"></a>概要

VPN ゲートウェイ接続には、次のコンポーネントが含まれます。

* オンプレミスの VPN デバイス ([検証済みの VPN デバイス](vpn-gateway-about-vpn-devices.md#devicetable)の一覧を表示)。
* パブリック インターネット
* Azure VPN Gateway
* Azure VM

次の図は、オンプレミスのネットワークから Azure 仮想ネットワークへの VPN 経由の論理接続を示しています。

![顧客のネットワークから MSFT ネットワークへの VPN を使用した論理接続](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>予想される最大イングレス/エグレスの計算

1. アプリケーションのベースライン スループット要件を特定します。
1. Azure VPN Gateway のスループットの制限を特定します。 [VPN Gateway](vpn-gateway-about-vpngateways.md#gwsku) に関するページの「ゲートウェイの SKU」を参照してください。
1. お使いの VM サイズの [Azure VM スループットのガイダンス](../virtual-machines/sizes.md)を特定します。
1. インターネット サービス プロバイダー (ISP) の帯域幅を特定します。
1. VM、VPN Gateway、または ISP のうち最小の帯域幅を取って、予想されるスループットを計算します。これは、1秒あたりのメガビット数を 8 で除算 (/) して測定されます。

計算したスループットが、アプリケーションのベースライン スループット要件を満たしていない場合は、ボトルネックとして識別されるリソースの帯域幅を増加する必要があります。 Azure VPN Gateway のサイズを変更するには、「[ゲートウェイの SKU を変更する](vpn-gateway-about-vpn-gateway-settings.md#gwsku)」を参照してください。 仮想マシンのサイズを変更するには、「[VM のサイズを変更する](../virtual-machines/windows/resize-vm.md)」を参照してください。 予想されるインターネット帯域幅が得られない場合は、ISP に問い合わせることもできます。

> [!NOTE]
> VPN Gateway のスループットは、すべてのサイト間接続\VNET 間接続、またはポイント対サイト接続の集合です。

## <a name="validate-network-throughput-by-using-performance-tools"></a>パフォーマンス ツールを使用したネットワーク スループットの検証

テスト時の VPN トンネルのスループットの飽和状態によって正確な結果が得られない場合、この検証は非ピーク時に実行する必要があります。

このテストに使用するツールは iPerf で、Windows と Linux の両方で動作し、クライアント モードとサーバー モードの両方があります。 これは、Windows VM では 3 Gbps に制限されています。

このツールは、ディスクへの読み取り/書き込み操作を実行しません。 1 つの終端から他方の終端への自動生成 TCP トラフィックのみを生成します。 これは、クライアント ノードとサーバー ノード間で使用可能な帯域幅を測定する実験に基づいて、統計情報を生成します。 2 台のノード間でテストする場合、1 台のノードはサーバーとして機能し、もう一方のノードはクライアントとして機能します。 このテストが完了したら、両方のノードでアップロードとダウンロードの両方のスループットをテストするために、ノードのロールを反転することをお勧めします。

### <a name="download-iperf"></a>iPerf のダウンロード

[iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip) をダウンロードします。 詳細については、[iPerf のドキュメント](https://iperf.fr/iperf-doc.php)を参照してください。

 > [!NOTE]
 > この記事で説明するサード パーティ製品は、Microsoft に関係のない企業によって製造されています。 Microsoft では、暗黙的またはそれ以外に関わらず、これらの製品のパフォーマンスや信頼性について何ら保証しません。

### <a name="run-iperf-iperf3exe"></a>iPerf (iperf3.exe) の実行

1. トラフィックを許可する NSG/ACL ルールを有効にします (Azure VM でのパブリック IP アドレス テスト用)。

1. 両方のノードで、ポート 5001 に対してファイアウォール例外を有効にします。

   **Windows:** 管理者として次のコマンドを実行します。

   ```CMD
   netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
   ```

   テストが完了した際にルールを削除するには、このコマンドを実行します。

   ```CMD
   netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
   ```

   **Azure Linux:** Azure Linux イメージには制限の緩やかなファイアウォールがあります。 ポートでリッスンするアプリケーションがある場合、トラフィックは通過を許可されます。 セキュリティ保護されているカスタム イメージには、明示的に開かれたポートが必要な可能性があります。 一般的な Linux OS レイヤー ファイアウォールには、`iptables`、`ufw`、または`firewalld` が含まれます。

1. サーバー ノードで、iperf3.exe の抽出元のディレクトリに移動します。 次に、iPerf をサーバー モードで実行し、次のコマンドとして、ポート 5001 でリッスンするように設定します。

   ```CMD
   cd c:\iperf-3.1.2-win65

   iperf3.exe -s -p 5001
   ```

   > [!Note]
   > ポート 5001 は、ご使用の環境における特定のファイアウォール制限に対応するようにカスタマイズ可能です。

1. クライアント ノードで、iperf ツールの抽出元のディレクトリに移動し、次のコマンドを実行します。

   ```CMD
   iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
   ```

   クライアントは、ポート 5001 で 30 秒間のトラフィックをサーバーに送信しています。 フラグ "-P" は、サーバー ノードへの 32 個の同時接続を行っていることを示します。

   次の画面は、この例の出力を示しています。

   ![出力](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

1. (省略可能) テスト結果を保持するためには、次のコマンドを実行します。

   ```CMD
   iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
   ```

1. 前の手順を完了したら、サーバー ノードがクライアント ノードになるか、またはその逆になるように、ロールを反転して、同じ手順を実行します。

> [!Note]
> Iperf は唯一のツールではありません。 [NTTTCP は、テストを実行するための代替ソリューションです](../virtual-network/virtual-network-bandwidth-testing.md)。

## <a name="test-vms-running-windows"></a>Windows を実行している VM をテストする

### <a name="load-latteexe-onto-the-vms"></a>Latte.exe を VM に読み込む

最新バージョンの [Latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b) をダウンロードします

Latte.exe は、別のフォルダー (`c:\tools` など) に入れることを検討してください

### <a name="allow-latteexe-through-the-windows-firewall"></a>Latte.exe が Windows ファイアウォールを通過することを許可する

受信側で、Windows ファイアウォールの許可ルールを作成して、Latte.exe トラフィックの到着を許可します。 特定の TCP ポートの受信を許可するのではなく、名前によって Latte.exe プログラム全体を許可する方が簡単です。

### <a name="allow-latteexe-through-the-windows-firewall-like-this"></a>次のように Latte.exe が Windows ファイアウォールを通過することを許可する

`netsh advfirewall firewall add rule program=<PATH>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

たとえば、latte.exe を "c:tools" フォルダーにコピーした場合、このコマンドは次のようになります

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>待機時間テストを実行する

受信側で latte.exe を開始します (PowerShell からではなく CMD から実行します)。

`latte -a <Receiver IP address>:<port> -i <iterations>`

65k 前後のイテレーションが、代表的な結果を返すのに十分な長さです。

使用可能な任意のポート番号で構いません。

VM に IP アドレス 10.0.0.4 がある場合、次のようになります。

`latte -c -a 10.0.0.4:5005 -i 65100`

送信側で latte.exe を開始します (PowerShell からではなく CMD から実行します)。

`latte -c -a <Receiver IP address>:<port> -i <iterations>`

結果として得られるコマンドは、これが "クライアント" (送信側) であることを示す "-c" が追加されている点を除き、受信側と同じです

`latte -c -a 10.0.0.4:5005 -i 65100`

結果を待ちます。 VM の距離によっては、完了までに数分かかる場合があります。 長いテストを実行する前に、成功についてテストするために少数のイテレーションで開始することを検討してください。

## <a name="test-vms-running-linux"></a>Linux を実行している VM をテストする

VM をテストするには、[SockPerf](https://github.com/mellanox/sockperf) を使用します。

### <a name="install-sockperf-on-the-vms"></a>SockPerf を VM にインストールする

Linux VM (送信側と受信側の両方) で、次のコマンドを実行して、VM 上に SockPerf を準備します。

#### <a name="centos--rhel---install-git-and-other-helpful-tools"></a>CentOS / RHEL - Git とその他の便利なツールをインストールする

`sudo yum install gcc -y -q`
`sudo yum install git -y -q`
`sudo yum install gcc-c++ -y`
`sudo yum install ncurses-devel -y`
`sudo yum install -y automake`

#### <a name="ubuntu---install-git-and-other-helpful-tools"></a>Ubuntu - Git とその他の便利なツールをインストールする

`sudo apt-get install build-essential -y`
`sudo apt-get install git -y -q`
`sudo apt-get install -y autotools-dev`
`sudo apt-get install -y automake`

#### <a name="bash---all"></a>Bash - すべて

bash コマンド ラインから (Git がインストールされていると想定)

`git clone https://github.com/mellanox/sockperf`
`cd sockperf/`
`./autogen.sh`
`./configure --prefix=`

Make は低速であり、数分かかることがあります

`make`

Make インストールは高速です

`sudo make install`

### <a name="run-sockperf-on-the-vms"></a>SockPerf を VM で実行する

#### <a name="sample-commands-after-installation-serverreceiver---assumes-servers-ip-is-10004"></a>インストール後のサンプル コマンド。 サーバー/受信側 - サーバーの IP は 10.0.0.4 と想定します

`sudo sockperf sr --tcp -i 10.0.0.4 -p 12345 --full-rtt`

#### <a name="client---assumes-servers-ip-is-10004"></a>クライアント - サーバーの IP は 10.0.0.4 と想定します

`sockperf ping-pong -i 10.0.0.4 --tcp -m 1400 -t 101 -p 12345  --full-rtt`

> [!Note]
> VM と Gateway 間でのスループット テスト中に中間ホップ (仮想アプライアンスなど) がないことを確認します。
> 上記の iPERF/NTTTCP テストの結果が (全体的なスループットに関して) 良くない場合は、次の記事を参照して、問題の考えられる根本原因の背後にある主な要因を理解してください: https://docs.microsoft.com/azure/virtual-network/virtual-network-tcpip-performance-tuning

特に、これらのテスト中にクライアントとサーバーから同時に収集されたパケット キャプチャ トレース (Wireshark/ネットワーク モニター) の分析は、良くないパフォーマンスの評価に役立ちます。 これらのトレースに、パケット損失、長い待ち時間、MTU サイズ、 断片化、TCP 0 ウィンドウ、誤順序のフラグメントなどが含まれる可能性があります。

## <a name="address-slow-file-copy-issues"></a>低速ファイル コピーの問題の対処

前の手順 (iPERF/NTTTCP/など) で評価された全体的なスループットが良好であったとしても、エクスプローラーを使用しているとき、または RDP セッションを介してドラッグ アンド ドロップを使用しているときに、ファイルのコピーが遅くなることがあります。 この問題は、次の要因の一方または両方に原因があります。

* エクスプローラーや RDP などのファイル コピー アプリケーションでは、ファイルのコピー時に複数のスレッドを使用しません。 パフォーマンス向上のためには、[Richcopy](/previous-versions/technet-magazine/dd547088(v=msdn.10)) などのマルチスレッド ファイル コピー アプリケーションを使用して、16 個または 32 個のスレッドを使用してファイルをコピーしてください。 Richcopy でファイル コピーのスレッド数を変更するには、 **[アクション]**  >  **[コピー オプション]**  >  **[ファイルのコピー]** をクリックします。

   ![低速ファイル コピーの問題](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>

   > [!Note]
   > すべてのアプリケーションが同様に動作するわけではなく、すべてのアプリケーション/プロセスがすべてのスレッドを利用するわけではありません。 テストを実行した場合、一部のスレッドが空で、正確なスループットの結果が得られないことがあります。
   > アプリケーション ファイル転送のパフォーマンスを確認するには、スレッド数を連続して増加してマルチスレッドを使用するか、アプリケーションまたはファイル転送の最適なスループットを検出するために削減します。

* 不十分な VM ディスク読み取り/書き込み速度。 詳細については、[Azure Storage のトラブルシューティング](/previous-versions/azure/storage/common/storage-e2e-troubleshooting)に関するページを参照してください。

## <a name="on-premises-device-external-facing-interface"></a>オンプレミスのデバイスの外部に接続するインターフェイス

Azure がローカル ネットワーク ゲートウェイで VPN 経由で接続するオンプレミスの範囲のサブネットについて説明しました。 同時に、オンプレミスのデバイスに対する Azure の VNET アドレス空間を定義します。

* **ルート ベースのゲートウェイ**:ルート ベースの VPN に向けたポリシーまたはトラフィック セレクターは任意の環境間 (またはワイルドカード) として構成されます。

* **ポリシー ベースのゲートウェイ**:ポリシー ベースの VPN では、パケットを暗号化し、オンプレミス ネットワークと Azure VNet の間でアドレスのプレフィックスの組み合わせに基づいて IPsec トンネル経由でそのパケットを送信します。 ポリシー (またはトラフィック セレクター) は、通常、VPN の構成でアクセス リストとして定義されます。

* **UsePolicyBasedTrafficSelector** 接続: 接続の "UsePolicyBasedTrafficSelectors" を $True に設定すると、Azure VPN ゲートウェイは、オンプレミスのポリシー ベースの VPN ファイアウォールに接続するように構成されます。 PolicyBasedTrafficSelectors を有効にする場合は、オンプレミス ネットワーク (ローカル ネットワーク ゲートウェイ) プレフィックスと Azure Virtual Network プレフィックスのすべての組み合わせに対応するトラフィック セレクターが VPN デバイスに定義されている必要があります (any-to-any は不可)。

不適切な構成では、トンネル内で接続が頻繁に切断され、パケットが破棄され、スループットが悪くなり、待機時間が発生する可能性があります。

## <a name="check-latency"></a>待機時間を確認する

待機時間は、次のツールを使用して確認できます。

* WinMTR
* TCPTraceroute
* `ping` と `psping` (これらのツールは RTT の適正な推定値を提供できますが、すべてのケースで使用できるわけではありません)。

![待機時間を確認する](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

MS ネットワーク バックボーンに入る前にいずれかのホップで長い待機時間のスパイクがあることに気付いた場合は、インターネット サービス プロバイダーによるさらなる調査に進むことができます。

"msn.net" 内のホップから大規模で異常な待ち時間のスパイクがあることに気付いた場合は、詳細な調査を行うために MS サポートにお問い合わせください。

## <a name="next-steps"></a>次のステップ

詳細やヘルプについては、次のリンク先を確認してください。

* [Microsoft サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)