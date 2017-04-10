---
title: "Azure Service Fabric クラスターの均衡をとる | Microsoft Docs"
description: "Service Fabric クラスター リソース マネージャーを使用してクラスターの均衡をとる方法について説明します。"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 030b1465-6616-4c0b-8bc7-24ed47d054c0
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: bb27d279396aa7b670187560cebe2ed074576bad
ms.openlocfilehash: ee77a01b2bb17ab70099e891e00255d1cec676f6
ms.lasthandoff: 01/25/2017


---
# <a name="balancing-your-service-fabric-cluster"></a>Service Fabric クラスターの均衡をとる
Service Fabric Cluster Resource Manager は、負荷を動的に変更したり、ノードやサービスの追加や削除に応答したり、制約違反を修正したり、必要に応じてクラスターの再調整を行ったりしますが、 これらはどのくらいの頻度で実行され、何によってトリガーされるのでしょうか。

均衡化に関する最初のコントロールは、一連のタイマーです。 これらのタイマーによって、クラスター Resource Manager がクラスターの状態について対応する必要のある事柄を確認する頻度が制御されます。 3 つの異なる作業のカテゴリがあり、それぞれに対応する独自のタイマーがあります。 次に例を示します。

1. 配置: この段階では、不足している任意のステートフル レプリカまたはステートレスなインスタンスの配置が処理されます。 これは、新しいサービスと、失敗したステートフルなレプリカやステートレスなインスタンスの処理の両方に対応します。 レプリカまたはインスタンスの削除はここで処理されます。
2. 制約チェック: この段階では、システム内のさまざまな配置の制約 (ルール) 違反の確認および修正が行われます。 ルールとは、たとえば、ノードに過剰な負荷がかかっていないこと、およびサービスを配置する上での制約が満たされていることを確認することです。
3. 分散: この段階では、さまざまなメトリックの構成済みの望ましい均衡レベルを基準に、先を見越しての再調整が必要であるかが確認されます。 必要な場合、よりクラスターの均衡をとる編成が検出されます。

## <a name="configuring-cluster-resource-manager-steps-and-timers"></a>クラスター リソース マネージャーの手順とタイマーを構成する
Cluster Resource Manager が行うことのできる、これらの異なる修正はいずれも、その頻度を制御する別々のタイマーによって管理されています。 それぞれのタイマーが作動したとき、タスクがスケジュールされます。 既定では、Resource Manager では次の処理が実行されます。

* 1/10 秒ごとに状態をスキャンして更新を適用する (例: ノードがダウンしたことを記録する)
* 1 秒ごとに配置と制約のチェック フラグを設定する
* 5 秒ごとに分散フラグを設定する

次の構成情報にこれが反映されます。

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

スタンドアロン デプロイの ClusterConfig.json 経由または Azure でホストされたクラスターの Template.json 経由:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PLBRefreshGap",
          "value": "0.10"
      },
      {
          "name": "MinPlacementInterval",
          "value": "1.0"
      },
      {
          "name": "MinLoadBalancingInterval",
          "value": "5.0"
      }
    ]
  }
]
```

現在、Cluster Resource Manager はこれらの処理を一度に&1; つずつのみ、順番に実行します (これらのタイマーを "最小間隔" と呼ぶのはこのためです)。 たとえば、Cluster Resource Manager は保留中の要求を処理して、クラスターに分散処理を行う前にサービスを作成します。 指定された既定の時間間隔でわかるように、Cluster Resource Manager は頻繁に実行する必要があるすべての処理をスキャンし、各手順の最後に加えられる一連の変更を小規模に抑えます。 Cluster Resource Manager は小規模な変更を頻繁に実行することで、クラスターで発生する事象に対応します。 同じ種類のイベントの多くは同時に発生する傾向があるため、既定のタイマーにはバッチ処理がいくつか用意されています。 既定では Cluster Resource Manager はクラスター内の数時間にわたり変更に目を通さず、すべての変更を一度で対応しようとします。 これにより、チャーンが突発的に発生することがあります。

また、クラスターが不均衡であるかどうかを Cluster Resource Manager が判定するには、追加の情報が必要です。 そのために、他に、"*分散しきい値*" と "*アクティビティしきい値*" という&2; つの構成要素があります。

## <a name="balancing-thresholds"></a>分散しきい値
分散しきい値は、先を見越した再調整をトリガーする主要コントロールです。 MinLoadBalancingInterval タイマーは、Cluster Resource Manager がチェックする頻度のみを決定します。何かの処理が発生することではありません。 分散しきい値は、特定のメトリックがどのようなときに、クラスター Resource Manager によってクラスターの均衡が崩れていると判断され、均衡化がトリガーされるのかを定義します。

分散しきい値は、クラスター定義の一部としてメトリックごとに定義されます。 メトリックについて詳しくは、[この記事](service-fabric-cluster-resource-manager-metrics.md)をご覧ください。

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

スタンドアロン デプロイの ClusterConfig.json 経由または Azure でホストされたクラスターの Template.json 経由:

```json
"fabricSettings": [
  {
    "name": "MetricBalancingThresholds",
    "parameters": [
      {
          "name": "MetricName1",
          "value": "2"
      },
      {
          "name": "MetricName2",
          "value": "3.5"
      }
    ]
  }
]
```

メトリックの分散しきい値は割合で規定されます。 最も負荷がかかっているノードの負荷量を、最も負荷がかかっていないノードの負荷量で割ったものがこの値を超過する場合、クラスターは不均衡であると見なされます。 結果として、次回 Cluster Resource Manager のチェックが行われるときに分散処理がトリガーされます。

<center>
![分散しきい値の例][Image1]
</center>

この例では、各サービスはいくつかのメトリックの&1; つの単位を消費しています。 一番上の例では、ノードに対する最大の負荷は&5; で最小は&2; です。 このメトリックの分散しきい値は&3; だとします。 クラスターの割合は 5/2 = 2.5 で、指定の分散しきい値 3 を下回るため、クラスターの均衡はとれています。 Cluster Resource Manager のチェックが行われるときに分散処理はトリガーされません。

下の例では、ノードに対する最大の負荷は&10; で最小は&2; であり、割合は&5; になります。 5 はそのメトリックの指定の分散しきい値&3; を上回ります。 その結果、再分散の実行が、次回の分散タイマーの作動時にスケジュールされます。 このような状況では、ほぼ確実に一部の負荷が Node3 に分散されます。 Service Fabric Cluster Resource Manager は最長一致のアプローチを使用しないため、一部の負荷は Node2 にも分散される可能性があります。 これによりノード間の全体的な差異が最小化されます。これは Cluster Resource Manager の目標の&1; つです。

<center>
![分散しきい値のアクション例][Image2]
</center>

分散しきい値を下回ることは明確な目標ではありません。 しきい値は Cluster Resource Manager にクラスターを確認して、改善ができることがある場合はそれを判断するように通知する "*トリガー*" に過ぎません。 実際、分散の検索が実行されても何かが移動されるとは限りません。クラスターが不均衡な状況を改善できないこともあります。

## <a name="activity-thresholds"></a>アクティビティしきい値
ノードが相対的に不均衡であるにもかかわらず、クラスターの負荷 "*全体*" が低い場合があります。 負荷がないことは一時的な低下またはクラスターが新しくて現在起動中であることが原因の可能性もあります。 いずれの場合も、得られる成果はほとんどないため、時間をかけてクラスターを分散することはお勧めしません。 クラスターで分散処理を実行しても、ネットワークとコンピューティングのリソースを無駄に消費し、確実な成果を挙げられないこともあります。** これを回避するために、アクティビティしきい値と呼ばれるもう&1; つのコントロールが用意されています。 アクティビティしきい値では、アクティビティに対して絶対的な下限を指定できます。 このしきい値を超えるノードがない場合、分散しきい値に達していても分散処理はトリガーされません。

例として、次の図を見てみましょう。 このメトリックに対する分散しきい値が 3 であると同時に、アクティビティしきい値が 1536 であるとします。 最初の例では、クラスターは分散しきい値に対しては不均衡ですが、アクティビティしきい値を満たすノードはないため、分散処理は発生しません。 下の例では、Node1 がアクティビティしきい値を大幅に超過しています。 メトリックが分散しきい値とアクティビティしきい値の両方を超過しているため、分散処理がスケジュールされます。

<center>
![アクティビティしきい値の例][Image3]
</center>

分散しきい値同様、アクティビティしきい値もクラスター定義でメトリックごとに定義します。

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

スタンドアロン デプロイの ClusterConfig.json 経由または Azure でホストされたクラスターの Template.json 経由:

```json
"fabricSettings": [
  {
    "name": "MetricActivityThresholds",
    "parameters": [
      {
          "name": "Memory",
          "value": "1536"
      }
    ]
  }
]
```

分散しきい値とアクティビティしきい値は、両方とも特定のメトリックに関連付けられています。同じメトリックが分散しきい値とアクティビティしきい値の両方を超えた場合にのみ分散処理がトリガーされます。

## <a name="balancing-services-together"></a>同時にサービスの均衡をとる
注目する点は、クラスターが不均衡であるかどうかはクラスター全体から判断するものであるということです。 しかし、個々のサービス レプリカやインスタンスを移動することでこれを修正します。 ご理解いただけましたでしょうか。 1 つのノードで発生するメモリのスタックは、複数のレプリカやインスタンスが関与している場合があります。 不均衡を修正するには、不均衡なメトリックを使用するステートフル レプリカやステートレス インスタンスを移動する必要がある場合があります。

まれに、均衡がとれていたサービスが移動されることがあります。 そのサービスのすべてのメトリックの均衡がたとえ完全に取れていても、他のサービスに不均衡がある場合、なぜサービスが移動してしまうのでしょうか。 確認してみましょう。

4 つのサービス、Service1、Service2、Service3、Service4 を例にとります。 Service1 は Metric1 と Metric2、Service2 は Metric2 と Metric3、Service3 は Metric3 と Metric4、Service4 は Metric99 に対してレポートを行います。 これで、お分かりいただけたでしょうか。 チェーンがあります。 実際には&4; つの独立したサービスがあるわけではなく、関連する複数のサービス (Service1、Service2、および Service3) と、独立したサービスが&1; つあります。

<center>
![サービスの分散処理を同時に行う][Image4]
</center>

したがって、Metric1 の不均衡は Service3 に属する (かつ Metric1 に属さない) レプリカやインスタンスが移動する原因になる可能性があります。 Metric1 の不均衡の度合いによっては、またそれを修正するためにクラスターでどのような変更を行う必要があったのかによっては、通常はほとんど発生しない移動が多発することがあります。 また、Metric1、2、または 3 の不均衡が Service4 の移動を引き起こすことはないと断言できます。 Service4 に属するレプリカやインスタンスを移動しても、Metric1、2、または 3 の均衡にはまったく影響しないため、移動しても意味がありません。

Cluster Resource Manager は、サービスの追加または削除や、メトリック構成の変更の可能性があるため、関連するサービスを自動的に検出します。 たとえば、分散処理を&2; 回実行する間に Service2 が再構成されて Metric2 が削除される可能性があります。 この変更によって、Service1 と Service2 の間のチェーンが崩れます。 2 グループの関連するサービスでなく、3 グループになります。

<center>
![サービスの分散処理を同時に行う][Image5]
</center>

## <a name="next-steps"></a>次のステップ
* メトリックは、Service Fabric クラスター リソース マネージャーが管理するクラスターの利用量と容量を表します。 メトリックの詳細とその構成方法については、[この記事](service-fabric-cluster-resource-manager-metrics.md)をご覧ください。
* 移動コストは、特定のサービスが他のサービスよりも高額になっていることをクラスター リソース マネージャーに警告する信号の&1; つです。 移動コストについて詳しくは、[この記事](service-fabric-cluster-resource-manager-movement-cost.md)をご覧ください。
* クラスター リソース マネージャーにはスロットルがいくつかあります。クラスターのチャーン (激しい動き) を落ち着かせるようにスロットルを構成できます。 通常は必要ありませんが、必要であれば、[ここ](service-fabric-cluster-resource-manager-advanced-throttling.md)で詳細を確認できます。

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png

