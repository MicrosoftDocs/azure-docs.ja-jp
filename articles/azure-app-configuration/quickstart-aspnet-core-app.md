---
title: ASP.NET Core による Azure App Configuration のクイック スタート | Microsoft Docs
description: Azure App Configuration を使用して ASP.NET Core アプリを作成し、ASP.NET Core アプリケーションのアプリケーション設定のストレージと管理を一元化します。
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp, contperf-fy21q1
ms.topic: quickstart
ms.date: 09/25/2020
ms.author: alkemper
ms.openlocfilehash: 353a934842fd872d48d091f1df1b6d94ac4599ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98663436"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>クイック スタート:Azure App Configuration を使用して ASP.NET Core アプリを作成する

このクイックスタートでは、Azure App Configuration を使用して、ASP.NET Core アプリのアプリケーション設定のストレージと管理を一元化します。 ASP.NET Core では、アプリによって指定された 1 つ以上のデータ ソースからの設定を使用して、キーと値に基づく 1 つの構成オブジェクトが作成されます。 これらのデータ ソースは、"*構成プロバイダー*" と呼ばれます。 App Configuration の .NET Core クライアントは構成プロバイダーとして実装されるため、このサービスは別のデータ ソースのように見えます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/dotnet)
* [.NET Core SDK](https://dotnet.microsoft.com/download)

> [!TIP]
> Azure Cloud Shell は無料の対話型シェルです。これを使用して、この記事のコマンド ライン命令を実行できます。 .NET Core SDK などの一般的な Azure ツールがプレインストールされています。 Azure サブスクリプションにログインしている場合は、shell.azure.com から [Azure Cloud Shell](https://shell.azure.com) を起動します。 Azure Cloud Shell の詳細については、[ドキュメントを参照](../cloud-shell/overview.md)してください

## <a name="create-an-app-configuration-store"></a>App Configuration ストアを作成する

[!INCLUDE[Azure App Configuration resource creation steps](../../includes/azure-app-configuration-create.md)]

7. **[操作]**  >  **[構成エクスプローラー]**  >  **[作成]**  >  **[キー値]** の順に選択して、次のキーと値のペアを追加します。

    | Key                                | Value                               |
    |------------------------------------|-------------------------------------|
    | `TestApp:Settings:BackgroundColor` | *#FFF*                              |
    | `TestApp:Settings:FontColor`       | *#000*                              |
    | `TestApp:Settings:FontSize`        | *24*                                |
    | `TestApp:Settings:Message`         | *Azure App Configuration からのデータ* |

    **[ラベル]** と **[コンテンツの種類]** は、現時点では空にしておきます。 **[適用]** を選択します。

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core Web アプリケーションの作成

[.NET Core コマンド ライン インターフェイス (CLI)](/dotnet/core/tools) を使用して、新しい ASP.NET Core MVC プロジェクトを作成します。 これらのツールは、[Azure Cloud Shell](https://shell.azure.com) によって提供されます。 また、Windows、macOS、Linux の各プラットフォームでも利用できます。

次のコマンドを実行して、新しい *TestAppConfig* フォルダーに ASP.NET Core MVC プロジェクトを作成します。

```dotnetcli
dotnet new mvc --no-https --output TestAppConfig
```

[!INCLUDE[Add Secret Manager support to an ASP.NET Core project](../../includes/azure-app-configuration-add-secret-manager.md)]

## <a name="connect-to-the-app-configuration-store"></a>App Configuration ストアに接続する

1. 次のコマンドを実行して、[Microsoft.Azure.AppConfiguration.AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) NuGet パッケージ参照を追加します。

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. *.csproj* ファイルと同じディレクトリで次のコマンドを実行します。 このコマンドでは、シークレット マネージャーを使用して、`ConnectionStrings:AppConfig` という名前のシークレットを格納します。これには、App Configuration ストアの接続文字列が格納されます。 `<your_connection_string>` プレースホルダーを App Configuration ストアの接続文字列に置き換えます。 接続文字列は、Azure portal の **[アクセス キー]** で確認できます。

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig "<your_connection_string>"
    ```

    > [!IMPORTANT]
    > 一部のシェルでは、引用符で囲まれていない限り、接続文字列が切り捨てられます。 `dotnet user-secrets list` コマンドの出力に接続文字列全体が表示されていることを確認します。 そうでない場合は、接続文字列を引用符で囲んでコマンドを再実行します。

    シークレット マネージャーは、Web アプリをローカルにテストするためだけに使用されます。 [Azure App Service](https://azure.microsoft.com/services/app-service/web) にアプリをデプロイした場合、シークレット マネージャーの代わりに App Service の **[接続文字列]** アプリケーション設定を使用して、接続文字列を格納します。

    このシークレットには、.NET Core 構成 API を使用してアクセスします。 構成名の中のコロン (`:`) は、サポートされているすべてのプラットフォーム上の構成 API で機能します。 詳細については、「[構成キーと値](/aspnet/core/fundamentals/configuration#configuration-keys-and-values)」をご覧ください。

1. *Program.cs* で、.NET Core 構成 API 名前空間への参照を追加します。

    ```csharp
    using Microsoft.Extensions.Configuration;
    ```

1. `AddAzureAppConfiguration` メソッドを呼び出して App Configuration を使用するように、`CreateWebHostBuilder` メソッドを更新します。

    > [!IMPORTANT]
    > `CreateHostBuilder` は、.NET Core 3.x の `CreateWebHostBuilder` に代わるものです。 お使いの環境に応じて適切な構文を選択します。

     #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    var connection = settings.GetConnectionString("AppConfig");
                    config.AddAzureAppConfiguration(connection);
                }).UseStartup<Startup>());
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    var connection = settings.GetConnectionString("AppConfig");
                    config.AddAzureAppConfiguration(connection);
                }).UseStartup<Startup>());
    ```

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration(config =>
            {
                var settings = config.Build();
                var connection = settings.GetConnectionString("AppConfig");
                config.AddAzureAppConfiguration(connection);
            })
            .UseStartup<Startup>();
    ```

    ---

    上記の変更により、[App Configuration の構成プロバイダー](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration)が .NET Core 構成 API に登録されました。

## <a name="read-from-the-app-configuration-store"></a>App Configuration ストアから読み取る

App Configuration ストアに保存されている値を読み取って表示するには、次の手順を実行します。 .NET Core 構成 API を使用してストアにアクセスします。 Razor 構文を使用してキーの値を表示します。

*\<app root>/Views/Home/Index.cshtml* を開き、その内容を次のコードに置き換えます。

```cshtml
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

上記のコードでは、App Configuration ストアのキーは次のように使用されます。

* `TestApp:Settings:BackgroundColor` キーの値は、CSS の `background-color` プロパティに割り当てられます。
* `TestApp:Settings:FontColor` キーの値は、CSS の `color` プロパティに割り当てられます。
* `TestApp:Settings:FontSize` キーの値は、CSS の `font-size` プロパティに割り当てられます。
* `TestApp:Settings:Message` キーの値は、見出しとして表示されます。

## <a name="build-and-run-the-app-locally"></a>アプリをビルドしてローカルで実行する

1. .NET Core CLI を使用してアプリをビルドするには、プロジェクトのルート ディレクトリに移動します。 次のコマンドをコマンド シェルで実行します。

    ```dotnetcli
    dotnet build
    ```

1. ビルドが正常に完了したら、次のコマンドを実行して、Web アプリをローカルで実行します。

    ```dotnetcli
    dotnet run
    ```

1. ローカル コンピューターで作業している場合は、ブラウザーを使用して `http://localhost:5000` に移動します。 このアドレスは、ローカルでホストされている Web アプリの既定の URL です。 Azure Cloud Shell で作業している場合は、 **[Web プレビュー]** 、 **[構成]** の順に選択します。

    ![[Web プレビュー] ボタンを見つける](./media/quickstarts/cloud-shell-web-preview.png)

    プレビュー用のポートを構成するよう求めるメッセージが表示されたら、「*5000*」と入力し、 **[開いて参照する]** を選択します。 Web ページには、"Data from Azure App Configuration" と表示されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE[Azure App Configuration cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次のステップ

このクイック スタートでは次の作業を行います。

* 新しい App Configuration ストアをプロビジョニングしました。
* App Configuration ストアの .NET Core 構成プロバイダーを登録しました。
* 構成プロバイダーを使用して、App Configuration ストアのキーを読み取りました。
* Razor 構文を使用して、App Configuration ストアのキー値を表示しました。

構成設定を動的に更新するように ASP.NET Core アプリを構成する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [動的な構成を有効にする](./enable-dynamic-configuration-aspnet-core.md)