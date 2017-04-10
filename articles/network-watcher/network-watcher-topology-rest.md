---
title: "Azure Network Watcher トポロジの確認 - REST API | Microsoft Docs"
description: "この記事では、REST API を使用してネットワーク トポロジを照会する方法を説明します。"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: de9af643-aea1-4c4c-89c5-21f1bf334c06
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 50d82847fb629880f298f79f9ab791a13836f01c
ms.openlocfilehash: 4fa34050a8039cebebe30842469c596c83744313
ms.lasthandoff: 03/31/2017

---

# <a name="view-network-watcher-topology-with-rest-api"></a>REST API を使用した Network Watcher トポロジの確認

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-topology-powershell.md)
> - [CLI](network-watcher-topology-cli.md)
> - [REST API](network-watcher-topology-rest.md)

Network Watcher のトポロジ機能では、サブスクリプションのネットワーク リソースを視覚的に表現します。 ポータルに、視覚化された情報が自動的に表示されます。 ポータルのトポロジ ビューの背後の情報は、PowerShell を使用して取得できます。
この機能により、データを他のツールで視覚化して使用できるため、トポロジの情報の汎用性が高まります。

相互接続は、2 つのリレーションシップでモデル化されます。

- **含有** - 例: VNet にサブネットが含まれ、サブネットに NIC が含まれる
- **関連付け** - 例: NIC が VM に関連付けられている

Topology REST API のクエリを実行したときに返されるプロパティは次のとおりです。

* **name** - リソースの名前。
* **id** - リソースの URI。
* **location** - リソースのある場所。
* **associations** - 参照されたオブジェクトへの関連付けのリスト。
    * **name** - 参照されたリソースの名前。
    * **resourceId** - 関連付けで参照されているリソースの URI。
    * **associationType** - 子オブジェクトと親のリレーションシップを参照する値。 有効な値は **Contains** または **Associated**。

## <a name="before-you-begin"></a>開始する前に

このシナリオではトポロジ情報を取得します。 PowerShell を使用して REST API を呼び出すには、ARMClient を使用します。 ARMClient は、[Chocolatey 上の ARMClient](https://chocolatey.org/packages/ARMClient) に関するページの chocolatey 上にあります。

このシナリオは、[Network Watcher の作成](network-watcher-create.md)に関する記事の手順に従って Network Watcher を作成済みであることを前提としています。

## <a name="scenario"></a>シナリオ

この記事で取り上げているシナリオでは、特定のリソース グループのトポロジの応答を取得します。

## <a name="log-in-with-armclient"></a>ARMClient でのログイン

Azure の資格情報を使用して ARMClient にログインします。

```PowerShell
armclient login
```

## <a name="retrieve-topology"></a>トポロジの取得

次の例では、REST API からトポロジを要求します。  この例は、柔軟に例を作成できるように、パラメーター化されています。  \< \> で囲まれたすべての値を置き換えてください。

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>" # Resource group name to run topology on
$NWresourceGroupName = "<resource group name>" # Network Watcher resource group name
$networkWatcherName = "<network watcher name>"
$requestBody = @"
{
    'targetResourceGroupName': '${resourceGroupName}'
}
"@

armclient POST "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/topology?api-version=2016-07-01" $requestBody
```

次の応答の例は、リソース グループのトポロジを取得したときに返される応答を短縮したものです。

```json
{
  "id": "ecd6c860-9cf5-411a-bdba-512f8df7799f",
  "createdDateTime": "2017-01-18T04:13:07.1974591Z",
  "lastModified": "2017-01-17T22:11:52.3527348Z",
  "resources": [
    {
      "name": "virtualNetwork1",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/{virtualNetworkName}",
      "location": "westcentralus",
      "associations": [
        {
          "name": "{subnetName}",
          "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/(virtualNetworkName)/subnets
/{subnetName}",
          "associationType": "Contains"
        }
      ]
    },
    {
      "name": "webtestnsg-wjplxls65qcto",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}
s65qcto",
      "associationType": "Associated"
    },
    ...
  ]
}
```

## <a name="next-steps"></a>次のステップ

[Power BI による NSG フロー ログの視覚化](network-watcher-visualize-nsg-flow-logs-power-bi.md)に関する記事を参照して、Power BI で NSG フロー ログを視覚化する方法を確認する


