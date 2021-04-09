---
title: 複数のインスタンスでスケーリングする - Azure SignalR Service
description: 多くのスケーリング シナリオでは、大規模なデプロイを作成するために、お客様が複数のインスタンスをプロビジョニングし、それらを一緒に使用しなければならない場合がよくあります。 たとえば、シャーディングでは複数のインスタンスのサポートが必要です。
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: fd6ac8c4d4fc4c3fec4f549f8ef4f955e2b1c637
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89439216"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>複数のインスタンスでの SignalR Service のスケーリング方法
最新の SignalR Service SDK では、SignalR Service インスタンスの複数のエンドポイントがサポートされています。 この機能を使用して同時接続をスケーリングすることや、リージョンをまたがるメッセージングにこれを使用することができます。

## <a name="for-aspnet-core"></a>ASP.NET Core の場合

### <a name="how-to-add-multiple-endpoints-from-config"></a>構成から複数のエンドポイントを追加する方法

SignalR Service 接続文字列のキー `Azure:SignalR:ConnectionString` または `Azure:SignalR:ConnectionString:` を使用して構成します。

キーが `Azure:SignalR:ConnectionString:` で開始する場合、`Azure:SignalR:ConnectionString:{Name}:{EndpointType}` の形式にする必要があります。ここで、`Name` と `EndpointType` は、`ServiceEndpoint` オブジェクトのプロパティであり、コードからアクセス可能です。

次の `dotnet` コマンドを使用して、複数のインスタンス接続文字列を追加できます。

```cmd
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>コードから複数のエンドポイントを追加する方法

Azure SignalR Service エンドポイントのプロパティを説明するために `ServicEndpoint` クラスが導入されています。
次のように、Azure SignalR Service SDK を使用して複数のインスタンス エンドポイントを構成できます。
```cs
services.AddSignalR()
        .AddAzureSignalR(options => 
        {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString0>"),
                new ServiceEndpoint("<ConnectionString1>", type: EndpointType.Primary, name: "east-region-a"),
                new ServiceEndpoint("<ConnectionString2>", type: EndpointType.Primary, name: "east-region-b"),
                new ServiceEndpoint("<ConnectionString3>", type: EndpointType.Secondary, name: "backup"),
            };
        });
```

### <a name="how-to-customize-endpoint-router"></a>エンドポイント ルーターのカスタマイズ方法

既定では、SDK では [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) を使用してエンドポイントを選択します。

#### <a name="default-behavior"></a>既定の動作 
1. クライアント要求のルーティング

    クライアントがアプリ サーバーを使用して `/negotiate` すると、 既定では、SDK は使用可能な一連のサービス エンドポイントから 1 つのエンドポイントを **ランダムに選択** します。

2. サーバー メッセージのルーティング

    *特定の**接続**にメッセージを送信*すると、ターゲット接続は現在のサーバーにルーティングされ、メッセージは接続済みエンドポイントに直接送信されます。 それ以外の場合、メッセージはすべての Azure SignalR エンドポイントにブロードキャストされます。

#### <a name="customize-routing-algorithm"></a>ルーティング アルゴリズムをカスタマイズする
メッセージの送信先エンドポイントを特定する特別な知識を持っている場合は、独自のルーターを作成できます。

例として、`east-` で開始するグループが常に `east` という名前のエンドポイントに送信される場合のカスタム ルーターの定義を次に示します。

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override IEnumerable<ServiceEndpoint> GetEndpointsForGroup(string groupName, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the group broadcast behavior, if the group name starts with "east-", only send messages to endpoints inside east
        if (groupName.StartsWith("east-"))
        {
            return endpoints.Where(e => e.Name.StartsWith("east-"));
        }

        return base.GetEndpointsForGroup(groupName, endpoints);
    }
}
```

別の例として、アプリ サーバーの場所に応じてエンドポイントを選択するために、既定のネゴシエート動作をオーバーライドする場合の定義を次に示します。

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(HttpContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (endpointName.Count == 0)
        {
            context.Response.StatusCode = 400;
            var response = Encoding.UTF8.GetBytes("Invalid request");
            context.Response.Body.Write(response, 0, response.Length);
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

次のように、必ずルーターを DI コンテナーに登録してください。

```cs
services.AddSingleton(typeof(IEndpointRouter), typeof(CustomRouter));
services.AddSignalR()
        .AddAzureSignalR(
            options => 
            {
                options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
            });
```

## <a name="for-aspnet"></a>ASP.NET の場合

### <a name="how-to-add-multiple-endpoints-from-config"></a>構成から複数のエンドポイントを追加する方法

SignalR Service 接続文字列のキー `Azure:SignalR:ConnectionString` または `Azure:SignalR:ConnectionString:` を使用して構成します。

キーが `Azure:SignalR:ConnectionString:` で開始する場合、`Azure:SignalR:ConnectionString:{Name}:{EndpointType}` の形式にする必要があります。ここで、`Name` と `EndpointType` は、`ServiceEndpoint` オブジェクトのプロパティであり、コードからアクセス可能です。

`web.config` に複数のインスタンス接続文字列を追加できます。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <connectionStrings>
    <add name="Azure:SignalR:ConnectionString" connectionString="<ConnectionString1>"/>
    <add name="Azure:SignalR:ConnectionString:en-us" connectionString="<ConnectionString2>"/>
    <add name="Azure:SignalR:ConnectionString:zh-cn:secondary" connectionString="<ConnectionString3>"/>
    <add name="Azure:SignalR:ConnectionString:Backup:secondary" connectionString="<ConnectionString4>"/>
  </connectionStrings>
  ...
</configuration>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>コードから複数のエンドポイントを追加する方法

Azure SignalR Service エンドポイントのプロパティを説明するために `ServicEndpoint` クラスが導入されています。
次のように、Azure SignalR Service SDK を使用して複数のインスタンス エンドポイントを構成できます。

```cs
app.MapAzureSignalR(
    this.GetType().FullName, 
    options => {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString1>"),
                new ServiceEndpoint("<ConnectionString2>"),
                new ServiceEndpoint("<ConnectionString3>"),
            }
        });
```

### <a name="how-to-customize-router"></a>ルーターをカスタマイズする方法

ASP.NET SignalR と ASP.NET Core SignalR の唯一の相違は、`GetNegotiateEndpoint` の http コンテキスト型です。 ASP.NET SignalR の場合は [IOwinContext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19) 型です。

ASP.NET SignalR のカスタム ネゴシエートの例を次に示します。

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(IOwinContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (string.IsNullOrEmpty(endpointName))
        {
            context.Response.StatusCode = 400;
            context.Response.Write("Invalid request.");
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

次のように、必ずルーターを DI コンテナーに登録してください。

```cs
var hub = new HubConfiguration();
var router = new CustomRouter();
hub.Resolver.Register(typeof(IEndpointRouter), () => router);
app.MapAzureSignalR(GetType().FullName, hub, options => {
    options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
});
```

## <a name="configuration-in-cross-region-scenarios"></a>リージョンをまたがるシナリオでの構成

`ServiceEndpoint` オブジェクトには、`EndpointType` プロパティがあり、`primary` または `secondary` の値を持ちます。

`primary` エンドポイントは、クライアント トラフィックの受信に推奨されるエンドポイントであり、信頼性の高いネットワーク接続を持ちます。`secondary` エンドポイントのネットワーク接続は、信頼性が低いと見なされ、メッセージのブロードキャストなどのサーバーからクライアントへのトラフィックの受信のみに使用され、クライアントからサーバーへのトラフィックの受信には使用されません。

リージョンをまたがるケースでは、ネットワークが不安定になる場合があります。 *米国東部* に配置されているアプリ サーバーの場合、同じ *米国東部* リージョンに配置された SignalR Service エンドポイントを `primary` として構成でき、他のリージョンにあるエンドポイントは `secondary` として設定されます。 この構成では、他のリージョンのサービス エンドポイントはこの *米国東部* のアプリ サーバーからのメッセージを **受信** できますが、**リージョンをまたがる** クライアントはこのアプリ サーバにルーティングされません。 次の図にアーキテクチャを示します。

![地域をまたがるインフラ](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

クライアントが既定のルーターを使用してアプリ サーバーとの `/negotiate` を試行すると、SDK は使用可能な一連の `primary` エンドポイントから 1 つのエンドポイントを **ランダムに選択** します。 プライマリ エンドポイントが使用できない場合、SDK は使用可能なすべての `secondary` エンドポイントから **ランダムに選択** します。 サーバーとサービス エンドポイント間の接続が保持されている場合、エンドポイントは **使用可能** としてマークされます。

リージョンをまたがるシナリオでは、*米国東部* でホストされているアプリ サーバーとの `/negotiate` をクライアントが試行すると、既定では、同じリージョンに配置されている `primary` エンドポイントが常に返されます。 *米国東部* のすべてのエンドポイントが使用不可の場合、クライアントは他のリージョンのエンドポイントにリダイレクトされます。 次の「フェールオーバー」セクションでこのシナリオについて詳しく説明します。

![通常のネゴシエート](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>フェールオーバー

すべての `primary` エンドポイントが使用不可の場合、クライアントの `/negotiate` により使用可能な `secondary` エンドポイントが選択されます。 このフェールオーバー メカニズムでは、各エンドポイントが少なくとも 1 つのアプリ サーバーに対して `primary` として機能する必要があります。

![フェールオーバー](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>次のステップ

このガイドでは、同じアプリケーション内の複数のインスタンスをスケーリング、シャーディング、およびリージョンをまたがるシナリオ向けに構成する方法について説明しました。

複数のエンドポイントのサポートは、高可用性およびディザスター リカバリーのシナリオでも使用できます。

> [!div class="nextstepaction"]
> [ディザスター リカバリーと高可用性のための SignalR Service の設定](./signalr-concept-disaster-recovery.md)
