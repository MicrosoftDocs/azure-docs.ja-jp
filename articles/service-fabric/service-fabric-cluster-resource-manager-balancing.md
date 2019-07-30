---
title: Azure Service Fabric クラスターの均衡をとる | Microsoft Docs
description: Service Fabric クラスター リソース マネージャーを使用してクラスターの均衡をとる方法について説明します。
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 030b1465-6616-4c0b-8bc7-24ed47d054c0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 3ea95405f68938906ba010836753cd74ab0f775e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446744"
---
# <a name="balancing-your-service-fabric-cluster"></a>Service Fabric クラスターの均衡をとる
Service Fabric クラスター リソース マネージャーは、動的な負荷の変更、ノードやサービスの追加や削除への応答をサポートしています。 また、制約違反を自動的に修正し、必要に応じてクラスターを再調整することもできます。 このような操作はどのくらいの頻度で実行されるでしょうか。また、何によってトリガーされるでしょうか。

クラスター リソース マネージャーが実行する作業には、3 つのカテゴリがあります。 次に例を示します。

1. 配置: この段階では、不足している任意のステートフル レプリカまたはステートレスなインスタンスの配置が処理されます。 配置には、新しいサービスと、失敗したステートフルなレプリカやステートレスなインスタンスの処理の両方が含まれます。 レプリカまたはインスタンスの削除はここで処理されます。
2. 制約チェック: この段階では、システム内のさまざまな配置の制約 (ルール) 違反の確認および修正が行われます。 ルールとは、たとえば、ノードに過剰な負荷がかかっていないこと、およびサービスを配置する上での制約が満たされていることを確認することです。
3. 分散: この段階では、さまざまなメトリックの構成済みの望ましい均衡レベルを基準に、再調整が必要であるかが確認されます。 必要な場合、よりクラスターの均衡をとる編成が検出されます。

## <a name="configuring-cluster-resource-manager-timers"></a>クラスター リソース マネージャーのタイマーを構成する
均衡化に関する最初のコントロールは、一連のタイマーです。 これらのタイマーによって、クラスター リソース マネージャーがクラスターを検査し、是正措置を実行する頻度が制御されます。

Cluster Resource Manager が行うことのできる、これらの異なる修正はいずれも、その頻度を制御する別々のタイマーによって管理されています。 それぞれのタイマーが作動したとき、タスクがスケジュールされます。 既定では、Resource Manager では次の処理が実行されます。

* 1/10 秒ごとに状態をスキャンして更新を適用する (例: ノードがダウンしたことを記録する)
* 配置チェック フラグを毎秒設定する
* 制約チェック フラグを毎秒設定する
* 分散フラグを 5 秒ごとに設定する

これらのタイマーを制御する構成例を次に示します。

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
          "name": "MinConstraintCheckInterval",
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

現在、クラスター リソース マネージャーは、常に一度に 1 つずつ、順番にこれらの処理を実行します。 そのため、これらのタイマーを "最小間隔" と呼び、タイマーが切れたときに実行されるアクションを "設定フラグ" と呼びします。 たとえば、Cluster Resource Manager は保留中の要求を処理して、クラスターに分散処理を行う前にサービスを作成します。 指定されている既定の時間間隔でわかるように、クラスター リソース マネージャーは頻繁に実行する必要があるすべての処理をスキャンします。 その結果、通常、各手順で加えられる一連の変更は小規模になります。 クラスター リソース マネージャーは小規模な変更を頻繁に実行することで、クラスターで発生する事象に応答できます。 同じ種類のイベントの多くは同時に発生する傾向があるため、既定のタイマーにはバッチ処理がいくつか用意されています。 

たとえば、ノードで障害が発生した場合、フォールト ドメイン全体を一度で処理することができます。 これらすべての障害は、*PLBRefreshGap* の次の状態更新時にキャプチャされます。 以降の配置、制約チェック、分散の実行中に、修正が決定されます。 既定では Cluster Resource Manager はクラスター内の数時間にわたり変更に目を通さず、すべての変更を一度で対応しようとします。 これにより、チャーンが突発的に発生することがあります。

また、クラスターが不均衡であるかどうかを Cluster Resource Manager が判定するには、追加の情報が必要です。 そのために、他に 2 つの構成があります (*BalancingThresholds* と *ActivityThresholds*)。

## <a name="balancing-thresholds"></a>分散しきい値
分散しきい値は、再調整をトリガーする主要コントロールです。 メトリックの分散しきい値は_割合_で規定されます。 最も負荷がかかっているノードのメトリックの負荷を、最も負荷がかかっていないノードの負荷量で割ったものが、そのメトリックの*分散しきい値*を超える場合、クラスターは不均衡であると見なされます。 結果として、次回 Cluster Resource Manager のチェックが行われるときに分散処理がトリガーされます。 *MinLoadBalancingInterval* タイマーは、再調整が必要かどうかをクラスター リソース マネージャーでチェックする頻度を定義します。 チェックは、何かがおこる処理ではありません。 

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

<center>

![分散しきい値の例][Image1]
</center>

この例では、各サービスはいくつかのメトリックの 1 つの単位を消費しています。 一番上の例では、ノードに対する最大の負荷は 5 で最小は 2 です。 このメトリックの分散しきい値は 3 だとします。 クラスターの割合は 5/2 = 2.5 で、指定の分散しきい値 3 を下回るため、クラスターの均衡はとれています。 Cluster Resource Manager のチェックが行われるときに分散処理はトリガーされません。

下の例では、ノードに対する最大の負荷は 10 で最小は 2 であり、割合は 5 になります。 5 はそのメトリックの指定の分散しきい値 3 を上回ります。 その結果、再分散の実行が、次回の分散タイマーの作動時にスケジュールされます。 このような状況では、通常、一部の負荷が Node3 に分散されます。 Service Fabric のクラスター リソース マネージャーは最長一致のアプローチを使用しないため、一部の負荷は Node2 にも分散される可能性があります。 

<center>

![分散しきい値のアクション例][Image2]
</center>

> [!NOTE]
> "分散" は、クラスター内の負荷を管理するために 2 つの戦略を処理しています。 クラスター リソース マネージャーが使用する既定の戦略では、クラスター内のノード全体に負荷が分散されます。 もう 1 つの戦略は[最適化](service-fabric-cluster-resource-manager-defragmentation-metrics.md)です。 最適化は、同じ分散処理の実行時に実行されます。 分散および最適化の戦略は、同じクラスター内の複数のメトリックに使用できます。 1 つのサービスが分散メトリックと最適化メトリックの両方を使用することができます。 最適化メトリックの場合、クラスターの負荷の比率が分散しきい値を _下回った_ 場合に再調整がトリガーされます。 
>

分散しきい値を下回ることは明確な目標ではありません。 分散しきい値は、単なる*トリガー*です。 分散を実行すると、クラスター リソース マネージャーは、実行可能な改善がある場合はそれを決定します。 分散検索が開始されたからといって、何かが移動するとは限りません。 場合によっては、クラスターの均衡が取れていなくても、制約が多すぎて修正できないことがあります。 また、改善には移動が必要であり、移動には[コストがかかりすぎる](service-fabric-cluster-resource-manager-movement-cost.md)場合があります。

## <a name="activity-thresholds"></a>アクティビティしきい値
ノードが相対的に不均衡であるにもかかわらず、クラスターの負荷 "*全体*" が低い場合があります。 負荷がないことは一時的な低下またはクラスターが新しくて現在起動中であることが原因の可能性もあります。 いずれの場合も、得られる成果はほとんどないため、時間をかけてクラスターを分散することはお勧めしません。 クラスターで分散処理を実行しても、ネットワークとコンピューティングのリソースを無駄に消費し、*確実*で大きな成果を挙げられないこともあります。 不要な移動を回避するために、アクティビティしきい値と呼ばれるもう 1 つのコントロールが用意されています。 アクティビティしきい値では、アクティビティに対して絶対的な下限を指定できます。 このしきい値を超えるノードがない場合、分散しきい値に達していても分散処理はトリガーされません。

このメトリックに 3 の分散しきい値を保持しているとします。 また、1536 のアクティビティしきい値があるとします。 最初の例では、クラスターは分散しきい値に対しては不均衡ですが、アクティビティしきい値を満たすノードはないため、分散処理は発生しません。 下の例では、Node1 がアクティビティしきい値を超えています。 メトリックが分散しきい値とアクティビティしきい値の両方を超過しているため、分散処理がスケジュールされます。 例として、次の図を見てみましょう。 

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

> [!NOTE]
> 指定しない場合、メトリックの分散しきい値は 1、アクティビティしきい値は 0 です。 つまり、クラスター リソース マネージャーは、指定された負荷について、そのメトリックが完全に分散された状態を保とうとします。 カスタム メトリックを使用している場合、メトリックの分散しきい値とアクティビティしきい値を明示的に定義することをお勧めします。 
>

## <a name="balancing-services-together"></a>同時にサービスの均衡をとる
クラスターの均衡がとれているかどうかは、クラスター全体の判断です。 しかし、個々のサービス レプリカやインスタンスを移動することでこれを修正します。 ご理解いただけましたでしょうか。 1 つのノードで発生するメモリのスタックは、複数のレプリカやインスタンスが関与している場合があります。 不均衡を修正するには、不均衡なメトリックを使用するステートフル レプリカやステートレス インスタンスを移動する必要がある場合があります。

ただし、不均衡ではないサービスが移動されることもよくあります (前述のローカルとグローバルの重み付けの説明を参照してください)。 サービスすべてのメトリックのバランスがとれている場合に、サービスが移動されるのはなぜでしょうか。 例を見てみましょう。

- Service1、Service2、Service3、Service4 という 4 つのサービスがあるとします。 
- Service1 は、メトリック Metric1 と Metric2 を報告します。 
- Service2 は、メトリック Metric2 と Metric3 を報告します。 
- Service3 は、メトリック Metric3 と Metric4 を報告します。
- Service4 は、メトリック Metric99 を報告します。 

これで、おわかりいただけたでしょうか。チェーンがあります。 実際には 4 つの独立したサービスがあるのではなく、関連している 3 つのサービスと、独立したサービスが 1 つあります。

<center>

![同時にサービスの均衡を取る][Image4]
</center>

このチェーンがあるため、Metric1-4 が不均衡な場合、Service1-3 に属するレプリカやインスタンスが移動する可能性があります。 また、Metric1、2、または 3 の不均衡によって Service4 で移動が発生しないこともわかります。 Service4 に属するレプリカやインスタンスを移動しても、Metric1-3 の均衡にはまったく影響しないため、移動しても意味がありません。

クラスター リソース マネージャーは、関連するサービスを自動的に判別します。 サービスのメトリックを追加、削除、または変更すると、それらの関係に影響する可能性があります。 たとえば、分散処理を 2 回実行する間に Service2 が更新されて Metric2 が削除される可能性があります。 これによって、Service1 と Service2 間のチェーンが崩れます。 2 グループの関連するサービスでなく、3 グループになります。

<center>

![同時にサービスの均衡を取る][Image5]
</center>

## <a name="next-steps"></a>次の手順
* メトリックは、Service Fabric クラスター リソース マネージャーが管理するクラスターの利用量と容量を表します。 メトリックの詳細とその構成方法については、[この記事](service-fabric-cluster-resource-manager-metrics.md)を参照してください。
* 移動コストは、特定のサービスが他のサービスよりも高額になっていることをクラスター リソース マネージャーに警告する信号の 1 つです。 移動コストについて詳しくは、[この記事](service-fabric-cluster-resource-manager-movement-cost.md)をご覧ください。
* クラスター リソース マネージャーにはスロットルがいくつかあります。クラスターのチャーン (激しい動き) を落ち着かせるようにスロットルを構成できます。 通常は必要ありませんが、必要であれば、[ここ](service-fabric-cluster-resource-manager-advanced-throttling.md)で詳細を確認できます。

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
