---
title: "Azure Network Watcher のインスタンスの作成 | Microsoft Docs"
description: "このページでは、ポータルと Azure REST API を使用して Network Watcher のインスタンスを作成する手順について説明します。"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: a39ce143796408f8e44b0d2c877e631e92473462
ms.lasthandoff: 03/28/2017

---

# <a name="create-an-azure-network-watcher-instance"></a>Azure Network Watcher のインスタンスの作成

Network Watcher は地域サービスであり、ネットワーク シナリオ レベルで Azure 内と Azure 間の状態を監視して診断できます。 シナリオ レベルの監視により、エンド ツー エンドのネットワーク レベル ビューで問題を診断できるようになります。 Network Watcher に搭載されているネットワークの診断および監視ツールを使用して、Azure 内のネットワークを把握および診断し、洞察を得ることができます。

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="register-the-preview-capability"></a>プレビュー機能の登録

Network Watcher は現在プレビュー段階です。Network Watcher の機能を使用するには、機能を登録する必要があります。 これを行うには、次の PowerShell サンプルを実行します。

```powershell
Register-AzureRmProviderFeature -FeatureName AllowNetworkWatcher -ProviderNamespace Microsoft.Network
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

登録が正しく行われたことを確認するには、次の PowerShell サンプルを実行します。

```powershell
Get-AzureRmProviderFeature -FeatureName AllowNetworkWatcher -ProviderNamespace  Microsoft.Network
```

この機能が適切に登録されると、出力は次のようになります。

```
FeatureName         ProviderName      RegistrationState
-----------         ------------      -----------------
AllowNetworkWatcher Microsoft.Network Registered
```

### <a name="instructions-for-cli-10"></a>CLI 1.0 の手順

次の通り登録します

```CLI
azure feature register  Microsoft.Network AllowNetworkWatcher
azure provider register Microsoft.Network
```

登録が正しく行われたことを確認するには、次の CLI コマンドを実行します。

```CLI
azure feature show Microsoft.Network AllowNetworkWatcher
```

この機能が適切に登録されると、出力は次のようになります。

```CLI
info:    Executing command feature show
data:    Feature Name:       AllowNetworkWatcher
data:    Provider Name:      Microsoft.Network
data:    Registration State: Registered
info:    feature show command OK
```

> [!NOTE]
> Network Watcher は現在 CLI 1.0 のみをサポートしているため、新しい Network Watcher インスタンスを作成する手順は CLI 1.0 で提供されています。 CLI 2.0 を使用してプロバイダーを登録するには、`az feature register -n AllowNetworkWatcher --namespace Microsoft.Network` を使用します。

## <a name="create-a-network-watcher-in-the-portal"></a>ポータルで Network Watcher を作成する

**[その他のサービス]** > **[ネットワーク]** > **[ネットワーク ウォッチャー]** の順に移動します。 Network Watcher を有効にするすべてのサブスクリプションを選択できます。 このアクションにより、利用可能なすべてのリージョンに Network Watcher が作成されます。

![Network Watcher の作成][1]

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












