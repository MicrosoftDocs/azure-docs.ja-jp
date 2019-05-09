---
title: ASP.NET Core アプリを構成する - Azure App Service | Microsoft Docs
description: Azure App Service で動作するように ASP.NET Core アプリを構成する方法について説明する
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: e203877b2bc939c1d7fb9390df39f3e2451d12d3
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551033"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Azure App Service 向けの Linux ASP.NET Core アプリを構成する

ASP.NET Core アプリは、コンパイル済みバイナリとしてデプロイする必要があります。 Visual Studio 発行ツールではソリューションがビルドされてからコンパイル済みバイナリが直接デプロイされますが、App Service 展開エンジンではまずコード リポジトリがデプロイされ、その後にバイナリがコンパイルされます。

このガイドでは、App Service のビルトイン Linux コンテナーを使用する ASP.NET Core 開発者のために、主要な概念と手順を示します。 Azure App Service を初めて使用する場合は、まず [ASP.NET Core クイックスタート](quickstart-dotnetcore.md)と [ASP.NET Core と SQL Database のチュートリアル](tutorial-dotnetcore-sqldb-app.md)に従ってください。

## <a name="show-net-core-version"></a>.NET Core バージョンを表示する

現在の .NET Core バージョンを表示するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

サポートされているすべての .NET Core バージョンを表示するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>.NET Core バージョンを設定する

.NET Core バージョンを 2.1 に設定するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="access-environment-variables"></a>環境変数へのアクセス

App Service では、アプリ コードの外部で[アプリ設定を指定](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings)できます。 その後、標準の ASP.NET パターンを使用して、それらにアクセスできます。

```csharp
include Microsoft.Extensions.Configuration;
// retrieve App Service app setting
System.Configuration.ConfigurationManager.AppSettings["MySetting"]
// retrieve App Service connection string
Configuration.GetConnectionString("MyDbConnection")
```

App Service と *Web.config* で同じ名前のアプリ設定を構成した場合は、App Service の値が Web.config の値よりも優先されます。 Web.config の値ではアプリをローカルでデバッグできますが、App Service の値では実稼働設定の製品内でアプリを実行できます。 接続文字列は同じように機能します。 これにより、コード リポジトリの外部にアプリケーション シークレットを保存し、コードを変更することなく適切な値にアクセスできます。

## <a name="get-detailed-exceptions-page"></a>例外の詳細ページを表示する

Visual Studio デバッガーで ASP.NET アプリの実行中に例外が発生すると、ブラウザーに例外の詳細ページが表示されますが、App Service ではそのページの代わりに汎用の **HTTP 500** エラーが表示されるか、「**要求の処理中にエラーが発生しました。**」 メッセージが表示されます。 App Service で例外の詳細ページを表示するには、<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a> で次のコマンドを実行してアプリ設定 `ASPNETCORE_ENVIRONMENT` をアプリに追加します。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>HTTPS セッションの検出

App Service では、[SSL 終了](https://wikipedia.org/wiki/TLS_termination_proxy)がネットワーク ロード バランサーで発生するため、すべての HTTPS リクエストは暗号化されていない HTTP リクエストとしてアプリに到達します。 ユーザー要求が暗号化されるかどうかをアプリのロジックで認識する必要がある場合は、*Startup.cs* で Forwarded Headers Middleware を構成します。

- `Startup.ConfigureServices` で、`X-Forwarded-For` ヘッダーと `X-Forwarded-Proto` ヘッダーを転送するように [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) を使ってミドルウェアを構成します。
- ミドルウェアが App Service のロード バランサーを信頼できるようにするために、既知のネットワークにプライベート IP アドレス範囲を追加します。
- 他のミドルウェアを呼び出す前に、`Startup.Configure` 内で [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) メソッドを呼び出します。

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

詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer)」を参照してください。

## <a name="deploy-multi-project-solutions"></a>マルチ プロジェクト ソリューションをデプロイする

ルート ディレクトリの *.csproj* ファイルを使用して ASP.NET リポジトリを展開エンジンにデプロイすると、エンジンによってプロジェクトがデプロイされます。 ルート ディレクトリの *.sln* ファイルを使用して ASP.NET リポジトリをデプロイすると、エンジンは最初に検出した Web サイトまたは Web アプリケーション プロジェクトを App Service アプリとして選択します。 必要なプロジェクトがエンジンで選択されない可能性があります。

マルチ プロジェクト ソリューションをデプロイする場合は、App Service で使用するプロジェクトを次の 2 つの方法で指定できます。

### <a name="using-deployment-file"></a>.deployment ファイルを使用

リポジトリのルートに *.deployment* ファイルを追加し、次のコードを追加します。

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>アプリ設定を使用

<a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a> で、次の CLI コマンドを実行して App Service アプリにアプリ設定を追加します。 *\<app-name>*、*\<resource-group-name>*、および *\<project-name>* を適切な値で置き換えます。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>診断ログにアクセスする

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>ブラウザーで SSH セッションを開く

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [チュートリアル:ASP.NET Core アプリと SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [App Service Linux の FAQ](app-service-linux-faq.md)