---
title: "Azure Network Watcher のインスタンスの作成 | Microsoft Docs"
description: "このページでは、ポータルと Azure REST API を使用して Network Watcher のインスタンスを作成する手順について説明します。"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 2aeaffdd5ab552e18677cbd1a24a748dd14bf172
ms.contentlocale: ja-jp
ms.lasthandoff: 06/02/2017

---

# <a name="create-an-azure-network-watcher-instance"></a>Azure Network Watcher のインスタンスの作成

Network Watcher は地域サービスであり、ネットワーク シナリオ レベルで Azure 内と Azure 間の状態を監視して診断できます。 シナリオ レベルの監視により、エンド ツー エンドのネットワーク レベル ビューで問題を診断できるようになります。 Network Watcher に搭載されているネットワークの診断および監視ツールを使用して、Azure 内のネットワークを把握および診断し、洞察を得ることができます。

> [!NOTE]
> Network Watcher は現在 CLI 1.0 のみをサポートしているため、新しい Network Watcher インスタンスを作成する手順は CLI 1.0 で提供されています。

## <a name="create-a-network-watcher-in-the-portal"></a>ポータルで Network Watcher を作成する

**[その他のサービス]** > **[ネットワーク]** > **[ネットワーク ウォッチャー]** の順に移動します。 Network Watcher を有効にするすべてのサブスクリプションを選択できます。 このアクションにより、利用可能なすべてのリージョンに Network Watcher が作成されます。

![Network Watcher の作成][1]

ポータルを使用して Network Watcher を有効にすると、Network Watcher インスタンスの名前が自動的に NetworkWatcher_region_name に設定されます (region_name は、インスタンスが有効にされた Azure リージョン)。  たとえば、"西中央アメリカ" リージョンで有効にされた Network Watcher は "NetworkWatcher_westcentralus" という名前になります。

また、Network Watcher インスタンスは自動的に、NetworkWatcherRG というリソース グループに追加されます。  このリソース グループが存在しない場合は、新たに作成されます。

Network Watcher インスタンスの名前とその配置先のリソース グループは、PowerShell、REST API、ARMClient のいずれかを使い、以下に説明した方法でカスタマイズすることができます。  いずれの方法も、Network Watcher の配置先となるリソース グループがあらかじめ存在している必要があります。  

## <a name="create-a-network-watcher-with-powershell"></a>PowerShell を使用して Network Watcher を作成する

Network Watcher のインスタンスを作成するには、次のコマンド例を実行します。

```powershell
New-AzureRmNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>REST API を使用して Network Watcher を作成する

PowerShell を使用している REST API を呼び出すには、ARMClient を使用します。 ARMClient は、[Chocolatey 上の ARMClient](https://chocolatey.org/packages/ARMClient) に関するページの chocolatey 上にあります。

### <a name="log-in-with-armclient"></a>ARMClient でログインする

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>ネットワーク ウォッチャーを作成する

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="next-steps"></a>次のステップ

これで Network Watcher のインスタンスが作成できました。利用可能な機能については、以下をご覧ください。

* [トポロジ](network-watcher-topology-overview.md)
* [パケット キャプチャ](network-watcher-packet-capture-overview.md)
* [IP フロー検証](network-watcher-ip-flow-verify-overview.md)
* [次ホップ](network-watcher-next-hop-overview.md)
* [セキュリティ グループ ビュー](network-watcher-security-group-view-overview.md)
* [NSG フロー ログの記録](network-watcher-nsg-flow-logging-overview.md)
* [Virtual Network Gateway のトラブルシューティング](network-watcher-troubleshoot-overview.md)

Network Watcher のインスタンスを作成すると、[アラートがトリガーされたパケット キャプチャの作成](network-watcher-alert-triggered-packet-capture.md)に関する次の記事に従って、パッケージ キャプチャを構成できるようになります。

[1]: ./media/network-watcher-create/figure1.png












