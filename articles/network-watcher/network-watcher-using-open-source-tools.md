---
title: オープン ソース ツールを使用してネットワーク トラフィック パターンを視覚化する
titleSuffix: Azure Network Watcher
description: このページでは、Network Watcher のパケット キャプチャ機能と CapAnalysis を使用して、VM の送受信トラフィックのパターンを視覚化する方法について説明しています。
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 0503f6910186b42bf381c662c7942e37c28bfdf0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101709514"
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>VM の送受信ネットワーク トラフィックのパターンをオープン ソース ツールで視覚化する

パケット キャプチャ データには、ネットワーク フォレンジクスやディープ パケット インスペクションに必要なネットワーク データが含まれています。 パケット キャプチャ データを分析することでネットワークに関する洞察を得ることができ、また、それを目的としたオープン ソース ツールも数多く存在します。 オープン ソースのパケット キャプチャ視覚化ツールである、CapAnalysis もそのようなツールの 1 つです。 パケット キャプチャ データを視覚化することは、ネットワーク内のパターンや異常に関する洞察をすばやく得ることのできる有益な手段です。 視覚化には、利用しやすい形でそのような洞察を共有できるという側面もあります。

Azure の Network Watcher には、ネットワーク上でパケット キャプチャを実行することによって、データをキャプチャする機能が備わっています。 この記事では、CapAnalysis と Network Watcher を使い、パケット キャプチャから視覚化して洞察を得る方法についてわかりやすく説明しています。

## <a name="scenario"></a>シナリオ

Azure 内の VM に単純な Web アプリケーションがデプロイされています。そのネットワーク トラフィックをオープン ソース ツールで視覚化し、フロー パターンや異常の可能性をすばやく識別する必要があります。 Network Watcher では、ネットワーク環境のパケット キャプチャ データを取得して、ストレージ アカウントに直接保存することができます。 そのパケット キャプチャ データを CapAnalysis で直接ストレージ BLOB から取り込み、その内容を視覚化することができます。

![scenario][1]

## <a name="steps"></a>手順

### <a name="install-capanalysis"></a>CapAnalysis のインストール

CapAnalysis を仮想マシンにインストールするには、公式のインストラクション (https://www.capanalysis.net/ca/how-to-install-capanalysis) を参照してください。
CapAnalysis にリモートからアクセスするには、新しい受信セキュリティ規則を追加することによって、VM 上のポート 9877 を開放する必要があります。 ネットワーク セキュリティ グループへの規則の作成について詳しくは、「[既存の NSG に規則を作成する](../virtual-network/manage-network-security-group.md#create-a-security-rule)」をご覧ください。 この規則が正しく追加されていれば、`http://<PublicIP>:9877` で CapAnalysis にアクセスできるようになります。

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Azure Network Watcher を使用してパケット キャプチャ セッションを開始する

仮想マシンが送受信するトラフィックは、Network Watcher でパケットをキャプチャすることによって追跡することができます。 パケット キャプチャ セッションの開始方法については、[Network Watcher を使用したパケット キャプチャの管理](network-watcher-packet-capture-manage-portal.md)に関するページの手順を参照してください。 パケット キャプチャをストレージ BLOB に保存することで、CapAnalysis から利用できるようになります。

### <a name="upload-a-packet-capture-to-capanalysis"></a>パケット キャプチャ データを CapAnalysis にアップロードする
Network Watcher で取得したパケット キャプチャ データは、[URL からインポートする] タブを使用し、パケット キャプチャ データの保存先となるストレージ BLOB へのリンクを指定することによって直接アップロードすることができます。

CapAnalysis へのリンクを指定するときは必ず、ストレージ BLOB の URL に SAS トークンを付加してください。  ストレージ アカウントから Shared Access Signature に移動して、付与するアクセス許可を指定し、[SAS の生成] ボタンをクリックしてトークンを作成します。 SAS トークンを、パケット キャプチャ データの格納先となるストレージ BLOB の URL に付加することができます。

結果の URL は、次のようになります。`http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere`


### <a name="analyzing-packet-captures"></a>パケット キャプチャ データの分析

CapAnalysis には、パケット キャプチャ データを視覚化するためのさまざまな選択肢が用意されており、多様な視点から分析を行うことができます。 こうして得た視覚的な手掛かりをもとに、ネットワーク トラフィックの傾向を把握し、異常な行動があればすぐに特定することができます。 以降、そうした機能をいくつか紹介します。

1. フロー テーブル

    このテーブルには、パケット データに含まれる一連のフローとそれに関するタイムスタンプ、各種プロトコル、送信元と送信先の IP アドレスが表示されます。

    ![CapAnalysis フロー ページ][5]

1. プロトコルの概要

    このウィンドウで、さまざまなプロトコルと地勢におけるネットワーク トラフィックの分布をすばやく確認できます。

    ![CapAnalysis プロトコル概要][6]

1. 統計

    ネットワーク トラフィックの統計情報は、このウィンドウで確認できます。送信元 IP と送信先 IP との間で送受信されたバイト数や、その IP ごとのフロー、各フローで使用されたプロトコル、フローの期間などが表示されます。

    ![CapAnalysis 統計情報][7]

1. Geomap

    このウィンドウには、ネットワーク トラフィックのマップ ビューが、送信元となった国/地域からのトラフィック量に応じて色分けされて表示されます。 ハイライトされた国/地域を選択すると、トラフィック フローについてのさらに詳しい統計情報 (その国/地域の IP に対して送信されたデータとその国/地域の IP から受信したデータの比率など) が表示されます。

    ![Geomap][8]

1. フィルタ

    CapAnalysis には、特定のパケットをすばやく分析することを目的とした一連のフィルターが用意されています。 たとえばプロトコルでデータをフィルタリングすれば、該当するプロトコルのトラフィックに絞り込んで洞察を得ることができます。

    ![filters][11]

    CapAnalysis のすべての機能に関する詳細については、[https://www.capanalysis.net/ca/#about](https://www.capanalysis.net/ca/#about) にアクセスしてください。

## <a name="conclusion"></a>まとめ

Network Watcher のパケット キャプチャ機能を使用すると、ネットワーク フォレンジクスを行うために必要なデータをキャプチャし、ネットワーク トラフィックに関する理解を深めることができます。 このシナリオでは、Network Watcher のパケット キャプチャとオープン ソースの視覚化ツールとを簡単に連携させる方法を紹介しました。 CapAnalysis などのオープン ソース ツールを使ってパケット キャプチャ データを視覚化することにより、ディープ パケット インスペクションを実行し、ネットワーク トラフィックにおける傾向をすばやく把握することができます。

## <a name="next-steps"></a>次のステップ

NSG フロー ログについて詳しくは、[ネットワーク セキュリティ グループのフロー ログ](network-watcher-nsg-flow-logging-overview.md)に関するページをご覧ください。

[Power BI による NSG フロー ログの視覚化](network-watcher-visualize-nsg-flow-logs-power-bi.md)に関するページから、Power BI で NSG フロー ログを視覚化する方法について確認する
<!--Image references-->

[1]: ./media/network-watcher-using-open-source-tools/figure1.png
[2]: ./media/network-watcher-using-open-source-tools/figure2.png
[3]: ./media/network-watcher-using-open-source-tools/figure3.png
[4]: ./media/network-watcher-using-open-source-tools/figure4.png
[5]: ./media/network-watcher-using-open-source-tools/figure5.png
[6]: ./media/network-watcher-using-open-source-tools/figure6.png
[7]: ./media/network-watcher-using-open-source-tools/figure7.png
[8]: ./media/network-watcher-using-open-source-tools/figure8.png
[9]: ./media/network-watcher-using-open-source-tools/figure9.png
[10]: ./media/network-watcher-using-open-source-tools/figure10.png
[11]: ./media/network-watcher-using-open-source-tools/figure11.png
