---
title: Azure Network Watcher を使用したパケット インスペクション | Microsoft Docs
description: この記事では、Network Watcher を使用して、VM から収集されたディープ パケット インスペクションを実行する方法について説明します
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 7b907d00-9c35-40f5-a61e-beb7b782276f
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 3bd85d6faf05fcf659e9335ee9de3d64198dfa08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98011171"
---
# <a name="packet-inspection-with-azure-network-watcher"></a>Azure Network Watcher を使用したパケット インスペクション

Network Watcher のパケット キャプチャ機能を使用すると、Azure VM でキャプチャ セッションを開始して管理できます。これは、ポータル、PowerShell、CLI で実行できるほか、SDK と REST API を介してプログラムで実行できます。 パケット キャプチャを使えば、すぐに使用できる形式で情報を取得して、パケット レベルのデータが必要なシナリオに対応できます。 無料で公開されているツールを利用してデータを検査することで、VM との送受信の通信内容を確認して、ネットワーク トラフィックに関する洞察を得ることができます。 パケット キャプチャ データの使用例としては、ネットワークまたはアプリケーションの問題の調査、ネットワークの悪用と侵入の試行の検出、法令順守の維持などがあります。 この記事では、一般的なオープン ソース ツールを使用して、Network Watcher で取得したパケット キャプチャ ファイルを開く方法について説明します。 加えて、接続の待ち時間を計算する方法、異常なトラフィックを特定する方法、ネットワーク統計を確認する方法を例示します。

## <a name="before-you-begin"></a>開始する前に

この記事では、以前実行されたパケット キャプチャに関する、事前構成されたいくつかのシナリオについて説明します。 これらのシナリオは、パケット キャプチャを確認することで利用できる機能を示します。 このシナリオでは、パケット キャプチャの検査に [Wireshark](https://www.wireshark.org/) が使用されています。

このシナリオは、仮想マシンでパケット キャプチャが実行済みであることを前提としています。 パケット キャプチャの作成方法については、[ポータルを使用したパケット キャプチャの管理](network-watcher-packet-capture-manage-portal.md)に関するページか、[REST API を使用したパケット キャプチャの管理](network-watcher-packet-capture-manage-rest.md)に関するページをご覧ください。

## <a name="scenario"></a>シナリオ

このシナリオでは次のことを行います。

* パケット キャプチャの確認

## <a name="calculate-network-latency"></a>ネットワーク待ち時間の計算

このシナリオでは、2 つのエンドポイントの間で行われた伝送制御プロトコル (TCP) 会話の初期ラウンドトリップ時間 (RTT) を確認する方法について説明します。

TCP 接続が確立されるとき、接続で送信された最初の 3 つのパケットは、一般に 3 ウェイ ハンドシェイクと呼ばれるパターンに従います。 このハンドシェイクで送信された最初の 2 つのパケット (クライアントからの初期要求とサーバーの応答) を確認することで、この接続が確立されたときの待ち時間を計算できます。 この待ち時間は、ラウンドトリップ時間 (RTT) と呼ばれます。 TCP プロトコルと 3 ウェイ ハンドシェイクの詳細については、次のリソースを参照してください。 [https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip](https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip)

### <a name="step-1"></a>ステップ 1

Wireshark を起動します。

### <a name="step-2"></a>ステップ 2

パケット キャプチャから **.cap** ファイルを読み込みます。 このファイルは BLOB で見つかります。構成によっては、ローカルの仮想マシンに保存されています。

### <a name="step-3"></a>ステップ 3

TCP 会話の初期ラウンドトリップ時間 (RTT) を確認するには、TCP ハンドシェイクに含まれる最初の 2 つのパケットを見るだけで済みます。 3 ウェイ ハンドシェイクに含まれる最初の 2 つのパケット ([SYN] パケットと [SYN,ACK] パケット) を使用することになります。 これらの名前は、TCP ヘッダーで設定されたフラグに由来します。 ハンドシェイクの最後のパケット ([ACK] パケット) は、このシナリオでは使用しません。 [SYN] パケットはクライアントから送信されます。 このパケットを受信したサーバーは、クライアントから SYN を受信した確認として、[ACK] パケットを送信します。 サーバーの応答に必要なオーバーヘッドがわずかである点を利用し、クライアントが [SYN, ACK] パケットを受信した時間からクライアントが [SYN] パケットを送信した時間を差し引いて、RTT を計算します。

Wireshark を使用すると、この値は自動的に計算されます。

TCP 3 ウェイ ハンドシェイクで送信される最初の 2 つのパケットをより簡単に確認するには、Wireshark に備わったフィルター処理機能を利用します。

Wireshark でフィルターを適用するには、キャプチャの [SYN] パケットの "Transmission Control Protocol" セグメントを展開し、TCP ヘッダーで設定されているフラグを確認します。

すべての [SYN] パケットと [SYN, ACK] パケットでフィルター処理するので、フラグの下の Syn ビットが 1 に設定されていることを確認してから、その Syn ビットを右クリックして [Apply as Filter (フィルターとして適用)]、[Selected (選択)] の順にクリックします。

![図 7][7]

### <a name="step-4"></a>手順 4

ウィンドウをフィルター処理し、[SYN] ビットが設定されているパケットのみが表示されるようになったので、初期 RTT を確認したい会話を簡単に選択できます。 Wireshark では RTT を簡単に確認する方法があり、"SEQ/ACK analysis" という印の付いたドロップダウンをクリックするだけで、 RTT が表示されます。 この例では、RTT は 0.0022114 秒 (2.211 ミリ秒) でした。

![図 8][8]

## <a name="unwanted-protocols"></a>望ましくないプロトコル

Azure でデプロイした仮想マシン インスタンスでは、多数のアプリケーションが実行されていることがあります。 これらのアプリケーションの多くはネットワーク経由で通信を行いますが、明示的なアクセス許可がない可能性があります。 ネットワーク通信を格納するパケット キャプチャを使用すると、ネットワークで交わされるアプリケーションのやり取りを調査して問題を探索できます。

この例では、以前実行したパケット キャプチャに望ましくないプロトコルがないかを確認します。このようなプロトコルは、マシンで実行中のアプリケーションから未承認の通信が行われたことを示唆している場合があります。

### <a name="step-1"></a>ステップ 1

先ほどのシナリオと同じキャプチャを使用して、 **[Statistics (統計)]**  >  **[Protocol Hierarchy (プロトコル階層)]** の順にクリックします。

![プロトコル階層のメニュー][2]

プロトコル階層のウィンドウが表示されます。 このビューでは、キャプチャ セッション中に使用されたすべてのプロトコルと、それらのプロトコルを使用して送受信されたパケットの数の一覧が表示されます。 このビューは、仮想マシン上またはネットワーク上の望ましくないネットワーク トラフィックを探し出すうえで役に立ちます。

![開かれたプロトコル階層][3]

上の画像のキャプチャからわかるように、(ピア ツー ピア ファイル共有に使用される) BitTorrent プロトコルを使用したトラフィックがありました。 管理者は、この特定の仮想マシンで BitTorrent トラフィックが確認されることを望んでいません。 このトラフィックに気付いた段階で、この仮想マシンにインストールされているピア ツー ピア ソフトウェアを削除したり、ネットワーク セキュリティ グループまたはファイアウォールを使用してトラフィックをブロックしたりできます。 さらに、スケジュールを設定してパケット キャプチャを実行することもできます。これにより、仮想マシンで使用されているプロトコルを定期的に確認できます。 Azure でネットワーク タスクを自動化する方法の例については、[Azure Automation を使用したネットワーク リソースの監視](network-watcher-monitor-with-azure-automation.md)に関するページを参照してください。

## <a name="finding-top-destinations-and-ports"></a>上位の送信先とポートの検索

通信で使用されているトラフィック、エンドポイント、ポートの種類を把握することは、ネットワーク上のアプリケーションとリソースを監視またはトラブルシューティングする際に重要です。 上記のパケット キャプチャ ファイルを利用して、VM が通信している上位の送信先と使用されているポートをすばやく確認できます。

### <a name="step-1"></a>ステップ 1

先ほどのシナリオと同じキャプチャを使用して、 **[Statistics (統計)]**  >  **[IPv4 Statistics (IPv4 統計)]**  >  **[Destinations and Ports (送信先とポート)]** の順にクリックします。

![パケット キャプチャのウィンドウ][4]

### <a name="step-2"></a>ステップ 2

結果を確認すると、1 つの行が目を引きます。ポート 111 に多数の接続がありました。 最も多く使用されていたのは 3389 で、これはリモート デスクトップです。また、残りのポートは RPC 動的ポートです。

このトラフィックに特に意味はない可能性もありますが、このポートは多数の接続で使用された、管理者にとって未知のポートです。

![図 5][5]

### <a name="step-3"></a>ステップ 3

不自然なポートを特定したところで、このポートに基づいてキャプチャをフィルター処理できます。

このシナリオのフィルターは次のようになります。

```
tcp.port == 111
```

上記のフィルター テキストをフィルターのボックスに入力して、Enter キーを押します。

![図 6][6]

結果から、すべてのトラフィックが同じサブネットのローカル仮想マシンから送信されていることがわかります。 このトラフィックが発生している理由がまだ明らかでない場合、さらにパケットを検査して、この仮想マシンがポート 111 でこれらの呼び出しを行う理由を特定できます。 この情報に基づいて、適切な処置を取ることができます。

## <a name="next-steps"></a>次のステップ

Network Watcher のその他の診断機能については、「[Azure network monitoring overview (Azure のネットワーク監視の概要)](network-watcher-monitoring-overview.md)」を参照してください。

[1]: ./media/network-watcher-deep-packet-inspection/figure1.png
[2]: ./media/network-watcher-deep-packet-inspection/figure2.png
[3]: ./media/network-watcher-deep-packet-inspection/figure3.png
[4]: ./media/network-watcher-deep-packet-inspection/figure4.png
[5]: ./media/network-watcher-deep-packet-inspection/figure5.png
[6]: ./media/network-watcher-deep-packet-inspection/figure6.png
[7]: ./media/network-watcher-deep-packet-inspection/figure7.png
[8]: ./media/network-watcher-deep-packet-inspection/figure8.png













