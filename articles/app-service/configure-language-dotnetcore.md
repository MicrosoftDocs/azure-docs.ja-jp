---
title: ASP.NET Core アプリの構成
description: Azure App Service のネイティブ Windows インスタンス、または事前に作成した Linux コンテナーで ASP.NET Core アプリを構成する方法について学習します。 この記事では、最も一般的な構成タスクを紹介しています。
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 3456adc2b143f1f51115183fe4873938d067d267
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961671"
---
# <a name="configure-an-aspnet-core-app-for-azure-app-service"></a>Azure App Service 向けの ASP.NET Core アプリを構成する

> [!NOTE]
> .NET Framework の ASP.NET については、「[Azure App Service 向けの ASP.NET アプリを構成する](configure-language-dotnet-framework.md)」を参照してください

ASP.NET Core アプリは、コンパイル済みバイナリとして Azure App Service にデプロイする必要があります。 Visual Studio 発行ツールではソリューションがビルドされてからコンパイル済みバイナリが直接デプロイされますが、App Service 展開エンジンではまずコード リポジトリがデプロイされ、その後にバイナリがコンパイルされます。

このガイドでは、ASP.NET Core 開発者向けに主要な概念と手順を説明します。 Azure App Service を初めて使用する場合は、まず [ASP.NET Core クイックスタート](quickstart-dotnetcore.md)と [ASP.NET Core と SQL Database のチュートリアル](tutorial-dotnetcore-sqldb-app.md)に従ってください。

::: zone pivot="platform-windows"  

## <a name="show-supported-net-core-runtime-versions"></a>サポートされている .NET Core ランタイム バージョンを表示する

App Service でサポートされているすべての .NET Core バージョンが Windows インスタンスに既にインストールされています。 使用可能な .NET Core ランタイムと SDK のバージョンを表示するには、`https://<app-name>.scm.azurewebsites.net/DebugConsole` に移動し、ブラウザーベースのコンソールで次のコマンドを実行します。

```azurecli-interactive
dotnet --info
```

::: zone-end

::: zone pivot="platform-linux"

## <a name="show-net-core-version"></a>.NET Core バージョンを表示する

現在の .NET Core バージョンを表示するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

サポートされているすべての .NET Core バージョンを表示するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

::: zone-end

## <a name="set-net-core-version"></a>.NET Core バージョンを設定する

::: zone pivot="platform-windows"  

ASP.NET Core プロジェクトのプロジェクト ファイルにターゲット フレームワークを設定します。 詳細については、.NET Core ドキュメントの「[使用する .NET Core バージョンを選択する](/dotnet/core/versions/selection)」を参照してください。

::: zone-end

::: zone pivot="platform-linux"

.NET Core バージョンを 3.1 に設定するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|3.1"
```

::: zone-end

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>ビルドの自動化のカスタマイズ

ビルドの自動化を有効にして Git または zip パッケージを使用してアプリをデプロイする場合、App Service のビルドの自動化によって、次の手順が実行されます。

1. `PRE_BUILD_SCRIPT_PATH` によって指定された場合、カスタム スクリプトを実行します。
1. `dotnet restore` を実行して、NuGet の依存関係を復元します。
1. `dotnet publish` を実行して、運用環境用のバイナリを構築します。
1. `POST_BUILD_SCRIPT_PATH` によって指定された場合、カスタム スクリプトを実行します。

`PRE_BUILD_COMMAND` および `POST_BUILD_COMMAND` は、既定では空の環境変数です。 ビルド前のコマンドを実行するには、`PRE_BUILD_COMMAND` を定義します。 ビルド後のコマンドを実行するには、`POST_BUILD_COMMAND` を定義します。

次の例では、一連のコマンドに対して 2 つの変数をコンマ区切りで指定しています。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

ビルドの自動化をカスタマイズするためのその他の環境変数については、「[Oryx の構成](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)」を参照してください。

Linux 上で App Service によって ASP.NET Core アプリが実行されビルドされる方法に関する詳細については、[Oryx ドキュメントの .NET Core アプリが検出されビルドされる方法](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/dotnetcore.md)に関するページを参照してください。

::: zone-end

## <a name="access-environment-variables"></a>環境変数へのアクセス

App Service では、アプリ コードの外部で[アプリ設定を指定](configure-common.md#configure-app-settings)できます。 次に、標準の ASP.NET Core 依存関係の挿入パターンを使用して、任意のクラスでこれらにアクセスできます。

```csharp
using Microsoft.Extensions.Configuration;

namespace SomeNamespace 
{
    public class SomeClass
    {
        private IConfiguration _configuration;
    
        public SomeClass(IConfiguration configuration)
        {
            _configuration = configuration;
        }
    
        public SomeMethod()
        {
            // retrieve nested App Service app setting
            var myHierarchicalConfig = _configuration["My:Hierarchical:Config:Data"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

たとえば、App Service と *appsettings.json* で同じ名前のアプリ設定を構成した場合は、App Service の値が *appsettings.json* の値よりも優先されます。 ローカルの *appsettings.json* 値ではアプリをローカルでデバッグできますが、App Service の値では実稼働設定の製品内でアプリを実行できます。 接続文字列は同じように機能します。 これにより、コード リポジトリの外部にアプリケーション シークレットを保存し、コードを変更することなく適切な値にアクセスできます。

> [!NOTE]
> *appsettings. json* の[階層型の構成データ](/aspnet/core/fundamentals/configuration/#hierarchical-configuration-data)は、.NET Core に標準の `:` 区切り記号を使用してアクセスされることに注意してください。 App Service で特定の階層型構成設定をオーバーライドするには、キーにアプリ設定名を同じ区切り形式で設定します。 [Cloud Shell](https://shell.azure.com) で次の例を実行できます。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings My:Hierarchical:Config:Data="some value"
```

## <a name="deploy-multi-project-solutions"></a>マルチ プロジェクト ソリューションをデプロイする

Visual Studio ソリューションに複数のプロジェクトが含まれている場合、Visual Studio の発行プロセスには、デプロイするプロジェクトの選択が既に含まれています。 ビルド自動化を有効にした状態で、Git や ZIP デプロイなどを使用して App Service 展開エンジンにデプロイすると、App Service 展開エンジンは、App Service アプリとして検出された最初の Web サイトまたは Web アプリケーション プロジェクトを選択します。 `PROJECT` アプリ設定を指定することで、App Service で使用するプロジェクトを指定できます。 たとえば、[Cloud Shell](https://shell.azure.com) で以下を実行します。

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>診断ログにアクセスする

ASP.NET Core には、[App Service 用の組み込みのログ プロバイダー](/aspnet/core/fundamentals/logging/#azure-app-service)が用意されています。 プロジェクトの *Program.cs* で、次の例に示すように、`ConfigureLogging` 拡張メソッドを使用してそのプロバイダーをアプリケーションに追加します。

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureLogging(logging =>
        {
            logging.AddAzureWebAppDiagnostics();
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

その後、[標準の .NET Core パターン](/aspnet/core/fundamentals/logging)を使用して、ログを構成して生成できます。

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

App Service での ASP.NET Core アプリのトラブルシューティングの詳細については、「[Azure App Service と IIS での ASP.NET Core のトラブルシューティング](/aspnet/core/test/troubleshoot-azure-iis)」を参照してください

## <a name="get-detailed-exceptions-page"></a>例外の詳細ページを表示する

Visual Studio デバッガーで ASP.NET Core アプリの実行中に例外が発生すると、ブラウザーに例外の詳細ページが表示されますが、App Service ではそのページの代わりに汎用の **HTTP 500** エラーが表示されるか、「**要求の処理中にエラーが発生しました。** 」 メッセージが表示されます。 App Service で例外の詳細ページを表示するには、<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a> で次のコマンドを実行してアプリ設定 `ASPNETCORE_ENVIRONMENT` をアプリに追加します。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>HTTPS セッションの検出

App Service では、[SSL 終了](https://wikipedia.org/wiki/TLS_termination_proxy)がネットワーク ロード バランサーで発生するため、すべての HTTPS リクエストは暗号化されていない HTTP リクエストとしてアプリに到達します。 ユーザー要求が暗号化されるかどうかをアプリのロジックで認識する必要がある場合は、*Startup.cs* で Forwarded Headers Middleware を構成します。

- `Startup.ConfigureServices` で、`X-Forwarded-For` ヘッダーと `X-Forwarded-Proto` ヘッダーを転送するように [ForwardedHeadersOptions](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) を使ってミドルウェアを構成します。
- ミドルウェアが App Service のロード バランサーを信頼できるようにするために、既知のネットワークにプライベート IP アドレス範囲を追加します。
- 他のミドルウェアを呼び出す前に、`Startup.Configure` 内で [UseForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) メソッドを呼び出します。

3 つの要素をすべてまとめると、次の例のようなコードになります。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:10.0.0.0"), 104));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:192.168.0.0"), 112));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:172.16.0.0"), 108));
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    ...

    app.UseMvc();
}
```

詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](/aspnet/core/host-and-deploy/proxy-load-balancer)」を参照してください。

::: zone pivot="platform-linux"

## <a name="open-ssh-session-in-browser"></a>ブラウザーで SSH セッションを開く

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:ASP.NET Core アプリと SQL Database](tutorial-dotnetcore-sqldb-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [App Service Linux の FAQ](faq-app-service-linux.md)

::: zone-end