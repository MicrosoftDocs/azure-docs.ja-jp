---
title: Azure Network Watcher のインスタンスの作成 | Microsoft Docs
description: Azure portal またはその他のテクノロジを使用して Azure リージョンに Azure Network Watcher を作成する方法と、Network Watcher を削除する方法について説明します。
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: c308824afdcae5f5c04a316c199bad71ad84a429
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94962010"
---
# <a name="create-an-azure-network-watcher-instance"></a>Azure Network Watcher のインスタンスの作成

Network Watcher は地域サービスであり、ネットワーク シナリオ レベルで Azure 内と Azure 間の状態を監視して診断できます。 シナリオ レベルの監視により、エンド ツー エンドのネットワーク レベル ビューで問題を診断できるようになります。 Network Watcher に搭載されているネットワークの診断および監視ツールを使用して、Azure 内のネットワークを把握および診断し、洞察を得ることができます。 Network Watcher は、Network Watcher リソースの作成を通じて有効化されます。 このリソースにより、Network Watcher の機能を利用できるようになります。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="network-watcher-is-automatically-enabled"></a>Network Watcher は自動的に有効になります
サブスクリプションで仮想ネットワークを作成したり更新したりすると、お使いの Virtual Network のリージョンで Network Watcher が自動的に有効になります。 Network Watcher は自動的に有効化され、リソースや関連する料金が影響を受けることはありません。

#### <a name="opt-out-of-network-watcher-automatic-enablement"></a>Network Watcher の自動有効化のオプトアウト
Network Watcher の自動有効化をオプトアウトしたい場合は、次のコマンドを実行することで、そのようにできます。

> [!WARNING]
> Network Watcher の自動有効化のオプトアウトは、永続的な変更です。 一度オプトアウトすると、[サポートに連絡](https://azure.microsoft.com/support/options/)しない限りオプトインできなくなります

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName DisableNetworkWatcherAutocreation -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

```azurecli-interactive
az feature register --name DisableNetworkWatcherAutocreation --namespace Microsoft.Network
az provider register -n Microsoft.Network
```



## <a name="create-a-network-watcher-in-the-portal"></a>ポータルで Network Watcher を作成する

**[すべてのサービス]**  >  **[ネットワーク]**  >  **[Network Watcher]** の順に移動します。 Network Watcher を有効にするすべてのサブスクリプションを選択できます。 このアクションにより、利用可能なすべてのリージョンに Network Watcher が作成されます。

![Network Watcher の作成](./media/network-watcher-create/figure1.png)

ポータルを使用して Network Watcher を有効にすると、Network Watcher インスタンスの名前が自動的に *NetworkWatcher_region_name* に設定されます (*region_name* は、インスタンスが有効にされている Azure リージョン)。 たとえば、米国中西部リージョンで有効にされた Network Watcher は *NetworkWatcher_westcentralus* という名前になります。

Network Watcher インスタンスが *NetworkWatcherRG* という名前のリソース グループに自動的に作成されます。 このリソース グループが存在しない場合は、新たに作成されます。

Network Watcher インスタンスの名前とその配置先のリソース グループをカスタマイズするには、以下のセクションで説明している PowerShell、Azure CLI、REST API、ARMClient のいずれかの方法を使用します。 いずれの方法でも、Network Watcher を作成する前に、配置先のリソース グループが存在している必要があります。  

## <a name="create-a-network-watcher-with-powershell"></a>PowerShell を使用して Network Watcher を作成する

Network Watcher のインスタンスを作成するには、次のコマンド例を実行します。

```powershell
New-AzNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>Azure CLI を使用して Network Watcher を作成する

Network Watcher のインスタンスを作成するには、次のコマンド例を実行します。

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>REST API を使用して Network Watcher を作成する

PowerShell を使用して REST API を呼び出すには、ARMClient を使用します。 ARMClient は、[Chocolatey 上の ARMClient](https://chocolatey.org/packages/ARMClient) に関するページの Chocolatey にあります

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

## <a name="create-a-network-watcher-using-azure-quickstart-template"></a>Azure クイックスタート テンプレートを使用して Network Watcher を作成する

Network Watcher のインスタンスを作成するには、こちらの[クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-networkwatcher-create/)を参照してください。

## <a name="delete-a-network-watcher-in-the-portal"></a>ポータル上で Network Watcher を削除する

**[すべてのサービス]**  >  **[ネットワーク]**  >  **[Network Watcher]** の順に移動します。

まだ選択されていない場合は、[概要] タブを選択します。 ドロップダウンを使用して、Network Watcher を無効にするサブスクリプションを選択します。
矢印をクリックして、選択したサブスクリプションに対するリージョンの一覧を展開します。 いずれの場合でも、右側にある 3 つの点を使用してコンテキスト メニューにアクセスします。
無効化を開始するには、[Network Watcher の無効化] をクリックします。 この手順を確定するように求められます。 [はい] をクリックして続行します。
ポータル上で、すべてのサブスクリプションにおいてリージョンごとに個別にこれを行う必要があります。


## <a name="delete-a-network-watcher-with-powershell"></a>PowerShell を使用して Network Watcher を削除する

Network Watcher のインスタンスを削除するには、次の例を実行します。

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location westcentralus
New-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG -Location westcentralus
Remove-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG
```

## <a name="next-steps"></a>次のステップ

これで Network Watcher のインスタンスが作成できました。利用可能な機能については、以下をご覧ください。

* [トポロジ](./view-network-topology.md)
* [パケット キャプチャ](network-watcher-packet-capture-overview.md)
* [IP フロー検証](network-watcher-ip-flow-verify-overview.md)
* [次ホップ](network-watcher-next-hop-overview.md)
* [セキュリティ グループ ビュー](network-watcher-security-group-view-overview.md)
* [NSG フロー ログの記録](network-watcher-nsg-flow-logging-overview.md)
* [Virtual Network Gateway のトラブルシューティング](network-watcher-troubleshoot-overview.md)