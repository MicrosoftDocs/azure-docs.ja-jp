
---
title: "Service Fabric でのサービスのリモート処理 | Microsoft Docs"
description: "Service Fabric のリモート処理では、クライアントとサービスがリモート プロシージャ コールを使用してサービスと通信できるようにします。"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: vturecek
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 92a8894f24c234fbf38eda086531b524cceccfc1
ms.contentlocale: ja-jp
ms.lasthandoff: 06/29/2017


---
# <a name="service-remoting-with-reliable-services"></a>Reliable Services によるサービスのリモート処理
WebAPI や Windows Communication Foundation (WCF) など、特定の通信プロトコルやスタックに関連付けられていないサービスでは、サービスのリモート プロシージャ コールを迅速かつ簡単に設定するためのリモート処理メカニズムを Reliable Services フレームワークが提供します。

## <a name="set-up-remoting-on-a-service"></a>サービスでのリモート処理の設定
サービスのリモート処理の設定は、次の 2 つの簡単な手順で行われます。

1. 実装するサービスのインターフェイスを作成します。 このインターフェイスはサービスのリモート プロシージャ コールで使用できるメソッドを定義します。 このメソッドはタスクを返す非同期メソッドである必要があります。 インターフェイスは `Microsoft.ServiceFabric.Services.Remoting.IService` を実装し、そのサービスにリモート処理インターフェイスがあることを示す必要があります。
2. サービスでリモート処理リスナーを使用します。 これは、リモート処理機能を提供する `ICommunicationListener` の実装です。 `Microsoft.ServiceFabric.Services.Remoting.Runtime` 名前空間には、ステートレス サービスとステートフル サービスの拡張メソッド `CreateServiceRemotingListener` が含まれています。このメソッドは、既定のリモート処理トランスポート プロトコルを使用してリモート処理リスナーを作成するときに使用できます。

注: `Remoting` 名前空間は、`Microsoft.ServiceFabric.Services.Remoting` という別の nuget パッケージで入手できます 

たとえば、次のステートレス サービスでは、リモート プロシージャ コールで "Hello World" を取得する 1 つのメソッドを公開します。

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] { new ServiceInstanceListener(context =>            this.CreateServiceRemotingListener(context)) };
    }
}
```
> [!NOTE]
> サービス インターフェイスの引数と戻り値の型は、単純型、複合型、またはカスタム型のいずれかにできますが、.NET の [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx)によってシリアル化できる必要があります。
>
>

## <a name="call-remote-service-methods"></a>リモート サービス メソッドの呼び出し
リモート処理スタックを使用したサービスでのメソッドの呼び出しは、 `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` クラスによるサービスへのローカル プロキシを使用して実行されます。 `ServiceProxy` メソッドは、サービスに実装されている同じインターフェイスを使用してローカル プロキシを作成します。 そのプロキシを使用して、インターフェイス上のメソッドをリモートで簡単に呼び出すことができます。

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

リモート処理フレームワークは、サービスでスローされた例外をクライアントに伝達します。 そのため、 `ServiceProxy` を使用したクライアントでの例外処理ロジックでは、サービスがスローする例外を直接処理できます。

## <a name="service-proxy-lifetime"></a>サービス プロキシの有効期間
ServiceProxy の作成は負荷の低い操作であり、ユーザーは必要に応じていくつでも ServiceProxy を作成できます。 サービス プロキシは、ユーザーがそれを必要とする間、再利用することができます。 例外が発生した場合は、同じプロキシを再利用することができます。 各 ServiceProxy は、メッセージをネットワーク経由で送信するための通信クライアントを含んでいます。 API を呼び出す間、使用されている通信クライアントが有効であるかどうかが内部的にチェックされます。 その結果に基づいて、通信クライアントが再作成されます。 したがって例外が発生した場合の ServiceProxy の再作成をユーザーが行う必要はありません。

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory の有効期間
[ServiceProxyFactory](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) は、さまざまなリモート処理インターフェイスのプロキシを作成するファクトリです。 API ServiceProxy.Create を使ってプロキシを作成する場合、フレームワークによってシングルトン ServiceProxyFactory が作成されます。
手動での作成は、[IServiceRemotingClientFactory](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.client.iserviceremotingclientfactory) プロパティを上書きする必要があるときに効果的です。
ファクトリは負荷の高い操作です。 ServiceProxyFactory は通信クライアントのキャッシュを保持します。
ServiceProxyFactory はできるだけ長くキャッシュすることをお勧めします。

## <a name="remoting-exception-handling"></a>リモート処理の例外処理
サービス API によってスローされるリモート処理の例外はすべて、AggregateException としてクライアントに返されます。 RemoteExceptions は DataContract Serializable である必要があります。それ以外の場合、シリアル化エラーと共に [ServiceException](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) がプロキシ API にスローされます。

ServiceProxy は、それが作成されたサービス パーティションのすべてのフェールオーバー例外を処理します。 フェールオーバー例外 (一時的ではない例外) が発生した場合、エンドポイントを再度解決し、正しいエンドポイントでの呼び出しを再試行します。 フェールオーバー例外の再試行回数に上限はありません。
一時的な例外の場合は、単に呼び出しが再試行されます。

既定の再試行パラメーターは、[OperationRetrySettings] で指定します。 (https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings) OperationRetrySettings オブジェクトを ServiceProxyFactory コンストラクターに渡すことでこれらの値を構成できます。

## <a name="next-steps"></a>次のステップ
* [Reliable Services の OWIN 対応 Web API](service-fabric-reliable-services-communication-webapi.md)
* [Reliable Services との WCF 通信](service-fabric-reliable-services-communication-wcf.md)
* [Reliable Services の通信のセキュリティ保護](service-fabric-reliable-services-secure-communication.md)

