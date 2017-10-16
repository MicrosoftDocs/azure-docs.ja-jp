---
title: "Azure Network Watcher で接続を確認する - Azure Portal | Microsoft Docs"
description: "このページは、Azure Portal を使用して Network Watcher で接続チェックを使用する方法について説明します。"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: jdial
ms.openlocfilehash: 1f19da71731039e1a39c4440f925b1369886a993
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="check-connectivity-with-azure-network-watcher-using-the-azure-portal"></a>Azure REST API を使用して Azure Network Watcher で接続を確認する

> [!div class="op_single_selector"]
> - [ポータル](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [CLI 2.0](network-watcher-connectivity-cli.md)
> - [Azure REST API](network-watcher-connectivity-rest.md)

仮想マシンから指定されたエンドポイントへの直接の TCP 接続が確立されたかどうかを確認するために、接続を使用する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

この記事では、次のリソースがあることを前提としています。

* 接続を確認するリージョンの Network Watcher のインスタンス。

* 接続を確認する仮想マシン。

> [!IMPORTANT]
> 接続チェックには、仮想マシン拡張機能 `AzureNetworkWatcherExtension` が必要です。 Windows VM への拡張機能のインストールについては、[Windows 用 Azure Network Watcher Agent 仮想マシン拡張機能](../virtual-machines/windows/extensions-nwa.md)に関する記事をご覧ください。Linux VM の場合は、[Linux 用 Azure Network Watcher Agent 仮想マシン拡張機能](../virtual-machines/linux/extensions-nwa.md)に関する記事をご覧ください。

## <a name="check-connectivity-to-a-virtual-machine"></a>仮想マシンへの接続を確認する

この例では、ポート 80 で接続先の仮想マシンへの接続を確認します。

Network Watcher に移動し、**[接続チェック (プレビュー)]** をクリックします。 接続を確認する仮想マシンを選択します。 **[Destination]\(接続先\)** セクションで、**[仮想マシンの選択]** を選択し、テスト対象の適切な仮想マシンおよびポートを選択します。

**[チェック]** をクリックすると、指定したポート上の仮想マシン間の接続が確認されます。 この例では、接続先の VM に到達できず、ホップの一覧が表示されます。

![仮想マシンの接続確認の結果][1]

## <a name="check-remote-endpoint-connectivity"></a>リモート エンドポイントの接続を確認する

リモート エンドポイントへの接続および待機時間を確認するには、**[Destination]\(接続先\)** セクションの **[手動で指定]** オプション ボタンを選択し、URL とポートを入力して、**[チェック]** をクリックします。  この手順は、Web サイトやストレージ エンドポイントなどのリモート エンドポイントで使用します。

![Web サイトの接続確認の結果][2]

## <a name="next-steps"></a>次のステップ

[アラートがトリガーするパケット キャプチャの作成](network-watcher-alert-triggered-packet-capture.md)に関するページを参照して、仮想マシンのアラートを使用してパケット キャプチャを自動化する方法を確認する

[IP フロー検証の確認](network-watcher-check-ip-flow-verify-portal.md)に関する記事を参照して、VM で送受信される特定のトラフィックが許可されているかどうかを調べる

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png
