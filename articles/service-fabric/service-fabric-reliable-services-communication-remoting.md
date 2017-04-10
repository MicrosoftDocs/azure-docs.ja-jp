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
ms.date: 02/10/2017
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c568e44ac4008d4252ef2e889150506307cc7a92


---
# <a name="service-remoting-with-reliable-services"></a>Reliable Services によるサービスのリモート処理
WebAPI や Windows Communication Foundation (WCF) など、特定の通信プロトコルやスタックに関連付けられていないサービスでは、サービスのリモート プロシージャ コールを迅速かつ簡単に設定するためのリモート処理メカニズムを Reliable Services フレームワークが提供します。

## <a name="set-up-remoting-on-a-service"></a>サービスでのリモート処理の設定
サービスのリモート処理の設定は、次の 2 つの簡単な手順で行われます。

1. 実装するサービスのインターフェイスを作成します。 このインターフェイスはサービスのリモート プロシージャ コールで使用できるメソッドを定義します。 このメソッドはタスクを返す非同期メソッドである必要があります。 インターフェイスは `Microsoft.ServiceFabric.Services.Remoting.IService` を実装し、そのサービスにリモート処理インターフェイスがあることを示す必要があります。
2. サービスでリモート処理リスナーを使用します。 これは、リモート処理機能を提供する `ICommunicationListener` の実装です。 `Microsoft.ServiceFabric.Services.Remoting.Runtime` 名前空間には、ステートレス サービスとステートフル サービスの拡張メソッド `CreateServiceRemotingListener` が含まれています。このメソッドは、既定のリモート処理トランスポート プロトコルを使用してリモート処理リスナーを作成するときに使用できます。

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
        return new[] { new ServiceInstanceListener(context => 
            this.CreateServiceRemotingListener(context)) };
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

## <a name="next-steps"></a>次のステップ
* [Reliable Services の OWIN 対応 Web API](service-fabric-reliable-services-communication-webapi.md)
* [Reliable Services との WCF 通信](service-fabric-reliable-services-communication-wcf.md)
* [Reliable Services の通信のセキュリティ保護](service-fabric-reliable-services-secure-communication.md)




<!--HONumber=Nov16_HO3-->


