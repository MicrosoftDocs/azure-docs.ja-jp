---
title: "Service Fabric クラスター リソース マネージャー：移動コスト | Microsoft Docs"
description: "Service Fabric サービスの移動コストの概要"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: f022f258-7bc0-4db4-aa85-8c6c8344da32
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: f85365a36aea39b4179805e728c7ddafa140f08b
ms.lasthandoff: 01/07/2017


---
# <a name="service-movement-cost-for-influencing-cluster-resource-manager-choices"></a>クラスター リソース マネージャーの選定に影響を与えるサービス移動コスト
クラスターに対して行う変更を決定するときに Service Fabric クラスター リソース マネージャーで考慮される重要な要因は、そのソリューションを実現するための全体的なコストです。 "コスト" の概念は、達成されるバランスの度合いとの交換条件として考慮されます。

サービス インスタンスやレプリカの移動では、最低でも CPU 時間とネットワーク帯域幅のコストがかかります。 ステートフル サービスの場合は、古いレプリカのシャット ダウン前に状態のコピーを作成するために必要なディスク領域とメモリ領域のコストもかかります。 Azure Service Fabric クラスター リソース マネージャーによって構成されるソリューションのコストが最小限であることが望ましいのは明らかです。 一方でクラスター内のリソースの割り当てを大幅に改善するソリューションを無視することは避けたいものです。

クラスター リソース マネージャーは、コストを&2; つの方法で計算し、制限します。これは、他の目的のためにクラスターを管理する場合にも同様に行われます。 第一に、クラスター リソース マネージャーはクラスターの新しいレイアウトを計画するときに、実行するすべての移動を個別にカウントします。 2 つのソリューションがほぼ同じバランス (スコア) で生成される場合には、コスト (移動の合計数) が小さいほうを選択します。

この戦略は効果的に機能します。 ただし、既定の負荷や静的な負荷の場合と同様に、すべての移動が等しい複雑なシステムではそうはいきません。 一部のコストがはるかに高くなる可能性があります。

## <a name="changing-a-replicas-move-cost-and-factors-to-consider"></a>レプリカの移動コストと考慮事項を変更する
サービスでは、負荷レポート (クラスター リソース マネージャーの別の機能) のように、自身の移動コストを動的にレポートすることもできます。

コード:

```csharp
this.ServicePartition.ReportMoveCost(MoveCost.Medium);
```

サービスの作成時に、既定の移動コストを指定することもできます。

MoveCost には、Zero、Low、Medium、High の&4; つのレベルがあります。 Zero を除き、MoveCost のレベルは相対的なものです。 移動コストが Zero の場合、移動にコストはかからず、ソリューションのスコアに対してカウントされません。 移動コストを High に設定しても、レプリカが&1; つの場所に維持されると保証されるわけでは*ありません*。

<center>
![移動するレプリカを選択する際の要因としての移動コスト][Image1]
</center>

移動コストは、同等のバランスを最も簡単に維持したまま全体的な中断が最小限になるソリューションを見つけるのに役立ちます。 サービスのコストの概念はさまざまなものに関係する可能性があります。 最も一般的なものは次のとおりです。

* サービスが移動する必要のある状態またはデータの量。
* クライアント切断のコスト。 通常、プライマリ レプリカを移動するコストは、セカンダリ レプリカの移動コストより高くなります。
* 実行中の操作を中断するコスト。 一部のデータ ストア レベル操作またはクライアント コールの応答で実行される操作にはコストがかかります。 特定の時点以降では、必要でなければ処理を中止しない方が適切です。 操作の実行中に移動コストを高くすると、そのサービス オブジェクトが移動される可能性は低くなります。 操作が完了したら、コストを通常の設定に戻します。

## <a name="next-steps"></a>次のステップ
* Service Fabric クラスター リソース マネージャーは、メトリックを使用して、クラスターの利用量と容量を管理します。 メトリックの詳細とその構成方法については、「 [Service Fabric のリソース使用量と負荷をメトリックで管理する](service-fabric-cluster-resource-manager-metrics.md)」を参照してください。
* クラスター リソース マネージャーでクラスターの負荷を管理し、分散するしくみについては、「 [Service Fabric クラスターの均衡をとる](service-fabric-cluster-resource-manager-balancing.md)」を参照してください。

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png

