---
title: 接続のトラブルシューティング - Azure portal
titleSuffix: Azure Network Watcher
description: Azure Portal を使用して Azure Network Watcher の接続のトラブルシューティング機能を使用する方法を説明します。
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.openlocfilehash: f33c5f0fdf69737df0d8bd83499ded1e0e0f4f88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97898115"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>Azure Portal を使用した Azure Network Watcher との接続のトラブルシューティング

> [!div class="op_single_selector"]
> - [ポータル](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [Azure CLI](network-watcher-connectivity-cli.md)
> - [Azure REST API](network-watcher-connectivity-rest.md)

仮想マシンから指定されたエンドポイントへの直接の TCP 接続が確立されたかどうかを確認するために、接続のトラブルシューティングを使用する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

この記事では、次のリソースがあることを前提としています。

* 接続のトラブルシューティングを行うリージョンの Network Watcher のインスタンス。
* 接続のトラブルシューティングを行う仮想マシン。

> [!IMPORTANT]
> 接続のトラブルシューティングを行うには、トラブルシューティングの実行に使用する VM に `AzureNetworkWatcherExtension` VM 拡張機能がインストールされている必要があります。 Windows VM への拡張機能のインストールについては、[Windows 用 Azure Network Watcher Agent 仮想マシン拡張機能](../virtual-machines/extensions/network-watcher-windows.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)に関する記事をご覧ください。Linux VM の場合は、[Linux 用 Azure Network Watcher Agent 仮想マシン拡張機能](../virtual-machines/extensions/network-watcher-linux.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)に関する記事をご覧ください。 接続先のエンドポイントでは、拡張機能は必要ありません。

## <a name="check-connectivity-to-a-virtual-machine"></a>仮想マシンへの接続を確認する

この例では、ポート 80 で接続先の仮想マシンへの接続を確認します。

Network Watcher に移動し、**[接続のトラブルシューティング]** をクリックします。 接続を確認する仮想マシンを選択します。 **[Destination]\(接続先\)** セクションで、**[仮想マシンの選択]** を選択し、テスト対象の適切な仮想マシンおよびポートを選択します。

**[チェック]** をクリックすると、指定したポート上の仮想マシン間の接続が確認されます。 この例では、接続先の VM に到達できず、ホップの一覧が表示されます。

![仮想マシンの接続確認の結果][1]

## <a name="check-remote-endpoint-connectivity"></a>リモート エンドポイントの接続を確認する

リモート エンドポイントへの接続および待機時間を確認するには、**[Destination]\(接続先\)** セクションの **[手動で指定]** オプション ボタンを選択し、URL とポートを入力して、**[チェック]** をクリックします。  この手順は、Web サイトやストレージ エンドポイントなどのリモート エンドポイントで使用します。

![Web サイトの接続確認の結果][2]

## <a name="next-steps"></a>次のステップ

[アラートがトリガーするパケット キャプチャの作成](network-watcher-alert-triggered-packet-capture.md)に関するページを参照して、仮想マシンのアラートを使用してパケット キャプチャを自動化する方法を確認する

[IP フロー検証の確認](diagnose-vm-network-traffic-filtering-problem.md)に関する記事を参照して、VM で送受信される特定のトラフィックが許可されているかどうかを調べる

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png