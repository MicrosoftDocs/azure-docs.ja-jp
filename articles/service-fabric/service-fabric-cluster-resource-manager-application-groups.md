---
title: "Service Fabric クラスター リソース マネージャー - アプリケーション グループ | Microsoft Docs"
description: "Service Fabric クラスター リソース マネージャーのアプリケーション グループ機能の概要"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 4cae2370-77b3-49ce-bf40-030400c4260d
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: eef9cbca0479801cbac53ae1ae5713d4dac6a4bf
ms.openlocfilehash: 57525cd41d518b12f3f465d5dea7247659e3c8b6
ms.lasthandoff: 01/07/2017


---
# <a name="introduction-to-application-groups"></a>アプリケーション グループの概要
通常、Service Fabric のクラスター リソース マネージャーは、クラスター全体で ([メトリック](service-fabric-cluster-resource-manager-metrics.md)で表される) 負荷を均等に分散することによって、クラスター リソースを管理します。 Service Fabric では、クラスター内のノードの容量、および[容量](service-fabric-cluster-resource-manager-cluster-description.md)の全体的な概念としてクラスターも管理されます。 メトリックと容量は、非常に多くの種類のワークロードに適していますが、複数の Service Fabric アプリケーションのインスタンスを頻繁に使用するパターンの場合、追加の要件が生じることがあります。 一般的な追加要件の例として、以下が挙げられます。

* クラスター上でアプリケーション インスタンスのサービスの容量を予約する機能
* アプリケーション内のサービスの実行がされるノードの合計数を制限する機能
* アプリケーション インスタンス内のサービスの数または全体的なリソースの消費量を制限するための、アプリケーション インスタンス自体の容量の定義

これらの要件を満たすために、Service Fabric のクラスター リソース マネージャーはアプリケーション グループをサポートします。

## <a name="managing-application-capacity"></a>アプリケーション容量の管理
アプリケーション容量は、アプリケーションによって展開されるノードの数を制限するために使用できます。 個々のノードのアプリケーションのサービスがトータルでかけるメトリックの負荷を制限することもできます。 これを使用して、アプリケーションのクラスター内でリソースを予約することも使用できます。

アプリケーション容量は、新しいアプリケーションの作成時に、これらのアプリケーションに対して設定できます。既存のアプリケーションに対しても新しく設定することができます。

### <a name="limiting-the-maximum-number-of-nodes"></a>最大ノード数の制限
アプリケーション容量の最も簡単な使用例は、アプリケーションのインスタンス化を、特定の最大ノード数に制限する必要がある場合です。 アプリケーション容量が指定されていない場合、Service Fabric のクラスター リソース マネージャーは、通常の規則 (分散または最適化) に従ってサービスを作成し配置します。

次の図に、ノードの最大数が定義されているアプリケーション インスタンスと定義されていないアプリケーション インスタンスを示します。 どのサービスのどのレプリカまたはインスタンスが一緒に配置されるか、またはどのノードが使用されるかは保証されません。

<center>
![最大ノード数を定義したアプリケーション インスタンス][Image1]
</center>

左の例では、アプリケーションにはアプリケーション容量が設定されておらず、3 つのサービスが含まれます。 クラスター リソース マネージャーでは、クラスター内の最適なバランスを実現するために、使用可能な&6; つのノードにすべてのレプリカを分散してきました。 右側の例では、同じアプリケーションが&3; つのノードに制限されています。

この動作を制御するパラメーターは、MaximumNodes と呼ばれます。 このパラメーターは、アプリケーションの作成時に設定でき、既に実行中のアプリケーション インスタンス用に更新することもできます。

Powershell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –Name fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;

adUpdate.Metrics.Add(appMetric);
```

## <a name="application-metrics-load-and-capacity"></a>アプリケーションのメトリック、負荷、および容量
アプリケーション グループを使用して、特定のアプリケーション インスタンスに関連付けられたメトリックと、これらのメトリックのアプリケーション容量を定義することもできます。 これにより、アプリケーション インスタンス内のサービスのリソース使用量の追跡、予約、および制限ができます。

各アプリケーションのメトリックに設定可能な値は&2; つです。

* **アプリケーションの合計容量** - 特定のメトリックに対するアプリケーションの合計容量を表します。 クラスター リソース マネージャーでは、合計負荷がこの値を超える原因となる新しいサービスは、アプリケーション インスタンス内で作成できません。 たとえば、アプリケーション インスタンスの容量が 10 で、既に 5 つ分の負荷がかかっているとします。 この場合、既定の合計負荷が 10 のサービスは作成できません。
* **最大ノード容量** - 単一ノード上のアプリケーション内のサービスのレプリカに対する最大負荷合計を指定します。 ノードの負荷合計がこの容量を超過すると、クラスター リソース マネージャーは、レプリカを他のノードに移動して容量の制約を順守しようとします。

## <a name="reserving-capacity"></a>容量の予約
アプリケーション グループの別の一般的な用途では、特定のアプリケーション インスタンスでクラスター内のリソースが確保されていることを確認します。 これは、アプリケーション インスタンスの中にまだサービスがない場合や、リソースをまだ消費していない場合でも行われます。 この確保を行う方法を以下で説明します。

### <a name="specifying-a-minimum-number-of-nodes-and-resource-reservation"></a>最小ノード数とリソースの予約の指定
アプリケーションのインスタンス用にリソースを予約するには、追加パラメーター *MinimumNodes* と *NodeReservationCapacity* を指定する必要があります。

* **MinimumNodes** - アプリケーション内のサービスを実行できる最大ノード数を指定するのと同様に、アプリケーションの最小数も指定できます。 この設定により、リソースが予約される必要のあるノード数が定義され、アプリケーション インスタンス作成の一部として、クラスター内の容量が確保されます。
* **NodeReservationCapacity** - アプリケーション内の各メトリックに対して NodeReservationCapacity を定義することができます。 この設定により、アプリケーション内のサービスのレプリカやインスタンスが配置されているノード上で、そのアプリケーション用に予約されているメトリック負荷の量が定義されます。

容量の予約の例を以下に示します。

<center>
![予約容量を定義したアプリケーション インスタンス][Image2]
</center>

左の例では、アプリケーションにアプリケーション容量が定義されていません。 クラスター リソース マネージャーは、通常の規則に従ってすべてを分散します。

右側の例では、アプリケーションは次の設定で作成されているとします。

* MinimumNodes を&2; に設定
* MaximumNodes を&3; に設定
* アプリケーションのメトリック定義
  * NodeReservationCapacity を 20
  * MaximumNodeCapacity を 50
  * TotalApplicationCapacity を 100

Service Fabric では、青色のアプリケーションについて&2; つのノードの容量を予約していて、クラスターの他のアプリケーション インスタンスのサービスには、その容量の使用を許可しません。 この予約されたアプリケーション容量は、そのノードとクラスター内の残りの容量に対して消費されたものとみなされてカウントされます。

アプリケーションを予約ありで作成すると、クラスター リソース マネージャーは MinimumNodes * NodeReservationCapacity に相当する容量 (各メトリックに対して) を予約します。 ただし、特定のノードに&1; つ以上のレプリカが配置される場合に限り、そのノードの容量が予約されます。 後者の予約では、リソースなときにノードで予約されるだけなので、柔軟性とリソース使用率の向上が可能になります。

## <a name="obtaining-the-application-load-information"></a>アプリケーションの負荷情報の取得
アプリケーション容量が定義されている各アプリケーションについて、サービスのレプリカ別に報告される負荷の集計に関する情報を取得できます。

たとえば、負荷は次の PowerShell コマンドレットを使用して取得できます。

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1

```

ApplicationLoad クエリでは、アプリケーションで指定されたアプリケーション容量に関する基本的な情報を返します。 この情報には、最小ノード数と最大ノード数の情報と、アプリケーションが現在使用している数が含まれます。 各アプリケーションの負荷メトリックに関する情報も含まれています。以下の内容が含まれます。

* Metric Name: メトリックの名前。
* Reservation Capacity: このアプリケーションで予約されているクラスター容量。
* Application Load: このアプリケーションの子レプリカの負荷合計。
* Application Capacity: アプリケーションの負荷の最大許容値。

## <a name="removing-application-capacity"></a>アプリケーション容量の削除
アプリケーション容量の各パラメーターは、アプリケーションに対して設定した後で、アプリケーション更新 API または PowerShell コマンドレットを使用して削除できます。 次に例を示します。

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

このコマンドは、すべてのアプリケーション容量の各パラメーターをアプリケーションから削除します。 コマンドの効果は、即座に反映されます。 このコマンドが完了すると、クラスター リソース マネージャーは、既定のアプリケーション管理動作に戻ります。 アプリケーション容量の各パラメーターは、Update-ServiceFabricApplication を使用してもう一度指定できます。

### <a name="restrictions-on-application-capacity"></a>アプリケーション容量に関する制限事項
アプリケーション容量パラメーターには、順守する必要があるいくつかの制限があります。 検証エラーが発生した場合、アプリケーションの作成または更新は拒否され、エラーが表示されます。なにも変更されません。

* すべての整数パラメーターは、マイナスではない数字にする必要があります。
* MinimumNodes を MaximumNodes より大きくすることはできません。
* 負荷メトリックの容量を定義する場合、これらの容量は以下の規則に従う必要があります。
  * ノードの予約容量を最大ノード容量よりも大きくすることはできません。 たとえば、ノードのメトリック "CPU" の容量を&2; ユニットに制限してから、各ノードで&3; ユニットを予約することはできません。
  * MaximumNodes を指定する場合は、MaximumNodes と最大ノード容量の積はアプリケーションの合計容量を超えないようにする必要があります。 たとえば、負荷メトリック "CPU" の最大ノード容量が&8; に設定されているとします。 最大のノード数は&10; に設定しているともしましょう。 ここで、この負荷メトリックに対するアプリケーションの総容量は 80 より大きくなければなりません。

この制限は、アプリケーションの作成時 (クライアント側) とアプリケーションの更新中 (サーバー側) の両方で適用されます。

## <a name="how-not-to-use-application-capacity"></a>アプリケーション容量を使用すべきでない場合
* アプリケーションを_特定_のノードのサブセットに制限するためにアプリケーション グループの機能を使用しないでください。 言い換えれば、アプリケーションを最大で&5; つのノードで実行するように指定できますが、クラスター内の&5; つの特定のノードを指定することはできません。 サービスの配置の制約を使用すれば、特定のノードにアプリケーションを制限できます。
* アプリケーション容量は、同じアプリケーションの&2; つのサービスが互いに並んで配置されるようにするために使用しないでください。 複数のサービスが同じノードで実行されるようにするには、アフィニティを使用するか、または特定の要件に応じて配置の制約を使用します。

## <a name="next-steps"></a>次のステップ
* サービスの構成に利用できるその他のオプションの詳細については、他に利用可能なクラスター リソース マネージャーの構成に関するトピック ([サービスの構成の詳細](service-fabric-cluster-resource-manager-configure-services.md)) を確認してください。
* クラスター リソース マネージャーでクラスターの負荷を管理し、分散するしくみについては、 [負荷分散](service-fabric-cluster-resource-manager-balancing.md)
* 最初から開始して、 [Service Fabric クラスター リソース マネージャーの概要を確認するにはこちらを参照してください](service-fabric-cluster-resource-manager-introduction.md)
* メトリックの一般的な動作について詳しくは、 [Service Fabric の負荷メトリック](service-fabric-cluster-resource-manager-metrics.md)
* クラスター リソース マネージャーには、クラスターを記述するためのさまざまなオプションがあります。 オプションの詳細については、[Service Fabric クラスターの記述](service-fabric-cluster-resource-manager-cluster-description.md)に関するこの記事を参照してください。

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png

