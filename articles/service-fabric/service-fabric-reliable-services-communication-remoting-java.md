---
title: "Azure Service Fabric でのサービスのリモート処理 | Microsoft Docs"
description: "Service Fabric のリモート処理では、クライアントとサービスがリモート プロシージャ コールを使用してサービスと通信できるようにします。"
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: 
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: dc4a362b5737bb424ca2c196c85f4c51b6ee5e30
ms.contentlocale: ja-jp
ms.lasthandoff: 07/01/2017


---
# <a name="service-remoting-with-reliable-services"></a>Reliable Services によるサービスのリモート処理
> [!div class="op_single_selector"]
> * [Windows での C# ](service-fabric-reliable-services-communication-remoting.md)
> * [Linux での Java](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Reliable Services フレームワークではリモート メカニズムが提供され、これにより、サービスのリモート プロシージャ コールを迅速かつ簡単に設定できます。

## <a name="set-up-remoting-on-a-service"></a>サービスでのリモート処理の設定
サービスのリモート処理の設定は、次の 2 つの簡単な手順で行われます。

1. 実装するサービスのインターフェイスを作成します。 このインターフェイスは、サービスのリモート プロシージャ コールで使用できるメソッドを定義します。 このメソッドはタスクを返す非同期メソッドである必要があります。 インターフェイスは `microsoft.serviceFabric.services.remoting.Service` を実装し、そのサービスにリモート処理インターフェイスがあることを示す必要があります。
2. サービスでリモート処理リスナーを使用します。 これは、リモート処理機能を提供する `CommunicationListener` の実装です。 `FabricTransportServiceRemotingListener` は、既定のリモート処理トランスポート プロトコルを使用してリモート リスナーを作成するときに使用できます。

たとえば、次のステートレス サービスでは、リモート プロシージャ コールで "Hello World" を取得する 1 つのメソッドを公開します。

```java
import java.util.ArrayList;
import java.util.concurrent.CompletableFuture;
import java.util.List;
import microsoft.servicefabric.services.communication.runtime.ServiceInstanceListener;
import microsoft.servicefabric.services.remoting.Service;
import microsoft.servicefabric.services.runtime.StatelessService;

public interface MyService extends Service {
    CompletableFuture<String> helloWorldAsync();
}

class MyServiceImpl extends StatelessService implements MyService {
    public MyServiceImpl(StatelessServiceContext context) {
       super(context);
    }

    public CompletableFuture<String> helloWorldAsync() {
        return CompletableFuture.completedFuture("Hello!");
    }

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
        listeners.add(new ServiceInstanceListener((context) -> {
            return new FabricTransportServiceRemotingListener(context,this);
        }));
        return listeners;
    }
}
```

> [!NOTE]
> サービス インターフェイスの引数と戻り値の型は、単純型、複合型、またはカスタム型のいずれかにできますが、シリアル化できる必要があります。
>
>

## <a name="call-remote-service-methods"></a>リモート サービス メソッドの呼び出し
リモート処理スタックを使用したサービスでのメソッドの呼び出しは、 `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` クラスによるサービスへのローカル プロキシを使用して実行されます。 `ServiceProxyBase` メソッドは、サービスに実装されている同じインターフェイスを使用してローカル プロキシを作成します。 そのプロキシを使用して、インターフェイス上のメソッドをリモートで簡単に呼び出すことができます。

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

リモート処理フレームワークは、サービスでスローされた例外をクライアントに伝達します。 そのため、 `ServiceProxyBase` を使用したクライアントでの例外処理ロジックでは、サービスがスローする例外を直接処理できます。

## <a name="service-proxy-lifetime"></a>サービス プロキシの有効期間
ServiceProxy の作成は負荷の低い操作であり、ユーザーは必要に応じていくつでも ServiceProxy を作成できます。 サービス プロキシは、ユーザーがそれを必要とする間、再利用することができます。 例外が発生した場合は、同じプロキシを再利用することができます。 各 ServiceProxy は、メッセージをネットワーク経由で送信するための通信クライアントを含んでいます。 API を呼び出す間、使用されている通信クライアントが有効であるかどうかが内部的にチェックされます。 その結果に基づいて、通信クライアントが再作成されます。 したがって例外が発生した場合の ServiceProxy の再作成をユーザーが行う必要はありません。

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory の有効期間
[FabricServiceProxyFactory](https://docs.microsoft.com/en-us/java/api/microsoft.servicefabric.services.remoting.client._fabric_service_proxy_factory) は、さまざまなリモート処理インターフェイスのプロキシを作成するファクトリです。 プロキシの作成に API `ServiceProxyBase.create` を使っている場合、フレームワークは `FabricServiceProxyFactory` を作成します。
手動での作成は、[ServiceRemotingClientFactory](https://docs.microsoft.com/en-us/java/api/microsoft.servicefabric.services.remoting.client._service_remoting_client_factory) プロパティを上書きする必要があるときに効果的です。
ファクトリは負荷の高い操作です。 `FabricServiceProxyFactory` は通信クライアントのキャッシュを保持します。
ベスト プラクティスは `FabricServiceProxyFactory` をできるだけ長くキャッシュすることです。

## <a name="remoting-exception-handling"></a>リモート処理の例外処理
サービス API によってスローされるリモート例外はすべて、RuntimeException または FabricException としてクライアントに返送されます。

ServiceProxy は、それが作成されたサービス パーティションのすべてのフェールオーバー例外を処理します。 フェールオーバー例外 (一時的ではない例外) が発生した場合、エンドポイントを再度解決し、正しいエンドポイントでの呼び出しを再試行します。 フェールオーバー例外の再試行回数に上限はありません。
一時的な例外の場合は、単に呼び出しが再試行されます。

既定の再試行パラメーターは、[OperationRetrySettings] で指定します。 (https://docs.microsoft.com/en-us/java/api/microsoft.servicefabric.services.communication.client._operation_retry_settings) OperationRetrySettings オブジェクトを ServiceProxyFactory コンストラクターに渡すことでこれらの値を構成できます。

## <a name="next-steps"></a>次のステップ
* [Reliable Services の通信のセキュリティ保護](service-fabric-reliable-services-secure-communication.md)

