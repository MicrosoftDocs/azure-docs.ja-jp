---
title: Microsoft Azure Virtual Network への VPN スループットの検証 | Microsoft Docs
description: このドキュメントの目的は、ユーザーがオンプレミスのリソースから Azure 仮想マシンへのネットワーク スループットを検証できるようにすることです。
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: jasmc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2018
ms.author: radwiv;chadmat;genli
ms.openlocfilehash: 819415712d8e605825957aa602fc99dcf6902d82
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2019
ms.locfileid: "56821663"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>仮想ネットワークへの VPN スループットを検証する方法

VPN ゲートウェイ接続を使用すると、Azure 内の Virtual Network とオンプレミスの IT インフラストラクチャ間の安全なクロスプレミス接続を確立できます。

この記事では、オンプレミスのリソースから Azure 仮想マシン (VM) へのネットワーク スループットを検証する方法を示します。 また、トラブルシューティングのガイドラインも示します。

>[!NOTE]
>この記事の目的は、一般的な問題の診断と修正に役立つことです。 次の情報を使用して問題を解決できない場合は、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
>
>

## <a name="overview"></a>概要

VPN ゲートウェイ接続には、次のコンポーネントが含まれます。

- オンプレミスの VPN デバイス ([検証済みの VPN デバイス](vpn-gateway-about-vpn-devices.md#devicetable)の一覧を表示)。
- パブリック インターネット
- Azure VPN Gateway
- Azure VM

次の図は、オンプレミスのネットワークから Azure 仮想ネットワークへの VPN 経由の論理接続を示しています。

![顧客のネットワークから MSFT ネットワークへの VPN を使用した論理接続](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>予想される最大イングレス/エグレスの計算

1.  アプリケーションのベースライン スループット要件を特定します。
2.  Azure VPN Gateway のスループットの制限を特定します。 [VPN Gateway](vpn-gateway-about-vpngateways.md#gwsku) に関するページの「ゲートウェイの SKU」を参照してください。
3.  お使いの VM サイズの [Azure VM スループットのガイダンス](../virtual-machines/virtual-machines-windows-sizes.md)を特定します。
4.  インターネット サービス プロバイダー (ISP) の帯域幅を特定します。
5.  (VM、ゲートウェイ、ISP) * 0.8 の最小の帯域幅の予想されるスループットを計算します。

計算したスループットが、アプリケーションのベースライン スループット要件を満たしていない場合は、ボトルネックとして識別されるリソースの帯域幅を増加する必要があります。 Azure VPN Gateway のサイズを変更するには、「[ゲートウェイの SKU を変更する](vpn-gateway-about-vpn-gateway-settings.md#gwsku)」を参照してください。 仮想マシンのサイズを変更するには、「[VM のサイズを変更する](../virtual-machines/virtual-machines-windows-resize-vm.md)」を参照してください。 予想されるインターネット帯域幅が得られない場合、ISP に問い合わせる必要がある場合があります。

## <a name="validate-network-throughput-by-using-performance-tools"></a>パフォーマンス ツールを使用したネットワーク スループットの検証

テスト時の VPN トンネルのスループットの飽和状態によって正確な結果が得られない場合、この検証は非ピーク時に実行する必要があります。

このテストに使用するツールは iPerf で、Windows と Linux の両方で動作し、クライアント モードとサーバー モードの両方があります。 これは、Windows VM では 3 Gbps に制限されています。

このツールは、ディスクへの読み取り/書き込み操作を実行しません。 1 つの終端から他方の終端への自動生成 TCP トラフィックのみを生成します。 これは、クライアント ノードとサーバー ノード間で使用可能な帯域幅を測定する実験に基づいて、統計情報を生成します。 2 台のノード間でテストする場合、1 台はサーバーとして機能し、他方はクライアントとして機能します。 このテストが完了したら、両方のノードでアップロードとダウンロードの両方のスループットをテストするために、ロールを反転することをお勧めします。

### <a name="download-iperf"></a>iPerf のダウンロード
[iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip) をダウンロードします。 詳細については、[iPerf のドキュメント](https://iperf.fr/iperf-doc.php)を参照してください。

 >[!NOTE]
 >この記事で説明するサード パーティ製品は、Microsoft に関係のない企業によって製造されています。 Microsoft では、暗黙的またはそれ以外に関わらず、これらの製品のパフォーマンスや信頼性について何ら保証しません。
 >
 >

### <a name="run-iperf-iperf3exe"></a>iPerf (iperf3.exe) の実行
1. トラフィックを許可する NSG/ACL ルールを有効にします (Azure VM でのパブリック IP アドレス テスト用)。

2. 両方のノードで、ポート 5001 に対してファイアウォール例外を有効にします。

    **Windows:** 管理者として次のコマンドを実行します。

    ```CMD
    netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
    ```

    テストが完了した際にルールを削除するには、このコマンドを実行します。

    ```CMD
    netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
    ```
     
    **Azure Linux:** Azure Linux イメージには制限の緩やかなファイアウォールがあります。 ポートでリッスンするアプリケーションがある場合、トラフィックは通過を許可されます。 セキュリティ保護されているカスタム イメージには、明示的に開かれたポートが必要な可能性があります。 一般的な Linux OS レイヤー ファイアウォールには、`iptables`、`ufw`、または`firewalld` が含まれます。

3. サーバー ノードで、iperf3.exe の抽出元のディレクトリに移動します。 iPerf をサーバー モードで実行し、次のコマンドとして、ポート 5001 でリッスンするように設定します。

     ```CMD
     cd c:\iperf-3.1.2-win65

     iperf3.exe -s -p 5001
     ```

4. クライアント ノードで、iperf ツールの抽出元のディレクトリに移動し、次のコマンドを実行します。

    ```CMD
    iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
    ```

    クライアントは、30 秒間ポート 5001 で、サーバーへのトラフィックを誘導しています。 フラグ '-P' は、サーバー ノードへの 32 個の同時接続を使用していることを示します。

    次の画面は、この例の出力を示しています。

    ![出力](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

5. (省略可能) テスト結果を保持するためには、次のコマンドを実行します。

    ```CMD
    iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
    ```

6. 前の手順を完了したら、サーバー ノードがクライアントになるか、またはその逆になるように、ロールを反転して、同じ手順を実行します。

## <a name="address-slow-file-copy-issues"></a>低速ファイル コピーの問題の対処
エクスプローラーを使用するか、RDP セッション経由でドラッグ アンド ドロップする場合に、低速ファイル コピーが発生することがあります。 この問題は、次の要因の一方または両方に原因があります。

- エクスプローラーや RDP などのファイル コピー アプリケーションでは、ファイルのコピー時に複数のスレッドを使用しません。 パフォーマンス向上のためには、[Richcopy](https://technet.microsoft.com/magazine/2009.04.utilityspotlight.aspx) などのマルチスレッド ファイル コピー アプリケーションを使用して、16 個または 32 個のスレッドを使用してファイルをコピーしてください。 Richcopy でファイル コピーのスレッド数を変更するには、**[アクション]**  >  **[コピー オプション]**  >  **[ファイルのコピー]** をクリックします。<br><br>
![低速ファイル コピーの問題](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>
- 不十分な VM ディスク読み取り/書き込み速度。 詳細については、[Azure Storage のトラブルシューティング](../storage/common/storage-e2e-troubleshooting.md)に関するページを参照してください。

## <a name="on-premises-device-external-facing-interface"></a>オンプレミスのデバイスの外部に接続するインターフェイス
オンプレミスの VPN デバイスのインターネットに接続する IP アドレスが、Azure の[ローカル ネットワーク](vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway)定義に含まれている場合、VPN が表示されない、突発的な切断、またはパフォーマンスの問題が発生することがあります。

## <a name="checking-latency"></a>待機時間のチェック
tracert を使用して、Microsoft Azure エッジ デバイスを追跡し、ホップ間で 100 ミリ秒を超える遅延があるかどうかを判断します。

オンプレミスのネットワークから、Azure Gateway または VM の VIP に対して *tracert* を実行します。 * のみが返された場合、Azure エッジに到達したことがわかります。 "MSN" を含む DNS 名が返された場合、Microsoft バックボーンに到達したことがわかります。<br><br>
![待機時間のチェック](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

## <a name="next-steps"></a>次の手順
詳細やヘルプについては、次のリンク先を確認してください。

- [Azure 仮想マシンのネットワーク スループットの最適化](../virtual-network/virtual-network-optimize-network-bandwidth.md)
- [Microsoft サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
