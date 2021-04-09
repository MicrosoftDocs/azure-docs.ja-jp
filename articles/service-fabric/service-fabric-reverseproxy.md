---
title: Azure Service Fabric のリバース プロキシ
description: Service Fabric のリバース プロキシを使用して、クラスターの内外からマイクロサービスとの通信を行います。
author: BharatNarasimman
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: bharatn
ms.custom: devx-track-csharp
ms.openlocfilehash: 012a49762596adee39988614ed0c1020cd8bc104
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791106"
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Azure Service Fabric のリバース プロキシ
Azure Service Fabric に組み込まれたリバース プロキシは、Service Fabric クラスターで実行されているマイクロサービスが HTTP エンドポイントを持つ他のサービスを検出してそのサービスと通信するのに役立ちます。

## <a name="microservices-communication-model"></a>マイクロサービス通信モデル
Service Fabric のマイクロサービスは、クラスター内のノード のサブセットで実行され、さまざまな理由からノード間で移動することができます。 その結果、マイクロサービスのエンドポイントが動的に変更される可能性があります。 マイクロサービスがクラスター内の他のサービスを検出してそのサービスと通信するには、次の手順を実行する必要があります。

1. ネーム サービスによってサービスの場所を解決します。
2. サービスに接続します。
3. 上記の手順を、接続エラーに適用するサービスの解決および再試行のポリシーを実装するループにラップします。

詳細については、[サービスとの接続と通信](service-fabric-connect-and-communicate-with-services.md)に関する記事をご覧ください。

### <a name="communicating-by-using-the-reverse-proxy"></a>リバース プロキシを使用した通信
リバース プロキシは、すべてのノードで実行され、エンドポイントの解決、自動再試行、その他の接続エラーをクライアント サービスに代わって処理するサービスです。 リバース プロキシを、クライアント サービスからの要求の処理時にさまざまなポリシーを適用するように構成できます。 リバース プロキシを使用すると、クライアント サービスがクライアント側の HTTP 通信ライブラリを使用でき、サービスに特別な解決および再試行のロジックを実装する必要がなくなります。 

リバース プロキシは、クライアント サービスが他のサービスに要求を送信するために使用する、ローカル ノード上の 1 つ以上のエンドポイントを公開します。

![内部通信][1]

> [!NOTE]
> **サポートされているプラットフォーム**
>
> Service Fabric のリバース プロキシでは現在、次のプラットフォームがサポートされています。
> * "*Windows クラスター*":Windows 8 以降または Windows Server 2012 以降
> * "*Linux クラスター*":現在、リバース プロキシは Linux クラスターでは使用できません
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>クラスターの外部からのマイクロサービスへの到達
マイクロサービスの既定の外部通信モデルは、外部クライアントから各サービスに直接アクセスできないオプトイン モデルです。 [Azure Load Balancer](../load-balancer/load-balancer-overview.md) は、マイクロサービスと外部クライアントの間のネットワーク境界であり、ネットワーク アドレス変換を実行して、外部要求を内部の IP:port エンドポイントに転送します。 マイクロサービスのエンドポイントが外部クライアントに直接アクセスできるようにするには、クラスター内のサービスで使用される各ポートにトラフィックを転送するように Load Balancer を構成しておく必要があります。 さらに、ほとんどのマイクロサービス (特にステートフル マイクロサービス) は、クラスターのすべてのノードに存在するわけではありません。 マイクロサービスは、フェールオーバー時にノード間を移動する可能性があります。 このような場合、Load Balancer はトラフィックの転送先となるレプリカのターゲット ノードの場所を実際には特定できません。

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>クラスターの外部からのリバース プロキシ経由でのマイクロサービスへの到達
Load Balancer で個々のサービスのポートを構成するのではなく、リバース プロキシのポートだけを構成できます。 この構成では、クラスターの外部のクライアントは、追加構成なしにリバース プロキシを使用してクラスター内のサービスに到達できます。

![外部通信][0]

> [!WARNING]
> Load Balancer でリバース プロキシのポートを構成すると、クラスター内の HTTP エンドポイントを公開するすべてのマイクロサービスをクラスターの外部からアドレス指定できるようになります。 つまり、内部的なものであるマイクロサービスを、悪意のあるユーザーが検出できる可能性があります。 これにより、次のような、悪用される可能性のある重大な脆弱性が潜在的に存在することになります。
>
> * 悪意のあるユーザーは、セキュリティで十分に保護されていない攻撃対象が存在する内部サービスを繰り返し呼び出すことによって、サービス拒否攻撃を加える場合があります。
> * 悪意のあるユーザーは無効なパケットを内部サービスに送信し、予期しない動作を発生させる場合があります。
> * 内部的なものであるサービスは公開することを意図していない個人情報または機密情報をクラスターの外部のサービスに返す場合があり、それによって機密情報が悪意のあるユーザーに晒される可能性があります。 
>
> リバース プロキシのポートをパブリックにする前に、クラスターおよびクラスター上で実行されているアプリに対する潜在的なセキュリティ上の影響を完全に理解し、それを軽減するようにしてください。 
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>リバース プロキシを使用してサービスのアドレス指定を行うための URI 形式
リバース プロキシでは、次の URI (Uniform Resource Identifier) 形式を使用して、受信要求の転送先となるサービス パーティションを特定します。

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http(s):** HTTP または HTTPS トラフィックを受け入れるようにリバース プロキシを構成できます。 HTTPS 転送の場合、HTTPS でリッスンするようにリバース プロキシをセットアップした後に、「[Connect to a secure service with the reverse proxy (リバース プロキシを使用したセキュリティで保護されたサービスへの接続)](service-fabric-reverseproxy-configure-secure-communication.md)」をご覧ください。
* **Cluster FQDN (完全修飾ドメイン名) | internal IP:** 外部クライアントの場合、クラスターのドメイン (例: mycluster.eastus.cloudapp.azure.com) を介して到達できるようにリバース プロキシを構成できます。 既定では、リバース プロキシはすべてのノードで実行されます。 内部トラフィックの場合、リバース プロキシには localhost または任意の内部ノード IP (例: 10.0.0.1) で到達できます。
* **Port:** リバース プロキシ用に指定されているポートです (例: 19081)。
* **ServiceInstanceName:** "fabric:/" スキームなしで到達しようとしているデプロイ済みのサービス インスタンスの完全修飾名です。 たとえば、*fabric:/myapp/myservice/* サービスに到達するには、*myapp/myservice* を使用します。

    サービス インスタンス名は、大文字小文字が区別されます。 URL のサービス インスタンス名に使用されている大文字小文字が異なる場合、要求は 404 (Not Found) で失敗します。
* **Suffix path:** 接続先となるサービスの実際の URL パスです (例: *myapi/values/add/3*)。
* **PartitionKey:** パーティション分割されたサービスの場合、到達するパーティションの計算済みのパーティション キーです。 これはパーティション ID の GUID では *ありません*。 シングルトン パーティション構成を使用するサービスでは、このパラメーターは不要です。
* **PartitionKind:** サービス パーティション構成です。 これには、"Int64Range" または "Named" を指定できます。 シングルトン パーティション構成を使用するサービスでは、このパラメーターは不要です。
* **ListenerName**: サービスのエンドポイント。{"Endpoints":{"Listener1":"Endpoint1","Listener2":"Endpoint2" ...}} の形式で指定します。 サービスが複数のエンドポイントを公開している場合、このパラメーターによって、クライアント要求の転送先となるエンドポイントを特定します。 サービスにリスナーが 1 つしかない場合、このパラメーターは省略できます。
* **TargetReplicaSelector**: ターゲット レプリカまたはインスタンスの選択方法を指定します。
  * ターゲット サービスがステートフルの場合、TargetReplicaSelector には "PrimaryReplica"、"RandomSecondaryReplica"、"RandomReplica" のいずれかを指定できます。 このパラメーターが指定されていない場合、既定値は "PrimaryReplica" です。
  * ターゲット サービスがステートレスの場合、リバース プロキシは要求の転送先となるサービス パーティションのインスタンスをランダムに選択します。
* **Timeout:** クライアント要求の代わりに、リバース プロキシによって作成される、サービスに対する HTTP 要求のタイムアウトを指定します。 既定値は 120 秒です。 これは省略可能なパラメーターです。

### <a name="example-usage"></a>使用例
例として、次の URL で HTTP リスナーを開く *fabric:/MyApp/MyService* サービスを取得します。

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

サービスのリソースは次のとおりです。

* `/index.html`
* `/api/users/<userId>`

サービスがシングルトン パーティション構成を使用している場合、*PartitionKey* および *PartitionKind* クエリ文字列パラメーターは不要であり、次のようにゲートウェイを使用してサービスに到達できます。

* 外部: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* 内部: `http://localhost:19081/MyApp/MyService`

サービスが Uniform Int64 パーティション構成を使用している場合、サービスのパーティションにアクセスするには、*PartitionKey* および *PartitionKind* クエリ文字列パラメーターを使用する必要があります。

* 外部: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* 内部: `http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

サービスが公開するリソースに到達するには、URL のサービス名の後にリソース パスを配置します。

* 外部: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* 内部: `http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

ゲートウェイは、サービスの URL にこれらの要求を転送します。

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>ポートを共有するサービスの特別な処理
サービスに到達できない場合、Service Fabric リバース プロキシはサービス アドレスの解決を再度試み、要求を再試行します。 一般に、サービスに到達できないときは、サービス インスタンスまたはレプリカが通常のライフサイクルの一環として別のノードに移動しています。 この場合、リバース プロキシは、エンドポイントが解決された元のアドレスではもう開いていないことを示すネットワーク接続エラーを受信する可能性があります。

ただし、レプリカまたはサービス インスタンスはホスト プロセスを共有できます。また、次のような http.sys ベースの Web サーバーによってホストされているときには、ポートを共有している場合もあります。

* [System.Net.HttpListener](/dotnet/api/system.net.httplistener)
* [ASP.NET Core WebListener](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

この状況では、Web サーバーはホスト プロセスで要求に応答することができますが、解決されたサービス インスタンスまたはレプリカはホストで使用できなくなっています。 この場合、ゲートウェイは Web サーバーから HTTP 404 応答を受信します。 そのため、HTTP 404 応答は 2 つの異なる意味を持つ可能性があります。

- ケース 1:サービス アドレスは正しいが、ユーザーから要求されたリソースが存在しない。
- ケース 2:サービス アドレスが間違っており、ユーザーから要求されたリソースが、実際には別のノードに存在する可能性がある。

最初のケースは通常の HTTP 404 であり、ユーザー エラーと見なされます。 ただし、2 番目のケースでは、ユーザーは存在するリソースを要求しています。 サービス自体が移動しているため、リバース プロキシがリソースを見つけることができませんでした。 この場合、リバース プロキシはアドレスを再度解決し、要求を再試行する必要があります。

そのため、リバース プロキシにはこの2つのケースを区別する手段が必要です。 この区別を行うには、サーバーからのヒントが必要になります。

* 既定では、リバース プロキシはケース 2 を想定し、アドレスを再度解決して要求を再発行することを試みます。
* リバース プロキシにケース 1 であることを示すには、サービスが次の HTTP 応答ヘッダーを返す必要があります。

  `X-ServiceFabric : ResourceNotFound`

この HTTP 応答ヘッダーは、要求されたリソースが存在しないという通常の HTTP 404 の状況を示すので、リバース プロキシはサービス アドレスの解決を再度試みることはなくなります。

## <a name="special-handling-for-services-running-in-containers"></a>コンテナーで実行されているサービスに対する特別な処理

コンテナー内で実行されているサービスの場合、次のコードのように、環境変数 `Fabric_NodeIPOrFQDN` を使って[リバース プロキシ URL](#uri-format-for-addressing-services-by-using-the-reverse-proxy) を作成できます。

```csharp
    var fqdn = Environment.GetEnvironmentVariable("Fabric_NodeIPOrFQDN");
    var serviceUrl = $"http://{fqdn}:19081/DockerSFApp/UserApiContainer";
```
ローカル クラスターでは、`Fabric_NodeIPOrFQDN` は既定で "localhost" に設定されます。 `-UseMachineName` パラメーターを使ってローカル クラスターを開始し、コンテナーがノードで実行されているリバース プロキシにアクセスできることを確認します。 詳しくは、「[コンテナーをデバッグするように開発者環境を構成する](service-fabric-how-to-debug-windows-containers.md#configure-your-developer-environment-to-debug-containers)」をご覧ください。

Docker Compose コンテナー内で実行される Service Fabric サービスには、特別な docker-compose.yml の *ポート セクション* http: または https: 構成が必要です。 詳細については、[Azure Service Fabric での Docker Compose デプロイのサポート](service-fabric-docker-compose.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
* [クラスターでのリバース プロキシの設定と構成](service-fabric-reverseproxy-setup.md)
* [リバース プロキシを使用したセキュリティで保護された HTTP サービスへの転送の設定](service-fabric-reverseproxy-configure-secure-communication.md)
* [リバース プロキシ イベントの診断](service-fabric-reverse-proxy-diagnostics.md)
* [GitHub のサンプル プロジェクト](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)で、サービス間の HTTP 通信の例を確認します。
* [Reliable Services のリモート処理によるリモート プロシージャ コール](service-fabric-reliable-services-communication-remoting.md)
* [Reliable Services の OWIN を使用する Web API](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Reliable Services を使用した WCF 通信](service-fabric-reliable-services-communication-wcf.md)

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
