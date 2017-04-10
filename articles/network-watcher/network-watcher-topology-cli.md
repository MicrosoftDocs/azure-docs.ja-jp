---
title: "Azure Network Watcher トポロジの確認 - Azure CLI | Microsoft Docs"
description: "この記事では Azure CLI を使用してネットワーク トポロジにクエリを実行する方法を説明します。"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 5cd279d7-3ab0-4813-aaa4-6a648bf74e7b
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 02add070405f3f00d3631362a2b139239d44c34d
ms.lasthandoff: 03/28/2017

---

# <a name="view-network-watcher-topology-with-azure-cli"></a>Azure CLI を使用した Network Watcher トポロジの確認

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-topology-powershell.md)
> - [CLI](network-watcher-topology-cli.md)
> - [REST API](network-watcher-topology-rest.md)

Network Watcher のトポロジ機能では、サブスクリプションのネットワーク リソースを視覚的に表現します。 ポータルに、視覚化された情報が自動的に表示されます。 ポータルのトポロジ ビューの背後の情報は、PowerShell を使用して取得できます。
この機能により、データを他のツールで視覚化して使用できるため、トポロジの情報の汎用性が高まります。

この記事では、Windows、Mac、Linux で使用できるクロスプラット フォーム Azure CLI 1.0 を使います。 Network Watcher では、CLI サポートの Azure CLI 1.0 が使用されています。

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

このシナリオでは、`network watcher topology` コマンドレットでトポロジ情報を取得します。 [REST API でネットワーク トポロジを取得する](network-watcher-topology-rest.md)方法を説明している記事もあります。

このシナリオは、[Network Watcher の作成](network-watcher-create.md)に関する記事の手順に従って Network Watcher を作成済みであることを前提としています。

## <a name="scenario"></a>シナリオ

この記事で取り上げているシナリオでは、特定のリソース グループのトポロジの応答を取得します。

## <a name="retrieve-topology"></a>トポロジの取得

`network watcher topology` コマンドレットは、特定のリソース グループのトポロジを取得します。 JSON 形式で出力を確認するには、引数 "--json" を追加します。

```azurecli
azure network watcher topology -g resourceGroupName -n networkWatcherName -r topologyResourceGroupName --json
```

## <a name="results"></a>結果

返される結果には "Resources" というプロパティ名が付いています。これには、`network watcher topology` コマンドレットの JSON 応答の本文が含まれます。  この応答には、ネットワーク セキュリティ グループのリソースとその関連付け (Contains、Associated) が含まれています。

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "createdDateTime": "2017-02-17T22:20:59.461Z",
  "lastModified": "2016-12-19T22:23:02.546Z",
  "resources": [
    {
      "name": "testrg-vnet",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet",
      "location": "westcentralus",
      "associations": [
        {
          "name": "default",
          "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet/subnets/default",
          "associationType": "Contains"
        }
      ]
    },
    {
      "name": "default",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet/subnets/default",
      "location": "westcentralus",
      "associations": []
    },
    {
      "name": "testclient",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testclient",
      "location": "westcentralus",
      "associations": [
        {
          "name": "testNic",
          "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testNic",
          "associationType": "Contains"
        }
      ]
    },
    ...    
  ]
}
```

## <a name="next-steps"></a>次のステップ

[セキュリティ グループ ビューの概要](network-watcher-security-group-view-overview.md)に関するページで、ネットワーク リソースに適用されるセキュリティ ルールを確認する

