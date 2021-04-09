---
title: Azure Functions による Azure App Configuration のクイック スタート | Microsoft Docs
description: このクイックスタートでは、Azure App Configuration および C# を使用して、Azure Functions アプリを作成します。 App Configuration ストアを作成して接続します。 関数をローカルでテストします。
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: 9d378b21132e6646329c459401255ef9a3ed9426
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724242"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>クイック スタート:Azure App Configuration を使用して Azure Functions アプリを作成する

このクイックスタートでは、コードとは別にすべてのアプリケーション設定のストレージと管理を一元化するために、Azure App Configuration サービスを Azure Functions アプリに組み込みます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/dotnet)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) と **Azure 開発** ワークロード。
- [Azure Functions ツール](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>App Configuration ストアを作成する

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. **[構成エクスプローラー]**  >  **[+ 作成]**  >  **[キー値]** の順に選択して、次のキーと値のペアを追加します。

    | キー | 値 |
    |---|---|
    | TestApp:Settings:Message | Azure App Configuration からのデータ |

    **[ラベル]** と **[コンテンツの種類]** は、現時点では空にしておきます。

8. **[適用]** を選択します。

## <a name="create-a-functions-app"></a>関数アプリを作成する

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>App Configuration ストアに接続する
このプロジェクトは、[.NET Azure Functions で依存関係の挿入](../azure-functions/functions-dotnet-dependency-injection.md)を使用して、新しい構成ソースとして Azure App Configuration を追加します。

1. プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。 **[参照]** タブで以下の NuGet パッケージを検索し、自分のプロジェクトに追加します。
   - [Microsoft.Extensions.Configuration.AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration/) バージョン 4.1.0 以降
   - [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/) バージョン 1.1.0 以降 

2. 次のコードを使用して、新しいファイル *Startup.cs* を追加します。 これにより、`FunctionsStartup` 抽象クラスを実装する `Startup` という名前のクラスが定義されます。 アセンブリ属性は、Azure Functions の起動時に使われる型名を指定するために使用されます。

    `AddAzureAppConfiguration()` を呼び出すことで、`ConfigureAppConfiguration` メソッドをオーバーライドし、Azure App Configuration プロバイダーを新しい構成ソースとして追加します。 この時点ではサービスを登録する必要がないため、`Configure` メソッドは空のままです。
    
    ```csharp
    using System;
    using Microsoft.Azure.Functions.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;

    [assembly: FunctionsStartup(typeof(FunctionApp.Startup))]

    namespace FunctionApp
    {
        class Startup : FunctionsStartup
        {
            public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
            {
                string cs = Environment.GetEnvironmentVariable("ConnectionString");
                builder.ConfigurationBuilder.AddAzureAppConfiguration(cs);
            }

            public override void Configure(IFunctionsHostBuilder builder)
            {
            }
        }
    }
    ```

3. *Function1.cs* を開いて、次の名前空間を追加します。

    ```csharp
    using Microsoft.Extensions.Configuration;
    ```

   依存関係の挿入によって、`IConfiguration` のインスタンスを取得するために使用するコンストラクターを追加します。

    ```csharp
    private readonly IConfiguration _configuration;

    public Function1(IConfiguration configuration)
    {
        _configuration = configuration;
    }
    ```

4. 構成から値を読み取るように `Run` メソッドを更新します。

    ```csharp
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string keyName = "TestApp:Settings:Message";
        string message = _configuration[keyName];

        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

   `Function1` クラスと `Run` メソッドを静的にすることはできません。 `static` 修飾子が自動生成された場合は削除します。

## <a name="test-the-function-locally"></a>関数をローカルでテストする

1. **ConnectionString** という名前の環境変数に、App Configuration ストアへのアクセス キーを設定します。 Windows コマンド プロンプトを使用する場合は、次のコマンドを実行してコマンド プロンプトを再起動し、変更が反映されるようにします。

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell を使用する場合は、次のコマンドを実行します。

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    macOS または Linux を使用する場合は、次のコマンドを実行します。

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. F5 キーを押して関数をテストします。 メッセージが表示されたら、Visual Studio からの要求に同意し、**Azure Functions Core (CLI)** ツールをダウンロードしてインストールします。 また、ツールで HTTP 要求を処理できるように、ファイアウォールの例外を有効にすることが必要になる場合もあります。

3. Azure Functions のランタイムの出力から、関数の URL をコピーします。

    ![クイック スタート: VS での関数のデバッグ](./media/quickstarts/function-visual-studio-debugging.png)

4. HTTP 要求の URL をブラウザーのアドレス バーに貼り付けます。 関数によって返されたローカルの GET 要求に対するブラウザーでの応答を次の図に示します。

    ![クイック スタート: ローカル環境での関数の起動](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、新しい App Configuration ストアを作成して、[App Configuration プロバイダー](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration)から Azure Functions アプリと共に使用しました。 構成が動的に更新されるように Azure Functions アプリを更新する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure Functions で動的な構成を有効にする](./enable-dynamic-configuration-azure-functions-csharp.md)