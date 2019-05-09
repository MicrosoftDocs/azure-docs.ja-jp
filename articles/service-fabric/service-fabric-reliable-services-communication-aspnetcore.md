---
title: ASP.NET Core によるサービス通信 | Microsoft Docs
description: ステートレス リライアブル サービスおよびステートフル リライアブル サービスで ASP.NET Core を使用する方法について説明します。
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 8aa4668d-cbb6-4225-bd2d-ab5925a868f2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 5a4b7514005da9e9a998dba014fa0ea6c014397a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59268519"
---
# <a name="aspnet-core-in-service-fabric-reliable-services"></a>Service Fabric リライアブル サービスでの ASP.NET Core

ASP.NET Core は新しいオープンソースのクロスプラットフォーム フレームワークであり、Web アプリ、IoT アプリ、モバイル バックエンドなど、最新のクラウドベースのインターネット接続アプリケーションを構築するために使用されます。 

この記事は、NuGet パッケージの **Microsoft.ServiceFabric.AspNetCore.*** セットを使用して、Service Fabric Reliable Services で ASP.NET Core サービスをホスティングするための詳細なガイドです。

Service Fabric の ASP.NET Core の入門チュートリアルと、開発環境のセットアップ方法については、[.NET アプリケーションの作成](service-fabric-tutorial-create-dotnet-app.md)に関するページを参照してください。

この記事の残りの部分では、読者が ASP.NET Core に慣れているものと想定しています。 慣れていない場合は、[ASP.NET Core の基礎](https://docs.microsoft.com/aspnet/core/fundamentals/index)に関するページを読むことをお勧めします。

## <a name="aspnet-core-in-the-service-fabric-environment"></a>Service Fabric 環境の ASP.NET Core

ASP.NET Core アプリと Service Fabric アプリは .NET Core および完全な .NET Framework で実行できます。 ASP.NET Core は、Service Fabric で 2 とおりの方法で使用できます。
 - **ゲスト実行可能ファイルとしてホストする**。 この方法は、主に、コードの変更なしで Service Fabric 上で既存の ASP.NET Core アプリケーションを実行するために使用します。
 - **リライアブル サービス**内で実行する。 Service Fabric ランタイムとの統合がより密になり、ステートフルな ASP.NET Core サービスが可能になります。

この記事の残りの部分では、Service Fabric SDK に付属する ASP.NET Core 統合コンポーネントを使用して、リライアブル サービス内で ASP.NET Core を使用する方法について説明します。 

## <a name="service-fabric-service-hosting"></a>Service Fabric サービスのホスティング

Service Fabric では、サービスの 1 つまたは複数のインスタンスやレプリカが "*サービス ホスト プロセス*" (サービス コードを実行する実行可能ファイル) で実行されます。 サービス作成者はサービス ホスト プロセスを所有し、Service Fabric はそれをアクティブ化して監視します。

従来の ASP.NET (MVC 5 まで) は、System.Web.dll を通じて IIS に厳密に結合されています。 ASP.NET Core は、Web サーバーと Web アプリケーションの分離を実現します。 これにより、Web アプリケーションをさまざまな Web サーバー間で移植することができます。また、Web サーバーを "*自己ホスト*" することもできます。つまり、IIS などの専用 Web サーバー ソフトウェアが所有するプロセスではなく、自分のプロセス内で Web サーバーを起動できます。 

Service Fabric サービスと ASP.NET をゲスト実行可能ファイルとして結合するか、リライアブル サービス内で結合するには、サービス ホスト プロセス内で ASP.NET を起動できる必要があります。 ASP.NET Core の自己ホストにより、これを行うことができます。

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>リライアブル サービスでの ASP.NET Core のホスティング
通常、自己ホスト型の ASP.NET Core アプリケーションでは、`Program.cs` の `static void Main()` メソッドのように、アプリケーションのエントリ ポイントに WebHost が作成されます。 この場合、WebHost のライフサイクルはプロセスのライフサイクルに拘束されます。

![プロセスでの ASP.NET Core のホスティング][0]

ただし、アプリケーションのエントリ ポイントは、リライアブル サービスに WebHost を作成する場所として適しません。それは、アプリケーションのエントリ ポイントは、Service Fabric ランタイムがそのサービス タイプのインスタンスを作成できるように、サービス タイプをランタイムに登録するためにのみ使用されるからです。 WebHost は、リライアブル サービス自体に作成する必要があります。 サービス ホスト プロセス内で、サービス インスタンスやレプリカは、複数のライフサイクルをたどることができます。 

リライアブル サービス インスタンスは、`StatelessService` または `StatefulService` から派生したサービス クラスによって表されます。 サービスの通信スタックは、サービス クラスの `ICommunicationListener` 実装に含まれています。 `Microsoft.ServiceFabric.AspNetCore.*` NuGet パッケージには、リライアブル サービスで Kestrel または HttpSys の ASP.NET Core WebHost を開始および管理する `ICommunicationListener` の実装が含まれています。

![リライアブル サービスでの ASP.NET Core のホスティング][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListener
`Microsoft.ServiceFabric.AspNetCore.*` NuGet パッケージの Kestrel と HttpSys の `ICommunicationListener` 実装は使用パターンが類似していますが、実行されるアクションは各 Web サーバーに応じて多少異なります。 

どちらの通信リスナーも、次の引数を受け取るコンストラクターを提供します。
 - **`ServiceContext serviceContext`**:実行中のサービスに関する情報を含む `ServiceContext` オブジェクト。
 - **`string endpointName`**: ServiceManifest.xml の `Endpoint` 構成の名前。 2 つの通信リスナーが異なるのは主にこの部分です。HttpSys では `Endpoint` 構成が**必要**ですが、Kestrel では必要ありません。
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: `IWebHost` を作成して返すために実装するラムダ。 これにより、ASP.NET Core アプリケーションで通常行う方法で `IWebHost` を構成することができます。 ラムダによって URL が提供されます。この URL は、使用した Service Fabric 統合オプションと提供した `Endpoint` 構成に応じて生成されます。 Web サーバーを起動するために、この URL を変更することも、そのまま使用することもできます。

## <a name="service-fabric-integration-middleware"></a>Service Fabric 統合ミドルウェア
`Microsoft.ServiceFabric.AspNetCore` NuGet パッケージには、Service Fabric 対応のミドルウェアを追加する `IWebHostBuilder` の `UseServiceFabricIntegration` 拡張メソッドが含まれています。 このミドルウェアは、Service Fabric Naming Service に一意のサービス URL を登録するように Kestrel または HttpSys `ICommunicationListener` を構成し、次にクライアント要求を検証して、クライアントが適切なサービスに接続していることを確認します。 これは、複数の Web アプリケーションが同一の物理マシンまたは仮想マシン上で実行できる一方で一意のホスト名が使用されない Service Fabric などの共有ホスト環境において、クライアントが不適切なサービスに誤って接続するのを防ぐために必要です。 このシナリオについては、次のセクションで詳しく説明します。

### <a name="a-case-of-mistaken-identity"></a>ID が誤っている場合
サービス レプリカは、プロトコルに関係なく、一意の "IP:ポート" の組み合わせでリッスンします。 "IP:ポート" エンドポイントでリッスンを開始したサービス レプリカは、クライアントまたはその他のサービスから検出できるように、そのエンドポイント アドレスを Service Fabric Naming Service に報告します。 動的に割り当てられるアプリケーション ポートがサービスで使用されている場合、同じ物理マシンまたは仮想マシンに以前に存在していた別のサービスの同じ "IP:ポート" エンドポイントがサービス レプリカによって偶然使用される可能性があります。 これにより、クライアントが誤って不適切なサービスに接続する可能性があります。 これは、次の一連のイベントが発生した場合に発生します。

 1. サービス A が、HTTP 経由で 10.0.0.1:30000 でリッスンする。 
 2. クライアントがサービス A を解決して、アドレス 10.0.0.1:30000 を取得する。
 3. サービス A が別のノードに移動する。
 4. サービス B が 10.0.0.1 に配置され、偶然に同じポート 30000 を使用する。
 5. クライアントが、キャッシュされているアドレス 10.0.0.1:30000 を使用してサービス A への接続を試みる。
 6. クライアントが、不適切なサービスに接続されていることを認識しないままサービス B に接続される。

これが原因で、診断するのが困難なバグが不規則に発生する場合があります。 

### <a name="using-unique-service-urls"></a>一意のサービス URL の使用
これを防ぐために、サービスは、一意の識別子を使用して Naming Service にエンドポイントを送信し、クライアント要求中にその一意の識別子を検証できます。 これは、悪意のないテナントの信頼できる環境におけるサービス間の協調アクションです。 これにより、悪意のあるテナント環境でセキュリティで保護されたサービスの認証が提供されるわけではありません。

信頼できる環境では、`UseServiceFabricIntegration` メソッドによって追加されたミドルウェアによって、Naming Service に送信されるアドレスに一意の識別子が自動的に追加され、各要求でその識別子が検証されます。 識別子が一致しない場合、ミドルウェアは即座に HTTP 410 Gone 応答を返します。

動的に割り当てられたポートを使用するサービスでは、このミドルウェアを使用する必要があります。

固定された一意のポートを使用するサービスでは、協調環境においてこの問題は発生しません。 通常、固定された一意のポートは、クライアント アプリケーションが接続するための一般的なポートを必要とする外部向けサービスに使用されます。 たとえば、ほとんどのインターネット接続 Web アプリケーションでは、Web ブラウザー接続にポート 80 または 443 が使用されます。 この場合は、一意の識別子を有効にしないでください。

次の図は、ミドルウェアを有効にした場合の要求フローを示しています。

![Service Fabric ASP.NET Core の統合][2]

Kestrel と HttpSys のどちらの `ICommunicationListener` の実装でも、まったく同じ方法でこのメカニズムが使用されます。 HttpSys では、基になる *http.sys* ポート共有機能を使用して、一意の URL パスを基に要求を内部的に区別できます。しかし、この機能は HttpSys `ICommunicationListener` 実装では使用 "*されません*"。前述のシナリオで HTTP 503 および HTTP 404 エラー状態コードが返される結果になるためです。 さらに、HTTP 503 と HTTP 404 は既に他のエラーを示すために一般的に使用されているため、クライアントがエラーの意味を判断するのが困難になります。 このように、Kestrel と HttpSys のどちらの `ICommunicationListener` 実装でも `UseServiceFabricIntegration` 拡張メソッドによって提供されるミドルウェアが標準となるため、クライアントで実行する必要があるのは HTTP 410 の応答に対するサービス エンドポイントの再解決アクションのみになります。

## <a name="httpsys-in-reliable-services"></a>Reliable Services での HttpSys
HttpSys は、**Microsoft.ServiceFabric.AspNetCore.HttpSys** NuGet パッケージをインポートすることにより、リライアブル サービスで使用できます。 このパッケージには、HttpSys を Web サーバーとして使用してリライアブル サービス内に ASP.NET Core WebHost を作成できるようにする `HttpSysCommunicationListener` (`ICommunicationListener` の実装) が含まれています。

HttpSys は、[Windows HTTP Server API](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx) に基づいて構築されています。 これにより、IIS で使用される *http.sys* カーネル ドライバーを使用して HTTP 要求が処理され、Web アプリケーションを実行しているプロセスにルーティングされます。 これにより、同一の物理マシンまたは仮想マシン上の複数のプロセスが、一意の URL パスまたはホスト名によって明確化された Web アプリケーションを同じポート上でホストできます。 これらの機能は、Service Fabric で同じクラスター内の複数の Web サイトをホストする場合に役立ちます。

>[!NOTE]
>HttpSys の実装は、Windows プラットフォームでのみ動作します。

次の図は、HttpSys がポート共有のために Windows 上で *http.sys* カーネル ドライバーを使用する方法を示しています。

![http.sys][3]

### <a name="httpsys-in-a-stateless-service"></a>ステートレス サービスでの HttpSys
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

### <a name="httpsys-in-a-stateful-service"></a>ステートフル サービスでの HttpSys

`HttpSysCommunicationListener` は、基になる *http.sys* ポート共有機能に関する複雑な要素が絡んでくるため、現時点ではステートフル サービス向けには設計されていません。 詳細については、HttpSys を使用した動的ポート割り当てに関する次のセクションを参照してください。 ステートフル サービスの場合、Web サーバーとして Kestrel が推奨されます。

### <a name="endpoint-configuration"></a>Endpoint configuration (エンドポイントの構成)

HttpSys を含む Windows HTTP Server API を使用する Web サーバーには、`Endpoint` 構成が必要です。 Windows HTTP Server API を使用する Web サーバーでは、最初に *http.sys* で URL を予約する必要があります (通常、この操作は [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) ツールを使用して実行します)。 このアクションには、サービスに既定では付与されていない昇格された特権が必要です。 *ServiceManifest.xml* の `Endpoint` 構成の `Protocol` プロパティの "http" または "https" オプションは、"[*強力なワイルドカード*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx)" URL プレフィックスを使用して、ユーザーの代わりに *http.sys* に URL を登録するよう Service Fabric ランタイムに明確に指示するために使用します。

たとえば、サービス用に `http://+:80` を予約するには、ServiceManifest.xml で次の構成を使用する必要があります。

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

#### <a name="use-httpsys-with-a-static-port"></a>HttpSys での静的ポートの使用
HttpSys で静的ポートを使用するには、`Endpoint` 構成でポート番号を指定します。

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>HttpSys での動的ポートの使用
HttpSys で動的に割り当てられたポートを使用するには、`Endpoint` 構成の `Port` プロパティを省略します。

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

`Endpoint` 構成によって割り当てられる動的ポートは、"*ホスト プロセスごとに*" 1 つのポートのみを提供します。 現在の Service Fabric ホスティング モデルでは、複数のサービス インスタンスやレプリカを同じプロセスでホストすることができます。つまり、`Endpoint` 構成によって割り当てられたときにそれぞれが同じポートを共有します。 複数の HttpSys インスタンスは基になる *http.sys* ポート共有機能を使用してポートを共有できますが、クライアント要求に関する複雑な要素が絡んでくるため、`HttpSysCommunicationListener` ではこの機能がサポートされません。 ポートを動的に使用する場合、Web サーバーとして Kestrel が推奨されます。

## <a name="kestrel-in-reliable-services"></a>リライアブル サービスでの Kestrel
Kestrel は、**Microsoft.ServiceFabric.AspNetCore.Kestrel** NuGet パッケージをインポートすることにより、リライアブル サービスで使用できます。 このパッケージには、Kestrel を Web サーバーとして使用してリライアブル サービス内に ASP.NET Core WebHost を作成できるようにする `KestrelCommunicationListener` (`ICommunicationListener` の実装) が含まれています。

Kestrel は、クロスプラットフォームの非同期 I/O ライブラリである libuv をベースにした ASP.NET Core 用のクロスプラットフォーム Web サーバーです。 HttpSys とは異なり、Kestrel では *http.sys* などの一元的なエンドポイント マネージャーは使用されません。 また、HttpSys とは異なり、Kestrel では複数のプロセス間のポート共有がサポートされません。 Kestrel の各インスタンスは、一意のポートを使用する必要があります。

![Kestrel][4]

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

`Endpoint` 構成名はステートフル サービスの `KestrelCommunicationListener` に提供**されません**。 これについては、次のセクションで詳しく説明します。

### <a name="configure-kestrel-to-use-https"></a>HTTPS を使用するように Kestrel を構成する
サービスの Kestrel で HTTPS を有効にするときは、複数のリッスン オプションを設定する必要があります。  EndpointHttps エンドポイントを使用し、特定のポート (ポート 443 など) でリッスンするように、`ServiceInstanceListener` を更新します。 Kestrel サーバーを使用するよう Web ホストを構成するときは、すべてのネットワーク インターフェイスで IPv6 アドレスをリッスンするように Kestrel を構成する必要があります。 

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

チュートリアルで使用されている例の完全なものについては、「[HTTPS を使用するように Kestrel を構成する](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https)」をご覧ください。


### <a name="endpoint-configuration"></a>Endpoint configuration (エンドポイントの構成)
Kestrel を使用するうえで `Endpoint` 構成は必要ありません。 

Kestrel は、単純なスタンドアロンの Web サーバーです。HttpSys (または HttpListener) とは異なり、Kestrel では起動の前に URL を登録する必要がないため、*ServiceManifest.xml* に `Endpoint` 構成は必要ありません。 

#### <a name="use-kestrel-with-a-static-port"></a>Kestrel での静的ポートの使用
Kestrel で使用する静的ポートは、ServiceManifest.xml の `Endpoint` 構成で構成できます。 これは厳密には必要ではありませんが、2 つの潜在的な利点があります。
 1. ポートがアプリケーションのポート範囲にない場合、Service Fabric によって OS のファイアウォールを介してポートが開かれます。
 2. `KestrelCommunicationListener` を介して提供される URL では、このポートが使用されます。

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

`Endpoint` 構成を使用しない場合は、`KestrelCommunicationListener` コンストラクターで名前を省略します。 この場合、動的ポートが使用されます。 詳細については、次のセクションを参照してください。

#### <a name="use-kestrel-with-a-dynamic-port"></a>Kestrel での動的ポートの使用
Kestrel では ServiceManifest.xml の `Endpoint` 構成からの自動ポート割り当てを使用できません。それは、`Endpoint` 構成からの自動ポート割り当てでは "*ホスト プロセス*" ごとに一意のポートが割り当てられ、単一のホスト プロセスが複数の Kestrel インスタンスを含むことができるためです。 Kestrel ではポート共有がサポートされず、各 Kestrel インスタンスを一意のポートで開く必要があるため、これは機能しません。

Kestrel で動的ポート割り当てを使用するには、ServiceManifest.xml の `Endpoint` 構成を省略し、エンドポイント名を `KestrelCommunicationListener` コンストラクターに渡さないようにします。

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

この構成では、`KestrelCommunicationListener` により、アプリケーション ポートの範囲から未使用のポートが自動的に選択されます。

## <a name="service-fabric-configuration-provider"></a>Service Fabric 構成プロバイダー
ASP.NET Core でのアプリの構成には、構成プロバイダーで確立したキーと値のペアを使用する必要があります。ASP.NET Core の一般的な構成のサポートの詳細については、「[ASP.NET Core の構成](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/)」を参照してください。

このセクションでは、`Microsoft.ServiceFabric.AspNetCore.Configuration` NuGet パッケージをインポートして、ASP.NET Core の構成に Service Fabric 構成プロバイダーを統合する方法について説明します。

### <a name="addservicefabricconfiguration-startup-extensions"></a>AddServiceFabricConfiguration 起動の拡張機能
`Microsoft.ServiceFabric.AspNetCore.Configuration` NuGet パッケージをインポートしたら、`IConfigurationBuilder` に対し `Microsoft.ServiceFabric.AspNetCore.Configuration` 名前空間に、**AddServiceFabricConfiguration** 拡張機能で ASP.NET Core 構成 API に Service Fabric 構成ソースを登録する必要があります。

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
Service Fabric 構成プロバイダーには、次の関数を使用し、パッケージ名、セクション名、プロパティ名を組み合わせて構成する asp.net core の構成キーが既定で含まれています。
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

たとえば、次の内容の `MyConfigPackage` という名前の構成パッケージがある場合、構成値は ASP.NET Core `IConfiguration` で、キー *MyConfigPackage:MyConfigSection:MyParameter* を介して使用可能になります。
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Service Fabric の構成オプション
Service Fabric 構成プロバイダーでは、キー マッピングの既定の動作を変更する `ServiceFabricConfigurationOptions` もサポートしています。

#### <a name="encrypted-settings"></a>暗号化された設定
Service Fabric では設定の暗号化をサポートしており、Service Fabric 構成プロバイダーでもこれをサポートしています。 既定の原則に安全に従い、暗号化された設定は既定では暗号化解除されません。暗号化された値が ASP.NET Core `IConfiguration` に格納されます。 ただし、ASP.NET Core IConfiguration に格納する値を暗号化解除したい場合、次のように DecryptValue フラグを `AddServiceFabricConfiguration` 拡張機能で false に設定できます。

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = true); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>複数の構成パッケージ
Service Fabric では複数の構成パッケージをサポートしています。 パッケージ名は既定で構成キーに含まれています。 `IncludePackageName` フラグを設定すると、既定の動作を変更できます。
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
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>カスタム キー マッピング、値の抽出、およびデータ カタログの作成
Service Fabric 構成プロバイダーでは、既定の動作を変更する上述の 2 つのフラグ以外にも、`ExtractKeyFunc` を使用したキー マッピングのカスタマイズ、および `ExtractValueFunc` を使用した値のカスタム抽出のより高度なシナリオもサポートしています。 Service Fabric 構成から ASP.NET Core 構成へデータを入力するプロセス全体を、`ConfigAction` を使用し変更することもできます。

次の例では、データ入力をカスタマイズする方法を `ConfigAction` を使用して示しています。
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
### <a name="configuration-update"></a>構成の更新
Service Fabric 構成プロバイダーでは、構成の更新もサポートしており、ASP.NET Core `IOptionsMonitor` を使用して変更通知を受信したり、`IOptionsSnapshot` を使用して構成データを再読み込みすることができます。 詳細については、[ASP.NET Core のオプション](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options)に関するページを参照してください。

これは既定でサポートされており、構成の更新の有効化には、コードを記述する必要はありません。

## <a name="scenarios-and-configurations"></a>シナリオと構成
このセクションでは、次のシナリオについて説明すると共に、適切に動作するサービスを実現するうえで必要な Web サーバー、ポート構成、Service Fabric 統合オプション、およびその他の設定について、推奨される組み合わせを示します。
 - 外部に公開される ASP.NET Core ステートレス サービス
 - 内部専用の ASP.NET Core ステートレス サービス
 - 内部専用の ASP.NET Core ステートフル サービス

**外部に公開される**サービスとは、通常、ロード バランサーを介してクラスターの外部から到達できるエンドポイントを公開するサービスです。

**内部専用**のサービスとは、クラスター内からのみエンドポイントに到達できるサービスです。

> [!NOTE]
> ステートフル サービス エンドポイントは、通常、インターネットに公開されません。 Azure Load Balancer などの Service Fabric サービス解決を認識しないロード バランサーの背後にあるクラスターは、ステートフル サービスを公開できません。ロード バランサーが適切なステートフル サービス レプリカを見つけてトラフィックをルーティングすることができないためです。 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>外部に公開される ASP.NET Core ステートレス サービス
外部のインターネットに接続された HTTP エンドポイントを公開するフロントエンド サービス用の Web サーバーとして、Kestrel をお勧めします。 Windows では、HttpSys を使用してポートの共有機能を提供できます。この機能を使用すると、同じポートを使用する同じノードのセット上で、ホスト名またはパスが異なる複数の Web サービスをホストできます。HTTP ルーティングを提供するためにフロントエンド プロキシやゲートウェイに頼る必要はありません。
 
インターネットに公開されるステートレス サービスでは、ロード バランサーを介して到達できる、安定した一般的なエンドポイントを使用する必要があります。 これは、アプリケーションのユーザーに提供する URL です。 次の構成をお勧めします。

|  |  | **メモ** |
| --- | --- | --- |
| Web サーバー | Kestrel | Kestrel は Windows と Linux でサポートされているため、好まれている Web サーバーです。 |
| ポート構成 | 静的 | 一般的な静的ポートを ServiceManifest.xml の `Endpoints` 構成で構成する必要があります (HTTP の場合は 80、HTTPS の場合は 443 など)。 |
| ServiceFabricIntegrationOptions | なし | Service Fabric 統合ミドルウェアを構成するときに `ServiceFabricIntegrationOptions.None` オプションを使用して、サービスが受信要求の一意の識別子を検証しないようにする必要があります。 アプリケーションの外部ユーザーは、ミドルウェアが使用する一意の識別情報を認識しません。 |
| Instance Count | -1 | 典型的な使用例では、ロード バランサーからのトラフィックを受信するすべてのノードでインスタンスを使用できるように、インスタンス数を "-1" に設定する必要があります。 |

外部に公開されている複数のサービスが同じノードのセットを共有する場合、URL パスが一意でありながら安定している HttpSys を使用できます。 これは、IWebHost の構成時に提供される URL を変更することで実現できます。 これは HttpSys にのみ適用される点に注意してください。

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

|  |  | **メモ** |
| --- | --- | --- |
| Web サーバー | Kestrel | 内部ステートレス サービスに HttpSys を使用することもできますが、サーバーで複数のサービス インスタンスがホストを共有できるようにするには、Kestrel が推奨されます。  |
| ポート構成 | 動的割り当て | ステートフル サービスの複数のレプリカがホスト プロセスまたはホスト オペレーティング システムを共有することができるため、一意のポートが必要になります。 |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | 動的なポート割り当てでこの設定を行うことにより、前述の誤った ID に関する問題を防ぐことができます。 |
| InstanceCount | 任意 | インスタンス数の設定は、サービスの実行に必要な任意の値に設定することができます。 |

### <a name="internal-only-stateful-aspnet-core-service"></a>内部専用のステートフル ASP.NET Core サービス
クラスター内からのみ呼び出されるステートフル サービスでは、複数のサービス間の協調動作を保証するために、動的に割り当てられたポートを使用する必要があります。 次の構成をお勧めします。

|  |  | **メモ** |
| --- | --- | --- |
| Web サーバー | Kestrel | `HttpSysCommunicationListener` は、レプリカがホスト プロセスを共有するステートフル サービス向けに設計されていません。 |
| ポート構成 | 動的割り当て | ステートフル サービスの複数のレプリカがホスト プロセスまたはホスト オペレーティング システムを共有することができるため、一意のポートが必要になります。 |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | 動的なポート割り当てでこの設定を行うことにより、前述の誤った ID に関する問題を防ぐことができます。 |

## <a name="next-steps"></a>次の手順
[Visual Studio による Service Fabric アプリケーションのデバッグ](service-fabric-debugging-your-application.md)

<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
