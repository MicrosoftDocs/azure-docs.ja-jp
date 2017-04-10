---
title: "Azure Network Watcher Next Hop を使用して次ホップを検索する - REST | Microsoft Docs"
description: "この記事では、Azure REST API で Next Hop を使用して、次ホップの種類と IP アドレスを確認する方法について説明します。"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 2216c059-45ba-4214-8304-e56769b779a6
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 95ecd8d4ef3dd7a956206e7c3e07c793db0111f7
ms.openlocfilehash: 04801ce75a767da2756165f51f49192f89887380
ms.lasthandoff: 03/31/2017

---

# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-aure-network-watcher-using-azure-rest-api"></a>Azure REST API で Azure Network Watcher の Next Hop 機能を使用して次ホップの種類を確認する

> [!div class="op_single_selector"]
> - [Azure ポータル](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI](network-watcher-check-next-hop-cli.md)
> - [Azure REST API](network-watcher-check-next-hop-rest.md)

Next Hop は Network Watcher の機能の&1; つであり、指定された仮想マシンに基づいて次ホップの種類と IP アドレスを取得できます。 この機能は、仮想マシンからのトラフィックがゲートウェイ、インターネット、または仮想ネットワークを経由して宛先に到達するかどうかを判断する際に役立ちます。

## <a name="before-you-begin"></a>開始する前に

PowerShell を使用して REST API を呼び出すには、ARMClient を使用します。 ARMClient は、[Chocolatey 上の ARMClient](https://chocolatey.org/packages/ARMClient) に関するページの chocolatey 上にあります。

このシナリオは、[Network Watcher の作成](network-watcher-create.md)に関する記事の手順に従って Network Watcher を作成済みであることを前提としています。

## <a name="scenario"></a>シナリオ

この記事で説明するシナリオでは、リソースの次ホップの種類と IP アドレスを調べる、Next Hop という Network Watcher の機能を使用します。 Next Hop の詳細については、[Next Hop の概要](network-watcher-next-hop-overview.md)に関する記事をご覧ください。

このシナリオでは次のことを行います。

* 仮想マシンの次ホップを取得する。

## <a name="log-in-with-armclient"></a>ARMClient でのログイン

Azure の資格情報を使用して ARMClient にログインします。

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>仮想マシンの取得

次のスクリプトを実行して、仮想マシンを取得します。 この情報は、次ホップを実行するために必要となります。

次のコードには、次の変数の値が必要です。

- **subscriptionId** - 使用するサブスクリプション ID。
- **resourceGroupName** - 仮想マシンを含むリソース グループの名前。

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

次の例では、次の出力から仮想マシンの ID が使用されます。

```json
...
,
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```

## <a name="get-next-hop"></a>次ホップの取得

Authorization ヘッダーが作成されたら、仮想マシンの次ホップを取得できます。 コード例を動作させるには、次の値を置き換える必要があります。

> [!Important]
> Network Watcher REST API の呼び出しでは、要求 URI のリソース グループ名は Network Watcher を含むリソース グループであり、診断アクションを実行しているリソースではありません。

```powershell
$sourceIP = "10.0.0.4"
$destinationIP = "8.8.8.8"
$resourceGroupName = <resource group name>
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'sourceIpAddress': '${sourceIP}',
    'destinationIpAddress': '${destinationIP}',
    'targetNicResourceId' : '${targetNic}'
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/nextHop?api-version=2016-12-01" $requestBody
```

> [!NOTE]
> Next Hop では、実行する VM リソースが割り当てられている必要があります。

## <a name="results"></a>結果

次のスニペットは受信した出力の例を示しています。 結果には次の値が含まれています。

* **nextHopType** - この値は、Internet、VirtualAppliance、VirtualNetworkGateway、VnetLocal、HyperNetGateway、None のいずれかになります。
* **nextHopIpAddress** - 次ホップの IP アドレス。
* **routeTableId** - この値は、ルートに関連付けられているルート テーブルの URI です。ユーザー定義ルートが定義されていない場合は、*System Route* という値が返されます。

json 形式の結果を次に示します。

```json
{
  "nextHopType": "Internet",
  "routeTableId": "System Route"
}
```

## <a name="next-steps"></a>次のステップ

仮想マシンの次ホップを確認できたら、[セキュリティ ビューの概要](network-watcher-security-group-view-overview.md)に関する記事を参照して、ネットワーク リソースのセキュリティを表示します。















