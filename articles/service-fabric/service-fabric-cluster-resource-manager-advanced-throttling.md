---
title: Service Fabric クラスター リソース マネージャーでのスロットル
description: Service Fabric クラスター リソース マネージャーで使用可能なスロットルの構成について説明します。
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b4d78b339bab02b5c44a31939e0da769dc21c3ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75452174"
---
# <a name="throttling-the-service-fabric-cluster-resource-manager"></a>Service Fabric クラスター リソース マネージャーのスロットル
クラスター リソース マネージャーを正しく構成している場合でも、クラスターが中断する場合があります。 たとえば、同時ノードや障害ドメインのエラーが考えられます。このようなことがアップグレードの実行中に発生したとしたらどうなるでしょうか。 クラスター リソース マネージャーは常に、クラスターの再編成と修正を試みるクラスター リソースを使用することで、すべてを修正しようとします。 スロットルは、安定化、つまり、ノードを元に戻し、ネットワーク パーティションを正常化し、修正されたビットをデプロイするためのリソースを提供する、バックネットの役割を果たします。

このような状況を支援するため、Service Fabric クラスター リソース マネージャーにはいくつかのスロットルが含まれています。 このようなスロットルはすべて、非常に大きな影響力を持つため、 通常は変更しないでください。変更するときは、必ず慎重に計画を立て、十分にテストを行ってください。

クラスター リソース マネージャーのスロットルを変更する場合は、予想される実際の負荷に応じて値をチューニングしてください。 一部のスロットルについては、状況の安定化にかかる時間がより長くなるとしても、必要と判断される場合があります。 スロットルの正しい値を判断するには、テストが必要です。 スロットルは、クラスターが適切な時間で変更に対応できる程度に高く、また、大量のリソース消費を防ぐことができる程度に低い値に設定する必要があります。 

スロットルを使用するお客様の多くは、使用している環境において既にリソース制約の問題を抱えているお客様です。 たとえば、各ノードのネットワーク帯域幅に制限があるお客様や、スループットの制限により、多数のステートフル レプリカを並列的に作成できないディスクを使用しているお客様です。 スロットルを使用しないと、操作によってリソースに過剰な負荷がかかり、操作が失敗したり、低速になったりする可能性があります。 こうしたお客様は、クラスターの安定化にかかる時間が長くなることを承知したうえで、スロットルを使用しています。 また、これらのお客様は、システム全体の信頼性が低下する可能性があることも承知したうえで、スロットルを使用しています。


## <a name="configuring-the-throttles"></a>スロットルの構成

Service Fabric には、レプリカの移動数のスロットルを行うためのメカニズムが 2 つあります。 Service Fabric 5.7 より前の既定のメカニズムでは、スロットルが、許可されている移動の絶対数として表されています。 これは一部のサイズのクラスターでは機能しません。 たとえば、既定値が設定されている場合、小規模クラスターでは何も問題はありませんが、この値は大規模クラスターには小さすぎるため、分散速度が大幅に低下し、必要な速度が出ません。 この以前のメカニズムは、割合を基にしたスロットリングに置き換えられ、サービスとノード数が定期的に変わる動的クラスターによって、スケール操作が強化されています。

スロットルは、クラスター内のレプリカの数の割合に基づいています。 割合を基にしたスロットルにより、たとえば、"10 分間隔で 10% を超えるレプリカを移動しない" といったルールが有効になります。

割合を基にしたスロットルの構成設定は次のとおりです。

  - GlobalMovementThrottleThresholdPercentage - クラスター内の任意の時点における移動の最大許容数。クラスター内のレプリカの合計数に対する割合として表されます。 0 は無制限であることを示します。 既定値は 0 です。 この設定と GlobalMovementThrottleThreshold の両方が指定されている場合は、より控え目な制限が使用されます。
  - GlobalMovementThrottleThresholdPercentageForPlacement - 配置フェーズにおける移動の最大許容数。クラスター内のレプリカの合計数に対する割合として表されます。 0 は無制限であることを示します。 既定値は 0 です。 この設定と GlobalMovementThrottleThresholdForPlacement の両方が指定されている場合は、より控え目な制限が使用されます。
  - GlobalMovementThrottleThresholdPercentageForBalancing - 分散フェーズにおける移動の最大許容数。クラスター内のレプリカの合計数に対する割合として表されます。 0 は無制限であることを示します。 既定値は 0 です。 この設定と GlobalMovementThrottleThresholdForBalancing の両方が指定されている場合は、より控え目な制限が使用されます。

スロットルの割合を指定するときは、5% を 0.05 として指定します。 こうしたスロットルが制御されます間隔は GlobalMovementThrottleCountingInterval です。これは秒単位で指定されます。


``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThresholdPercentage" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForPlacement" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForBalancing" Value="0" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
</Section>
```

スタンドアロン デプロイの ClusterConfig.json 経由または Azure でホストされたクラスターの Template.json 経由:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "GlobalMovementThrottleThresholdPercentage",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForPlacement",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForBalancing",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleCountingInterval",
          "value": "600"
      }
    ]
  }
]
```

### <a name="default-count-based-throttles"></a>既定のカウント ベースのスロットル
この情報は、古いクラスターがある場合、またはアップグレードされたクラスターでこうした構成がまだ保持されているときに提供されます。 一般的には、上記の割合を基にしたスロットルに置き換えることをお勧めします。 割合に基づくスロットルは既定で無効になっているため、このカウント ベースのスロットルは、無効にして、割合に基づくスロットルで置き換えない限り、引き続き既定のスロットルとして使用されます。 

  - GlobalMovementThrottleThreshold - この設定は、一定の期間にわたるクラスターの移動の合計数を制御します。 この時間は、GlobalMovementThrottleCountingInterval として秒単位で指定されます。 GlobalMovementThrottleThreshold の既定値は 1000 です。GlobalMovementThrottleCountingInterval の既定値は 600 です。
  - MovementPerPartitionThrottleThreshold – この設定は、一定の期間にわたるサービス パーティションの移動の合計数を制御します。 この時間は、MovementPerPartitionThrottleCountingInterval として秒単位で指定されます。 MovementPerPartitionThrottleThreshold の既定値は 50 です。MovementPerPartitionThrottleCountingInterval の既定値は 600 です。

こうしたスロットルの構成は、割合に基づくスロットルと同じパターンに従います。

## <a name="next-steps"></a>次のステップ
- クラスター リソース マネージャーでクラスターの負荷を管理し、分散するしくみについては、 [負荷分散](service-fabric-cluster-resource-manager-balancing.md)
- Cluster Resource Manager には、クラスターを記述するためのさまざまなオプションがあります。 オプションの詳細については、[Service Fabric クラスターの記述](service-fabric-cluster-resource-manager-cluster-description.md)に関するこの記事を参照してください。
