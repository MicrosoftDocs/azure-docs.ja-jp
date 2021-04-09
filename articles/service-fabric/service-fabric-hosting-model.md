---
title: Azure Service Fabric ホスティング モデル
description: デプロイされた Service Fabric サービスのレプリカ (またはインスタンス) と、サービス ホスト プロセスとの関係を説明します。
author: harahma
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: harahma
ms.custom: devx-track-csharp
ms.openlocfilehash: 9932c11332a616928d59c213d4f4806feb81cfe2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791647"
---
# <a name="azure-service-fabric-hosting-model"></a>Azure Service Fabric ホスティング モデル
この記事では、Azure Service Fabric によって提供されるアプリケーション ホスティング モデルの概要、および **共有プロセス** モデルと **排他的プロセス** モデルとの相違点について説明します。 デプロイされたアプリケーションが Service Fabric ノード上でどのように表示されるかについて、またこのサービスのレプリカ (またはインスタンス) とサービス ホスト プロセスとの間の関係について説明します。

先に進む前に、「[Service Fabric でのアプリケーションのモデル化][a1]」で説明されているさまざまな概念とリレーションシップをよく理解しておいてください。 

> [!NOTE]
> この記事では、別のことが明記されていない限り、以下のとおりとします。
>
> - "*レプリカ*" は、ステートフル サービスのレプリカおよびステートレス サービスのインスタンスの両方を意味します。
> - *CodePackage* は、*ServiceType* を登録する *ServiceHost* プロセスに相当するものとして扱われ、その *ServiceType* のサービスのレプリカをホストします。
>

ホスティング モデルを理解するための例を紹介します。 たとえば、"MyAppType" という *ApplicationType* があり、これは "MyServiceType" という *ServiceType* を持つものとします。 'MyServiceType' は、'MyServicePackage' という *ServicePackage* によって提供され、'MyServicePackage' は 'MyCodePackage' という *CodePackage* を持ちます。 'MyCodePackage' は、'MyServiceType' という *ServiceType* を実行時に登録します。

3 ノード クラスターがあるとして、"MyAppType" というタイプの **fabric:/App1** という *アプリケーション* を作成します。 この **fabric:/App1** というアプリケーションの内部に、"MyServiceType" タイプのサービス **fabric:/App1/ServiceA** を作成します。 このサービスには、2 つのパーティション (たとえば、**P1** と **P2**) と、パーティションごとに 3 つのレプリカがあります。 次の図は、最終的にノードにデプロイされるこのアプリケーションのビューを示しています。


![最終的にノードにデプロイされるこのアプリケーションのビューを示す図。][node-view-one]


Service Fabric によって "MyServicePackage" がアクティブ化されます。これにより、"MyCodePackage" が起動され、さらに両方のパーティションのレプリカがホスティングされています。 クラスター内のノードと同じ数のレプリカをパーティションごとに選択したので、クラスターのノードはすべて同じビューを持ちます。 それでは、アプリケーション **fabric:/App1** の中に、別のサービス **fabric:/App1/ServiceB** を作成してみましょう。 このサービスには、1 つのパーティション (たとえば **P3**) と、パーティションごとに 3 つのレプリカがあります。 次の図は、このノードの新しいビューを示しています。


![ノードの新しいビューを示す図。][node-view-two]


Service Fabric によって、アクティブ化した既存の "MyServicePackage" 内に **fabric:/App1/ServiceB** というサービスの **P3** パーティションの新しいレプリカが配置されました。 次に、 "MyAppType" タイプの別のアプリケーション **fabric:/App2** を作成してみましょう。 **fabric:/App2** の内部に、サービス **fabric:/App2/ServiceA** を作成します。 このサービスには、2 つのパーティション (**P4** と **P5**) と、パーティションごとに 3 つのレプリカがあります。 次の図は、この新しいノードのビューを示しています。


![新しいノードのビューを示す図。][node-view-three]


Service Fabric は 'MyServicePackage' の新しいコピーをアクティブ化し、これによって 'MyCodePackage' の新しいコピーが起動されます。 サービス **fabric:/App2/ServiceA** の両方のパーティション (**P4** と **P5**) のレプリカが、この "MyCodePackage" という新しいコピーに配置されます。

## <a name="shared-process-model"></a>共有プロセス モデル
前のセクションでは、Service Fabric によって提供される、共有プロセス モデルと呼ばれる既定のホスティング モデルについて説明しました。 このモデルでは、特定のアプリケーションに対し、特定の *ServicePackage* のコピーが 1 つだけ、(含まれるすべての *CodePackages* を起動する) ノード上でアクティブ化されます。 特定の *ServiceType* のすべてのサービスのすべてのレプリカが、その *ServiceType* を登録する *CodePackage* に配置されます。 つまり、特定の *ServiceType* のノード上ですべてのサービスのすべてのレプリカが同じプロセスを共有します。

## <a name="exclusive-process-model"></a>専有プロセス モデル
Service Fabric が提供するもう 1 つのホスティング モデルが、専有プロセス モデルです。 このモデルでは、特定のノードで、各レプリカが専用のプロセスで稼働します。 Service Fabric は、*ServicePackage* の新しいコピーをアクティブ化します (これにより、それに含まれるすべての *CodePackages* が開始されます)。 レプリカは、レプリカが属しているサービスの *ServiceType* を登録した *CodePackage* に配置されます。 

Service Fabric バージョン 5.6 以降を使っている場合は、サービスを作成するときに専有プロセス モデルを選ぶことができます ([PowerShell][p1]、[REST][r1]、または [FabricClient][c1] を使います)。 "ExclusiveProcess" として **ServicePackageActivationMode** を指定します。

```powershell
PS C:\>New-ServiceFabricService -ApplicationName "fabric:/App1" -ServiceName "fabric:/App1/ServiceA" -ServiceTypeName "MyServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1 -ServicePackageActivationMode "ExclusiveProcess"
```

```csharp
var serviceDescription = new StatelessServiceDescription
{
    ApplicationName = new Uri("fabric:/App1"),
    ServiceName = new Uri("fabric:/App1/ServiceA"),
    ServiceTypeName = "MyServiceType",
    PartitionSchemeDescription = new SingletonPartitionSchemeDescription(),
    InstanceCount = -1,
    ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
};

var fabricClient = new FabricClient(clusterEndpoints);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

アプリケーション マニフェストで既定のサービスが指定されていれば、**ServicePackageActivationMode** 属性を指定することで専有プロセス モデルを選択できます。

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
次に、アプリケーション **fabric:/App1** の中に、別のサービス **fabric:/App1/ServiceC** を作成します。 このサービスには、2 つのパーティション (たとえば、**P6** と **P7**) と、パーティションごとに 3 つのレプリカがあります。 **ServicePackageActivationMode** を "ExclusiveProcess" に設定します。 次の図は、ノードの新しいビューを示しています。


![デプロイされたアプリケーションのノード ビューの図][node-view-four]


ご覧のとおり、Service Fabric は、"MyServicePackage" (パーティション **P6** および **P7** のレプリカごとに 1 つ) の 2 つのコピーを新しくアクティブ化しました。 Service Fabric は、*CodePackage* の専用のコピーに各レプリカを配置しました。 専有プロセス モデルを使うと、特定のアプリケーションについて、特定の *ServicePackage* の複数のコピーを 1 つのノードでアクティブ化できます。 上の例では、"MyServicePackage" の 3 つのコピーが **fabric:/App1** に対してアクティブ化されています。 "MyServicePackage" のアクティブなコピーのそれぞれに、**ServicePackageActivationId** が関連付けられています。 この ID は、そのコピーをアプリケーション **fabric:/App1** 内で識別します。

アプリケーションで共有プロセス モデルのみを使うと、ノードには *ServicePackage* のアクティブなコピーが 1 つだけ存在します。 *ServicePackage* のこのアクティブ化の **ServicePackageActivationId** は空の文字列です。 これは、たとえば **fabric:/App2** の場合です。

> [!NOTE]
>- 共有プロセス ホスティング モデルは、**ServicePackageActivationMode** が **SharedProcess** と等しい場合に対応します。 これは既定のホスティング モデルであり、サービス作成時に **ServicePackageActivationMode** を指定する必要はありません。
>
>- 専有プロセス ホスティング モデルは、**ServicePackageActivationMode** が **ExclusiveProcess** と等しい場合に対応します。 この設定を使うには、サービスを作成するときに明示的に指定する必要があります。 
>
>- サービスのホスティング モデルを知るには、[サービスの説明][p2]のクエリを行って、**ServicePackageActivationMode** の値を確認します。
>
>

## <a name="work-with-a-deployed-service-package"></a>デプロイされたサービス パッケージの操作
ノード上の *ServicePackage* のアクティブなコピーは、[デプロイされたサービス パッケージ][p3]と呼ばれます。 専有プロセス モデルを使ってサービスを作成した場合、特定のアプリケーションに、同じ *ServicePackage* の複数のサービス パッケージがデプロイされる可能性があります。 デプロイされているサービス パッケージに固有の操作を実行する場合は、**ServicePackageActivationId** を指定して、デプロイされている特定のサービス パッケージを示す必要があります。 たとえば、[デプロイされたサービス パッケージの正常性を報告する][p4]場合、または[デプロイされたサービス パッケージのコード パッケージを再起動する][p5]場合は、ID を指定します。

デプロイされたサービス パッケージの **ServicePackageActivationId** は、ノードで [デプロイされたサービス パッケージ][p3]のリストのクエリを実行することで確認できます。 ノード上にある、[デプロイされたサービスのタイプ][p6]、[デプロイされたレプリカ][p7]、[デプロイされたコード パッケージ][p8]のクエリを実行すると、クエリ結果には親のデプロイされたサービス パッケージの **ServicePackageActivationId** も含まれます。

> [!NOTE]
>- 共有プロセス ホスティング モデルでは、特定のノード上の特定のアプリケーションに対して、*ServicePackage* のコピーが 1 つだけアクティブ化されます。 このコピーの **ServicePackageActivationId** は "*空の文字列*" であるため、デプロイされたサービス パッケージに関連する操作を行うときに、この ID を指定する必要はありません。 
>
> - 専有プロセス ホスティング モデルでは、特定のノード上の特定のアプリケーションに対して、*ServicePackage* のコピーが 1 つ以上アクティブ化される可能性があります。 各アクティブ化には "*空ではない*" **ServicePackageActivationId** があり、デプロイされたサービス パッケージに関連する操作を行うときに指定されます。 
>
> - **ServicePackageActivationId** を指定しないと、既定で "*空の文字列*" になります。 デプロイされたサービス パッケージが共有プロセス モデルでアクティブ化されている場合は、そのパッケージに対して操作が行われます。 それ以外の場合は、操作が失敗します。
>
> - **ServicePackageActivationId** のクエリを 1 回行ってキャッシュに格納する方法は使わないでください。 ID は動的に生成され、さまざまな理由で変化することがあります。 **ServicePackageActivationId** を必要とする操作を行う前にまず、ノードに [デプロイされたサービス パッケージ][p3]のリストのクエリを行う必要があります。 その後、クエリ結果の **ServicePackageActivationId** を使って、本来の操作を実行します。
>
>

## <a name="guest-executable-and-container-applications"></a>ゲスト実行可能ファイルとコンテナー アプリケーション
Service Fabric は、[ゲスト実行可能ファイル][a2]と[コンテナー][a3] アプリケーションを、自己完結型のステートレス サービスとして扱います。 *ServiceHost* (プロセスまたはコンテナー) には Service Fabric ランタイムは存在しません。 これらのサービスは自己完結型であるため、*ServiceHost* ごとのレプリカの数はこれらのサービスに適用できません。 これらのサービスで使われる最も一般的な構成は、[InstanceCount][c2] が -1 である (サービス コードの 1 つのコピーがクラスターの各ノードで実行される) 単一パーティションです。 

これらのサービスの既定の **ServicePackageActivationMode** は **SharedProcess** であり、この場合、Service Fabric は特定のアプリケーションについて 1 つのノード上では *ServicePackage* のコピーを 1 つだけアクティブ化します。  つまり、ノードを実行するサービス コードのコピーは 1 つのみです。 1 つのノードでサービス コードの複数のコピーを実行したい場合は、サービスを作成するときに、**ServicePackageActivationMode** を **ExclusiveProcess** として指定します。 たとえば、これは、(*ServiceManifest* で指定されている) *ServiceType* の複数のサービス (*Service1* から *ServiceN*) を作成するとき、またはサービスが複数のパーティションに分割されるときに、行うことができます。 

## <a name="change-the-hosting-model-of-an-existing-service"></a>既存のサービスのホスティング モデルを変更する
現時点では、既存のサービスのホスティング モデルを共有プロセスから専有プロセスに (またはその逆に) 変更することはできません。

## <a name="choose-between-the-hosting-models"></a>ホスティング モデルを選択する
要件に最も合ったホスティング モデルを評価する必要があります。 共有プロセス モデルは、生成されるプロセスが少なく、同じプロセスの複数のレプリカがポートを共有できるため、オペレーティング システムのリソースをより効率的に使用できます。 ただし、レプリカのうちの 1 つでエラーが発生してサービス ホストを停止する必要が生じた場合は、同じプロセスの他のすべてのレプリカに影響が出ます。

 専有プロセス モデルは、すべてのレプリカに専用のプロセスがあるので、レプリカの分離が向上します。 レプリカの 1 つでエラーが発生しても、他のレプリカには影響ありません。 このモデルは、通信プロトコルでポート共有がサポートされていない場合に役に立ちます。 レプリカ レベルでリソース ガバナンスを適用することが容易になります。 ただし、専有プロセスは、ノード上の各レプリカに 1 つずつプロセスが生成されるため、オペレーティング システムのリソースをより多く消費します。

## <a name="exclusive-process-model-and-application-model-considerations"></a>専有プロセス モデルとアプリケーション モデルに関する考慮事項
ほとんどのアプリケーションでは、*ServicePackage* ごとに *ServiceType* を 1 つに保つことで、Service Fabric でアプリケーションをモデル化できます。 

特定のケースでは、*ServicePackage* ごとに複数の *ServiceType* を使うこともできます (また、1 つの *CodePackage* で複数の *ServiceType* を登録できます)。 これらの構成が役に立つ状況を次に示します。

- 発生するプロセスが少なく、プロセスごとのレプリカの密度を高めてリソースの使用率を最適化したい場合。
- 初期化またはメモリの消費が多い共通のデータを、*ServiceTypes* が異なるレプリカで共有する必要がある場合。
- 無料のサービスを提供するときなどに、サービスのすべてのレプリカを同じプロセスにしてリソースの使用を制限したい場合。

専有プロセス ホスティング モデルは、*ServicePackage* ごとに複数の *ServiceTypes* を持つアプリケーション モデルには適していません。 これは、*ServicePackage* ごとに複数の *ServiceTypes* を設定できる機能が、レプリカ間で共有するリソースの使用率を高めたり、プロセスごとのレプリカの密度を高めたりするために設計されているからです。 専有プロセス モデルは、異なる結果を実現することを目的として設計されています。

*ServicePackage* ごとに複数の *ServiceTypes* があり、異なる *CodePackage* が各 *ServiceType* を登録している場合について考えます。 たとえば、次のような 2 つの *CodePackage* を持つ 'MultiTypeServicePackage' という *ServicePackage* があるとします。

- 'MyServiceTypeA' という *ServiceType* を登録している 'MyCodePackageA'。
- 'MyServiceTypeB' という *ServiceType* を登録している 'MyCodePackageB'。

ここで、たとえばアプリケーション **fabric:/SpecialApp** を作成します。 **fabric:/SpecialApp** の内部に、専有プロセス モデルで次の 2 つのサービスを作成します。

- "MyServiceTypeA" タイプのサービス **fabric:/SpecialApp/ServiceA** と、2 つのパーティション (たとえば、**P1** と **P2**) およびパーティションごとに 3 つのレプリカ。
- "MyServiceTypeB" タイプのサービス **fabric:/SpecialApp/ServiceB** と、2 つのパーティション (**P3** と **P4**) およびパーティションごとに 3 つのレプリカ。

特定のノード上で、両方のサービスはそれぞれ 2 つのレプリカを持ちます。 サービスを専有プロセス モデルで作成したため、Service Fabric は各レプリカについて "MyServicePackge" の新しいコピーをアクティブ化します。 "MultiTypeServicePackage" の各アクティブ化によって、"MyCodePackageA" と "MyCodePackageB" のコピーが開始します。 ただし、"MultiTypeServicePackage" がアクティブ化されたレプリカをホストするのは、"MyCodePackageA" または "MyCodePackageB" のどちらか 1 つだけです。 次の図で、このノードのビューを示します。


![ノードのビューを示す図。][node-view-five]


サービス **fabric:/SpecialApp/ServiceA** のパーティション **P1** のレプリカに対する "MultiTypeServicePackage" のアクティブ化では、"MyCodePackageA" がレプリカをホストします。 "MyCodePackageB" は実行しています。 同様に、サービス **fabric:/SpecialApp/ServiceB** のパーティション **P3** のレプリカに対する "MultiTypeServicePackage" のアクティブ化では、"MyCodePackageB" がレプリカをホストします。 "MyCodePackageA" は実行しています。 そのため、*ServicePackage* ごとの (異なる *ServiceTypes* を登録している) *CodePackage* の数が多くなれば、それだけリソース使用の冗長性が高くなることになります。 
 
 一方、サービス **fabric:/SpecialApp/ServiceA** と **fabric:/SpecialApp/ServiceB** を共有プロセス モデルで作成した場合は、Service Fabric はアプリケーション **fabric:/SpecialApp** に対して "MultiTypeServicePackage" のコピーを 1 つだけアクティブ化します。 "MyCodePackageA" がサービス **fabric:/SpecialApp/ServiceA** のすべてのレプリカをホストします。 "MyCodePackageB" がサービス **fabric:/SpecialApp/ServiceB** のすべてのレプリカをホストします。 次の図は、この設定のノードのビューを示しています。 


![デプロイされたアプリケーションのノード ビューの図][node-view-six]


前の例を見ると、"MyCodePackageA" に "MyServiceTypeA" と "MyServiceTypeB" の両方が登録されていて、かつ "MyCodePackageB" が存在しなければ、冗長な *CodePackage* は 1 つも実行されていないように見えるかもしれません。 これは正しいことですが、このアプリケーション モデルは専有プロセス ホスティング モデルとは一致しません。 それぞれのレプリカに専用のプロセスを設定することが目的であれば、同じ *CodePackage* から両方の *ServiceTypes* を登録する必要はありません。 代わりに、単に各 *ServiceType* を専用の *ServicePackage* に格納します。

### <a name="reliable-services-and-actor-forking-subprocesses"></a>Reliable Services および Reliable Actor によるサブプロセスのフォーク

Service Fabric では、Reliable Services とそれに続く Reliable Actor によるサブプロセスのフォークはサポートされていません。 これがサポートされない理由は、サブプロセスを登録するために [CodePackageActivationContext](/dotnet/api/system.fabric.codepackageactivationcontext) を使用することができないこと、およびキャンセル トークンが登録済みのプロセスにのみ送信されることにあります。その結果、親プロセスがキャンセル トークンを受け取った後にサブプロセスが終了しない場合、アップグレードの失敗など、あらゆる種類の問題が発生します。

## <a name="next-steps"></a>次のステップ
[アプリケーションをパッケージ化][a4]して展開できるようにします。

[アプリケーションをデプロイおよび削除][a5]します。 この記事では、PowerShell を使ってアプリケーション インスタンスを管理する方法について説明しています

<!--Image references-->
[node-view-one]: ./media/service-fabric-hosting-model/node-view-one.png
[node-view-two]: ./media/service-fabric-hosting-model/node-view-two.png
[node-view-three]: ./media/service-fabric-hosting-model/node-view-three.png
[node-view-four]: ./media/service-fabric-hosting-model/node-view-four.png
[node-view-five]: ./media/service-fabric-hosting-model/node-view-five.png
[node-view-six]: ./media/service-fabric-hosting-model/node-view-six.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-guest-executables-introduction.md
[a3]: service-fabric-containers-overview.md
[a4]: service-fabric-package-apps.md
[a5]: service-fabric-deploy-remove-applications.md

[r1]: /rest/api/servicefabric/sfclient-api-createservice

[c1]: /dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync
[c2]: /dotnet/api/system.fabric.description.statelessservicedescription.instancecount

[p1]: /powershell/module/servicefabric/new-servicefabricservice
[p2]: /powershell/module/servicefabric/get-servicefabricservicedescription
[p3]: /powershell/module/servicefabric/get-servicefabricdeployedservicepackage
[p4]: /powershell/module/servicefabric/send-servicefabricdeployedservicepackagehealthreport
[p5]: /powershell/module/servicefabric/restart-servicefabricdeployedcodepackage
[p6]: /powershell/module/servicefabric/get-servicefabricdeployedservicetype
[p7]: /powershell/module/servicefabric/get-servicefabricdeployedreplica
[p8]: /powershell/module/servicefabric/get-servicefabricdeployedcodepackage
