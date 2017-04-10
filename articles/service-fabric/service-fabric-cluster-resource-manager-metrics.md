---
title: "メトリックを使用した Azure のマイクロサービスの負荷の管理 | Microsoft Docs"
description: "Service Fabric 内でメトリックを構成および使用して、サービスのリソース使用量を管理する方法について説明します。"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 0d622ea6-a7c7-4bef-886b-06e6b85a97fb
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: bb27d279396aa7b670187560cebe2ed074576bad
ms.openlocfilehash: c0e5418574920495bf277f4127b97ff5b07b56ef


---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>Service Fabric のリソース使用量と負荷をメトリックで管理する
メトリックとは、サービスが動作するために必要であり、クラスター内のノードによって提供されるリソースを表す、Service Fabric の一般的な用語です。 一般的に、メトリックは、サービスのパフォーマンスを調整するために管理する必要があるすべての要素を指します。

メモリ、ディスク、CPU 使用率などはすべてメトリックの例です。 これは物理的なメトリック、つまり管理する必要があるノード上の物理リソースに対応するリソースです。 また、メトリックは論理メトリックの場合もあり、一般的にメトリックは論理的です。 "MyWorkQueueDepth"、"MessagesToProcess"、"TotalRecords" などは論理メトリックです。 論理メトリックはアプリケーションに定義されており、一部の物理リソース使用量に対応しますが、アプリケーションにそれを実際に計測する方法はありません。 これは一般的です。 ユーザーが使うほとんどのメトリックは論理メトリックです。 これは主に、今日の多くのサービスがマネージ コードで記述されているためです。 マネージ コードということは、ホスト プロセス内から単一のサービス オブジェクトによる物理リソースの消費量を計測およびレポートすることが困難であることを意味します。 独自のメトリックを計測およびレポートすることが複雑であることも、構成済みの既定のメトリックを提供している理由の&1; つです。

## <a name="default-metrics"></a>Default metrics
たとえば、今後利用するリソース、または重要になりそうなリソースがわからないまま、作業を開始する必要があるとします。 そこで、メトリックを指定せずに、とりあえず実装してからサービスを作成することにします。 それでも、何も問題ありません。 Service Fabric Cluster Resource Manager では簡単なメトリックが自動的に選択されています。 メトリックを指定しなかった場合に使用される既定のメトリックは、PrimaryCount、ReplicaCount、および Count です。 次の表に、各メトリックの負荷がどの程度各サービス オブジェクトに関連付けられているかについて示します。

| メトリック | ステートレス インスタンス負荷 | ステートフル セカンダリ負荷 | ステートフル プライマリ負荷 |
| --- | --- | --- | --- |
| PrimaryCount |0 |0 |1 |
| ReplicaCount |0 |1 |1 |
| Count |1 |1 |1 |

では、この既定のメトリックを利用すると、どのようなメリットがあるでしょうか。 基本的なワークロードについては、適切に処理を分散できるようになります。 次の例から、2 つのサービスを作成すると何が起こるかを見てみましょう。 1 つ目は、それぞれ&3; つのサイズのターゲット レプリカが設定された&3; つのパーティションがあるステートフル サービスです。 2 つ目は、インスタンス数が&3; つの&1; つのパーティションがあるステートレス サービスです。

<center>
![既定のメトリックを含むクラスターのレイアウト][Image1]
</center>

次のようになります。

* ステートフル サービスに対応するプライマリ レプリカが&1; つのノードにスタックされない
* 同じパーティションのレプリカが同じノードに置かれていない
* プライマリおよびセカンダリの数がクラスター内に分散されている
* サービス オブジェクトがすべて各ノードに均等に割り当てられている

いいでしょう。

では、実際のワークロードが多数実行されるようになった場合、選択したパーティション スキームにより、ワークロードがすべてのパーティションで完全かつ均等に使用されるようになるのでしょうか。 特定のサービスに対する負荷が長期間一定であるか、少なくとも今と同じである可能性はどれくらいあるのでしょうか。

現実的には既定のメトリックのみで実行することも可能ですが、通常それはクラスターの使用率が想定よりも低くなることを意味します。 これは、既定のメトリック レポートは適応性がなく、すべてが均等であると仮定しているためです。 最悪の場合、既定のメトリックのみを使用すると一部のノードに負荷の割り当てが集中し、パフォーマンスが低下することもあります。 この問題は、次に説明する、カスタム メトリックと動的な負荷レポートを使用することで改善できます。 膨大なワークロードでは、すべてのサービスが無期限に均等になる確率は低くなります。 クラスターのパフォーマンスを最大限に発揮し、パフォーマンスに関する問題を回避するには、カスタム メトリックを使用することをお勧めします。

## <a name="custom-metrics"></a>カスタム メトリック
メトリックは、サービスを作成するときに名前付きサービス インスタンスごとに構成されます。

すべてのメトリックにはその内容について説明するプロパティ (名前、既定の負荷、重み) があります。

* Metric Name: メトリックの名前。 メトリック名は、Resource Manager から見たクラスター内のメトリックの一意の識別子です。
* Default Load: 既定の負荷は、サービスがステートレスまたはステートフルであるかによって異なる方法で表されます。
  * ステートレス サービスの場合、各メトリックに DefaultLoad という名前のプロパティが&1; つだけあります。
  * ステートフル サービスの場合は、次のように定義します。
    * PrimaryDefaultLoad: このサービスがプライマリのときに使用する、このメトリックの既定値。
    * SecondaryDefaultLoad: このサービスがセカンダリのときに使用する、このメトリックの既定値。
* Weight: メトリックの重みは、このサービスの他のメトリックに対する、このメトリックの相対的な重要度を定義します。

カスタム メトリックを定義し、既定のメトリックも併用するには、既定のメトリックを明示的に再度追加する必要があります。 これは、既定のメトリックとカスタム メトリックの間の関連性を明確にする必要があるためです。 たとえば、プライマリの分散よりも Memory の使用量や WorkQueueDepth のほうがはるかに重要である場合もあります。

### <a name="defining-metrics-for-your-service---an-example"></a>サービスに対するメトリックの定義 - 例
"MemoryInMb" と呼ばれるメトリックをレポートするサービスを構成し、既定のメトリックも併用する必要があるとします。 また、測定を何度か行った結果、通常はそのサービスのプライマリ レプリカが "MemoryInMb" を 20 ユニット、セカンダリ レプリカが 5 ユニットを使用することを把握しているとします。 この特定のサービスのパフォーマンスを管理する上で最も重要なメトリックは Memory ですが、プライマリ レプリカの負荷の分散も考慮する必要があります。 プライマリの分散は、一部のノードの損失や障害ドメインがプライマリ レプリカの大多数に影響を及ぼさないために有効です。 これらの調整が必要なメトリック以外は、既定のメトリックを使用できます。

このメトリックの構成でサービスを作成するためのコードを次に示します。

コード:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
StatefulServiceLoadMetricDescription memoryMetric = new StatefulServiceLoadMetricDescription();
memoryMetric.Name = "MemoryInMb";
memoryMetric.PrimaryDefaultLoad = 20;
memoryMetric.SecondaryDefaultLoad = 5;
memoryMetric.Weight = ServiceLoadMetricWeight.High;

StatefulServiceLoadMetricDescription primaryCountMetric = new StatefulServiceLoadMetricDescription();
primaryCountMetric.Name = "PrimaryCount";
primaryCountMetric.PrimaryDefaultLoad = 1;
primaryCountMetric.SecondaryDefaultLoad = 0;
primaryCountMetric.Weight = ServiceLoadMetricWeight.Medium;

StatefulServiceLoadMetricDescription replicaCountMetric = new StatefulServiceLoadMetricDescription();
replicaCountMetric.Name = "ReplicaCount";
replicaCountMetric.PrimaryDefaultLoad = 1;
replicaCountMetric.SecondaryDefaultLoad = 1;
replicaCountMetric.Weight = ServiceLoadMetricWeight.Low;

StatefulServiceLoadMetricDescription totalCountMetric = new StatefulServiceLoadMetricDescription();
totalCountMetric.Name = "Count";
totalCountMetric.PrimaryDefaultLoad = 1;
totalCountMetric.SecondaryDefaultLoad = 1;
totalCountMetric.Weight = ServiceLoadMetricWeight.Low;

serviceDescription.Metrics.Add(memoryMetric);
serviceDescription.Metrics.Add(primaryCountMetric);
serviceDescription.Metrics.Add(replicaCountMetric);
serviceDescription.Metrics.Add(totalCountMetric);

await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

(既定のメトリックのみを使用する場合、メトリックを収集したり、サービスを作成する場合に特別なことをしたりすることを考慮する必要性はまったくありません。)

ここまで、導入部と例を紹介しました。次はこれらの各設定の詳細と動作について説明します。

## <a name="load"></a>Load
メトリックを定義することの本質は、負荷を表現することです。 「負荷」とは、一部のサービス インスタンスまたは特定のノード上のレプリカによって使用される特定のメトリックの量のことです。 想定負荷は、サービスを作成するとき、サービスの作成後に更新するとき、サービス オブジェクト別にレポートするとき、またはそのすべてのタイミングで構成できます。

## <a name="default-load"></a>既定の負荷
既定の負荷は、このサービスの各サービス オブジェクト (インスタンスまたはレプリカ) が既定で使用する負荷の量を示します。 単純なサービスの場合、この数値は更新されることがない静的な定義になるため、サービスの全期間にわたって使用されます。 既定の負荷は、ワークロード別に専用のリソースが一定量割り当てられる、単純な容量計画シナリオに適しています。

Cluster Resource Manager では、ステートフル サービスがプライマリとセカンダリの両方に対して異なる既定の負荷を指定することを許可する一方で、ステートレス サービスは&1; つの値のみ指定できます。 ステートフル サービスの場合、プライマリ レプリカとセカンダリ レプリカの各ロールで行われる処理の種類が異なるため、各レプリカの既定の負荷は一般的に異なります。 たとえば、プライマリは通常、読み取りと書き込みの両方 (および計算作業のほとんど) が行われる一方で、セカンダリでは行われません。 プライマリ レプリカの既定の負荷はセカンダリ レプリカよりも高いことが想定されますが、実際の数値は測定結果によって変更することをお勧めします。

## <a name="dynamic-load"></a>動的な負荷
一定期間サービスを実行し、 監視したところ、次のことに気付いたとします。

1. 指定のサービスの一部のパーティションやインスタンスが他よりも多くのリソースを使用している
2. 一部のサービスの負荷が時間の経過とともに変化する

このような負荷の変動を引き起こす要因は数多く存在します。 サービスやパーティションは特定の顧客に関連付けられる場合もあれば、1 日の経過とともに変化するワークロードに対応する場合もあります。 理由に関係なく、既定の負荷に使用できる単一の数字はありません。 既定の負荷に対して選択した値はときに正しくありません。 Cluster Resource Manager に対して正しくない既定の負荷を設定すると、サービスに対するリソースの割り当てが過剰または過少になります。 その結果、Cluster Resource Manager では均衡がとれていると認識されている場合でも、過剰または過少に使用されているノードが存在します。 既定の負荷は一定の情報を提供するため有用ですが、実際のワークロードに完全に対応することは稀です。 Cluster Resource Manager が各サービス オブジェクトの実行時にそれぞれの負荷を更新することを許可しているのはこれが理由です。 これを動的な負荷レポートと呼びます。

動的な負荷レポートを利用して、レプリカまたはインスタンスで有効期間内におけるメトリックの割り当てとレポートされる負荷を調整できます。 通常、コールド状態で何の動作もしていないサービス レプリカまたはインスタンスは、特定少数のメトリックを使用しているとレポートします。 負荷が高いレプリカやインスタンスは特定多数のメトリックを使用しているとレポートします。

レプリカまたはインスタンス別のレポートでは、サービスが必要なリソースを確保できるように、Cluster Resource Manager がクラスター内の個々のサービス オブジェクトを再編成します。 負荷の高いサービスは他の負荷の低いレプリカやインスタンスからリソースを効率的に「回収」します。

Reliable Service 内で負荷を動的にレポートするコードは次のようになります。

コード:

```csharp
this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
```

サービスのレプリカやインスタンスは、作成されたときに使用するように構成されたメトリックに対する負荷のみをレポートできます。 サービスがレポートできるメトリックの一覧は、サービスが作成されたときに指定されたセットと同じです。 また、サービスに関連付けられているメトリックの一覧は動的に更新できます。 サービスのレプリカまたはインスタンスで現在使用するように構成されていないメトリックの負荷に関するレポートは、Service Fabric で記録されますが無視されます。 同じ API 呼び出しでレポートされた有効なその他のメトリックがある場合、それらのレポートが受け入れられて使用されます。 これにより、実験を幅広く行うことができるため便利です。 コードは把握しているすべてのメトリックを測定およびレポート可能で、演算子はコードを変更することなくそのサービスのメトリックの構成を指定および更新できます。 たとえば、管理者や運用チームは、あるサービスのレポートのバグが多いときにメトリックを無効にしたり、動作に応じてメトリックの重みを再構成したり、他のメカニズムを使用してコードがデプロイされ検証された後でのみ新しいメトリックを有効にしたりできます。

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>既定の負荷値と動的負荷レポートの混在
既定の負荷では十分でなく、負荷を動的にレポートすることが推奨される場合、両方を使用できるのでしょうか。 はい。 実際、それが推奨される構成です。 既定の負荷が設定されており、動的な負荷レポートが使用されると、動的なレポートが現れるまで既定の負荷が推定値として使用されます。 クラスターの Resource Manager に処理の根拠を提供するため、これは便利です。 既定の負荷により、Cluster Resource Manager がサービス オブジェクトが作成されたときにそれを適切な場所に配置します。 既定の負荷の情報が提供されていない場合、サービスはランダムに配置されます。 負荷レポートが後で提供されると、ほとんどの場合 Cluster Resource Manager はサービスを移動する必要があります。

前の例を使用して、カスタム レポートと動的な負荷レポートを追加するとどうなるかを見てみましょう。 この例では、メトリックの例として "Memory" を使用します。 最初に次のコマンドを使用してステートフル サービスを作成していたとします。

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

繰り返しますが、この構文は ("MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad") です。

考えられるクラスター レイアウトの&1; つは、次のようなものになります。

<center>
![既定のメトリックとカスタム メトリックの両方の均衡がとれたクラスター][Image2]
</center>

注目すべき点がいくつかあります。

* レプリカまたはインスタンスがそれぞれの独自の負荷をレポートするまで、レプリカまたはインスタンスではサービスの既定の負荷が使用されるため、ステートフル サービスのパーティション 1 の内部のレプリカがその負荷を動的にレポートしていないことがわかります。
* パーティション内のセカンダリ レプリカは独自の負荷を持つことができます。
* 全体としてメトリックの均衡はとれているように見えます。 メモリについては、最大負荷と最小負荷の比率は 1.75 (負荷が最も高いのは N3、最も低いのは N2 で、28/16 = 1.75) です。

さらに説明が必要な点がいくつかあります。

* 1.75 という比率が妥当であるかどうかを、何を根拠に決定するのか。 Cluster Resource Manager はそれが十分であるか、もっと調整が必要であるかをどのように判断するのか。
* 負荷分散はいつ実行されるのか。
* Memory の重みが "High" であるとは、どのような意味があるのか。

## <a name="metric-weights"></a>Metric weights
同じメトリックを異なるサービスにわたって追跡できることは重要です。 Cluster Resource Manager はこの観点からクラスター内の使用量を追跡し、ノードが許容量を超えないようにノード間の使用量を調整します。 ただし、同じメトリックの重要性はサービスによって異なるため、サービスによって見方が異なる場合があります。 また、クラスターのメトリックやサービスの数が多い場合、すべてのメトリックについて完璧に均衡のとれたソリューションは存在しないことがあります。 Cluster Resource Manager はこのような状況をどのように処理するのでしょうか。

メトリックの重みは、Cluster Resource Manager でクラスターの均衡をとる方法を決定するときに、完璧な答えが存在しない場合の判断材料となります。 また、Cluster Resource Manager はメトリックの重みを使用して、サービスごとに均衡をとります。 メトリックには、Zero、Low、Medium、High という&4; 種類の重みレベルを設定できます。 重み Zero のメトリックは均衡がとれているかどうかを判断するときにはまったく考慮されませんが、容量などのときにはその負荷が考慮されます。

メトリックの重みがクラスター内で異なることの実際の影響は、Cluster Resource Manager が異なるソリューションを生成することです。 メトリックの重みは、あるメトリックが他よりも重要なことを Cluster Resource Manager に伝えます。 完璧な解決策がない場合、Cluster Resource Manager はより高い重みのメトリックの均衡をとります。 あるサービスで重要でないと思われているメトリックは、そのメトリックの使用が不均衡であることが原因であることがあります。 これにより、別のサービスにとって重要なメトリックが均等に分散されるようになります。

それでは、いくつかの負荷レポートの例を使用して、メトリックの重みが変わるとクラスター内の割り当てがどのように変化するか見てみましょう。 この例では、メトリックの相対的な重みを切り替えることで、Cluster Resource Manager で別のソリューションが優先され、サービスの配置が変わることがわかります。

<center>
![メトリックの重みの例と負荷分散ソリューションに与える影響][Image3]
</center>

この例では、2 つの異なるメトリック A と B に対してすべて異なる値をレポートする&4; つの異なるサービスがあります。最初のケースでは、すべてのサービスが MetricA が最も重要である (重み = High)、MetricB が最も重要でない (重み = Low) と定義されています。 このケースでは、Cluster Resource Manager が MetricA が MetricB よりも均衡がとれる (標準偏差が低くなる) ようにサービスを配置していることがわかります。 2 番目のケースでは、メトリックの重みが逆になっています。 その結果、Cluster Resource Manager は MetricA よりも MetricB の均衡を重視した配置にするために、サービス A とサービス B を入れ替える可能性が高いことがわかります。

### <a name="global-metric-weights"></a>メトリックの重み
では、ServiceA で MetricA を最も重要なメトリックとして定義する一方、ServiceB ではまったく考慮しなかった場合、実際に使用される重みはどうなるでしょうか。

実際にはすべてのメトリックに対して複数の重みが追跡されます。 最初のセットは、各サービスがメトリックに対して定義した重みです。 それ以外の重みはグローバルな重みであり、そのメトリックをレポートするすべてのサービスの重みの平均です。 Cluster Resource Manager はこれら両方の重みを使用してソリューションのスコアを計算します。 これは、均衡がサービスの独自の優先度に基づいてとられるだけでなく、負荷がクラスター全体で正しく割り当てられることも重要であるためです。

Cluster Resource Manager がグローバルとローカルのどちらかの均衡を考慮しなかった場合、どうなるでしょうか。 グローバルに均衡のとれたソリューションを構築することは簡単ですが、個々のサービスで偏ったリソース割り当てが生じることになります。 次の例で、ステートフル サービスを構成する既定のメトリックについて、グローバルな均衡を考慮した場合はどうなるかを見てみましょう。

<center>
![グローバルな均衡のみを考慮したソリューションの影響][Image4]
</center>

上の方の例では、グローバルに均衡をとることのみを重視しています。実際にクラスター全体に対しては均衡がとれており、 すべてのノードに同じ数のプライマリが含まれ、レプリカの合計数も同じです。 ただし、この割り当ての実際の影響を見てみると、それほど良いとは言えません。ノードが&1; つでも失われると、その中のプライマリもすべて失われるため、特定のワークロードに偏りが生じます。 たとえば、1 番目のノードで障害が発生すると、円のサービスの&3; つの異なるパーティションに対応する&3; つのプライマリが同時に失われます。 逆に、その他の&2; つのサービス (三角形と六角形) はそれぞれパーティションを持っており、レプリカが失われてもサービスの中断は生じません (ダウンしたレプリカを回復する必要がある場合を除く)。

下の方の例では、Cluster Resource Manager がグローバルのバランスとサービス単位のバランスの両方を考慮してレプリカを分散しています。 ソリューションのスコアを計算するとき、ほとんどの重みはグローバル ソリューションに充てられ、(構成可能な) 一部は個々のサービスに充てられます。 グローバルなバランスは、各サービス用に構成されたメトリックの平均重みに基づいて算出されます。 各サービスは、独自に定義されたメトリックの重みに従って均衡がとられます。 これにより、可能な限り各サービス独自のニーズに応じて均衡がとられます。 その結果、同じ&1; 番目のノードで障害が発生しても、失われるプライマリ (およびセカンダリ) の数はすべてのサービスのすべてのパーティションに分散され、 各サービスへの影響が同じになります。

## <a name="next-steps"></a>次のステップ
* サービスの構成に利用できるその他のオプションについて詳しくは、その他の Cluster Resource Manager の構成で使用可能な[サービスの構成の詳細に関するトピック](service-fabric-cluster-resource-manager-configure-services.md)をご覧ください。
* 最適化メトリックの定義は、負荷を分散するのではなく、ノードで統合する方法の&1; つです。 最適化を構成する方法については、 [この記事](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* クラスター リソース マネージャーでクラスターの負荷を管理し、分散するしくみについては、 [負荷分散](service-fabric-cluster-resource-manager-balancing.md)
* 最初から開始して、 [Service Fabric クラスター リソース マネージャーの概要を確認するにはこちらを参照してください](service-fabric-cluster-resource-manager-introduction.md)
* 移動コストは、特定のサービスが他のサービスよりも高額になっていることをクラスター リソース マネージャーに警告する信号の&1; つです。 移動コストの詳細については、 [この記事](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png



<!--HONumber=Jan17_HO4-->


