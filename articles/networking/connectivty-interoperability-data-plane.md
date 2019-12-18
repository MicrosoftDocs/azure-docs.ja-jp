---
title: 'Azure バックエンド接続機能の相互運用性: データ プレーンの分析 |Microsoft Docs'
description: この記事では、Azure における ExpressRoute、サイト間 VPN、仮想ネットワーク ピアリングの相互運用性を分析する際に使用できるテスト セットアップのデータ プレーン分析について説明します。
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 11c964bedce7a8b979434b888d756c2121d06a60
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873830"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-data-plane-analysis"></a>Azure バックエンド接続機能の相互運用性: データ プレーンの分析

この記事では､[テスト セットアップ][Setup]のデータ プレーン分析について説明します。 [テスト セットアップの構成][Configuration]テスト セットアップの[ コントロール プレーンの分析][Control-Analysis]もご覧ください。

データ プレーンの分析では、あるトポロジ内の 1 つのローカル ネットワーク (LAN または仮想ネットワーク) から別のローカル ネットワークへと移動するパケットが通過したパスについて調べます。 2 つのローカル ネットワーク間のデータ パスは、必ずしも対称ではありません。 したがってこの記事では、逆方向のパスとは異なる、あるローカル ネットワークから別のネットワークへの転送パスについて分析します。

## <a name="data-path-from-the-hub-vnet"></a>ハブ VNet からのデータ パス

### <a name="path-to-the-spoke-vnet"></a>スポーク VNet へのパス

仮想ネットワーク (VNet) ピアリングは、ピアリングされている 2 つの VNet 間のネットワーク ブリッジ機能をエミュレートします。 以下に、ハブ VNet からスポーク VNet 内の VM への traceroute 出力を示します。

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.11.30.4

    Trace complete.

次の図は、Azure Network Watcher の観点から見た、ハブ VNet とスポーク VNet のグラフィカル接続ビューを示しています。


![1][1]

### <a name="path-to-the-branch-vnet"></a>ブランチ VNet へのパス

以下に、ハブ VNet からブランチ VNet 内の VM への traceroute 出力を示します。

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms     1 ms     1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     2 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

この traceroute では、最初のホップは、ハブ VNet の Azure VPN Gateway 内にある VPN ゲートウェイです。 2 番目のホップでは、ブランチ VNet の VPN ゲートウェイです。 ブランチ VNet の VPN ゲートウェイの IP アドレスは、ハブ VNet ではアドバタイズされません。 3 番目のホップは、ブランチ VNet 上の VM です。

次の図は、Network Watcher の観点から見た、ハブ VNet とブランチ VNet のグラフィカル接続ビューを示しています。

![2][2]

同じ接続について、次の図は、Network Watcher でのグリッド ビューを示しています。

![3][3]

### <a name="path-to-on-premises-location-1"></a>オンプレミスの場所 1 へのパス

以下に、ハブ VNet からオンプレミスの場所 1 内の VM への traceroute 出力を示します。

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     2 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

この traceroute では、最初のホップは、Microsoft Enterprise Edge Router (MSEE) への Azure ExpressRoute ゲートウェイ トンネル エンドポイントです。 2 番目と 3 番目のホップは、顧客のエッジ (CE) ルーターと、オンプレミスの場所 1 の LAN IP です。 これらの IP アドレスは、ハブ VNet ではアドバタイズされません。 4 番目のホップは、オンプレミスの場所 1 の VM です。


### <a name="path-to-on-premises-location-2"></a>オンプレミスの場所 2 へのパス

以下に、ハブ VNet からオンプレミスの場所 2 内の VM への traceroute 出力を示します。

    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    75 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

この traceroute では、最初のホップは、MSEE への ExpressRoute ゲートウェイ トンネル エンドポイントです。 2 番目と 3 番目のホップは、CE ルーターと、オンプレミスの場所 2 の LAN IP です。 これらの IP アドレスは、ハブ VNet ではアドバタイズされません。 4 番目のホップは、オンプレミスの場所 2 の VM です。

### <a name="path-to-the-remote-vnet"></a>リモート VNet へのパス

以下に、ハブ VNet からリモート VNet 内の VM への traceroute 出力を示します。

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    69 ms  10.17.30.4

    Trace complete.

この traceroute では、最初のホップは、MSEE への ExpressRoute ゲートウェイ トンネル エンドポイントです。 2 番目のホップは、リモート VNet のゲートウェイ IP です。 2 番目のホップの IP 範囲は、ハブ VNet ではアドバタイズされません。 3 番目のホップは、リモート VNet 上の VM です。

## <a name="data-path-from-the-spoke-vnet"></a>スポーク VNet からのデータ パス

スポーク VNet は、ハブ VNet のネットワーク ビューを共有しています。 スポーク VNet は VNet ピアリングによって、ハブ VNet のリモート ゲートウェイ接続を、スポーク VNet に直接接続されているかのように使用します。

### <a name="path-to-the-hub-vnet"></a>ハブ VNet へのパス

以下に、スポーク VNet からハブ VNet 内の VM への traceroute 出力を示します。

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet"></a>ブランチ VNet へのパス

以下に、スポーク VNet からブランチ VNet 内の VM への traceroute 出力を示します。

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

この traceroute では、最初のホップは、ハブ VNet の VPN ゲートウェイです。 2 番目のホップでは、ブランチ VNet の VPN ゲートウェイです。 ブランチ VNet の VPN ゲートウェイの IP アドレスは、ハブ/スポーク VNet ではアドバタイズされません。 3 番目のホップは、ブランチ VNet 上の VM です。

### <a name="path-to-on-premises-location-1"></a>オンプレミスの場所 1 へのパス

以下に、スポーク VNet からオンプレミスの場所 1 内の VM への traceroute 出力を示します。

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

この traceroute では、最初のホップは、ハブ VNet の、MSEE への ExpressRoute ゲートウェイ トンネル エンドポイントです。 2 番目と 3 番目のホップは、CE ルーターと、オンプレミスの場所 1 の LAN IP です。 これらの IP アドレスは、ハブ/スポーク VNet ではアドバタイズされません。 4 番目のホップは、オンプレミスの場所 1 の VM です。

### <a name="path-to-on-premises-location-2"></a>オンプレミスの場所 2 へのパス

以下に、スポーク VNet からオンプレミスの場所 2 内の VM への traceroute 出力を示します。


    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

この traceroute では、最初のホップは、ハブ VNet の、MSEE への ExpressRoute ゲートウェイ トンネル エンドポイントです。 2 番目と 3 番目のホップは、CE ルーターと、オンプレミスの場所 2 の LAN IP です。 これらの IP アドレスは、ハブ/スポーク VNet ではアドバタイズされません。 4 番目のホップは、オンプレミスの場所 2 の VM です。

### <a name="path-to-the-remote-vnet"></a>リモート VNet へのパス

以下に、スポーク VNet からリモート VNet 内の VM への traceroute 出力を示します。

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.10.30.133
      2     *        *        *     Request timed out.
      3    71 ms    70 ms    70 ms  10.17.30.4

    Trace complete.

この traceroute では、最初のホップは、ハブ VNet の、MSEE への ExpressRoute ゲートウェイ トンネル エンドポイントです。 2 番目のホップは、リモート VNet のゲートウェイ IP です。 2 番目のホップの IP 範囲は、ハブ/スポーク VNet ではアドバタイズされません。 3 番目のホップは、リモート VNet 上の VM です。

## <a name="data-path-from-the-branch-vnet"></a>ブランチの VNet からのデータ パス

### <a name="path-to-the-hub-vnet"></a>ハブ VNet へのパス

以下に、ブランチ VNet からハブ VNet 内の VM への traceroute 出力を示します。

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     3 ms  10.10.30.4

    Trace complete.

この traceroute では、最初のホップはブランチ VNet の VPN ゲートウェイです。 2 番目のホップは、ハブ VNet の VPN ゲートウェイです。 ハブ VNet の VPN ゲートウェイの IP アドレスは、リモート VNet ではアドバタイズされません。 3 番目のホップは、ハブ VNet 上の VM です。

### <a name="path-to-the-spoke-vnet"></a>スポーク VNet へのパス

以下に、ブランチ VNet からスポーク VNet 内の VM への traceroute 出力を示します。

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     1 ms    <1 ms     1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     2 ms  10.11.30.4

    Trace complete.

この traceroute では、最初のホップはブランチ VNet の VPN ゲートウェイです。 2 番目のホップは、ハブ VNet の VPN ゲートウェイです。 ハブ VNet の VPN ゲートウェイの IP アドレスは、リモート VNet ではアドバタイズされません。 3 番目のホップは、スポーク VNet 上の VM です。

### <a name="path-to-on-premises-location-1"></a>オンプレミスの場所 1 へのパス

以下に、ブランチ VNet からオンプレミスの場所 1 内の VM への traceroute 出力を示します。

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.2.30.125
      4     *        *        *     Request timed out.
      5     3 ms     3 ms     3 ms  10.2.30.10

    Trace complete.

この traceroute では、最初のホップはブランチ VNet の VPN ゲートウェイです。 2 番目のホップは、ハブ VNet の VPN ゲートウェイです。 ハブ VNet の VPN ゲートウェイの IP アドレスは、リモート VNet ではアドバタイズされません。 3 番目のホップは、プライマリ CE ルーター上の VPN トンネルの終了ポイントです。 4 番目のホップは、オンプレミスの場所 1 の内部 IP アドレスです。 この LAN IP アドレスは、CE ルーターの外部ではアドバタイズされません。 5 番目のホップは、オンプレミスの場所 1 の宛先 VM です。

### <a name="path-to-on-premises-location-2-and-the-remote-vnet"></a>オンプレミスの場所 2 およびリモート VNet へのパス

コントロール プレーンの分析で説明しているように、ネットワーク構成に従って、ブランチ VNet にはオンプレミスの場所 2 やリモート VNet への可視性がありません。 次の ping の結果によって以下を確認します。 

    C:\Users\rb>ping 10.1.31.10

    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

    C:\Users\rb>ping 10.17.30.4

    Pinging 10.17.30.4 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.17.30.4:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

## <a name="data-path-from-on-premises-location-1"></a>オンプレミスの場所 1 からのデータ パス

### <a name="path-to-the-hub-vnet"></a>ハブ VNet へのパス

以下に、オンプレミスの場所 1 からハブ VNet 内の VM への traceroute 出力を示します。

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     2 ms     2 ms     2 ms  10.10.30.4

    Trace complete.

この traceroute では、最初の 2 つのホップはオンプレミス ネットワークの一部です。 3 番目のホップは、CE ルーターに接続するプライマリ MSEE インターフェイスです。 4 番目のホップは、ハブ VNet の ExpressRoute ゲートウェイです。 ハブ VNet の ExpressRoute ゲートウェイの IP 範囲は、オンプレミス ネットワークにアドバタイズされません。 5 番目のホップは宛先 VM です。

Network Watcher で提供されるビューは、Azure を中心としたものだけです。 オンプレミスの観点については、Azure Network Performance Monitor を使用します。 Network Performance Monitor は、データ パス分析のために、Azure 外のネットワークにあるサーバーにインストールできるエージェントを提供します。

次の図は、オンプレミスの場所 1 の VM から ExpressRoute を介してハブ VNet 上の VM に接続するトポロジのビューを示しています。

![4][4]

前述したように、テスト セットアップでは、サイト間 VPN を、オンプレミスの場所 1 とハブ VNet 間の ExpressRoute のバックアップ接続として使用しています。 バックアップ データ パスをテストするために、オンプレミスの場所 1 のプライマリ CE ルーターと、対応する MSEE の間で、ExpressRoute のリンク障害を発生させてみましょう。 ExpressRoute のリンク障害を発生させるには、MSEE に接続している CE インターフェイスをシャット ダウンします。

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     3 ms  10.10.30.4

    Trace complete.

次の図は、ExpressRoute 接続がダウンしたときに、オンプレミスの場所 1 の VM からサイト間 VPN 接続を介してハブ VNet 上の VM に接続するトポロジのビューを示しています。

![5][5]

### <a name="path-to-the-spoke-vnet"></a>スポーク VNet へのパス

以下に、オンプレミスの場所 1 からスポーク VNet 内の VM への traceroute 出力を示します。

スポーク VNet に向うデータ パスの分析を行うため、ExpressRoute のプライマリ接続を元に戻しましょう。

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     3 ms     2 ms     2 ms  10.11.30.4

    Trace complete.

残りのデータ パス分析のために、プライマリの ExpressRoute 1 接続を起動します。

### <a name="path-to-the-branch-vnet"></a>ブランチ VNet へのパス

以下に、オンプレミスの場所 1 からブランチ VNet 内の VM への traceroute 出力を示します。

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

### <a name="path-to-on-premises-location-2"></a>オンプレミスの場所 2 へのパス

[コントロールプレーンの分析][Control-Analysis] について説明しているように、オンプレミスの場所 1 では、ネットワーク構成ごとにオンプレミスの場所 2 を表示できません。 次の ping の結果によって以下を確認します。 

    C:\Users\rb>ping 10.1.31.10
    
    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

### <a name="path-to-the-remote-vnet"></a>リモート VNet へのパス

以下に、オンプレミスの場所 1 からリモート VNet 内の VM への traceroute 出力を示します。

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2     2 ms     5 ms     7 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5    69 ms    70 ms    69 ms  10.17.30.4

    Trace complete.

## <a name="data-path-from-on-premises-location-2"></a>オンプレミスの場所 2 からのデータ パス

### <a name="path-to-the-hub-vnet"></a>ハブ VNet へのパス

以下に、オンプレミスの場所 2 からハブ VNet 内の VM への traceroute 出力を示します。

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.4
      3    <1 ms    <1 ms    <1 ms  192.168.31.22
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>スポーク VNet へのパス

以下に、オンプレミスの場所 2 からスポーク VNet 内の VM への traceroute 出力を示します。

    C:\Windows\system32>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops
      1    <1 ms    <1 ms     1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.0
      3    <1 ms    <1 ms    <1 ms  192.168.31.18
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-on-premises-location-1-and-the-remote-vnet"></a>ブランチ VNet、オンプレミスの場所 1、およびリモート VNet へのパス

[コントロールプレーンの分析][Control-Analysis]で説明しているように、オンプレミスの場所 1 は、ブランチ VNet、オンプレミスの場所 1、またはネットワーク構成ごとのリモート VNet を表示できません。 

## <a name="data-path-from-the-remote-vnet"></a>リモート VNet からのデータ パス

### <a name="path-to-the-hub-vnet"></a>ハブ VNet へのパス

以下に、リモート VNet からハブ VNet 内の VM への traceroute 出力を示します。

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    65 ms    65 ms    65 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    68 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>スポーク VNet へのパス

以下に、リモート VNet からスポーク VNet 内の VM への traceroute 出力を示します。

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    71 ms    69 ms    69 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-and-on-premises-location-2"></a>ブランチ VNet およびオンプレミスの場所 2 へのパス

[コントロールプレーンの分析][Control-Analysis] で説明しているように、リモート VNet では、ネットワーク構成ごとにブランチ VNet またはオンプレミスの場所 2 を表示できません。 

### <a name="path-to-on-premises-location-1"></a>オンプレミスの場所 1 へのパス

以下に、リモート VNet からオンプレミスの場所 1 内の VM への traceroute 出力を示します。

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    69 ms    69 ms    69 ms  10.2.30.10

    Trace complete.


## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute とサイト間 VPN 接続の併用

###  <a name="site-to-site-vpn-over-expressroute"></a>ExpressRoute 上のサイト間 VPN

ExpressRoute Microsoft ピアリングを使用してサイト間 VPN を構成することにより、オンプレミス ネットワークと Azure VNet の間でプライベートにデータを交換することができます。 この構成を使用すれば、機密性、信頼性、整合性が確保されたデータ交換を実現できます。 また、このデータ交換には、アンチリプレイ対策も講じられています。 ExpressRoute Microsoft ピアリングを使用してトンネル モードでサイト間 IPsec VPN を構成する方法の詳細については、[ExpressRoute Microsoft ピアリングでのサイト間 VPN][S2S-Over-ExR] に関するページを参照してください。 

Microsoft ピアリングを使用するサイト間 VPN 構成に伴う主な制限はスループットです。 IPsec トンネル上では、VPN ゲートウェイの容量によりスループットが制限されます。 VPN ゲートウェイのスループットは、ExpressRoute のスループットよりも低くなります。 このシナリオでは、高いセキュリティが要求されるトラフィックには IPsec トンネルを使用し、それ以外のすべてのトラフィックにはプライベート ピアリングを使用すると、ExpressRoute の帯域幅使用率の最適化に役立ちます。

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>ExpressRoute の安全なフェールオーバー パスとしてのサイト間 VPN

ExpressRoute は、高可用性を確保する冗長回線ペアの役割を果たします。 異なる Azure リージョンで geo 冗長 ExpressRoute 接続を構成することができます。 また、このテスト セットアップで紹介したとおり、Azure リージョン内でサイト間 VPN を使用して、ExpressRoute 接続のフェールオーバー パスを作成することができます。 ExpressRoute とサイト間 VPN の両方に同じプレフィックスがアドバタイズされた場合、Azure では ExpressRoute が優先されます。 ExpressRoute とサイト間 VPN の間で非対称なルーティングを回避するために、オンプレミス ネットワークも、ExpressRoute 接続をサイト間 VPN 接続よりも優先して使用するように構成する必要があります。

ExpressRoute とサイト間 VPN が共存する接続を構成する方法の詳細については、[ExpressRoute とサイト間の共存][ExR-S2S-CoEx]に関するページを参照してください。

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>バックエンド接続をスポーク VNet とブランチの場所に拡張する

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>VNet ピアリングを使用したスポーク VNet 接続

ハブ アンド スポークの VNet アーキテクチャは、広く利用されています。 ハブは Azure 内の VNet であり、スポーク VNet とオンプレミス ネットワークの間の接続の中央拠点として機能します。 スポークはハブとピアリングする VNet であり、ワークロードの分離に利用できます。 オンプレミスのデータセンターとハブとの間のトラフィックは、ExpressRoute または VPN 接続を経由して送信されます。 このアーキテクチャの詳細については、「[Azure にハブスポーク ネットワーク トポロジを実装する][Hub-n-Spoke]」を参照してください。

リージョン内の VNet ピアリングでは、スポーク VNet はハブ VNet ゲートウェイ (VPN ゲートウェイと ExpressRoute ゲートウェイの両方) を使用して、リモート ネットワークと通信を行うことができます。

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>サイト間 VPN を使用したブランチ VNet 接続

異なるリージョンにあるブランチ VNet と、オンプレミス ネットワークを、ハブ VNet を介して相互に通信させたい場合があります。 この構成に対する Azure のネイティブ ソリューションは、VPN を使用したサイト間 VPN 接続です。 その他に、ネットワーク仮想アプライアンス (NVA) をハブでのルーティングに使用する方法もあります。

詳細については、「[VPN ゲートウェイとは][VPN]」および[高可用性 NVA のデプロイ][Deploy-NVA]に関するページを参照してください。


## <a name="next-steps"></a>次の手順

「[ExpressRoute の FAQ][ExR-FAQ]」を参照して以下を行います。
-   ExpressRoute ゲートウェイに接続できる ExpressRoute 回線の数について学習する。
-   ExpressRoute 回線に接続できる ExpressRoute ゲートウェイの数について学習する。
-   ExpressRoute のその他のスケールの制限について学習する。


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "ハブ VNet からスポーク VNet への接続の Network Watcher ビュー"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "ハブ VNet からブランチ VNet への接続の Network Watcher ビュー"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "ハブ VNet からブランチ VNet への接続の Network Watcher グリッド ビュー"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "場所 1 の VM から ExpressRoute 1 経由でハブ VNet に接続する Network Performance Monitor ビュー"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "場所 1 の VM からサイト間 VPN 経由でハブ VNet に接続する Network Performance Monitor ビュー"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering


