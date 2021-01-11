---
title: Azure Functions による Azure App Configuration のクイック スタート | Microsoft Docs
description: このクイックスタートでは、Azure App Configuration および C# を使用して、Azure Functions アプリを作成します。 App Configuration ストアを作成して接続します。 関数をローカルでテストします。
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 1/9/2019
ms.author: lcozzens
ms.openlocfilehash: e15c83504ee0eebb925c122d0efd4896e7b55916
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590287"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>クイック スタート:Azure App Configuration を使用して Azure Functions アプリを作成する

このクイックスタートでは、コードとは別にすべてのアプリケーション設定のストレージと管理を一元化するために、Azure App Configuration サービスを Azure Functions アプリに組み込みます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) と **Azure 開発**ワークロード。
- [Azure Functions ツール](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>App Configuration ストアを作成する

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. **[構成エクスプローラー]**  >  **[+ 作成]**  >  **[キー値]** の順に選択して、次のキーと値のペアを追加します。

    | キー | 値 |
    |---|---|
    | TestApp:Settings:Message | Azure App Configuration からのデータ |

    **[ラベル]** と **[コンテンツの種類]** は、現時点では空にしておきます。

7. **[適用]** を選択します。

## <a name="create-a-functions-app"></a>関数アプリを作成する

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>App Configuration ストアに接続する

1. プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。 **[参照]** タブで `Microsoft.Extensions.Configuration.AzureAppConfiguration` NuGet パッケージを検索し、プロジェクトに追加します。 見つからない場合は、**[プレリリースを含める]** チェック ボックスをオンにします。

2. *Function1.cs* を開き、.Net Core 構成および App Configuration 構成プロバイダーの名前空間を追加します。

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

3. `IConfiguration` のシングルトン インスタンスを作成するために、`Configuration` という名前の `static` プロパティを追加します。 `AddAzureAppConfiguration()` を呼び出して App Configuration に接続するための `static` コンストラクターを追加します。 これによって、アプリケーションの起動時に一度だけ構成が読み込まれます。 以後、すべての Functions 呼び出しについて、同じ構成インスタンスが使用されます。

    ```csharp
    private static IConfiguration Configuration { set; get; }

    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        Configuration = builder.Build();
    }
    ```

4. 構成から値を読み取るように `Run` メソッドを更新します。

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];

        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

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

このクイックスタートでは、新しい App Configuration ストアを作成して、[App Configuration プロバイダー](https://go.microsoft.com/fwlink/?linkid=2074664)から Azure Functions アプリと共に使用しました。 構成設定を動的に更新するように Azure Functions アプリを構成する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [動的な構成を有効にする](./enable-dynamic-configuration-azure-functions-csharp.md)
