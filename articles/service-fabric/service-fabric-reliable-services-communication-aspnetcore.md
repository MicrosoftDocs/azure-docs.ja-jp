---
title: "ASP.NET Core によるサービス通信 | Microsoft Docs"
description: "ステートレス リライアブル サービスおよびステートフル リライアブル サービスで ASP.NET Core を使用する方法について説明します。"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 8aa4668d-cbb6-4225-bd2d-ab5925a868f2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 03/22/2017
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: cce66615ebe457ed7230401d154ddad07941f5bc
ms.lasthandoff: 03/23/2017


---

# <a name="aspnet-core-in-service-fabric-reliable-services"></a>Service Fabric リライアブル サービスでの ASP.NET Core

ASP.NET Core は新しいオープンソースのクロスプラットフォーム フレームワークであり、Web アプリ、IoT アプリ、モバイル バックエンドなど、最新のクラウドベースのインターネット接続アプリケーションを構築するために使用されます。 ASP.NET Core アプリは .NET Core または完全な .NET Framework で実行できますが、Service Fabric サービスは、現時点で完全な .NET Framework でのみ実行できます。 つまり、ASP.NET Core Service Fabric サービスを構築する場合にも完全な .NET Framework を対象にする必要があります。

ASP.NET Core は、Service Fabric で 2 とおりの方法で使用できます。
 - **ゲスト実行可能ファイルとしてホストする**。 この方法は、主に、コードの変更なしで Service Fabric 上で既存の ASP.NET Core アプリケーションを実行するために使用します。
 - **リライアブル サービス**内で実行する。 Service Fabric ランタイムとの統合がより密になり、ステートフルな ASP.NET Core サービスが可能になります。

この記事の残りの部分では、Service Fabric SDK に付属する ASP.NET Core 統合コンポーネントを使用して、リライアブル サービス内で ASP.NET Core を使用する方法について説明します。 

> [!NOTE]
>この記事の残りの部分では、読者が SP.NET Core でのホスティングに慣れているものと想定しています。 ASP.NET Core でのホスティングの詳細については、「[Introduction to hosting in ASP.NET Core (ASP.NET Core でのホスティングの概要)](https://docs.microsoft.com/aspnet/core/fundamentals/hosting)」を参照してください。

> [!NOTE]
> Visual Studio 2015 で ASP.NET Core を使ったリライアブル サービスを開発するには、[.NET Core VS 2015 Tooling Preview 2](https://www.microsoft.com/net/download/core) がインストールされている必要があります。

## <a name="service-fabric-service-hosting"></a>Service Fabric サービスのホスティング
Service Fabric では、サービスの 1 つまたは複数のインスタンスやレプリカが "*サービス ホスト プロセス*" (サービス コードを実行する実行可能ファイル) で実行されます。 サービス作成者はサービス ホスト プロセスを所有し、Service Fabric はそれをアクティブ化して監視します。

従来の ASP.NET (MVC 5 まで) は、System.Web.dll を通じて IIS に厳密に結合されています。 ASP.NET Core は、Web サーバーと Web アプリケーションの分離を実現します。 これにより、Web アプリケーションをさまざまな Web サーバー間で移植することができます。また、Web サーバーを "*自己ホスト*" することもできます。つまり、IIS などの専用 Web サーバー ソフトウェアが所有するプロセスではなく、自分のプロセスで Web サーバーを起動できます。 

Service Fabric サービスと ASP.NET をゲスト実行可能ファイルとして結合するか、リライアブル サービス内で結合するには、サービス ホスト プロセス内で ASP.NET を起動できる必要があります。 ASP.NET Core の自己ホストにより、これを行うことができます。

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>リライアブル サービスでの ASP.NET Core のホスティング
通常、自己ホスト型の ASP.NET Core アプリケーションでは、`Program.cs` の `static void Main()` メソッドのように、アプリケーションのエントリ ポイントに WebHost が作成されます。 この場合、WebHost のライフサイクルはプロセスのライフサイクルにバインドされます。

![プロセスでの ASP.NET Core のホスティング][0]

ただし、アプリケーションのエントリ ポイントは、リライアブル サービスに WebHost を作成する場所として適しません。それは、アプリケーションのエントリ ポイントは、サービス タイプを Service Fabric ランタイムに登録するためにのみ使用されることから、そのサービス タイプのインスタンスが作成される可能性があるためです。 WebHost は、リライアブル サービス自体に作成する必要があります。 サービス ホスト プロセス内で、サービス インスタンスやレプリカは、複数のライフサイクルをたどることができます。 

リライアブル サービス インスタンスは、`StatelessService` または `StatefulService` から派生したサービス クラスによって表されます。 サービスの通信スタックは、サービス クラスの `ICommunicationListener` 実装に含まれています。 `Microsoft.ServiceFabric.Services.AspNetCore.*` NuGet パッケージには、リライアブル サービスで Kestrel または WebListener の ASP.NET Core WebHost を開始および管理する `ICommunicationListener` の実装が含まれています。

![リライアブル サービスでの ASP.NET Core のホスティング][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListener
`Microsoft.ServiceFabric.Services.AspNetCore.*` NuGet パッケージの Kestrel と WebListener の `ICommunicationListener` 実装は使用パターンが類似していますが、実行されるアクションは各 Web サーバーに応じて多少異なります。 

どちらの通信リスナーも、次の引数を受け取るコンストラクターを提供します。
 - **`ServiceContext serviceContext`**: 実行中のサービスに関する情報を含む `ServiceContext` オブジェクト。
 - **`string endpointName`**: ServiceManifest.xml の `Endpoint` 構成の名前。 2 つの通信リスナーが異なるのは主にこの部分です。WebListener では `Endpoint` 構成が**必要**ですが、Kestrel では必要ありません。
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: `IWebHost` を作成して返すために実装するラムダ。 これにより、ASP.NET Core アプリケーションで通常行う方法で `IWebHost` を構成することができます。 ラムダによって URL が提供されます。この URL は、使用した Service Fabric 統合オプションと提供した `Endpoint` 構成に応じて生成されます。 Web サーバーを起動するために、この URL を変更することも、そのまま使用することもできます。

## <a name="service-fabric-integration-middleware"></a>Service Fabric 統合ミドルウェア
`Microsoft.ServiceFabric.Services.AspNetCore` NuGet パッケージには、Service Fabric 対応のミドルウェアを追加する `IWebHostBuilder` の `UseServiceFabricIntegration` 拡張メソッドが含まれています。 このミドルウェアは、Service Fabric Naming Service に一意のサービス URL を登録するように Kestrel または WebListener `ICommunicationListener` を構成し、次にクライアント要求を検証して、クライアントが適切なサービスに接続していることを確認します。 これは、複数の Web アプリケーションが同一の物理マシンまたは仮想マシン上で実行できる一方で一意のホスト名が使用されない Service Fabric などの共有ホスト環境において、クライアントが不適切なサービスに誤って接続するのを防ぐために必要です。 このシナリオについては、次のセクションで詳しく説明します。

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
これを防ぐために、サービスは、一意の識別子を使用して Naming Service にエンドポイントを送信し、クライアント要求中にその一意の識別子を検証できます。 これは、悪意のないテナントの信頼できる環境におけるサービス間の協調アクションです。 悪意のあるテナント環境では、セキュリティで保護されたサービスの認証は提供されません。

信頼できる環境では、`UseServiceFabricIntegration` メソッドによって追加されたミドルウェアによって、Naming Service に送信されるアドレスに一意の識別子が自動的に追加され、各要求でその識別子が検証されます。 識別子が一致しない場合、ミドルウェアは即座に HTTP 410 Gone 応答を返します。

動的に割り当てられたポートを使用するサービスでは、このミドルウェアを使用する必要があります。

固定された一意のポートを使用するサービスでは、協調環境においてこの問題は発生しません。 通常、固定された一意のポートは、クライアント アプリケーションが接続するための一般的なポートを必要とする外部向けサービスに使用されます。 たとえば、ほとんどのインターネット接続 Web アプリケーションでは、Web ブラウザー接続にポート 80 または 443 が使用されます。 この場合は、一意の識別子を有効にしないでください。

次の図は、ミドルウェアを有効にした場合の要求フローを示しています。

![Service Fabric ASP.NET Core の統合][2]

Kestrel と WebListener のどちらの `ICommunicationListener` の実装でも、まったく同じ方法でこのメカニズムが使用されます。 WebListener では、基になる *http.sys* ポート共有機能を使用して、一意の URL パスを基に要求を内部的に区別できます。しかし、この機能は WebListener `ICommunicationListener` 実装では使用 "*されません*"。前述のシナリオで HTTP 503 および HTTP 404 エラー状態コードが返される結果になるためです。 さらに、HTTP 503 と HTTP 404 は既に他のエラーを示すために一般的に使用されているため、クライアントがエラーの意味を判断するのが非常に困難になります。 このように、Kestrel と WebListener のどちらの `ICommunicationListener` 実装でも `UseServiceFabricIntegration` 拡張メソッドによって提供されるミドルウェアが標準となるため、クライアントで実行する必要があるのは HTTP 410 の応答に対するサービス エンドポイントの再解決アクションのみになります。

## <a name="weblistener-in-reliable-services"></a>リライアブル サービスでの WebListener
WebListener は、**Microsoft.ServiceFabric.AspNetCore.WebListener** NuGet パッケージをインポートすることにより、リライアブル サービスで使用できます。 このパッケージには、WebListener を Web サーバーとして使用してリライアブル サービス内に ASP.NET Core WebHost を作成できるようにする `WebListenerCommunicationListener` (`ICommunicationListener` の実装) が含まれています。

WebListener は、[Windows HTTP Server API](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx) 上に構築されています。 これにより、IIS で使用される *http.sys* カーネル ドライバーを使用して HTTP 要求が処理され、Web アプリケーションを実行しているプロセスにルーティングされます。 これにより、同一の物理マシンまたは仮想マシン上の複数のプロセスが、一意の URL パスまたはホスト名によって明確化された Web アプリケーションを同じポート上でホストできます。 これらの機能は、Service Fabric で同じクラスター内の複数の Web サイトをホストする場合に役立ちます。

次の図は、WebListener がポート共有のために Windows 上で *http.sys* カーネル ドライバーを使用する方法を示しています。

![http.sys][3]

### <a name="weblistener-in-a-stateless-service"></a>ステートレス サービスでの WebListener
ステートレス サービスで `WebListener` を使用するには、`CreateServiceInstanceListeners` メソッドを上書きして `WebListenerCommunicationListener` インスタンスを返します。

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new WebListenerCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseWebListener()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build()))
    };
}
```

### <a name="weblistener-in-a-stateful-service"></a>ステートフル サービスでの WebListener

`WebListenerCommunicationListener` は、基になる *http.sys* ポート共有機能に関する複雑な要素が絡んでくるため、現時点ではステートフル サービス向けには設計されていません。 詳細については、WebListener を使用した動的ポート割り当てに関する次のセクションを参照してください。 ステートフル サービスの場合、Web サーバーとして Kestrel が推奨されます。

### <a name="endpoint-configuration"></a>Endpoint configuration (エンドポイントの構成)

WebListener を含む Windows HTTP Server API を使用する Web サーバーには、`Endpoint` 構成が必要です。 Windows HTTP Server API を使用する Web サーバーでは、最初に *http.sys* で URL を予約する必要があります (通常、この操作は [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) ツールを使用して実行します)。 このアクションには、サービスに既定では付与されていない昇格された特権が必要です。 *ServiceManifest.xml* の `Endpoint` 構成の `Protocol` プロパティの "http" または "https" オプションは、"[*強力なワイルドカード*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx)" URL プレフィックスを使用して、ユーザーの代わりに *http.sys* に URL を登録するよう Service Fabric ランタイムに明確に指示するために使用します。

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

また、エンドポイント名は `WebListenerCommunicationListener` コンストラクターに渡す必要があります。

```csharp
 new WebListenerCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseWebListener()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-weblistener-with-a-static-port"></a>WebListener での静的ポートの使用
WebListener で静的ポートを使用するには、`Endpoint` 構成でポート番号を指定します。

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-weblistener-with-a-dynamic-port"></a>WebListener での動的ポートの使用
WebListener で動的に割り当てられたポートを使用するには、`Endpoint` 構成の `Port` プロパティを省略します。

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

`Endpoint` 構成によって割り当てられる動的ポートは、"*ホスト プロセスごとに*" 1 つのポートのみを提供することに注意してください。 現在の Service Fabric ホスティング モデルでは、複数のサービス インスタンスやレプリカを同じプロセスでホストすることができます。つまり、`Endpoint` 構成によって割り当てられたときにそれぞれが同じポートを共有します。 複数の WebListener インスタンスは基になる *http.sys* ポート共有機能を使用してポートを共有できますが、クライアント要求に関する複雑な要素が絡んでくるため、`WebListenerCommunicationListener` ではこの機能がサポートされません。 ポートを動的に使用する場合、Web サーバーとして Kestrel が推奨されます。

## <a name="kestrel-in-reliable-services"></a>リライアブル サービスでの Kestrel
Kestrel は、**Microsoft.ServiceFabric.AspNetCore.Kestrel** NuGet パッケージをインポートすることにより、リライアブル サービスで使用できます。 このパッケージには、Kestrel を Web サーバーとして使用してリライアブル サービス内に ASP.NET Core WebHost を作成できるようにする `KestrelCommunicationListener` (`ICommunicationListener` の実装) が含まれています。

Kestrel は、クロスプラットフォームの非同期 I/O ライブラリである libuv をベースにした ASP.NET Core 用のクロスプラットフォーム Web サーバーです。 WebListener とは異なり、Kestrel では *http.sys* などの一元的なエンドポイント マネージャーは使用されません。 また、WebListener とは異なり、Kestrel では複数のプロセス間のポート共有がサポートされません。 Kestrel の各インスタンスは、一意のポートを使用する必要があります。

![Kestrel][4]

### <a name="kestrel-in-a-stateless-service"></a>ステートレス サービスでの Kestrel
ステートレス サービスで `Kestrel` を使用するには、`CreateServiceInstanceListeners` メソッドを上書きして `KestrelCommunicationListener` インスタンスを返します。

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
ステートフル サービスで `Kestrel` を使用するには、`CreateServiceReplicaListeners` メソッドを上書きして `KestrelCommunicationListener` インスタンスを返します。

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

`Endpoint` 構成名はステートフル サービスの `KestrelCommunicationListener` に提供**されない**ことに注意してください。 これについては、次のセクションで詳しく説明します。

### <a name="endpoint-configuration"></a>Endpoint configuration (エンドポイントの構成)
Kestrel を使用するうえで `Endpoint` 構成は必要ありません。 

Kestrel は、単純なスタンドアロンの Web サーバーです。WebListener (または HttpListener) とは異なり、Kestrel では起動の前に URL を登録する必要がないため、*ServiceManifest.xml* に `Endpoint` 構成は必要ありません。 

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

Kestrel で動的ポート割り当てを使用するには、ServiceManifest.xml の `Endpoint` 構成を完全に省略し、エンドポイント名を `KestrelCommunicationListener` コンストラクターに渡さないようにします。

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

この構成では、`KestrelCommunicationListener` により、アプリケーション ポートの範囲から未使用のポートが自動的に選択されます。

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
Windows 上の外部のインターネットに接続された HTTP エンドポイントを公開するフロントエンド サービス用の Web サーバーとして、WebListener が推奨されます。 WebListener では、攻撃に対するより適切な保護が提供され、Windows 認証やポート共有など、Kestrel でサポートされない機能がサポートされます。 

現時点で、Kestrel はエッジ (インターネット接続) サーバーとしてサポートされていません。 パブリック インターネットからのトラフィックを処理するには、IIS、Nginx などのリバース プロキシ サーバーを使用する必要があります。
 
インターネットに公開されるステートレス サービスでは、ロード バランサーを介して到達できる、安定した一般的なエンドポイントを使用する必要があります。 これは、アプリケーションのユーザーに提供する URL です。 次の構成をお勧めします。

|  |  | **メモ** |
| --- | --- | --- |
| Web サーバー | WebListener | サービスが信頼できるネットワーク (イントラネットなど) にのみ公開される場合は、Kestrel を使用することができます。 それ以外の場合は、WebListener をお勧めします。 |
| ポート構成 | 静的 | 一般的な静的ポートを ServiceManifest.xml の `Endpoints` 構成で構成する必要があります (HTTP の場合は 80、HTTPS の場合は 443 など)。 |
| ServiceFabricIntegrationOptions | なし | Service Fabric 統合ミドルウェアを構成するときに `ServiceFabricIntegrationOptions.None` オプションを使用して、サービスが一意の識別子の受信要求の検証を試みないようにする必要があります。 アプリケーションの外部ユーザーは、ミドルウェアが使用する一意の識別情報を認識しません。 |
| Instance Count | -1 | 典型的な使用例では、ロード バランサーからのトラフィックを受信するすべてのノードでインスタンスを使用できるように、インスタンス数を "-1" に設定する必要があります。 |

外部に公開されている複数のサービスが同じノードのセットを共有する場合、一意である一方で安定している URL パスを使用する必要があります。 これは、IWebHost の構成時に提供される URL を変更することで実現できます。 これは WebListener のみに当てはまることに注意してください。

 ```csharp
 new WebListenerCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseWebListener()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>内部専用のステートレス ASP.NET Core サービス
クラスター内からのみ呼び出されるステートレス サービスでは、複数のサービス間の協調動作を保証するために、一意の URL と動的に割り当てられたポートを使用する必要があります。 次の構成をお勧めします。

|  |  | **メモ** |
| --- | --- | --- |
| Web サーバー | Kestrel | 内部ステートレス サービスに WebListener を使用することもできますが、サーバーで複数のサービス インスタンスがホストを共有できるようにするには、Kestrel が推奨されます。  |
| ポート構成 | 動的割り当て | ステートフル サービスの複数のレプリカがホスト プロセスまたはホスト オペレーティング システムを共有することができるため、一意のポートが必要になります。 |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | 動的なポート割り当てでこの設定を行うことにより、前述の誤った ID に関する問題を防ぐことができます。 |
| InstanceCount | 任意 | インスタンス数の設定は、サービスの実行に必要な任意の値に設定することができます。 |

### <a name="internal-only-stateful-aspnet-core-service"></a>内部専用のステートフル ASP.NET Core サービス
クラスター内からのみ呼び出されるステートフル サービスでは、複数のサービス間の協調動作を保証するために、動的に割り当てられたポートを使用する必要があります。 次の構成をお勧めします。

|  |  | **メモ** |
| --- | --- | --- |
| Web サーバー | Kestrel | `WebListenerCommunicationListener` は、レプリカがホスト プロセスを共有するステートフル サービス向けに設計されていません。 |
| ポート構成 | 動的割り当て | ステートフル サービスの複数のレプリカがホスト プロセスまたはホスト オペレーティング システムを共有することができるため、一意のポートが必要になります。 |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | 動的なポート割り当てでこの設定を行うことにより、前述の誤った ID に関する問題を防ぐことができます。 |

## <a name="next-steps"></a>次のステップ
[Visual Studio による Service Fabric アプリケーションのデバッグ](service-fabric-debugging-your-application.md)

<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
