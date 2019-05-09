---
title: Azure Service Fabric のノードの種類と仮想マシン スケール セット | Microsoft Docs
description: Azure Service Fabric のノードの種類を仮想マシン スケール セットに関連付ける方法のほか、スケール セット インスタンスまたはクラスター ノードにリモートで接続する方法について説明します。
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: chackdan
ms.openlocfilehash: 7f9397ee21f74fe6a776881940e5721264216b0f
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58660616"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Azure Service Fabric のノードの種類と仮想マシン スケール セット
[仮想マシン スケール セット](/azure/virtual-machine-scale-sets)は、Azure コンピューティング リソースです。 スケール セットを使用すると、仮想マシンのコレクションをセットとしてデプロイおよび管理できます。 Azure Service Fabric クラスターで定義するノードの種類ごとに別個のスケールが設定されます。  Service Fabric ランタイムは、スケール セットの各仮想マシンにインストールされます。 各ノードの種類を個別にスケールアップまたはスケールダウンしたり、各クラスター ノードで実行されている OS SKU を変更したり、異なるポートのセットを開いたり、別の容量メトリックを使用したりできます。

次の図は、2 種類のノードがあるクラスターを示しています (ノードの名前は FrontEnd と BackEnd)。 各ノードの種類には、5 つのノードがあります。

![2 種類のノードがあるクラスター][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>仮想マシン スケール セットのインスタンスをノードにマップする
前の図に示されているように、スケール セットのインスタンスはインスタンス 0 で始まり、1 つずつ増えていきます。 番号はノード名に反映されます。 たとえば、ノード BackEnd_0 は、BackEnd スケール セットのインスタンス 0 です。 この特定のスケール セットには、BackEnd_0、BackEnd_1、BackEnd_2、BackEnd_3、BackEnd_4 という名前の 5 つのインスタンスがあります。

スケール セットをスケールアップすると、新しいインスタンスが作成されます。 通常、新しいスケール セットのインスタンス名は、スケール セットの名前に次のインスタンス番号を付加したものになります。 この例では、BackEnd_5 です。

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>スケール セットのロード バランサーをノードの種類とスケール セットにマップする
Azure Portal でクラスターをデプロイした場合、またはサンプル Azure Resource Manager テンプレートを使用した場合、リソース グループのすべてのリソースが一覧に表示されます。 また、各スケール セットまたはノードの種類のロード バランサーを確認できます。 ロード バランサー名には、次の形式が使用されます: **LB-&lt;ノードの種類の名前&gt;**。 たとえば、次の図で示すように、LB-sfcluster4doc-0 などです。

![リソース][Resources]


## <a name="next-steps"></a>次の手順
* ["任意の場所にデプロイ" 機能の概要と Azure で管理されるクラスターとの比較](service-fabric-deploy-anywhere.md)に関するページを参照します。
* [クラスター セキュリティ](service-fabric-cluster-security.md)について学習します。
* 特定のスケール セット インスタンスに[リモート接続する](service-fabric-cluster-remote-connect-to-azure-cluster-node.md)
* 展開後、クラスター VM の [RDP ポート範囲値を更新する](./scripts/service-fabric-powershell-change-rdp-port-range.md)
* クラスター VM の[管理者ユーザー名とパスワードを変更する](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md)

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
