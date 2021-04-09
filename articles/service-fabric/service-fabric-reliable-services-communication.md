---
title: Reliable Services 通信の概要
description: サービスのリッスン開始、エンドポイントの解決、サービス間の通信など、Reliable Service 通信モデルの概要について説明します。
ms.topic: conceptual
ms.date: 11/01/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 3436d29446e963faea9bda47f5a5247b7de7d859
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97912616"
---
# <a name="how-to-use-the-reliable-services-communication-apis"></a>Reliable Services 通信 API の使用方法
プラットフォームとしての Azure Service Fabric は、サービス間の通信にまったく依存しません。 UDP から HTTP まで、あらゆるプロトコルとスタックに対応します。 サービスの通信方法の選択は、サービス開発者に委ねられています。 Reliable Services アプリケーション フレームワークには、組み込みの通信スタックと、カスタム通信コンポーネントの構築に使用できる API が用意されています。

## <a name="set-up-service-communication"></a>サービス通信のセットアップ
Reliable Services API では、サービス通信に単純なインターフェイスを使用します。 サービスのエンドポイントを開くには、単純にこのインターフェイスを実装します。

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

```java
public interface CommunicationListener {
    CompletableFuture<String> openAsync(CancellationToken cancellationToken);

    CompletableFuture<?> closeAsync(CancellationToken cancellationToken);

    void abort();
}
```

その後、サービス基本クラスのメソッド オーバーライドで通信リスナー実装を返すことで、これを追加できます。

ステートレスの場合、次のようになります。

```csharp
public class MyStatelessService : StatelessService
{
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        ...
    }
    ...
}
```
```java
public class MyStatelessService extends StatelessService {

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ...
    }
    ...
}
```

ステートフルの場合、次のようになります。

```java
    @Override
    protected List<ServiceReplicaListener> createServiceReplicaListeners() {
        ...
    }
    ...
```

```csharp
public class MyStatefulService : StatefulService
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        ...
    }
    ...
}
```

どちらの場合も、リスナーのコレクションが返されます。 これにより、サービスでは、複数のリスナーを使用して、異なるプロトコルを使用している可能性のある複数のエンドポイントでリッスンすることができます。 たとえば、HTTP リスナーと別の WebSocket リスナーがあるとします。 各リスナーが名前を取得し、その結果としての *名前とアドレス* のペアのコレクションは、クライアントがサービス インスタンスまたはパーティションのリッスン アドレスを要求したときに JSON オブジェクトとして表されます。

ステートレス サービスでは、オーバーライドによって ServiceInstanceListeners のコレクションが返されます。 `ServiceInstanceListener` には、`ICommunicationListener(C#) / CommunicationListener(Java)` を作成して名前を付ける関数が含まれます。 ステートフル サービスでは、オーバーライドによって ServiceReplicaListeners のコレクションが返されます。 `ServiceReplicaListener` は、ステートレス サービスとは少し異なり、セカンダリ レプリカで `ICommunicationListener` を開くオプションを備えています。 これにより、サービスで複数の通信リスナーを使用できるだけでなく、セカンダリ レプリカで要求を受け入れるリスナーと、プライマリ レプリカでリッスンするだけのリスナーを指定することもできます。

たとえば、プライマリ レプリカで RPC 呼び出しのみを受け取る ServiceRemotingListener と、セカンダリ レプリカで HTTP を介して読み取り要求を受け取る 2 つ目のカスタム リスナーを使用できます。

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(context =>
            new MyCustomHttpListener(context),
            "HTTPReadonlyEndpoint",
            true),

        new ServiceReplicaListener(context =>
            this.CreateServiceRemotingListener(context),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

> [!NOTE]
> サービスのリスナーを複数作成する場合は、各リスナーに一意の名前を付ける **必要があります** 。
>
>

最後に、 [サービス マニフェスト](service-fabric-application-and-service-manifests.md) の Endpoints セクションに、サービスに必要なエンドポイントを記述します。

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

通信リスナーは、`ServiceContext` の `CodePackageActivationContext` から、割り当てられているエンドポイント リソースにアクセスできます。 その後、リスナーは開かれたときに、要求のリッスンを開始できます。

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```
```java
CodePackageActivationContext codePackageActivationContext = serviceContext.getCodePackageActivationContext();
int port = codePackageActivationContext.getEndpoint("ServiceEndpoint").getPort();

```

> [!NOTE]
> エンドポイント リソースはサービス パッケージ全体に共通であり、サービス パッケージがアクティブ化されたときに Service Fabric によって割り当てられます。 同じ ServiceHost でホストされている複数のサービス レプリカは、同じポートを共有する場合があります。 これは、通信リスナーがポート共有をサポートする必要があることを意味します。 これを実行する推奨される方法は、通信リスナーが、リッスン アドレスを生成する際に、パーティション ID とレプリカ/インスタンス ID を使用することです。
>
>

### <a name="service-address-registration"></a>サービスのアドレスの登録
Service Fabric クラスターでは、 *ネーム サービス* と呼ばれるシステム サービスが実行されます。 ネーム サービスは、サービスの各インスタンスまたはレプリカがリッスンしているサービスとそのアドレスのレジストラーです。 `ICommunicationListener(C#) / CommunicationListener(Java)` の `OpenAsync(C#) / openAsync(Java)` メソッドが完了すると、戻り値がネーム サービスに登録されます。 ネーム サービスで公開されるこの戻り値は、値が任意である文字列です。 この文字列値は、クライアントがネーム サービスにサービスのアドレスを要求したときに表示されます。

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = string.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/",
                port);

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

    // the string returned here will be published in the Naming Service.
    return Task.FromResult(this.publishAddress);
}
```
```java
public CompletableFuture<String> openAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.getCodePackageActivationContext.getEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.getPort();

    this.publishAddress = String.format("http://%s:%d/", FabricRuntime.getNodeContext().getIpAddressOrFQDN(), port);

    this.webApp = new WebApp(port);
    this.webApp.start();

    /* the string returned here will be published in the Naming Service.
     */
    return CompletableFuture.completedFuture(this.publishAddress);
}
```

Service Fabric は、クライアントや他のサービスがこのアドレスをサービス名で要求するための API を提供します。 これが重要なのは、サービスのアドレスが静的でないためです。 リソースの分散と可用性のために、サービスはクラスター内を移動します。 これは、クライアントがサービスのリッスン アドレスを解決するためのメカニズムです。

> [!NOTE]
> 通信リスナーの記述方法の詳しいチュートリアルについては、C# の場合は「[OWIN 自己ホストによる Service Fabric Web API サービス](./service-fabric-reliable-services-communication-aspnetcore.md)」を参照してください。Java の場合は、独自の HTTP サーバー実装を記述することができます。 https://github.com/Azure-Samples/service-fabric-java-getting-started にある EchoServer アプリケーションの例を参照してください。
>
>

## <a name="communicating-with-a-service"></a>サービスとの通信
Reliable Services API は、サービスと通信するクライアントを作成するために、以下のライブラリを提供します。

### <a name="service-endpoint-resolution"></a>サービス エンドポイントの解決
サービスと通信するには、まず、通信するサービスのパーティションまたはインスタンスのエンドポイント アドレスを解決します。 `ServicePartitionResolver(C#) / FabricServicePartitionResolver(Java)` ユーティリティ クラスは、クライアントが実行時にサービスのエンドポイントを特定するのに役立つ基本プリミティブです。 Service Fabric の用語では、サービスのエンドポイントを特定するプロセスを *サービス エンドポイントの解決* と呼びます。

クラスター内のサービスに接続するには、既定の設定を使用して、ServicePartitionResolver を作成できます。 これは、ほとんどの場合に推奨される使用法です。

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();
```

別のクラスター内のサービスに接続するには、一連のクラスター ゲートウェイ エンドポイントを指定して ServicePartitionResolver を作成できます。 ゲートウェイ エンドポイントは、同じクラスターに接続するさまざまなエンドポイントであることに注意してください。 次に例を示します。

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

代わりに、`ServicePartitionResolver` には、内部で使用される `FabricClient` を作成するための関数を指定することもできます。

```csharp
public delegate FabricClient CreateFabricClientDelegate();
```
```java
public FabricServicePartitionResolver(CreateFabricClient createFabricClient) {
...
}

public interface CreateFabricClient {
    public FabricClient getFabricClient();
}
```

`FabricClient` は、Service Fabric クラスターに対するさまざまな管理操作のために、クラスターとの通信に使用されるオブジェクトです。 これは、サービス パーティション リゾルバーとクラスターとの対話を細かく制御する場合に役立ちます。 内部でキャッシュを実行する `FabricClient` は、通常、作成にコストがかかるため、`FabricClient` インスタンスをできるだけ再利用することが重要です。

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver(() -> new CreateFabricClientImpl());
```

Resolve メソッドは、サービスのアドレスまたはパーティション分割されたサービスのサービス パーティションのアドレスの取得に使用されます。

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();

ResolvedServicePartition partition =
    await resolver.ResolveAsync(new Uri("fabric:/MyApp/MyService"), new ServicePartitionKey(), cancellationToken);
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();

CompletableFuture<ResolvedServicePartition> partition =
    resolver.resolveAsync(new URI("fabric:/MyApp/MyService"), new ServicePartitionKey());
```

サービスのアドレスは ServicePartitionResolver を使用して簡単に解決できますが、解決されたアドレスを正しく使用できるようにするために必要な作業が増えます。 クライアントは、接続試行の失敗が、再試行可能な一時的なエラー (サービスが移動している、サービスが一時的に使用できないなど) によるものか、永続的なエラー (サービスが削除された、要求したリソースが存在しないなど) によるものかを検出する必要があります。 サービスのインスタンスまたはレプリカは、いくつかの理由によりノード間でいつでも移動できます。 ServicePartitionResolver によって解決されたサービスのアドレスは、クライアント コードが接続を試みた時点で古くなっている可能性があります。 その場合、クライアントはアドレスをもう一度解決する必要があります。 以前の `ResolvedServicePartition` を提供すると、リゾルバーはキャッシュされたアドレスを単に取得するのではなく、アドレスをもう一度解決する必要があります。

通常は、クライアント コードで ServicePartitionResolver を直接操作する必要はありません。 リゾルバーが作成されると、Reliable Services API の通信クライアント ファクトリに渡されます。 ファクトリでは、リゾルバーを内部で使用して、サービスとの通信に使用できるクライアント オブジェクトを生成します。

### <a name="communication-clients-and-factories"></a>通信クライアントと通信ファクトリ
通信ファクトリ ライブラリは、解決済みのサービス エンドポイントへの接続の再試行を容易にする、標準的なエラー処理再試行パターンを実装しています。 ファクトリ ライブラリが再試行メカニズムを提供し、開発者がエラー ハンドラーを提供します。

`ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)` は、Service Fabric サービスと通信できるクライアントを生成する通信クライアント ファクトリによって実装される基本インターフェイスを定義します。 CommunicationClientFactory の実装は、クライアントが通信しようとする Service Fabric サービスで使用される通信スタックによって異なります。 Reliable Services API は `CommunicationClientFactoryBase<TCommunicationClient>` を提供します。 これは、CommunicationClientFactory インターフェイスの基本実装を提供し、すべての通信スタックに共通するタスクを実行します (これらのタスクには、ServicePartitionResolver を使用した、サービス エンドポイントの特定も含まれます)。 クライアントは、通常 CommunicationClientFactoryBase 抽象クラスを実装して、通信スタック固有のロジックを処理します。

通信クライアントはアドレスを受け取り、そのアドレスを使用してサービスに接続します。 クライアントは必要なプロトコルを使用できます。

```csharp
public class MyCommunicationClient : ICommunicationClient
{
    public ResolvedServiceEndpoint Endpoint { get; set; }

    public string ListenerName { get; set; }

    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}
```
```java
public class MyCommunicationClient implements CommunicationClient {

    private ResolvedServicePartition resolvedServicePartition;
    private String listenerName;
    private ResolvedServiceEndpoint endPoint;

    /*
     * Getters and Setters
     */
}
```

クライアント ファクトリは、主に通信クライアントを作成する役割を担います。 HTTP クライアントなど、永続的な接続を維持しないクライアントの場合、ファクトリはクライアントを作成して返すだけで済みます。 一部のバイナリ プロトコルなど、永続的な接続を維持する他のプロトコルの場合、接続を再作成する必要があるかどうかを決定するために、ファクトリによる検証も必要です (`ValidateClient(string endpoint, MyCommunicationClient client)`)。  

```csharp
public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
    }
}
```
```java
public class MyCommunicationClientFactory extends CommunicationClientFactoryBase<MyCommunicationClient> {

    @Override
    protected boolean validateClient(MyCommunicationClient clientChannel) {
    }

    @Override
    protected boolean validateClient(String endpoint, MyCommunicationClient client) {
    }

    @Override
    protected CompletableFuture<MyCommunicationClient> createClientAsync(String endpoint) {
    }

    @Override
    protected void abortClient(MyCommunicationClient client) {
    }
}
```

最後に、例外ハンドラーは、例外が発生したときに実行するアクションを決定する役割を担います。 例外は、**再試行可能** と **再試行不可能** に分類されます。

* **再試行不可能な** 例外は、呼び出し元に単に再スローされます。
* **再試行可能** な例外は、さらに **一時的** と **非一時的** に分類されます。
  * **一時的な** 例外は、サービス エンドポイント アドレスを再度解決せずに再試行できる例外です。 一時的な例外には、一時的なネットワークの問題やサービスのエラー応答 (サービス エンドポイントのアドレスが存在しないことを示すエラー応答を除く) などがあります。
  * **非一時的な** 例外は、サービス エンドポイント アドレスを再度解決する必要がある例外です。 非一時的な例外には、サービス エンドポイントに到達できなかったことを示す (サービスが別のノードに移動したことを示す) 例外などがあります。

`TryHandleException` は、特定の例外について判断を下します。 特定の例外についてどのような判断を下せばよいか **わからない** 場合は、**false** を返します。 どのような判断を下せばよいか **わかっている** 場合は、それに応じて結果を設定し、**true** を返します。

```csharp
class MyExceptionHandler : IExceptionHandler
{
    public bool TryHandleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings, out ExceptionHandlingResult result)
    {
        // if exceptionInformation.Exception is known and is transient (can be retried without re-resolving)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, true, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;


        // if exceptionInformation.Exception is known and is not transient (indicates a new service endpoint address must be resolved)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, false, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;

        // if exceptionInformation.Exception is unknown (let the next IExceptionHandler attempt to handle it)
        result = null;
        return false;
    }
}
```
```java
public class MyExceptionHandler implements ExceptionHandler {

    @Override
    public ExceptionHandlingResult handleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings) {

        /* if exceptionInformation.getException() is known and is transient (can be retried without re-resolving)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), true, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;


        /* if exceptionInformation.getException() is known and is not transient (indicates a new service endpoint address must be resolved)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), false, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;

        /* if exceptionInformation.getException() is unknown (let the next ExceptionHandler attempt to handle it)
         */
        result = null;
        return false;

    }
}
```
### <a name="putting-it-all-together"></a>まとめ
通信プロトコルを中心に構築された `ICommunicationClient(C#) / CommunicationClient(Java)`、`ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)`、`IExceptionHandler(C#) / ExceptionHandler(Java)` を使用して、`ServicePartitionClient(C#) / FabricServicePartitionClient(Java)` はすべてをまとめてラップし、これらのコンポーネントを中心としたエラー処理とサービス パーティションのアドレス解決ループを提供します。

```csharp
private MyCommunicationClientFactory myCommunicationClientFactory;
private Uri myServiceUri;

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(async (client) =>
   {
      // Communicate with the service using the client.
   },
   CancellationToken.None);

```
```java
private MyCommunicationClientFactory myCommunicationClientFactory;
private URI myServiceUri;

FabricServicePartitionClient myServicePartitionClient = new FabricServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

CompletableFuture<?> result = myServicePartitionClient.invokeWithRetryAsync(client -> {
      /* Communicate with the service using the client.
       */
   });

```

## <a name="next-steps"></a>次のステップ
* [Reliable Services を使用した ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)
* [Reliable Services のリモート処理によるリモート プロシージャ コール](service-fabric-reliable-services-communication-remoting.md)
* [Reliable Services を使用した WCF 通信](service-fabric-reliable-services-communication-wcf.md)
