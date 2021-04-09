---
title: サブクラスター化されたメトリックの均衡化
description: 均衡化における配置の制約の影響と、その処理方法
author: nipavlo
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: nipavlo
ms.openlocfilehash: 7f571a851e4da147240c524b742bcd652bc54181
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "82183120"
---
# <a name="balancing-of-subclustered-metrics"></a>サブクラスター化されたメトリックの均衡化

## <a name="what-is-subclustering"></a>サブクラスター化とは

サブクラスター化は、配置の制約が異なるサービスに共通のメトリックがあり、両方のサービスで負荷がレポートされる場合に発生します。 サービスによってレポートされた負荷が大幅に異なる場合、ノードの総負荷は大きな標準偏差を持ち、最適なバランスが取られている場合でも、クラスターが不均衡であるように見えます。

## <a name="how-subclustering-affects-load-balancing"></a>サブクラスター化が負荷分散に与える影響

異なるノード上のサービスによってレポートされた負荷が大幅に異なる場合、大きな不均衡が発生していない場所でも、それが発生しているように見える場合があります。 また、サブクラスター化に起因する偽の不均衡が実際の不均衡よりも大きい場合は、Resource Manager の負荷分散アルゴリズムを混乱させ、クラスターの最適なバランスが得られない可能性があります。

たとえば、4 つのサービスがあり、これらすべてが以下のようにメトリック Metric1 の負荷をレポートしているとします。

* サービス A - 配置の制約 "NodeType==Frontend" があり、10 の負荷をレポートします
* サービス B - 配置の制約 "NodeType==Frontend" があり、10 の負荷をレポートします
* サービス C - 配置の制約 "NodeType==Backend" があり、100 の負荷をレポートします
* サービス D - 配置の制約 "NodeType==Backend" があり、100 の負荷をレポートします
* また、4 つのノードがあります。 そのうちの 2 つは NodeType が "Frontend" に設定されていて、残りの 2 つは "Backend" になっています

また、配置は次のようになっています。

<center>

![サブクラスター化された配置の例][Image1]
</center>

ノード 3 と 4 の負荷が大きく、クラスターが不均衡に見えるかもしれませんが、このような状況での最適なバランスは、この配置により得られます。

Resource Manager はサブクラスターの状況を認識でき、ほとんどの場合で、与えられた状況における最適なバランスを生み出すことができます。

Resource Manager がサブクラスター化されたメトリックを最適に均衡化できないような例外的な状況においても、サブクラスター化は検出され、問題を修正するための正常性レポートが作成されます。

## <a name="types-of-subclustering-and-how-they-are-handled"></a>サブクラスター化の種類とその処理方法

サブクラスター化の状況は、3 つの異なるカテゴリに分類できます。 個別のサブクラスター化状況のカテゴリによって、Resource Manager による処理方法が決まります。

### <a name="first-category--flat-subclustering-with-disjoint-node-groups"></a>最初のカテゴリ - 不整合なノード グループを使用したフラット サブクラスター化

このカテゴリは、ノードを別々のグループに分割し、各サービスをこれらのグループのいずれかのノードにのみ配置できる、サブクラスター化の最も単純な形式を取ります。 各ノードは、1 つのグループにのみ属しています。 上記で説明した状況は、ほとんどのサブクラスターの状況と同様に、このカテゴリに属します。 

このカテゴリの状況では、Resource Manager により最適なバランスを取ることができ、それ以上の操作は必要ありません。

### <a name="second-category--subclustering-with-hierarchical-node-groups"></a>2 番目のカテゴリ - 階層ノード グループを使用したサブクラスター化

この状況は、あるサービスで許可されているノードのグループが、別のサービスで許可されているノードのグループのサブセットである場合に発生します。 この状況の最も一般的な例は、一部のサービスに配置の制約が定義されていて、別のサービスには配置の制約がなく、任意のノードに配置できる場合です。

例:

* サービス A: 配置の制約なし
* サービス B: 配置の制約 "NodeType==Frontend"
* サービス C: 配置の制約 "NodeType==Backend"

この構成では、さまざまなサービスのノード グループ間にサブセットとスーパーセットの関係が作成されます。

<center>

![サブセットとスーパーセットのサブクラスター][Image2]
</center>

このような状況では、最適なバランスが得られない可能性があります。

この状況は Resource Manager により認識され、サービス A を、Frontend ノードに配置できるサービス A1 と、Backend ノードに配置できるサービス A2 の 2 つのサービスに分割することを勧める正常性レポートが生成されます。 これにより、最適なバランスを取ることができる最初のカテゴリの状況に戻ります。

### <a name="third-category--subclustering-with-partial-overlap-between-node-sets"></a>3 番目のカテゴリ - ノード セット間で部分的に重複するサブクラスター化

この状況は、いくつかのサービスを配置できるノード セット間で部分的に重複する場合に発生します。

たとえば、NodeColor というノード プロパティがあり、次の 3 つのノードがあるとします。

* ノード 1:NodeColor=Red
* ノード 2:NodeColor=Blue
* ノード 2:NodeColor=Green

また、2 つのサービスがあります。

* サービス A: 配置の制約 "Color==Red || Color==Blue" あり
* サービス B: 配置の制約 "Color==Blue || Color==Green" あり

これにより、サービス A はノード 1 とノード 2 に配置でき、サービス B はノード 2 とノード 3 に配置できます。

このような状況では、最適なバランスが得られない可能性があります。

この状況は Resource Manager により認識され、いくつかのサービスを分割することを勧める正常性レポートが生成されます。

このような状況では、複数の分割を行うことができ、最適なサービスの分割方法を予測する手段がないため、Resource Manager はサービスの分割方法を提案できません。

## <a name="configuring-subclustering"></a>サブクラスター化の構成

サブクラスター化に関する Resource Manager の動作は、次の構成パラメーターを変更することによって変更できます。
* SubclusteringEnabled - パラメーターは、Resource Manager が負荷分散の実行時にサブクラスターを考慮するかどうかを指定します。 このパラメーターをオフにすると、Resource Manager はサブクラスター化を無視し、グローバル レベルで最適なバランスを達成しようとします。 このパラメーターの既定値は false です。
* SubclusteringReportingPolicy - 階層および部分的に重複するサブクラスター化の正常性レポートを Resource Manager が生成する方法を指定します。 値を "0" に設定すると、サブクラスター化に関する正常性レポートが無効になります。"1" は、最適でないサブクラスター化の状況に対して警告の正常性レポートが生成されることを意味し、値 "2" は "OK" の正常性レポートを生成します。 このパラメーターの既定値は "1" です。

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="SubclusteringEnabled" Value="true" />
            <Parameter Name="SubclusteringReportingPolicy" Value="1" />
        </Section>
```

スタンドアロン デプロイの ClusterConfig.json 経由または Azure でホストされたクラスターの Template.json 経由:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "SubclusteringEnabled",
          "value": "true"
      },
      {
          "name": "SubclusteringReportingPolicy",
          "value": "1"
      },
    ]
  }
]
```

## <a name="next-steps"></a>次のステップ
* クラスター リソース マネージャーでクラスターの負荷を管理し、分散するしくみについては、 [負荷分散](service-fabric-cluster-resource-manager-balancing.md)
* 特定のノードにのみ配置されるようにサービスを制限する方法については、「[ノードのプロパティと配置の制約](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)」を参照してください

[Image1]:./media/cluster-resource-manager-subclustering/subclustered-placement.png
[Image2]:./media/cluster-resource-manager-subclustering/subset-superset-nodes.png
