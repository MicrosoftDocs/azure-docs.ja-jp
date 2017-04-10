---
title: "Service Fabric クラスター リソース マネージャーのスロットル | Microsoft Docs"
description: "Service Fabric クラスター リソース マネージャーで使用可能なスロットルの構成について説明します。"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 4a44678b-a5aa-4d30-958f-dc4332ebfb63
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 8a8419497bda3f1df523d6aff28028548abc155a
ms.lasthandoff: 01/07/2017


---

# <a name="throttling-the-behavior-of-the-service-fabric-cluster-resource-manager"></a>Service Fabric クラスター リソース マネージャーの動作を調整する
クラスター リソース マネージャーを正しく構成している場合でも、クラスターが中断する場合があります。 たとえば、同時ノードまたは障害ドメインのエラーが考えられます。このようなことがアップグレードの実行中に発生したとしたらどうなるでしょうか。 クラスター リソース マネージャーはすべてを修正しようとしますが、それによってクラスター内に混乱が発生する可能性があります。 スロットルは、クラスターを安定させる (ノードを元に戻し、ネットワーク パーティションを正常化し、修正されたビットをデプロイする) ためのリソースを提供する、バックネットの役割を果たします。

このような状況を支援するため、Service Fabric クラスター リソース マネージャーにはいくつかのスロットルが含まれています。 これらのスロットルは、非常に大きな影響力を持ちます。 これらの設定を既定値から変更する場合は、クラスターが並列で実行できる作業量についての計算を慎重に行ってください。

スロットルの既定値は、Service Fabric チームが経験を通じてその適切性を確認したものです。 これらを変更する必要がある場合は、予想される実際の負荷に応じて値をチューニングしてください。 一部のスロットルについては、本線の状況安定化にかかる時間がより長くなるとしても、必要と判断される場合があります。

## <a name="configuring-the-throttles"></a>スロットルの構成
既定で含まれているスロットルには、次のようなものがあります。

* GlobalMovementThrottleThreshold - この設定は (GlobalMovementThrottleCountingInterval で秒単位で定義した) 一定の期間にわたるクラスターの移動の合計数を制御します
* MovementPerPartitionThrottleThreshold – この設定は (MovementPerPartitionThrottleCountingInterval で秒単位で定義した) 一定の期間にわたるサービス パーティションの移動の合計数を制御します

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThreshold" Value="1000" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
     <Parameter Name="MovementPerPartitionThrottleThreshold" Value="50" />
     <Parameter Name="MovementPerPartitionThrottleCountingInterval" Value="600" />
</Section>
```

スタンドアロン デプロイの ClusterConfig.json 経由または Azure でホストされたクラスターの Template.json 経由:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "GlobalMovementThrottleThreshold",
          "value": "1000"
      },
      {
          "name": "GlobalMovementThrottleCountingInterval",
          "value": "600"
      },
      {
          "name": "MovementPerPartitionThrottleThreshold",
          "value": "50"
      },
      {
          "name": "MovementPerPartitionThrottleCountingInterval",
          "value": "600"
      }
    ]
  }
]
```

これらのスロットルを使用するお客様の多くは、使用している環境において既にリソース制約の問題を抱えているお客様です。 たとえば、各ノードへのネットワーク帯域幅に制限があるお客様や、スループットの制限により、多数のレプリカを並列的に作成できないディスクを使用しているお客様です。 この種の制限がある場合は、スロットルを使用しなかったとしても、障害発生時にトリガーされる操作が成功しなかったり、低速になったりします。 これらのお客様は、クラスターの安定化にかかる時間が長くなることを承知したうえで、スロットルを使用しています。 また、これらのお客様は、システム全体の信頼性が低下する可能性があることも承知したうえで、スロットルを使用しています。

## <a name="next-steps"></a>次のステップ
* クラスター リソース マネージャーでクラスターの負荷を管理し、分散するしくみについては、 [負荷分散](service-fabric-cluster-resource-manager-balancing.md)
* クラスター リソース マネージャーには、クラスターを記述するためのさまざまなオプションがあります。 オプションについて詳しくは、[Service Fabric クラスターの記述](service-fabric-cluster-resource-manager-cluster-description.md)に関する記事をご覧ください

