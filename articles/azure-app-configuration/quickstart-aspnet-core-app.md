---
title: ASP.NET Core による Azure App Configuration のクイック スタート | Microsoft Docs
description: Azure App Configuration を使用して ASP.NET Core アプリを作成し、ASP.NET Core アプリケーションのアプリケーション設定のストレージと管理を一元化します。
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 41675eb1911eede750b5a9cdc19cfe49e4699bac
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590304"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>クイック スタート:Azure App Configuration を使用して ASP.NET Core アプリを作成する

このクイックスタートでは、Azure App Configuration を使用して、ASP.NET Core アプリのアプリケーション設定のストレージと管理を一元化します。 ASP.NET Core では、アプリケーションによって指定される 1 つ以上のデータ ソースからの設定を使用して、キーと値に基づく 1 つの構成オブジェクトが作成されます。 これらのデータ ソースは、"*構成プロバイダー*" と呼ばれます。 App Configuration の .NET Core クライアントは構成プロバイダーとして実装されるため、このサービスは別のデータ ソースのように見えます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download)

>[!TIP]
> Azure Cloud Shell は無料のインタラクティブ シェルです。これを使用して、この記事のコマンド ライン命令を実行することができます。  .NET Core SDK などの一般的な Azure ツールがプレインストールされています。 Azure サブスクリプションにログインしている場合は、shell.azure.com から [Azure Cloud Shell](https://shell.azure.com) を起動します。  Azure Cloud Shell の詳細については、[ドキュメントを参照](../cloud-shell/overview.md)してください

## <a name="create-an-app-configuration-store"></a>App Configuration ストアを作成する

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. **[構成エクスプローラー]**  >  **[+ 作成]**  >  **[キー値]** の順に選択して、次のキーと値のペアを追加します。

    | Key | Value |
    |---|---|
    | TestApp:Settings:BackgroundColor | White |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | Black |
    | TestApp:Settings:Message | Azure App Configuration からのデータ |

    **[ラベル]** と **[コンテンツの種類]** は、現時点では空にしておきます。 **[適用]** を選択します。

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core Web アプリケーションの作成

[.NET Core コマンド ライン インターフェイス (CLI)](https://docs.microsoft.com/dotnet/core/tools/) を使用して、新しい ASP.NET Core MVC Web アプリ プロジェクトを作成します。 これらのツールは、[Azure Cloud Shell](https://shell.azure.com) によって提供されます。  また、Windows、macOS、Linux の各プラットフォームでも利用できます。

1. プロジェクト用の新規フォルダーを作成します。 このクイック スタートでは、*TestAppConfig* という名前にします。

1. 新しいフォルダーで次のコマンドを実行して、新しい ASP.NET Core MVC Web アプリ プロジェクトを作成します。

```dotnetcli
dotnet new mvc --no-https
```

## <a name="add-secret-manager"></a>シークレット マネージャーを追加する

シークレット マネージャーを使用するには、 *.csproj* ファイルに `UserSecretsId` 要素を追加します。

1. *.csproj* ファイルを開きます。

1.  ここに示すように、`UserSecretsId` 要素を追加します。 同じ GUID を使用することも、この値を独自の値に置き換えることもできます。

    > [!IMPORTANT]
    > `CreateHostBuilder` により、.NET Core 3.0 の `CreateWebHostBuilder` が置き換えられます。  お使いの環境に応じて適切な構文を選択します。
    
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)
    
    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    
        <PropertyGroup>
            <TargetFramework>netcoreapp2.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>
    
        <ItemGroup>
            <PackageReference Include="Microsoft.AspNetCore.App" />
            <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
        </ItemGroup>
    
    </Project>
    ```
    
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)
    
    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
        
        <PropertyGroup>
            <TargetFramework>netcoreapp3.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>
    
    </Project>
    ```
    ---

1. *.csproj* ファイルを保存します。

シークレット マネージャー ツールは、開発作業の機密データをプロジェクト ツリーの外部に格納します。 これにより、ソース コード内のアプリ シークレットが偶発的に共有されるのを防止できます。

> [!TIP]
> シークレット マネージャーの詳細については、「[ASP.NET Core での開発におけるアプリ シークレットの安全な保存](https://docs.microsoft.com/aspnet/core/security/app-secrets)」を参照してください。

## <a name="connect-to-an-app-configuration-store"></a>App Configuration ストアに接続する

1. 次のコマンドを実行して、`Microsoft.Azure.AppConfiguration.AspNetCore` NuGet パッケージへの参照を追加します。

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. 次のコマンドを実行して、プロジェクトのパッケージを復元します。

    ```dotnetcli
    dotnet restore
    ```

1. シークレット マネージャーに、*ConnectionStrings:AppConfig* という名前のシークレットを追加します。

    このシークレットには、App Configuration ストアにアクセスするための接続文字列が格納されます。 次のコマンドの値を、自分の App Configuration ストアの接続文字列に置き換えます。 接続文字列は、Azure portal の **[アクセス キー]** で確認できます。

    このコマンドは、 *.csproj* ファイルと同じディレクトリで実行する必要があります。

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    > [!IMPORTANT]
    > 一部のシェルでは、引用符で囲まれていない限り、接続文字列が切り捨てられます。 `dotnet user-secrets` コマンドの出力に接続文字列全体が表示されていることを確認します。 そうでない場合は、接続文字列を引用符で囲んでコマンドを再実行します。

    シークレット マネージャーは、Web アプリをローカルにテストするためだけに使用されます。 たとえば、[Azure App Service](https://azure.microsoft.com/services/app-service/web) にアプリをデプロイすると、シークレット マネージャーの代わりに App Service の **[接続文字列]** アプリケーション設定を使用して、接続文字列を格納することができます。

    このシークレットには、構成 API を使用してアクセスします。 サポートされているすべてのプラットフォームで、構成 API を使用する際の構成名にコロン (:) を使用できます。 [環境別の構成](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0)に関するページを参照してください。

1. *Program.cs* を開き、.NET Core App Configuration プロバイダーへの参照を追加します。

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. `config.AddAzureAppConfiguration()` メソッドを呼び出して App Configuration を使用するように、`CreateWebHostBuilder` メソッドを更新します。

    > [!IMPORTANT]
    > `CreateHostBuilder` により、.NET Core 3.0 の `CreateWebHostBuilder` が置き換えられます。  お使いの環境に応じて適切な構文を選択します。

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
        })
        .UseStartup<Startup>());
    ```

    ---

1. *<app root>[Views]、[Home]* の順に移動し、*Index.cshtml* を開きます。 その内容を次のコードに置き換えます。

    ```HTML
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"]px;
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]</h1>
    ```

1. *<app root>[Views]、[Shared]* の順に移動し、 *_Layout.cshtml* を開きます。 その内容を次のコードに置き換えます。

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>@ViewData["Title"] - hello_world</title>

        <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
        <link rel="stylesheet" href="~/css/site.css" />
    </head>
    <body>
        <div class="container body-content">
            @RenderBody()
        </div>

        <script src="~/lib/jquery/dist/jquery.js"></script>
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>

        @RenderSection("Scripts", required: false)
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>アプリをビルドしてローカルで実行する

1. .NET Core CLI を使用してアプリをビルドするには、アプリケーションのルート ディレクトリに移動し、コマンド シェルで次のコマンドを実行します。

    ```dotnetcli
    dotnet build
    ```

1. ビルドが正常に完了したら、次のコマンドを実行して、Web アプリをローカルで実行します。

    ```dotnetcli
    dotnet run
    ```

1. ローカル コンピューターで作業している場合は、ブラウザーを使用して `http://localhost:5000` に移動します。 これは、ローカルにホストされている Web アプリの既定の URL です。  

Azure Cloud Shell で作業している場合は、 *[Web プレビュー]* 、 *[構成]* の順に選択します。  

![[Web プレビュー] ボタンを見つける](./media/quickstarts/cloud-shell-web-preview.png)

プレビュー用のポートを構成するよう求めるメッセージが表示されたら、「5000」と入力し、 *[開いて参照する]* を選択します。  Web ページには、"Data from Azure App Configuration" と表示されます。

![クイックスタート アプリを起動する](./media/quickstarts/aspnet-core-app-launch-local-before.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、新しい App Configuration ストアを作成して、[App Configuration プロバイダー](https://go.microsoft.com/fwlink/?linkid=2074664)から ASP.NET Core Web アプリと共に使用しました。 構成設定を動的に更新するように ASP.NET Core アプリを構成する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [動的な構成を有効にする](./enable-dynamic-configuration-aspnet-core.md)
