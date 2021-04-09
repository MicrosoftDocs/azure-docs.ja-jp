---
title: ASP.NET Core によるサービス通信
description: ステートレスおよびステートフルな Azure Service Fabric Reliable Services アプリケーションで ASP.NET Core を使用する方法について説明します。
ms.topic: conceptual
ms.date: 10/12/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: a125c6a1972b51f518175a4c69248119f71ada7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791596"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>Azure Service Fabric Reliable Services での ASP.NET Core

ASP.NET Core は、オープンソースでクロスプラットフォームのフレームワークです。 このフレームワークは、Web アプリ、IoT アプリ、モバイル バックエンドなど、クラウドベースのインターネットに接続されたアプリケーションを構築することを目的に設計されています。

この記事は、Service Fabric Reliable Services で ASP.NET Core サービスをホスティングするための詳細なガイドであり、**Microsoft.ServiceFabric.AspNetCore.** セットの NuGet パッケージを使います。

Service Fabric の ASP.NET Core の入門チュートリアルと、開発環境のセットアップ方法については、「[チュートリアル: ASP.NET Core Web API フロントエンド サービスとステートフルなバックエンド サービスを含むアプリケーションを作成およびデプロイする](service-fabric-tutorial-create-dotnet-app.md)」をご覧ください。

この記事の残りの部分では、読者が ASP.NET Core に慣れているものと想定しています。 そうでない場合は、[ASP.NET Core の基礎](/aspnet/core/fundamentals/index)に関するページをご覧ください。

## <a name="aspnet-core-in-the-service-fabric-environment"></a>Service Fabric 環境の ASP.NET Core

ASP.NET Core アプリと Service Fabric アプリは、.NET Core または完全な .NET Framework で実行できます。 Service Fabric では、ASP.NET Core を 2 つの方法で使用できます。
 - **ゲスト実行可能ファイルとしてホストする**。 この方法は、主に、コードを変更せずに Service Fabric 上で既存の ASP.NET Core アプリケーションを実行するために使います。
 - **リライアブル サービス内で実行する**。 この方法では、Service Fabric ランタイムとの統合がより密になり、ステートフルな ASP.NET Core サービスが可能になります。

この記事の残りの部分では、Service Fabric SDK に付属する ASP.NET Core 統合コンポーネントを使用して、リライアブル サービス内で ASP.NET Core を使用する方法について説明します。

## <a name="service-fabric-service-hosting"></a>Service Fabric サービスのホスティング

Service Fabric では、サービスの 1 つまたは複数のインスタンスやレプリカが "*サービス ホスト プロセス*" (サービス コードを実行する実行可能ファイル) で実行されます。 サービス作成者はサービス ホスト プロセスを所有し、Service Fabric はそれをアクティブ化して監視します。

従来の ASP.NET (MVC 5 まで) は、System.Web.dll を通じて IIS に厳密に結合されています。 ASP.NET Core は、Web サーバーと Web アプリケーションの分離を実現します。 この分離により、Web アプリケーションを異なる Web サーバー間で移植できます。 また、Web サーバーを "*セルフホステッド*" にできます。 つまり、IIS のような専用の Web サーバー ソフトウェアによって所有されるプロセスではなく、独自のプロセスで Web サーバーを起動することができます。

Service Fabric サービスと ASP.NET をゲスト実行可能ファイルとして結合するか、リライアブル サービス内で結合するには、サービス ホスト プロセス内で ASP.NET を起動できる必要があります。 ASP.NET Core の自己ホストにより、これを行うことができます。

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>リライアブル サービスでの ASP.NET Core のホスティング
通常、自己ホスト型の ASP.NET Core アプリケーションでは、`Program.cs` の `static void Main()` メソッドのように、アプリケーションのエントリ ポイントに WebHost が作成されます。 この場合、WebHost のライフサイクルはプロセスのライフサイクルに拘束されます。

![プロセスでの ASP.NET Core のホスティング][0]

ただし、アプリケーション エントリ ポイントはリライアブル サービス内で WebHost を作成する適切な場所はありません。 それは、アプリケーション エントリ ポイントは、あるサービスの種類のインスタンスを作成できるように、そのサービスの種類を Service Fabric ランタイムに登録するためだけに使われるからです。 WebHost は、リライアブル サービス自体内に作成する必要があります。 サービス ホスト プロセス内で、サービス インスタンスやレプリカは、複数のライフサイクルをたどることができます。 

リライアブル サービス インスタンスは、`StatelessService` または `StatefulService` から派生したサービス クラスによって表されます。 サービスの通信スタックは、サービス クラスの `ICommunicationListener` 実装に含まれています。 `Microsoft.ServiceFabric.AspNetCore.*` NuGet パッケージには、リライアブル サービス内で Kestrel または HTTP.sys 用の ASP.NET Core WebHost を開始および管理する `ICommunicationListener` の実装が含まれています。

![リライアブル サービスでの ASP.NET Core のホスティングの図][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListener
`Microsoft.ServiceFabric.AspNetCore.*` NuGet パッケージ内の Kestrel および HTTP.sys 用の `ICommunicationListener` の実装には、似た使用パターンがあります。 ただし、Web サーバーごとに固有の若干異なるアクションが実行されます。 

どちらの通信リスナーも、次の引数を受け取るコンストラクターを提供します。
 - **`ServiceContext serviceContext`** :これは、実行中のサービスに関する情報を含む `ServiceContext` オブジェクトです。
 - **`string endpointName`** :これは、ServiceManifest.xml での `Endpoint` 構成の名前です。 2 つの通信リスナーが異なるのは主にこの部分です。 HTTP.sys では `Endpoint` 構成が "*必要です*" が、Kestrel では必要ありません。
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`** :これは、`IWebHost` を作成して返すために実装するラムダです。 それにより、ASP.NET Core アプリケーションで通常行う方法で、`IWebHost` を構成することができます。 ラムダによって URL が提供されます。この URL は、使用した Service Fabric 統合オプションと提供した `Endpoint` 構成に応じて自動的に生成されます。 その後、その URL を変更したり、その URL を使って Web サーバーを起動したりできます。

## <a name="service-fabric-integration-middleware"></a>Service Fabric 統合ミドルウェア
`Microsoft.ServiceFabric.AspNetCore` NuGet パッケージには、Service Fabric 対応のミドルウェアを追加する `IWebHostBuilder` の `UseServiceFabricIntegration` 拡張メソッドが含まれています。 このミドルウェアでは、Service Fabric Naming Service に一意のサービス URL を登録するように、Kestrel または HTTP.sys の `ICommunicationListener` が構成されます。 その後、クライアント要求を検証して、クライアントが適切なサービスに接続していることが確認されます。 

このステップは、クライアントが誤って間違ったサービスに接続するのを防ぐために必要です。 それは、Service Fabric のような共有ホスト環境では、複数の Web アプリケーションが同一の物理マシンまたは仮想マシンで実行できますが、一意のホスト名を使わないためです。 このシナリオについては、次のセクションで詳しく説明します。

### <a name="a-case-of-mistaken-identity"></a>ID が誤っている場合
サービス レプリカは、プロトコルに関係なく、一意の "IP:ポート" の組み合わせでリッスンします。 "IP:ポート" エンドポイントでリッスンを開始したサービス レプリカでは、そのエンドポイント アドレスが Service Fabric Naming Service に報告されます。 そこでは、クライアントまたは他のサービスがそれを検出できます。 動的に割り当てられるアプリケーション ポートがサービスで使用されている場合、同じ物理マシンまたは仮想マシンに以前に存在していた別のサービスの同じ "IP:ポート" エンドポイントがサービス レプリカによって偶然使用される可能性があります。 これにより、クライアントが誤って不適切なサービスに接続する可能性があります。 このシナリオは、次の一連のイベントが発生した場合に発生する可能性があります。

 1. サービス A が、HTTP 経由で 10.0.0.1:30000 でリッスンする。 
 2. クライアントがサービス A を解決して、アドレス 10.0.0.1:30000 を取得する。
 3. サービス A が別のノードに移動する。
 4. サービス B が 10.0.0.1 に配置され、偶然に同じポート 30000 を使用する。
 5. クライアントが、キャッシュされているアドレス 10.0.0.1:30000 を使用してサービス A への接続を試みる。
 6. クライアントが、不適切なサービスに接続されていることを認識しないままサービス B に接続される。

これが原因で、診断するのが困難なバグが不規則に発生する場合があります。

### <a name="using-unique-service-urls"></a>一意のサービス URL の使用
これらのバグを防ぐため、サービスでは、一意の識別子を使用して Naming Service にエンドポイントを送信し、クライアント要求中にその一意の識別子を検証できます。 これは、悪意のないテナントの信頼できる環境におけるサービス間の協調アクションです。 それにより、悪意のあるテナント環境でセキュリティで保護されたサービスの認証が提供されるわけではありません。

信頼できる環境では、`UseServiceFabricIntegration` メソッドによって追加されたミドルウェアによって、Naming Service に送信されるアドレスに一意の識別子が自動的に追加されます。 それにより、各要求でその識別子が検証されます。 識別子が一致しない場合、ミドルウェアでは即座に HTTP 410 Gone 応答が返されます。

動的に割り当てられたポートを使用するサービスでは、このミドルウェアを使用する必要があります。

固定された一意のポートを使用するサービスでは、協調環境においてこの問題は発生しません。 通常、固定された一意のポートは、クライアント アプリケーションが接続するための一般的なポートを必要とする外部向けサービスに使用されます。 たとえば、ほとんどのインターネット接続 Web アプリケーションでは、Web ブラウザー接続にポート 80 または 443 が使用されます。 この場合は、一意の識別子を有効にしないでください。

次の図は、ミドルウェアを有効にした場合の要求フローを示しています。

![Service Fabric ASP.NET Core の統合][2]

Kestrel と HTTP.sys のどちらの `ICommunicationListener` の実装でも、まったく同じ方法でこのメカニズムが使用されます。 HTTP.sys では、基になる **HTTP.sys** ポート共有機能を使用して、一意の URL パスを基に要求を内部的に区別できますが、その機能は HTTP.sys の `ICommunicationListener` の実装では使用 "*されません*"。 それは、前述のシナリオで HTTP 503 および HTTP 404 エラー状態コードになるためです。 さらに、HTTP 503 と HTTP 404 は他のエラーを示すために一般的に使用されるため、クライアントでエラーの意味を判断するのが困難になります。 

このため、Kestrel と HTTP.sys どちらの `ICommunicationListener` の実装も、`UseServiceFabricIntegration` 拡張メソッドによって提供されるミドルウェアで標準化されます。 したがって、クライアントでは、HTTP 410 の応答に対するサービス エンドポイントの再解決アクションを実行することだけが必要です。

## <a name="httpsys-in-reliable-services"></a>Reliable Services での HTTP.sys
**Microsoft.ServiceFabric.AspNetCore.HttpSys** NuGet パッケージをインポートすることにより、Reliable Services で HTTP.sys を使用できます。 このパッケージには、`ICommunicationListener` の実装である `HttpSysCommunicationListener` が含まれます。 `HttpSysCommunicationListener` により、Web サーバーとして HTTP.sys を使って、リライアブル サービスの内部に ASP.NET Core WebHost を作成できます。

HTTP.sys は、[Windows HTTP Server API](/windows/win32/http/http-api-start-page) に基づいて構築されています。 この API では、**HTTP.sys** カーネル ドライバーを使って HTTP 要求が処理され、Web アプリケーションを実行するプロセスにルーティングされます。 これにより、同一の物理マシンまたは仮想マシン上の複数のプロセスが、一意の URL パスまたはホスト名によって明確化された Web アプリケーションを同じポート上でホストできます。 これらの機能は、Service Fabric で同じクラスター内の複数の Web サイトをホストする場合に役立ちます。

>[!NOTE]
>HTTP.sys の実装は、Windows プラットフォームでのみ動作します。

次の図では、HTTP.sys がポート共有のために Windows 上で **HTTP.sys** カーネル ドライバーを使用する方法を示します。

![HTTP.sys の図][3]

### <a name="httpsys-in-a-stateless-service"></a>ステートレス サービスでの HTTP.sys
ステートレス サービスで `HttpSys` を使用するには、`CreateServiceInstanceListeners` メソッドをオーバーライドして `HttpSysCommunicationListener` インスタンスを返します。

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseHttpSys()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build()))
    };
}
```

### <a name="httpsys-in-a-stateful-service"></a>ステートフル サービスでの HTTP.sys

`HttpSysCommunicationListener` は、基になる **HTTP.sys** ポート共有機能に関する複雑な要素が絡んでくるため、現時点ではステートフル サービス向けには設計されていません。 詳細については、HTTP.sys を使用した動的ポート割り当てに関する次のセクションを参照してください。 ステートフル サービスの場合、Web サーバーとして Kestrel が推奨されます。

### <a name="endpoint-configuration"></a>Endpoint configuration (エンドポイントの構成)

HTTP.sys を含む Windows HTTP Server API を使用する Web サーバーには、`Endpoint` 構成が必要です。 Windows HTTP Server API を使用する Web サーバーでは、最初に HTTP.sys で URL を予約する必要があります (通常、この操作は [netsh](/windows/win32/http/netsh-commands-for-http) ツールを使用して実行します)。 

このアクションには、サービスに既定では付与されていない昇格された特権が必要です。 ServiceManifest.xml の `Endpoint` 構成の `Protocol` プロパティの "http" または "https" オプションは、ユーザーの代わりに HTTP.sys に URL を登録するよう Service Fabric ランタイムに明確に指示するために使用されます。 それは、[*強力なワイルドカード*](/windows/win32/http/urlprefix-strings) URL プレフィックスを使用して行われます。

たとえば、サービス用に `http://+:80` を予約するには、ServiceManifest.xml で次の構成を使用します。

```xml
<ServiceManifest ... >
    ...
    <Resources>
        <Endpoints>
            <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>

</ServiceManifest>
```

また、エンドポイント名は `HttpSysCommunicationListener` コンストラクターに渡す必要があります。

```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseHttpSys()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-httpsys-with-a-static-port"></a>静的ポートで HTTP.sys を使用する
HTTP.sys で静的ポートを使用するには、`Endpoint` 構成でポート番号を指定します。

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>動的ポートで HTTP.sys を使用する
HTTP.sys で動的に割り当てられたポートを使用するには、`Endpoint` 構成の `Port` プロパティを省略します。

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

`Endpoint` 構成によって割り当てられた動的ポートでは、"*ホスト プロセスごとに*" 1 つのポートのみが提供されます。 現在の Service Fabric ホスティング モデルは、複数のサービス インスタンスやレプリカを、同じプロセスでホストできます。 つまり、`Endpoint` 構成によって割り当てられると、それぞれで同じポートが共有されます。 基になる **HTTP.sys** ポート共有機能を使用して、複数の **HTTP.sys** インスタンスで 1 つのポートを共有できます。 ただし、`HttpSysCommunicationListener` の場合は、クライアント要求にもたらされる複雑さのためサポートされません。 ポートを動的に使用する場合、Web サーバーとして Kestrel が推奨されます。

## <a name="kestrel-in-reliable-services"></a>リライアブル サービスでの Kestrel
**Microsoft.ServiceFabric.AspNetCore.Kestrel** NuGet パッケージをインポートすることにより、Reliable Services で Kestrel を使用できます。 このパッケージには、`ICommunicationListener` の実装である `KestrelCommunicationListener` が含まれます。 `KestrelCommunicationListener` により、Web サーバーとして Kestrel を使って、リライアブル サービスの内部に ASP.NET Core WebHost を作成できます。

Kestrel は、ASP.NET Core 用のクロスプラットフォーム Web サーバーです。 HTTP.sys とは異なり、Kestrel では一元的なエンドポイント マネージャーは使用されません。 また、HTTP.sys とは異なり、Kestrel では複数のプロセス間のポート共有はサポートされません。 Kestrel の各インスタンスは、一意のポートを使用する必要があります。 Kestrel の詳細については、[実装の詳細 ](/aspnet/core/fundamentals/servers/kestrel)に関する記事を参照してください。

![Kestrel の図][4]

### <a name="kestrel-in-a-stateless-service"></a>ステートレス サービスでの Kestrel
ステートレス サービスで `Kestrel` を使用するには、`CreateServiceInstanceListeners` メソッドをオーバーライドして `KestrelCommunicationListener` インスタンスを返します。

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

### <a name="kestrel-in-a-stateful-service"></a>ステートフル サービスでの Kestrel
ステートフル サービスで `Kestrel` を使用するには、`CreateServiceReplicaListeners` メソッドをオーバーライドして `KestrelCommunicationListener` インスタンスを返します。

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new ServiceReplicaListener[]
    {
        new ServiceReplicaListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                         services => services
                             .AddSingleton<StatefulServiceContext>(serviceContext)
                             .AddSingleton<IReliableStateManager>(this.StateManager))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

この例では、`IReliableStateManager` のシングルトン インスタンスが WebHost 依存関係挿入コンテナーに提供されています。 これは厳密には必要ではありませんが、MVC コントローラーのアクション メソッドで `IReliableStateManager` と Reliable Collection を使用することができます。

`Endpoint` 構成名はステートフル サービスの `KestrelCommunicationListener` に提供 *されません*。 これについては、次のセクションで詳しく説明します。

### <a name="configure-kestrel-to-use-https"></a>HTTPS を使用するように Kestrel を構成する
サービスの Kestrel で HTTPS を有効にするときは、複数のリッスン オプションを設定する必要があります。 *EndpointHttps* エンドポイントを使用し、特定のポート (ポート 443 など) でリッスンするように、`ServiceInstanceListener` を更新します。 Kestrel Web サーバーを使用するよう Web ホストを構成するときは、すべてのネットワーク インターフェイスで IPv6 アドレスをリッスンするように Kestrel を構成する必要があります。 

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(GetCertificateFromStore());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

チュートリアルの例の完全なものについては、「[HTTPS を使用するように Kestrel を構成する](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https)」をご覧ください。


### <a name="endpoint-configuration"></a>Endpoint configuration (エンドポイントの構成)
Kestrel を使用するうえで `Endpoint` 構成は必要ありません。 

Kestrel は単純なスタンドアロンの Web サーバーです。 HTTP.sys (または HttpListener) とは異なり、開始する前に URL を登録する必要がないため、ServiceManifest.xml の `Endpoint` 構成は必要ありません。 

#### <a name="use-kestrel-with-a-static-port"></a>Kestrel での静的ポートの使用
ServiceManifest.xml の `Endpoint` 構成において、Kestrel で使用する静的ポートを構成できます。 これは厳密には必要ではありませんが、2 つの潜在的な利点があります。
 - ポートがアプリケーションのポート範囲にない場合、Service Fabric によって OS のファイアウォールを介してポートが開かれます。
 - `KestrelCommunicationListener` を介して提供される URL では、このポートが使用されます。

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

`Endpoint` が構成されている場合は、その名前を `KestrelCommunicationListener` コンストラクターに渡す必要があります。 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

ServiceManifest.xml で `Endpoint` 構成を使わない場合は、`KestrelCommunicationListener` コンストラクター内で名前を省略します。 この場合、動的ポートが使用されます。 この詳細については、次のセクションを参照してください。

#### <a name="use-kestrel-with-a-dynamic-port"></a>Kestrel での動的ポートの使用
Kestrel では、ServiceManifest.xml での `Endpoint` 構成からの自動ポート割り当てを使用できません。 それは、`Endpoint` 構成からの自動ポート割り当てでは "*ホスト プロセス*" ごとに一意のポートが割り当てられ、単一のホスト プロセスが複数の Kestrel インスタンスを含むことができるためです。 Kestrel では、ポート共有がサポートされていないため、これは動作しません。 そのため、各 Kestrel インスタンスを固有のポートで開く必要があります。

Kestrel で動的ポート割り当てを使用するには、ServiceManifest.xml の `Endpoint` 構成を省略し、次のように、エンドポイント名を `KestrelCommunicationListener` コンストラクターに渡さないようにします。

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

この構成では、`KestrelCommunicationListener` により、アプリケーション ポートの範囲から未使用のポートが自動的に選択されます。

HTTPS では、ServiceManifest.xml へのポートの指定なしで HTTPS プロトコルでエンドポイントを構成し、エンドポイント名を KestrelCommunicationListener コンストラクターに渡します。


## <a name="service-fabric-configuration-provider"></a>Service Fabric 構成プロバイダー
ASP.NET Core でのアプリの構成は、構成プロバイダーによって設定されるキーと値のペアに基づきます。 一般的な ASP.NET Core 構成のサポートの詳細については、「[ASP.NET Core の構成](/aspnet/core/fundamentals/configuration/)」を参照してください。

このセクションでは、`Microsoft.ServiceFabric.AspNetCore.Configuration` NuGet パッケージをインポートして、ASP.NET Core の構成に Service Fabric 構成プロバイダーを統合する方法について説明します。

### <a name="addservicefabricconfiguration-startup-extensions"></a>AddServiceFabricConfiguration 起動の拡張機能
`Microsoft.ServiceFabric.AspNetCore.Configuration` NuGet パッケージをインポートした後、ASP.NET Core 構成 API に Service Fabric 構成ソースを登録する必要があります。 これを行うには、`IConfigurationBuilder` に対して `Microsoft.ServiceFabric.AspNetCore.Configuration` 名前空間で **AddServiceFabricConfiguration** 拡張機能をチェックします。

```csharp
using Microsoft.ServiceFabric.AspNetCore.Configuration;

public Startup(IHostingEnvironment env)
{
    var builder = new ConfigurationBuilder()
        .SetBasePath(env.ContentRootPath)
        .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
        .AddServiceFabricConfiguration() // Add Service Fabric configuration settings.
        .AddEnvironmentVariables();
    Configuration = builder.Build();
}

public IConfigurationRoot Configuration { get; }
```

これで、他のアプリケーション設定と同様に ASP.NET Core サービスが Service Fabric 構成設定にアクセスできるようになります。 たとえば、このオプションのパターンを使用すると、厳密に型指定されたオブジェクトに設定を読み込むことができます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>既定のキー マッピング
既定では、Service Fabric 構成プロバイダーには、パッケージ名、セクション名、およびプロパティ名が含まれます。 これらにより、次のように、ASP.NET Core 構成キーが形成されます。
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

たとえば、次の内容の `MyConfigPackage` という名前の構成パッケージがある場合、構成値は ASP.NET Core `IConfiguration` で、*MyConfigPackage:MyConfigSection:MyParameter* を介して使用可能になります。
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Service Fabric の構成オプション
Service Fabric 構成プロバイダーでは、キー マッピングの既定の動作を変更する `ServiceFabricConfigurationOptions` もサポートされています。

#### <a name="encrypted-settings"></a>暗号化された設定
Service Fabric では暗号化された設定がサポートされており、Service Fabric 構成プロバイダーでもサポートされています。 暗号化された設定は、既定では ASP.NET Core `IConfiguration` に復号化されません。 代わりに暗号化された値がそこに保存されます。 ただし、ASP.NET Core IConfiguration に格納する値を暗号化解除したい場合、次のように `AddServiceFabricConfiguration` 拡張機能で *DecryptValue* フラグを false に設定できます。

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = false); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>複数の構成パッケージ
Service Fabric では複数の構成パッケージをサポートしています。 既定では、パッケージ名は構成キーに含まれます。 ただし、次のように `IncludePackageName` フラグを false に設定することができます。
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        // exclude package name from key.
        .AddServiceFabricConfiguration(activationContext, (options) => options.IncludePackageName = false); 
    Configuration = builder.Build();
}
```
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>カスタム キー マッピング、値の抽出、およびデータの設定
Service Fabric 構成プロバイダーでは、`ExtractKeyFunc` によるキー マッピングのカスタマイズや、`ExtractValueFunc` による値のカスタム抽出など、より高度なシナリオもサポートされています。 Service Fabric 構成から ASP.NET Core 構成にデータを設定するプロセス全体を、`ConfigAction` を使用して変更することもできます。

次の例では、`ConfigAction` を使用してデータ設定をカスタマイズする方法を示します。
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    
    this.valueCount = 0;
    this.sectionCount = 0;
    var builder = new ConfigurationBuilder();
    builder.AddServiceFabricConfiguration(activationContext, (options) =>
        {
            options.ConfigAction = (package, configData) =>
            {
                ILogger logger = new ConsoleLogger("Test", null, false);
                logger.LogInformation($"Config Update for package {package.Path} started");

                foreach (var section in package.Settings.Sections)
                {
                    this.sectionCount++;

                    foreach (var param in section.Parameters)
                    {
                        configData[options.ExtractKeyFunc(section, param)] = options.ExtractValueFunc(section, param);
                        this.valueCount++;
                    }
                }

                logger.LogInformation($"Config Update for package {package.Path} finished");
            };
        });
  Configuration = builder.Build();
}
```

### <a name="configuration-updates"></a>構成の更新
Service Fabric の構成プロバイダーでは、構成の更新もサポートされています。 ASP.NET Core の `IOptionsMonitor` を使って変更通知を受け取った後、`IOptionsSnapshot` を使って構成データを再度読み込むことができます。 詳細については、[ASP.NET Core のオプション](/aspnet/core/fundamentals/configuration/options)に関するページを参照してください。

これらのオプションは既定でサポートされています。 構成の更新を有効にするために、さらにコーディングする必要はありません。

## <a name="scenarios-and-configurations"></a>シナリオと構成
このセクションでは、以下のシナリオをトラブルシューティングするために推奨される、Web サーバー、ポート構成、Service Fabric 統合オプション、およびその他の設定の組み合わせを示します。
 - 外部に公開される ASP.NET Core ステートレス サービス
 - 内部専用の ASP.NET Core ステートレス サービス
 - 内部専用の ASP.NET Core ステートフル サービス

**外部に公開されるサービス** とは、通常はロード バランサーを介してクラスターの外部から呼び出されるエンドポイントを公開するサービスです。

**内部専用** のサービスとは、クラスター内からのみエンドポイントを呼び出すことができるサービスです。

> [!NOTE]
> ステートフル サービス エンドポイントは、通常、インターネットに公開されません。 Azure Load Balancer など、Service Fabric のサービス解決に対応していないロード バランサーの背後にあるクラスターでは、ステートフル サービスを公開できません。 それは、ロード バランサーで適切なステートフル サービス レプリカを特定してトラフィックをルーティングできないためです。 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>外部に公開される ASP.NET Core ステートレス サービス
外部のインターネットに接続された HTTP エンドポイントを公開するフロントエンド サービス用の Web サーバーとして、Kestrel をお勧めします。 Windows では、HTTP.sys で提供できるポート共有機能により、同じポートを使用して同じノードのセットで複数の Web サービスをホストすることができます。 このシナリオでは、Web サービスはホスト名またはパスによって区別され、HTTP ルーティングを提供するためにフロントエンド プロキシまたはゲートウェイに依存しません。
 
インターネットに公開されるステートレス サービスでは、ロード バランサーを介して到達できる、安定した一般的なエンドポイントを使用する必要があります。 アプリケーションのユーザーにこの URL を提供します。 次の構成をお勧めします。

| Type | 推奨 | Notes |
| ---- | -------------- | ----- |
| Web サーバー | Kestrel | Kestrel は Windows と Linux でサポートされているため、好まれている Web サーバーです。 |
| ポート構成 | 静的 | 一般的な静的ポートを ServiceManifest.xml の `Endpoints` 構成で構成する必要があります (HTTP の場合は 80、HTTPS の場合は 443 など)。 |
| ServiceFabricIntegrationOptions | なし | Service Fabric 統合ミドルウェアを構成するときに `ServiceFabricIntegrationOptions.None` オプションを使用し、サービスによって受信要求で一意の識別子が検証されないようにします。 アプリケーションの外部ユーザーは、ミドルウェアで使用される一意の識別情報を知りません。 |
| Instance Count | -1 | 一般的なユース ケースでは、インスタンス数の設定を *-1* に設定する必要があります。 このようにするのは、ロード バランサーからトラフィックを受信するすべてのノードでインスタンスを使用できるようにするためです。 |

外部に公開される複数のサービスで同じノードのセットを共有する場合は、URL パスが一意でありながら安定している HTTP.sys を使用できます。 これは、IWebHost の構成時に提供される URL を変更することで実現できます。 これは HTTP.sys にのみ適用されることに注意してください。

 ```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseHttpSys()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>内部専用のステートレス ASP.NET Core サービス
クラスター内からのみ呼び出されるステートレス サービスでは、複数のサービス間の協調動作を保証するために、一意の URL と動的に割り当てられたポートを使用する必要があります。 次の構成をお勧めします。

| Type | 推奨 | Notes |
| ---- | -------------- | ----- |
| Web サーバー | Kestrel | 内部のステートレス サービスに HTTP.sys を使用することもできますが、サーバーで複数のサービス インスタンスがホストを共有できるようにするには、Kestrel が推奨されます。  |
| ポート構成 | 動的割り当て | ステートフル サービスの複数のレプリカでは、ホスト プロセスまたはホスト オペレーティング システムを共有できるため、一意のポートが必要になります。 |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | 動的なポート割り当てでこの設定を行うことにより、前述の誤った ID に関する問題を防ぐことができます。 |
| InstanceCount | any | インスタンス数の設定は、サービスの実行に必要な任意の値に設定することができます。 |

### <a name="internal-only-stateful-aspnet-core-service"></a>内部専用のステートフル ASP.NET Core サービス
クラスター内からのみ呼び出されるステートフル サービスでは、複数のサービス間の協調動作を保証するために、動的に割り当てられたポートを使用する必要があります。 次の構成をお勧めします。

| Type | 推奨 | Notes |
| ---- | -------------- | ----- |
| Web サーバー | Kestrel | `HttpSysCommunicationListener` は、レプリカがホスト プロセスを共有するステートフル サービス向けに設計されていません。 |
| ポート構成 | 動的割り当て | ステートフル サービスの複数のレプリカでは、ホスト プロセスまたはホスト オペレーティング システムを共有できるため、一意のポートが必要になります。 |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | 動的なポート割り当てでこの設定を行うことにより、前述の誤った ID に関する問題を防ぐことができます。 |

## <a name="next-steps"></a>次のステップ
[Visual Studio による Service Fabric アプリケーションのデバッグ](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
