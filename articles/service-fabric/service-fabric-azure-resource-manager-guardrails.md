---
title: Service Fabric Azure Resource Manager デプロイのガードレール
description: この記事では、Azure Resource Manager を使用して Service Fabric クラスターをデプロイするときの一般的な間違いの概要と、それらを回避する方法について説明します。
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: pepogors
ms.openlocfilehash: aa1499c57ead28bfcee90a2f224ef9c3bb1d7f58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86247826"
---
# <a name="service-fabric-guardrails"></a>Service Fabric のガードレール 
Service Fabric クラスターをデプロイするとき、ガードレールが設定されています。これにより、無効なクラスター構成の場合、Azure Resource Manager のデプロイは失敗します。 以下のセクションでは、クラスター構成に関する一般的な問題の概要と、これらの問題を軽減するために必要な手順について説明します。 

## <a name="durability-mismatch"></a>持続性の不一致
### <a name="overview"></a>概要
Service Fabric ノード タイプの持続性の値は、Azure Resource Manager テンプレートの 2 つの異なるセクションで定義されます。 仮想マシン スケール セット リソースの仮想マシン スケール セット拡張機能のセクションと、Service Fabric クラスター リソースのノード タイプのセクションです。 これらのセクションの持続性の値が一致している必要があります。そうしないと、リソースのデプロイは失敗します。

次のセクションでは、仮想マシン スケール セット拡張機能の持続性の設定と、Service Fabric ノード タイプの持続性の設定との、持続性の不一致の例を示します。  

**仮想マシン スケール セットの持続性の設定**
```json 
{
  "extensions": [
    {
      "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
      "properties": {
        "type": "ServiceFabricNode",
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
          "durabilityLevel": "Silver",
        }
      }
    }
  ]
}
```

**Service Fabric ノード タイプの持続性の設定** 
```json
{
  "nodeTypes": [
    {
      "name": "[variables('vmNodeType0Name')]",
      "durabilityLevel": "Bronze",
      "isPrimary": true,
      "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    }
  ]
}
```

### <a name="error-messages"></a>エラー メッセージ
* Virtual Machine Scale Set durability mismatch does not match the current Service Fabric Node Type durability level (仮想マシン スケール セットの持続性の不一致で、現在の Service Fabric ノード タイプの持続性レベルと一致しません)
* Virtual Machine Scale Set durability does not match the target Service Fabric Node Type durability level (仮想マシン スケール セットの持続性がターゲットの Service Fabric ノード タイプの持続性レベルと一致しません)
* Virtual Machine Scale Set durability does match the current Service Fabric durability level or the target Service Fabric Node Type durability level (仮想マシン スケール セットの持続性が、現在の Service Fabric の持続性レベルまたはターゲットの Service Fabric ノード タイプの持続性レベルと一致しません) 

### <a name="mitigation"></a>対応策
上記のいずれかのエラー メッセージによって示される持続性の不一致を修正するには、次のようにします。
1. Azure Resource Manager テンプレートの仮想マシン スケール セット拡張機能または Service Fabric ノード タイプのセクションで、値が一致するように持続性レベルを更新します。
2. 更新された値を使用して Azure Resource Manager テンプレートを再デプロイします。


## <a name="seed-node-deletion"></a>シード ノードの削除 
### <a name="overview"></a>概要
Service Fabric クラスターには、クラスターのプライマリ ノード タイプで実行されるシステムサービスのレプリカ数を決定するために使用される [信頼性レベル](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) プロパティがあります。 必要なレプリカの数によって、クラスターのプライマリ ノード タイプで維持する必要があるノードの最小数が決まります。 プライマリ ノード タイプのノード数が信頼性レベルで必要な最小値を下回ると、クラスターが不安定になります。  

### <a name="error-messages"></a>エラー メッセージ 
シード ノードの削除操作が検出されたため、拒否されます。 
* この操作を実行すると、{0} の可能性のあるシード ノードだけがクラスターに残され、{1} が最低限必要になります。
* {0} のシードノードを {1} から削除すると、シード ノードのクォーラムが喪失するためクラスターがダウンします。 一度に削除できるシードノードの最大数は {2} です。
 
### <a name="mitigation"></a>対応策 
プライマリ ノード タイプに、クラスターで指定されている信頼性の Virtual Machines が十分にあることを確認してください。 仮想マシンスケールセットが、指定された信頼性レベルのノードの最小数を下回る場合、仮想マシンを削除することはできません。
* 信頼性レベルが正しく指定されている場合は、信頼性レベルに必要な十分な数のノードがプライマリ ノード タイプにあることを確認してください。 
* 信頼性レベルが正しく指定されていない場合は、仮想マシン スケールセットの操作を開始する前に、まず信頼性レベルを下げて Service Fabric リソースの変更を開始し、完了するまで待機します。
* 信頼性レベルが Bronze の場合は、次の [手順](./service-fabric-cluster-scale-in-out.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set) に従って、クラスターを適切にスケール インしてください。

## <a name="next-steps"></a>次のステップ
* Windows Server を実行している VM またはコンピューター上にクラスターを作成する:[Windows Server 用の Service Fabric クラスターの作成](service-fabric-cluster-creation-for-windows-server.md)
* Linux を実行している VM またはコンピューター上にクラスターを作成する:[Linux クラスターの作成](service-fabric-cluster-creation-via-portal.md)
* Service Fabric のトラブルシューティング:[トラブルシューティング ガイド](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
