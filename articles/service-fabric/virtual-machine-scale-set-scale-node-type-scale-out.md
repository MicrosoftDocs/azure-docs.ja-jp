---
title: Azure Service Fabric クラスターにノード タイプを追加する
description: 仮想マシン スケール セットを追加することで、Service Fabric クラスターをスケールアウトする方法について説明します。
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: efd329c07b4881c6710d4173857b4186965438d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88719327"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>仮想マシン スケール セットを追加して Service Fabric クラスターをスケールアウトする
この記事では、既存のクラスターに新しいノード タイプを追加することで、Azure Service Fabric クラスターをスケーリングする方法について説明します。 Service Fabric クラスターは、ネットワークで接続された一連の仮想マシンまたは物理マシンで、マイクロサービスがデプロイおよび管理されます。 クラスターに属しているコンピューターまたは VM を "ノード" と呼びます。 仮想マシン スケール セットは、セットとして仮想マシンのコレクションをデプロイおよび管理するために使用する Azure コンピューティング リソースです。 Azure クラスターで定義されているすべてのノードの種類は、[異なるスケール セットとしてセットアップされます](service-fabric-cluster-nodetypes.md)。 その後は、ノードの種類ごとに個別に管理できます。 Service Fabric クラスターを作成した後は、新しいノード タイプ (仮想マシン スケール セット) を既存のクラスターに追加することで、クラスターを水平方向にスケーリングできます。  クラスターは、クラスターでワークロードを実行中であっても、いつでもスケーリングできます。  クラスターをスケーリングすると、アプリケーションも自動的にスケーリングされます。

## <a name="add-an-additional-scale-set-to-an-existing-cluster"></a>既存のクラスターに別のスケール セットを追加する
既存のクラスターへの新しいノード タイプ (仮想マシン スケール セットによって支持されている) の追加は、[プライマリ ノード タイプのアップグレード](service-fabric-scale-up-primary-node-type.md)と似ていますが、同じ NodeTypeRef を使わないこと、明らかにアクティブに使われている仮想マシン スケール セットを無効にしないこと、およびプライマリ ノード タイプを更新しない場合はクラスターの可用性を失わないことが異なります。 

NodeTypeRef プロパティは、仮想マシン スケール セットの Service Fabric 拡張機能プロパティ内で宣言されています。
```json
<snip>
"publisher": "Microsoft.Azure.ServiceFabric",
     "settings": {
     "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
     "nodeTypeRef": "[parameters('vmNodeType2Name')]",
     "dataPath": "D:\\\\SvcFab",
     "durabilityLevel": "Silver",
<snip>
```

さらに、この新しいノード タイプを Service Fabric クラスター リソースに追加する必要があります。

```json
<snip>
"nodeTypes": [
      {
      "name": "[parameters('vmNodeType2Name')]",
      "applicationPorts": {
                "endPort": "[parameters('nt2applicationEndPort')]",
                "startPort": "[parameters('nt2applicationStartPort')]"
      },
      "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
      "durabilityLevel": "Silver",
       "ephemeralPorts": {
                "endPort": "[parameters('nt2ephemeralEndPort')]",
                "startPort": "[parameters('nt2ephemeralStartPort')]"
      },
      "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
      "isPrimary": false,
      "vmInstanceCount": "[parameters('nt2InstanceCount')]"
},
<snip>
```

## <a name="next-steps"></a>次のステップ
* [プライマリ ノード タイプをスケールアップする方法](service-fabric-scale-up-primary-node-type.md)を学習します。
* [アプリケーションのスケーラビリティ](service-fabric-concepts-scalability.md)について学習します。
* [Azure クラスターをスケールインまたはスケールアウト](service-fabric-tutorial-scale-cluster.md)します。
* fluent Azure コンピューティング SDK を使用して [Azure クラスターをプログラムでスケーリングします](service-fabric-cluster-programmatic-scaling.md)。
* [スタンドアロン クラスターのスケールインまたはスケールアウトします](service-fabric-cluster-windows-server-add-remove-nodes.md)。

