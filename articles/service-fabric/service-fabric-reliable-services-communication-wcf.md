---
title: Reliable Services の WCF 通信スタック
description: Service Fabric の組み込み WCF 通信スタックは、Reliable Services にクライアント サービス WCF 通信を提供します。
author: BharatNarasimman
ms.topic: conceptual
ms.date: 06/07/2017
ms.author: bharatn
ms.custom: devx-track-csharp
ms.openlocfilehash: c5b5d413eee2528e2d5c7d04d06f1607949beaae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89012706"
---
# <a name="wcf-based-communication-stack-for-reliable-services"></a>Reliable Services の WCF ベースの通信スタック
Reliable Services フレームワークにより、サービスの作成者はサービスに使用する通信スタックを選択できます。 **CreateServiceReplicaListeners または CreateServiceInstanceListeners** メソッドから返された [ICommunicationListener](service-fabric-reliable-services-communication.md) を介し、選択した通信スタックをプラグインできます。 フレームワークでは、Windows Communication Foundation (WCF) ベースの通信を使用したいと考えるサービス作成者に、WCF に基づいた通信スタックの実装を提供します。

## <a name="wcf-communication-listener"></a>WCF 通信リスナー
**ICommunicationListener** の WCF 固有の実装は、**Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener** クラスによって提供されます。

`ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

次のようにして、サービスの WCF 通信リスナーを作成できます。

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[] { new ServiceReplicaListener((context) =>
        new WcfCommunicationListener<ICalculator>(
            wcfServiceObject:this,
            serviceContext:context,
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
            //
            endpointResourceName: "WcfServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            listenerBinding: WcfUtility.CreateTcpListenerBinding()
        )
    )};
}

```

## <a name="writing-clients-for-the-wcf-communication-stack"></a>WCF の通信スタック用のクライアントの作成
WCF を使用して、サービスと通信するクライアントを作成する場合、フレームワークには **ClientCommunicationFactoryBase** の WCF 固有の実装である [WcfClientCommunicationFactory](service-fabric-reliable-services-communication.md)があります。

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

WCF 通信チャネルは **WcfCommunicationClientFactory** よって作成された **WcfCommunicationClient** からアクセスできます。

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

クライアント コードでは、**ServicePartitionClient** を実装する **WcfCommunicationClient** と共に **WcfCommunicationClientFactory** を使用して、サービス エンドポイントを特定し、サービスと通信できます。

```csharp
// Create binding
Binding binding = WcfUtility.CreateTcpClientBinding();
// Create a partition resolver
IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();
// create a  WcfCommunicationClientFactory object.
var wcfClientFactory = new WcfCommunicationClientFactory<ICalculator>
    (clientBinding: binding, servicePartitionResolver: partitionResolver);

//
// Create a client for communicating with the ICalculator service that has been created with the
// Singleton partition scheme.
//
var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
                wcfClientFactory,
                ServiceUri,
                ServicePartitionKey.Singleton);

//
// Call the service to perform the operation.
//
var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
                client => client.Channel.Add(2, 3)).Result;

```
> [!NOTE]
> 既定の ServicePartitionResolver は、クライアントがサービスと同じクラスターで実行されているものと想定します。 そうでない場合は、ServicePartitionResolver オブジェクトを作成して、クラスター接続エンドポイントを渡します。
> 
> 

## <a name="next-steps"></a>次のステップ
* [Reliable Services のリモート処理によるリモート プロシージャ コール](service-fabric-reliable-services-communication-remoting.md)
* [Reliable Services の OWIN 対応 Web API](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Reliable Services の通信のセキュリティ保護](service-fabric-reliable-services-secure-communication-wcf.md)
