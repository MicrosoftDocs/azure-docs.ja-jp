---
title: Service Fabric Cluster Resource Manager - アプリケーション グループ
description: Service Fabric クラスター リソース マネージャーのアプリケーション グループ機能の概要
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 988c7ce52125800c16aa785d5b1458604a927ecd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75452150"
---
# <a name="introduction-to-application-groups"></a>アプリケーション グループの概要
通常、Service Fabric のクラスター リソース マネージャーは、クラスター全体で ([メトリック](service-fabric-cluster-resource-manager-metrics.md)で表される) 負荷を均等に分散することによって、クラスター リソースを管理します。 Service Fabric では、クラスター内のノードの容量が管理され、[容量](service-fabric-cluster-resource-manager-cluster-description.md)によってクラスター全体が管理されます。 メトリックと容量は、非常に多くの種類のワークロードに適していますが、複数の Service Fabric アプリケーションのインスタンスを頻繁に使用するパターンの場合、追加の要件が生じることがあります。 たとえば、次のような場合があります。

- 一部の名前付きアプリケーション インスタンス内のサービスについて、クラスター内のノードに一部の容量を予約する
- (クラスター全体に分散するのではなく) 名前付きアプリケーション インスタンス内のサービスが実行されるノードの合計数を制限する
- 名前付きアプリケーション インスタンス自体の容量を定義して、サービス数またはインスタンス内のサービスのリソース消費量合計を制限する

これらの要件を満たすために、Service Fabric のクラスター リソース マネージャーはアプリケーション グループという機能をサポートしています。

## <a name="limiting-the-maximum-number-of-nodes"></a>最大ノード数の制限
アプリケーション容量の最も簡単な使用例は、アプリケーションのインスタンスを、特定の最大ノード数に制限する必要がある場合です。 この場合、そのアプリケーション インスタンス内のすべてのサービスが、設定した数のコンピューターに統合されます。 統合は、その名前付きアプリケーション インスタンス内でサービスに使用される物理リソースを予測する場合や、上限を設定する場合に便利です。 

次の図に、ノードの最大数が定義されているアプリケーション インスタンスと定義されていないアプリケーション インスタンスを示します。

<center>

![最大ノード数を定義したアプリケーション インスタンス][Image1]
</center>

左の例では、アプリケーションの最大ノード数は定義されておらず、3 つのサービスがあります。 クラスター リソース マネージャーでは、クラスター内の最適なバランスを実現するために、使用可能な 6 つのノードにすべてのレプリカを分散してきました (既定の動作)。 右側の例では、同じアプリケーションが 3 つのノードに制限されています。

この動作を制御するパラメーターは、MaximumNodes と呼ばれます。 このパラメーターは、アプリケーションの作成時に設定でき、既に実行中のアプリケーション インスタンス用に更新することもできます。

Powershell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –ApplicationName fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
await fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
await fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

```

ノード セット内で、クラスター リソース マネージャーは、まとめと配置されるサービス オブジェクトや、使用されるノードを保証していません。

## <a name="application-metrics-load-and-capacity"></a>アプリケーションのメトリック、負荷、および容量
アプリケーション グループを使用して、特定の名前付きアプリケーション インスタンスに関連付けられたメトリックと、これらのメトリックのアプリケーション インスタンスの容量を定義することもできます。 アプリケーション メトリックスにより、アプリケーション インスタンス内のサービスのリソース使用量の追跡、予約、および制限ができます。

各アプリケーションのメトリックに設定可能な値は 2 つです。

- **アプリケーションの合計容量** - 特定のメトリックに対するアプリケーションの合計容量を表します。 クラスター リソース マネージャーでは、合計負荷がこの値を超える原因となる新しいサービスは、アプリケーション インスタンス内で作成できません。 たとえば、アプリケーション インスタンスの容量が 10 で、既に 5 つ分の負荷がかかっているとします。 既定の合計負荷が 10 のサービスは作成できません。
- **最大ノード容量** - 単一ノード上のアプリケーションの最大負荷合計を指定します。 負荷がこの容量を超えると、クラスター リソース マネージャーはレプリカを他のノードに移動して、負荷を減らします。


Powershell:

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -Metrics @("MetricName:Metric1,MaximumNodeCapacity:100,MaximumApplicationCapacity:1000")
```

C#:

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;
appMetric.MaximumNodeCapacity = 100;
ad.Metrics.Add(appMetric);
await fc.ApplicationManager.CreateApplicationAsync(ad);
```

## <a name="reserving-capacity"></a>容量の予約
アプリケーション グループの別の一般的な用途では、特定のアプリケーション インスタンスでクラスター内のリソースが確保されていることを確認します。 アプリケーション インスタンスが作成されるときに、この容量が常に予約されます。

クラスター内にアプリケーションの容量を予約する処理は、次の場合でも即時に実行されます。
- アプリケーション インスタンスが作成されたが、そのインスタンス内にまだサービスがない
- アプリケーション インスタンス内のサービス数が常に変動する 
- サービスは存在するが、リソースを消費していない 

アプリケーションのインスタンス用にリソースを予約するには、2 つの追加パラメーターを指定する必要があります (*MinimumNodes* と *NodeReservationCapacity*)。

- **MinimumNodes** - アプリケーション インスタンスを実行する最小ノード数を定義します。  
- **NodeReservationCapacity** - この設定は、アプリケーションのメトリックに従います。 この値は、そのアプリケーションのサービスが実行されている任意のノードで、アプリケーション用に予約されているメトリックの合計です。

**MinimumNodes** と **NodeReservationCapacity** を組み合わせることで、クラスター内のアプリケーションについて最小負荷の予約が保証されます。 必要な合計予約よりもクラスター内の残容量が少ない場合、アプリケーションの作成は失敗します。 

容量の予約の例を以下に示します。

<center>

![予約容量を定義したアプリケーション インスタンス][Image2]
</center>

左の例では、アプリケーションにアプリケーション容量が定義されていません。 クラスター リソース マネージャーは、通常の規則に従ってすべてを分散します。

右側の例では、Application1 は次の設定で作成されているとします。

- MinimumNodes を 2 に設定
- アプリケーションのメトリック定義
  - NodeReservationCapacity を 20

Powershell

 ``` posh
 New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MinimumNodes 2 -Metrics @("MetricName:Metric1,NodeReservationCapacity:20")
 ```

C#

 ``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MinimumNodes = 2;

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.NodeReservationCapacity = 20;

ad.Metrics.Add(appMetric);

await fc.ApplicationManager.CreateApplicationAsync(ad);
```

Service Fabric は、Application1 用に 2 つのノード上の容量を予約し、その時点で Application1 内のサービスが消費している負荷がない場合でも、Application2 のサービスがその容量を消費することを禁止します。 この予約されたアプリケーション容量は、そのノードとクラスター内の残りの容量に対して消費されたものとみなされてカウントされます。  予約の場合、残りのクラスター容量から即時に差し引かれます。一方、予約済みの消費の場合、少なくとも 1 つのサービス オブジェクトが配置されているときにのみ、特定ノードの容量から差し引かれます。 後者の予約では、リソースなときにノードで予約されるだけなので、柔軟性とリソース使用率の向上が可能になります。

## <a name="obtaining-the-application-load-information"></a>アプリケーションの負荷情報の取得
1 つまたは複数のメトリックついてアプリケーション容量が定義されている各アプリケーションで、そのサービスのレプリカから報告される集計負荷に関する情報を取得できます。

Powershell:

``` posh
Get-ServiceFabricApplicationLoadInformation –ApplicationName fabric:/MyApplication1
```

C#

``` csharp
var v = await fc.QueryManager.GetApplicationLoadInformationAsync("fabric:/MyApplication1");
var metrics = v.ApplicationLoadMetricInformation;
foreach (ApplicationLoadMetricInformation metric in metrics)
{
    Console.WriteLine(metric.ApplicationCapacity);  //total capacity for this metric in this application instance
    Console.WriteLine(metric.ReservationCapacity);  //reserved capacity for this metric in this application instance
    Console.WriteLine(metric.ApplicationLoad);  //current load for this metric in this application instance
}
```

ApplicationLoad クエリでは、アプリケーションで指定されたアプリケーション容量に関する基本的な情報を返します。 この情報には、最小ノード数と最大ノード数の情報と、アプリケーションが現在使用している数が含まれます。 各アプリケーションの負荷メトリックに関する情報も含まれています。以下の内容が含まれます。

* Metric Name:メトリックの名前。
* Reservation Capacity: このアプリケーションで予約されているクラスター容量。
* Application Load: このアプリケーションの子レプリカの負荷合計。
* Application Capacity: アプリケーションの負荷の最大許容値。

## <a name="removing-application-capacity"></a>アプリケーション容量の削除
アプリケーション容量の各パラメーターは、アプリケーションに対して設定した後で、アプリケーション更新 API または PowerShell コマンドレットを使用して削除できます。 次に例を示します。

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

このコマンドは、アプリケーション インスタンスからアプリケーション容量の管理パラメーターを削除します。 たとえば、MinimumNodes、MaximumNodes、アプリケーションのメトリックなどがあります。 コマンドの効果は、即座に反映されます。 このコマンドが完了すると、クラスター リソース マネージャーは既定のアプリケーション管理動作を使用します。 アプリケーション容量の各パラメーターは、`Update-ServiceFabricApplication`/`System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()` を使用してもう一度指定できます。

### <a name="restrictions-on-application-capacity"></a>アプリケーション容量に関する制限事項
アプリケーション容量パラメーターには、順守する必要があるいくつかの制限があります。 検証エラーが発生する場合、変更は実行されません。

- すべての整数パラメーターは、マイナスではない数字にする必要があります。
- MinimumNodes を MaximumNodes より大きくすることはできません。
- 負荷メトリックの容量を定義する場合、これらの容量は以下の規則に従う必要があります。
  - ノードの予約容量を最大ノード容量よりも大きくすることはできません。 たとえば、ノードのメトリック "CPU" の容量を 2 ユニットに制限してから、各ノードで 3 ユニットを予約することはできません。
  - MaximumNodes を指定する場合は、MaximumNodes と最大ノード容量の積はアプリケーションの合計容量を超えないようにする必要があります。 たとえば、負荷メトリック "CPU" の最大ノード容量が 8 に設定されているとします。 また、最大のノード数を 10 に設定しているとします。 ここで、この負荷メトリックに対するアプリケーションの総容量は 80 より大きくなければなりません。

この制限は、アプリケーションの作成中と更新中の両方に適用されます。

## <a name="how-not-to-use-application-capacity"></a>アプリケーション容量を使用すべきでない場合
- アプリケーションを _特定_ のノードのサブセットに制限するためにアプリケーション グループの機能を使用しないでください。 言い換えれば、アプリケーションを最大で 5 つのノードで実行するように指定できますが、クラスター内の 5 つの特定のノードを指定することはできません。 サービスの配置の制約を使用すれば、特定のノードにアプリケーションを制限できます。
- 同じアプリケーションの 2 つのサービスを同じノードに配置する目的では、アプリケーション容量を使用しないでください。 この場合は、[アフィニティ](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)または[配置](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)の制約を使用してください。

## <a name="next-steps"></a>次のステップ
- サービスの構成の詳細については、[サービスの構成についての学習](service-fabric-cluster-resource-manager-configure-services.md)に関する記事を参照してください。
- クラスター リソース マネージャーでクラスターの負荷を管理し、分散するしくみについては、 [負荷分散](service-fabric-cluster-resource-manager-balancing.md)
- 最初から開始して、 [Service Fabric クラスター リソース マネージャーの概要を確認するにはこちらを参照してください](service-fabric-cluster-resource-manager-introduction.md)
- メトリックの一般的な動作について詳しくは、 [Service Fabric の負荷メトリック](service-fabric-cluster-resource-manager-metrics.md)
- Cluster Resource Manager には、クラスターを記述するためのさまざまなオプションがあります。 オプションの詳細については、[Service Fabric クラスターの記述](service-fabric-cluster-resource-manager-cluster-description.md)に関するこの記事を参照してください。

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png
