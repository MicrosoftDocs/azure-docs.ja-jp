---
title: "ASP.NET Web API によるサービス通信 | Microsoft Docs"
description: "Reliable Services API で OWIN 自己ホストと ASP.NET Web API を使用して、サービスの通信を実装する方法を説明します。"
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
ms.date: 02/10/2017
ms.author: vturecek
redirect_url: /azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 73b7e1c0cb93ae7c54780a3aab837b0e5bcdb0a0
ms.lasthandoff: 03/28/2017


---
# <a name="get-started-service-fabric-web-api-services-with-owin-self-hosting"></a>はじめに: OWIN 自己ホストによる Service Fabric Web API サービス
Azure Service Fabric は、サービスとユーザーとの通信および各ユーザーとの通信の方法を決定する際に役立ちます。 このチュートリアルでは、Service Fabric の Reliable Services API で、ASP.NET Web API と Open Web Interface for .NET (OWIN) 自己ホストを使用して、サービスの通信を実装することに焦点を合わせています。 Reliable Services のプラグ可能な通信 API について詳しく説明します。 また、Web API を手順を追った例で使用し、カスタム通信リスナーを設定する方法を説明しします。

## <a name="introduction-to-web-api-in-service-fabric"></a>Service Fabric での Web API の概要
ASP.NET Web API は、.NET Framework に基づいて HTTP API を構築するための人気のある強力なフレームワークです。 このフレームワークにまだ詳しくない場合、「 [ASP.NET Web API 2 の概要](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) 」を参照してください。

Service Fabric の Web API は、馴染みのある同じ ASP.NET Web API です。 違いは、Web API アプリケーションを *ホスト* する方法です。 Microsoft インターネット インフォメーション サービス (IIS) は使用しません。 違いを深く理解するために、2 つの部分に分けてみましょう。

1. Web API アプリケーション (コント ローラーとモデルをなど)
2. ホスト (Web サーバー、通常 IIS)

Web API アプリケーション自体には違いはありません。 これまでに作成したことがある Web API アプリケーションと違いはなく、アプリケーション コードのほとんどを単純にそっくり移動できます。 ただし、IIS でホストしていた場合、慣れているものとはアプリケーションをホストする場所が多少異なる場合があります。 ホストの部分に入る前に、馴染みのある部分である Web API アプリケーションから始めましょう。

## <a name="create-the-application"></a>アプリケーションを作成する
Visual Studio 2015 で、1 つのステートレス サービスと新しい Service Fabric アプリケーションを作成することから始めます。

Web API を使用するステートレス サービス用の Visual Studio テンプレートを使用できます。 このチュートリアルでは、このテンプレートを選択した場合に入手できる Web API プロジェクトを最初から作成します。

空のステートレス サービス プロジェクトを選択して、Web API プロジェクトを最初から作成する方法を学習するか、ステートレス サービス Web API テンプレートを使用して開始し、手順に従うことができます。  

最初の手順は、Web API のいくつかの NuGet パッケージを取得することです。 使用するパッケージは Microsoft.AspNet.WebApi.OwinSelfHost です。 このパッケージには、必要なすべての Web API パッケージと、 *ホスト* パッケージが含まれます。 これは後で重要になります。

パッケージがインストールされたら、基本的な Web API プロジェクト構造の構築を開始できます。 Web API を使用していた場合、プロジェクトの構造は非常に馴染みがあるように見えるはずです。 まず、 `Controllers` ディレクトリと単純な Values コントローラーを追加します。

**ValuesController.cs**

```csharp
using System.Collections.Generic;
using System.Web.Http;

namespace WebService.Controllers
{
    public class ValuesController : ApiController
    {
        // GET api/values 
        public IEnumerable<string> Get()
        {
            return new string[] { "value1", "value2" };
        }

        // GET api/values/5 
        public string Get(int id)
        {
            return "value";
        }

        // POST api/values 
        public void Post([FromBody]string value)
        {
        }

        // PUT api/values/5 
        public void Put(int id, [FromBody]string value)
        {
        }

        // DELETE api/values/5 
        public void Delete(int id)
        {
        }
    }
}

```

次に、プロジェクト ルートに Startup クラスを追加して、ルーティング、フォーマッタ、その他の構成セットアップを登録します。 これは、Web API が *ホスト*にプラグインする場所でもあり、後で再度参照します。 

**Startup.cs**

```csharp
using System.Web.Http;
using Owin;

namespace WebService
{
    public static class Startup
    {
        public static void ConfigureApp(IAppBuilder appBuilder)
        {
            // Configure Web API for self-host. 
            HttpConfiguration config = new HttpConfiguration();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );

            appBuilder.UseWebApi(config);
        }
    }
}
```

以上が、アプリケーション部分です。 この時点では、ごく基本的な Web API プロジェクト レイアウトをセットアップしただけです。 過去に作成した Web API プロジェクトや基本的な Web API テンプレートと、今のところそれほど大きく違うように見えないはずです。 ビジネス ロジックは、通常どおりに、コント ローラーとモデルに進みます。

実際にそれを実行できるように、ホストをどのように処理すればよいでしょうか。

## <a name="service-hosting"></a>サービスのホスト
Service Fabric では、サービスは *サービス ホスト プロセス*(サービス コードを実行する実行可能ファイル) で実行されます。 Reliable Services API を使用してサービスを作成する場合、サービス プロジェクトは、サービスの種類を登録して、コードを実行する実行可能ファイルにコンパイルされるだけです。 .NET で Service Fabric のサービスを記述する場合、ほとんどこのようになります。 ステートレス サービス プロジェクトの Program.cs を開いた場合、次が表示されます。

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric.Services.Runtime;

internal static class Program
{
    private static void Main()
    {
        try
        {
            ServiceRuntime.RegisterServiceAsync("WebServiceType",
                context => new WebService(context)).GetAwaiter().GetResult();

            ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(WebService).Name);

            // Prevents this host process from terminating so services keeps running. 
            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

コンソール アプリケーションへのエントリ ポイントのように見える場合は、それが次のようになっているためです。

サービス ホスト プロセスとサービス登録の詳細については、この記事では取り上げません。 ただし、現時点では、 *サービス コードが独自のプロセスで実行されている*ことを理解しておくことが重要です。

## <a name="self-host-web-api-with-an-owin-host"></a>OWIN ホストによる自己ホスト型 Web API
Web API アプリケーション コードが、その独自のプロセスでホストされている場合、それをどのようにして Web サーバーに接続するのでしょうか。 [OWIN](http://owin.org/)に入ります。 OWIN は .NET Web アプリケーションと Web サーバー間の単なるコントラクトです。 従来、ASP.NET (MVC 5 まで) を使用した場合、Web アプリケーションは System.Web 経由で IIS に厳密に結合されていました。 しかし、Web API は OWIN を実装しているため、ホストされる Web サーバーから分離した Web アプリケーションを書くことができます。 これにより、独自のプロセスで起動できる *自己ホスト* OWIN Web サーバーを使用できます。 これは、前述の Service Fabric ホスティング モデルに適しています。

この記事では、Web API アプリケーションの OWIN ホストとして Katana を使用します。 Katana は、[System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener.aspx) と Windows [HTTP Server API](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx) 上に構築されたオープン ソース OWIN ホスト実装です。

> [!NOTE]
> Katana の詳細については、 [Katana サイト](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana)を参照してください。 Katana を使用して Web API を自己ホストする方法の概要については、「 [Use OWIN to Self-Host ASP.NET Web API 2 (OWIN を使用して ASP.NET Web API 2 を自己ホストする)](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api)」を参照してください。
> 
> 

## <a name="set-up-the-web-server"></a>Web サーバーのセットアップ
Reliable Services API が提供する通信エントリ ポイントに通信スタックを接続し、ユーザーおよびクライアントがサービスに接続できるようにすることができます。

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}

```

Web サーバー (および Websocket などの今後使用する可能性のある他の任意の通信スタック) は、システムに正しく統合されるように ICommunicationListener インターフェイスを使用する必要があります。 この理由は、次の手順で明らかになります。

まず ICommunicationListener を実装する OwinCommunicationListener というクラスを作成します。

**OwinCommunicationListener.cs**

```csharp
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;
using System;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;

namespace WebService
{
    internal class OwinCommunicationListener : ICommunicationListener
    {
        public void Abort()
        {
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
        }
    }
}
```

ICommunicationListener インターフェイスは、サービスの通信リスナーを管理する 3 つのメソッドを提供します。

* *OpenAsync*。 要求のリッスンを開始します。
* *CloseAsync*。 要求のリッスンを停止し、実行中の要求を完了して、正常にシャット ダウンします。
* *Abort*。 すべてのものをキャンセルし、ただちに停止します。

まず、リスナーが機能するために必要なものにプライベート クラス メンバーを追加します。 これらは、コンストラクターによって初期化され、後でリッスンする URL を設定するときに使用されます。

```csharp
internal class OwinCommunicationListener : ICommunicationListener
{
    private readonly ServiceEventSource eventSource;
    private readonly Action<IAppBuilder> startup;
    private readonly ServiceContext serviceContext;
    private readonly string endpointName;
    private readonly string appRoot;

    private IDisposable webApp;
    private string publishAddress;
    private string listeningAddress;

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
        : this(startup, serviceContext, eventSource, endpointName, null)
    {
    }

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
    {
        if (startup == null)
        {
            throw new ArgumentNullException(nameof(startup));
        }

        if (serviceContext == null)
        {
            throw new ArgumentNullException(nameof(serviceContext));
        }

        if (endpointName == null)
        {
            throw new ArgumentNullException(nameof(endpointName));
        }

        if (eventSource == null)
        {
            throw new ArgumentNullException(nameof(eventSource));
        }

        this.startup = startup;
        this.serviceContext = serviceContext;
        this.endpointName = endpointName;
        this.eventSource = eventSource;
        this.appRoot = appRoot;
    }


    ...

```

## <a name="implement-openasync"></a>OpenAsync の実装
Web サーバーをセットアップするには、2 つの情報が必要です。

* *URL パス プレフィックス*。 オプションですが、アプリケーションで複数の Web サービスを安全にホストできるように、ここでこれを設定することをお勧めします。
* *ポート*。

Web サーバーのポートを取得する前に、Service Fabric が、アプリケーションとそれが実行される基盤のオペレーティング システム間のバッファーとして機能するアプリケーション層を提供することを理解しておくことが重要です。 そのために Service Fabric は、サービスの*エンドポイント*を構成する方法を提供します。 Service Fabric では、サービスでエンドポイントを確実に使用できるようにします。 そのため、ユーザーは基盤の OS 環境で、自分でそれを構成する必要はありません。 これにより、さまざまな環境で、Service Fabric アプリケーションを変更する必要なく簡単にホストできます。 (たとえば、Azure や独自のデータ センターで、同じアプリケーションをホストできます。)

PackageRoot\ServiceManifest.xml で HTTP エンドポイントを構成します。

```xml
<Resources>
    <Endpoints>
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="8281" />
    </Endpoints>
</Resources>

```

サービス ホスト プロセスは制限付き資格情報 (Windows 上のネットワーク サービス) で実行されるため、この手順が重要になります。 つまり、サービスには自分で HTTP エンドポイントを設定するためのアクセスがないことを意味します。 エンドポイント構成を使用することによって、Service Fabric は、サービスがリッスンする URL の正しい ACL を設定することを認識します。 Service Fabric は、エンドポイントを構成する標準の場所も提供します。

OwinCommunicationListener.cs に戻り、OpenAsync の実装を始めることができます。 ここで Web サーバーを開始します。 最初に、エンドポイントの情報を取得し、サービスがリッスンする URL を作成します。 この URL は、リスナーをステートレス サービスとステートフル サービスのどちらで使用するかによって異なります。 ステートフル サービスの場合、リスナーはリッスンするすべてのステートフル サービス レプリカの一意のアドレスを作成する必要があります。 ステートレス サービスの場合、アドレスをもっと簡単なものにすることができます。 

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
    var protocol = serviceEndpoint.Protocol;
    int port = serviceEndpoint.Port;

    if (this.serviceContext is StatefulServiceContext)
    {
        StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}{3}/{4}/{5}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/',
            statefulServiceContext.PartitionId,
            statefulServiceContext.ReplicaId,
            Guid.NewGuid());
    }
    else if (this.serviceContext is StatelessServiceContext)
    {
        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/');
    }
    else
    {
        throw new InvalidOperationException();
    }

    ...

```

ここで "http://+" が使われていることに注意してください。 これは、Web サーバーが、localhost、FQDN、マシン IP など、使用可能なすべてのアドレスで確実にリッスンするようにするためです。

OpenAsync の実装は、Web サーバー (または任意の通信スタック) が、サービスで `RunAsync()` から直接開かれるのではなく、ICommunicationListener として実装されている最も重要な理由の 1 つです。 OpenAsync からの戻り値は、Web サーバーがリッスンしているアドレスです。 このアドレスがシステムに返されると、システムはアドレスをサービスに登録します。 Service Fabric は、クライアントおよびその他のサービスがサービス名でこのアドレスを要求できるようにする API を提供します。 これが重要なのは、サービスのアドレスが静的でないためです。 リソースの分散と可用性のために、サービスはクラスター内を移動します。 これは、クライアントがサービスのリッスンしているアドレスを解決できるようにするメカニズムです。

それを考慮して、OpenAsync は Web サーバーを起動し、リッスンしているアドレスを返します。 それは "http://+" でリッスンしますが、OpenAsync がアドレスを返す前に、"+" は現在存在しているノードの IP または FQDN に置き換えられます。 この方法で返されるアドレスは、システムに登録されているものです。 これは、サービスのアドレスを求めた際にクライアントや他のサービスに示されるものでもあります。 クライアントがそれに正しく接続するためには、アドレスの実際の IP または FQDN が必要です。

```csharp
    ...

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    try
    {
        this.eventSource.Message("Starting web server on " + this.listeningAddress);

        this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

        this.eventSource.Message("Listening on " + this.publishAddress);

        return Task.FromResult(this.publishAddress);
    }
    catch (Exception ex)
    {
        this.eventSource.Message("Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

        this.StopWebServer();

        throw;
    }
}

```

これは、コンストラクターで OwinCommunicationListener に渡された Startup クラスを参照します。 この Startup インスタンスは、Web サーバーが Web API アプリケーションを起動するために使用します。

後でアプリケーションを実行して Web サーバーが正常に起動していることを確認するときに、[診断イベント] ウィンドウに `ServiceEventSource.Current.Message()` 行が表示されます。

## <a name="implement-closeasync-and-abort"></a>CloseAsync と Abort の実装
最後に、Web サーバーを停止する CloseAsync と Abort の両方を実装します。 Web サーバーを停止するには、OpenAsync 時に作成されたサーバー ハンドルを破棄します。

```csharp
public Task CloseAsync(CancellationToken cancellationToken)
{
    this.eventSource.Message("Closing web server on endpoint {0}", this.endpointName);

    this.StopWebServer();

    return Task.FromResult(true);
}

public void Abort()
{
    this.eventSource.Message("Aborting web server on endpoint {0}", this.endpointName);

    this.StopWebServer();
}

private void StopWebServer()
{
    if (this.webApp != null)
    {
        try
        {
            this.webApp.Dispose();
        }
        catch (ObjectDisposedException)
        {
            // no-op
        }
    }
}
```

この例の実装では、CloseAsync と Abort のどちらも単に Web サーバーを停止するだけです。 CloseAsync を選択した場合、Web サーバーをより適切に調整してシャット ダウンできます。 たとえば、返される前、実行中の要求が完了するまで、シャット ダウンを待機させることができます。

## <a name="start-the-web-server"></a>Web サーバーの起動
これで、OwinCommunicationListener のインスタンスを作成して、返し、Web サーバーを起動する準備が整いました。 Service クラス (WebService.cs) に戻り、`CreateServiceInstanceListeners()` メソッドをオーバーライドします。

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                           .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                           .Select(endpoint => endpoint.Name);

    return endpoints.Select(endpoint => new ServiceInstanceListener(
        serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
}
```

ここで、Web API *アプリケーション*と OWIN *ホスト*が最終的に接触します。 ホスト (OwinCommunicationListener) には、Startup 経由の *アプリケーション* (Web API) のインスタンスが提供されます。 その後、Service Fabric がそのライフサイクルを管理します。 この同じパターンは、一般に通信スタックでも従うことができます。

## <a name="put-it-all-together"></a>すべてをまとめた配置
この例では、 `RunAsync()` メソッドで何も実行する必要はないため、オーバーライドを簡単に削除できます。

最後のサービス実装は、きわめて単純なはずです。 通信リスナーのみ作成する必要があります。

```csharp
using System;
using System.Collections.Generic;
using System.Fabric;
using System.Fabric.Description;
using System.Linq;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace WebService
{
    internal sealed class WebService : StatelessService
    {
        public WebService(StatelessServiceContext context)
            : base(context)
        { }

        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
            var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                                   .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                                   .Select(endpoint => endpoint.Name);

            return endpoints.Select(endpoint => new ServiceInstanceListener(
                serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
        }
    }
}
```

完全な `OwinCommunicationListener` クラス:

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;

namespace WebService
{
    internal class OwinCommunicationListener : ICommunicationListener
    {
        private readonly ServiceEventSource eventSource;
        private readonly Action<IAppBuilder> startup;
        private readonly ServiceContext serviceContext;
        private readonly string endpointName;
        private readonly string appRoot;

        private IDisposable webApp;
        private string publishAddress;
        private string listeningAddress;

        public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
            : this(startup, serviceContext, eventSource, endpointName, null)
        {
        }

        public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
        {
            if (startup == null)
            {
                throw new ArgumentNullException(nameof(startup));
            }

            if (serviceContext == null)
            {
                throw new ArgumentNullException(nameof(serviceContext));
            }

            if (endpointName == null)
            {
                throw new ArgumentNullException(nameof(endpointName));
            }

            if (eventSource == null)
            {
                throw new ArgumentNullException(nameof(eventSource));
            }

            this.startup = startup;
            this.serviceContext = serviceContext;
            this.endpointName = endpointName;
            this.eventSource = eventSource;
            this.appRoot = appRoot;
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
            var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
            var protocol = serviceEndpoint.Protocol;
            int port = serviceEndpoint.Port;

            if (this.serviceContext is StatefulServiceContext)
            {
                StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}{3}/{4}/{5}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/',
                    statefulServiceContext.PartitionId,
                    statefulServiceContext.ReplicaId,
                    Guid.NewGuid());
            }
            else if (this.serviceContext is StatelessServiceContext)
            {
                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/');
            }
            else
            {
                throw new InvalidOperationException();
            }

            this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

            try
            {
                this.eventSource.Message("Starting web server on " + this.listeningAddress);

                this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

                this.eventSource.Message("Listening on " + this.publishAddress);

                return Task.FromResult(this.publishAddress);
            }
            catch (Exception ex)
            {
                this.eventSource.Message("Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

                this.StopWebServer();

                throw;
            }
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
            this.eventSource.Message("Closing web server on endpoint {0}", this.endpointName);

            this.StopWebServer();

            return Task.FromResult(true);
        }

        public void Abort()
        {
            this.eventSource.Message("Aborting web server on endpoint {0}", this.endpointName);

            this.StopWebServer();
        }

        private void StopWebServer()
        {
            if (this.webApp != null)
            {
                try
                {
                    this.webApp.Dispose();
                }
                catch (ObjectDisposedException)
                {
                    // no-op
                }
            }
        }
    }
}
```

すべての部分を適切に配置したら、プロジェクトは、Reliable Services API エントリ ポイントと OWIN ホストのある一般的な Web API アプリケーションのようになったはずです。

## <a name="run-and-connect-through-a-web-browser"></a>Web ブラウザーによる実行と接続
[開発環境を設定](service-fabric-get-started.md)していない場合は、設定します。

これでサービスを構築し、デプロイできます。 Visual Studio で **F5** キーを押して、アプリケーションを構築し、デプロイします。 [診断イベント] ウィンドウに、Web サーバーが http://localhost:8281/ で開かれたことを示すメッセージが表示されます。

> [!NOTE]
> ポートが既に、コンピューターの別のプロセスによって開かれている場合、ここでエラーが表示されます。 これは、リスナーを開けなかったことを示します。 その場合は、ServiceManifest.xml のエンドポイント構成で、別のポートを使用してください。
> 
> 

サービスが実行されたら、ブラウザーを開き、 [http://localhost:8281/api/values](http://localhost:8281/api/values) に移動してテストします。

## <a name="scale-it-out"></a>スケール アウト
ステートレス Web アプリをスケール アウトすることは、コンピューターを追加して、それらで Web アプリを実行することを意味します。 Service Fabric のオーケストレーション エンジンは、新しいノードがクラスターに追加されるたびに、自動的にこれを実行します。 ステートレス サービスのインスタンスを作成する場合、作成するインスタンスの数を指定できます。 Service Fabric は、クラスターのノードにその数のインスタンスを配置します。 そして 1 つのノードに複数のインスタンスを作成しないようにします。 インスタンス数に **-1** を指定することで、常にすべてのノードにインスタンスを作成するよう Service Fabric に指示することもできます。 これにより、クラスターにノードを追加して、クラスターをスケールアウトするたびに、新しいノードにステートレス サービスのインスタンスが作成されることが保証されます。 この値はサービス インスタンスのプロパティであるため、サービス インスタンスを作成するときに設定されます。 これは PowerShell を使用して、実行できます。

```powershell

New-ServiceFabricService -ApplicationName "fabric:/WebServiceApplication" -ServiceName "fabric:/WebServiceApplication/WebService" -ServiceTypeName "WebServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1

```

これは、Visual Studio ステートレス サービス プロジェクトで既定のサービスを定義するときも設定できます。

```xml

<DefaultServices>
  <Service Name="WebService">
    <StatelessService ServiceTypeName="WebServiceType" InstanceCount="-1">
      <SingletonPartition />
    </StatelessService>
  </Service>
</DefaultServices>

```

アプリケーションおよびサービス インスタンスの作成方法の詳細については、「 [アプリケーションをデプロイする](service-fabric-deploy-remove-applications.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
[Visual Studio による Service Fabric アプリケーションのデバッグ](service-fabric-debugging-your-application.md)


