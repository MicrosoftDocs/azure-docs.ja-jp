---
title: ASP.NET Core による Azure App Configuration のクイック スタート | Microsoft Docs
description: ASP.NET Core で Azure App Configuration を使用する場合のクイック スタートです
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET Core
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 600c808c0bda991bb7203bbf60c098918e274da6
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326623"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>クイック スタート:Azure App Configuration を使用して ASP.NET Core アプリを作成する

このクイック スタートでは、コードとは別にアプリケーション設定のストレージと管理を一元化するために、Azure App Configuration を ASP.NET Core アプリに組み込みます。 ASP.NET Core では、アプリケーションによって指定される 1 つ以上のデータ ソースからの設定を使用して、キーと値に基づく 1 つの構成オブジェクトが作成されます。 これらのデータ ソースは、"*構成プロバイダー*" と呼ばれます。 App Configuration の .NET Core クライアントはそのようなプロバイダーとして実装されるため、このサービスは他のデータ ソースと同じように表示されます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
- [.NET コア SDK](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>アプリ構成ストアを作成する

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. **[Configuration Explorer]\(構成エクスプローラー)**  >  **[+ 作成]** の順に選択して、次のキーと値のペアを追加します。

    | キー | 値 |
    |---|---|
    | TestApp:Settings:BackgroundColor | 白 |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | 黒 |
    | TestApp:Settings:Message | Azure App Configuration からのデータ |

    **[ラベル]** と **[コンテンツの種類]** は、現時点では空にしておきます。

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core Web アプリケーションの作成

[.NET Core コマンド ライン インターフェイス (CLI)](https://docs.microsoft.com/dotnet/core/tools/) を使用して、新しい ASP.NET Core MVC Web アプリ プロジェクトを作成します。 Visual Studio ではなく .NET Core CLI を使用する利点は、Windows、macOS、および Linux プラットフォームで使用できることです。

1. プロジェクト用の新規フォルダーを作成します。 このクイック スタートでは、*TestAppConfig* という名前にします。

2. 新しいフォルダーで次のコマンドを実行して、新しい ASP.NET Core MVC Web アプリ プロジェクトを作成します。

        dotnet new mvc

## <a name="add-secret-manager"></a>シークレット マネージャーを追加する

プロジェクトに[シークレット マネージャー ツール](https://docs.microsoft.com/aspnet/core/security/app-secrets)を追加します。 シークレット マネージャー ツールは、開発作業の機密データをプロジェクト ツリーの外部に格納します。 これにより、ソース コード内のアプリ シークレットが偶発的に共有されるのを防止できます。

- *.csproj* ファイルを開きます。 次に示すように、`UserSecretsId` 要素を追加し、その値を自分の値 (通常は GUID) に置き換えます。 ファイルを保存します。

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

## <a name="connect-to-an-app-configuration-store"></a>アプリ構成ストアに接続する

1. 次のコマンドを実行して、`Microsoft.Azure.AppConfiguration.AspNetCore` NuGet パッケージへの参照を追加します。

        dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-009200001-7

2. 次のコマンドを実行して、プロジェクトのパッケージを復元します。

        dotnet restore

3. シークレット マネージャーに、*ConnectionStrings:AppConfig* という名前のシークレットを追加します。

    このシークレットには、アプリ構成ストアにアクセスするための接続文字列が格納されます。 次のコマンドの値を、自分のアプリ構成ストアの接続文字列に置き換えます。

    このコマンドは、 *.csproj* ファイルと同じディレクトリで実行する必要があります。

        dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>

    シークレット マネージャーは、Web アプリをローカルにテストするためだけに使用されます。 たとえば [Azure App Service](https://azure.microsoft.com/services/app-service/web) にアプリをデプロイするときは、シークレット マネージャーで接続文字列を格納する代わりに、App Service でアプリケーションの設定の**接続文字列**を使用します。

    このシークレットには、構成 API を使用してアクセスします。 サポートされているすべてのプラットフォームで、構成 API を使用する際の構成名にコロン (:) を使用できます。 [環境別の構成](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0)に関するページを参照してください。

4. *Program.cs* を開き、.NET Core App Configuration プロバイダーへの参照を追加します。

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

5. `config.AddAzureAppConfiguration()` メソッドを呼び出して App Configuration を使用するように、`CreateWebHostBuilder` メソッドを更新します。

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

6. Views の Home ディレクトリにある *Index.cshtml* を開いて、内容を次のコードに置き換えます。

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]</h1>
    ```

7. Views の Shared ディレクトリにある *_Layout.cshtml* を開いて、内容を次のコードに置き換えます。

    ```html
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

1. .NET Core CLI を使用してアプリケーションをビルドするには、コマンド シェルで次のコマンドを実行します。

        dotnet build

2. ビルドが正常に完了したら、次のコマンドを実行して、Web アプリをローカルで実行します。

        dotnet run

3. ブラウザー ウィンドウを開いて、`http://localhost:5000` (ローカルでホストされた Web アプリの既定の URL) に移動します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次の手順

このクイック スタートでは、新しいアプリ構成ストアを作成して、[App Configuration プロバイダー](https://go.microsoft.com/fwlink/?linkid=2074664)から ASP.NET Core Web アプリと共に使用しました。 App Configuration の使用方法についてさらに学習するには、認証について示した次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [マネージド ID の統合](./howto-integrate-azure-managed-service-identity.md)
