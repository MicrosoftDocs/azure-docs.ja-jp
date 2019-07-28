---
title: Azure Service Fabric での Java を使用したサービスのリモート処理 | Microsoft Docs
description: Service Fabric のリモート処理では、クライアントとサービスがリモート プロシージャ コールを使用して Java のサービスと通信できるようにします。
services: service-fabric
documentationcenter: java
author: pavankunapareddymsft
manager: chackdan
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 51c8c689bd3fe3e8967bab77e776ad02f9cb59f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "62123637"
---
# <a name="service-remoting-in-java-with-reliable-services"></a>Java での Reliable Services を使用したサービスのリモート処理
> [!div class="op_single_selector"]
> * [Windows での C#](service-fabric-reliable-services-communication-remoting.md)
> * [Linux での Java](service-fabric-reliable-services-communication-remoting-java.md)
>
>

WebAPI や Windows Communication Foundation (WCF) など、特定の通信プロトコルやスタックに関連付けられていないサービスでは、サービスのリモート プロシージャ コールを迅速かつ簡単に設定するためのリモート処理メカニズムを Reliable Services フレームワークが提供します。  この記事では、Java で作成されたサービス向けにリモート プロシージャ コールを設定する方法について説明します。

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
ServiceProxy の作成は負荷の低い処理であり、必要に応じていくつでも ServiceProxy を作成できます。 サービス プロキシ インスタンスは、それらが必要とされる間、再利用することができます。 リモート プロシージャ コールから例外がスローされても、同じプロキシ インスタンスを再利用できます。 各 ServiceProxy は、メッセージをネットワーク経由で送信するための通信クライアントを含んでいます。 リモート呼び出しが実行されると、通信クライアントが有効かどうかを調べる内部チェックが実行されます。 それらのチェックの結果に基づき、必要に応じて通信クライアントが再作成されます。 したがって例外が発生しても、`ServiceProxy` を自分で再作成する必要はありません。

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory の有効期間
[FabricServiceProxyFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.fabricserviceproxyfactory) は、さまざまなリモート処理インターフェイスのプロキシを作成するファクトリです。 プロキシの作成に API `ServiceProxyBase.create` を使っている場合、フレームワークは `FabricServiceProxyFactory` を作成します。
手動での作成は、[ServiceRemotingClientFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.serviceremotingclientfactory) プロパティをオーバーライドする必要があるときに効果的です。
ファクトリは負荷の高い操作です。 `FabricServiceProxyFactory` は通信クライアントのキャッシュを保持します。
ベスト プラクティスは `FabricServiceProxyFactory` をできるだけ長くキャッシュすることです。

## <a name="remoting-exception-handling"></a>リモート処理の例外処理
サービス API によってスローされるリモート例外はすべて、RuntimeException または FabricException としてクライアントに返送されます。

ServiceProxy は、それが作成されたサービス パーティションのすべてのフェールオーバー例外を処理します。 フェールオーバー例外 (一時的ではない例外) が発生した場合、エンドポイントを再度解決し、正しいエンドポイントでの呼び出しを再試行します。 フェールオーバー例外の再試行回数に上限はありません。
一時的な例外の場合は、単に呼び出しが再試行されます。

既定の再試行パラメーターは、[OperationRetrySettings](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.communication.client.operationretrysettings) で指定します。
ユーザーは、ServiceProxyFactory コンストラクターに OperationRetrySettings オブジェクトを渡すことによって、これらの値を構成できます。

## <a name="next-steps"></a>次の手順
* [Reliable Services の通信のセキュリティ保護](service-fabric-reliable-services-secure-communication-java.md)
