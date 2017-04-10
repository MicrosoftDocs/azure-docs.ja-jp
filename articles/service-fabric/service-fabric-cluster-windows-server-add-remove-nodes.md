---
title: "スタンドアロン Service Fabric クラスターでノードを追加または削除する | Microsoft Docs"
description: "Windows Server を実行する物理コンピューターまたは仮想マシン上で、Azure Service Fabric クラスターにノードを追加または削除する方法について説明します。追加先または削除元は、オンプレミスでも、任意のクラウドでもかまいません。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: bc6b8fc0-d2af-42f8-a164-58538be38d02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/02/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: af121309be44852ee51f34130330533adf19d586
ms.openlocfilehash: 68474b24519a46db71fe59b5d0574cc4700efccb
ms.lasthandoff: 02/16/2017


---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Windows Server を実行するスタンドアロン Service Fabric クラスターでノードを追加または削除する
[Windows Server コンピューターでスタンドアロン Service Fabric クラスターを作成](service-fabric-cluster-creation-for-windows-server.md)した後に、ビジネス ニーズが変更されて、クラスターへの複数のノードの追加または削除が必要になるかもしれません。 この記事では、これを実行する詳細の手順について説明します。

## <a name="add-nodes-to-your-cluster"></a>クラスターへのノードの追加
1. [クラスターのデプロイのためにマシンの前提条件を満たす準備をする](service-fabric-cluster-creation-for-windows-server.md) セクションの手順に従って、クラスターに追加する VM/マシンを準備します。
2. この VM/マシンをどの障害ドメインとアップグレード ドメインに追加するかを計画します。
3. クラスターに追加する VM/マシンにリモート デスクトップ (RDP) 接続します。
4. この VM/マシンに [Windows Server 用の Service Fabric のスタンドアロン パッケージをダウンロード](http://go.microsoft.com/fwlink/?LinkId=730690) またはコピーし、パッケージを解凍します。
5. 管理者として Powershell を実行し、解凍したパッケージのある場所に移動します。
6. 追加する新しいノードを記述したパラメーターを指定して、 *AddNode.ps1* Powershell を実行します。 次の例では、名前が VM5、タイプが NodeType0、IP アドレスが 182.17.34.52 の新しいノードを UD1 と fd:/dc1/r0 に追加します。 *ExistingClusterConnectionEndPoint* は、既存のクラスターに既にあるノードの接続エンドポイントです。 このエンドポイントでは、クラスター内の " *任意* " のノードの IP アドレスを選択できます。

```
.\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA

```
新しいノードが追加されたかどうかは、[Get-ServiceFabricNode](https://docs.microsoft.com/powershell/servicefabric/vlatest/Get-ServiceFabricNode) コマンドレットを実行して確認できます。


## <a name="remove-nodes-from-your-cluster"></a>クラスターからのノードの削除
クラスターに対して選択した信頼性レベルによっては、プライマリ ノード タイプの最初の n (3/5/7/9) ノードを削除することができません。 また、開発用クラスターでの RemoveNode コマンドの実行はサポートされていません。

1. クラスターから削除する VM/マシンにリモート デスクトップ (RDP) 接続します。
2. [Windows Server 用の Service Fabric のスタンドアロン パッケージをダウンロード](http://go.microsoft.com/fwlink/?LinkId=730690) またはコピーし、この VM/マシンにパッケージを解凍します。
3. 管理者として Powershell を実行し、解凍したパッケージのある場所に移動します。
4. Powershell で *RemoveNode.ps1* を実行します。 次の例では、現在のノードをクラスターから削除します。 *ExistingClientConnectionEndpoint* は、クラスター内に残るすべてのノードに対するクライアント接続エンドポイントです。 クラスター内にある*任意の* **他のノード** の IP アドレスとエンドポイント ポートを選択します。 この**他のノード**はさらに、削除されたノードのクラスターの構成を更新します。 

```

.\RemoveNode.ps1 -ExistingClientConnectionEndpoint 182.17.34.50:19000

```

> [!NOTE]
> システム サービスの依存性に起因し、一部のノードが削除されないことがあります。 そのようなノードはプライマリ ノードであり、`Get-ServiceFabricClusterManifest` を利用してクラスター マニフェストに問い合わせると識別できます。`IsSeedNode=”true”` のマークが付いたノード エントリを探してください。 
> 
> 

ノードを削除した後でも、クリエまたは SFX に「ダウン中」として表示される場合、それは既知の不具合であるとして理解してください。 今後のリリースで修正される予定です。 


## <a name="remove-node-types-from-your-cluster"></a>クラスターからのノードの種類の削除
ノードの種類を削除するには、特別な注意が必要です。 ノードの種類を削除する前に、そのノードの種類を参照しているノードがないことを再確認してください。


## <a name="replace-primary-nodes-of-your-cluster"></a>クラスターのプライマリ ノードの置き換え
プライマリ ノードの置き換えは、まとめて削除して後で追加するのではなく、1 ノードづつ実行する必要があります。


## <a name="next-steps"></a>次のステップ
* [スタンドアロン Windows クラスターの構成設定](service-fabric-cluster-manifest.md)
* [X.509 証明書を使用した Windows でのスタンドアロン クラスターの保護](service-fabric-windows-cluster-x509-security.md)
* [Windows を実行する Azure VM を使用してスタンドアロン Service Fabric クラスターを作成する](service-fabric-cluster-creation-with-windows-azure-vms.md)


