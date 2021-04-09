---
title: Azure App Configuration の動的な構成を Azure Functions アプリで使用するためのチュートリアル | Microsoft Docs
description: このチュートリアルでは、Azure Functions アプリの構成データを動的に更新する方法を学習します。
services: azure-app-configuration
documentationcenter: ''
author: zhenlan
manager: qingye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/17/2019
ms.author: zhenlwa
ms.custom: devx-track-csharp, azure-functions
ms.tgt_pltfrm: Azure Functions
ms.openlocfilehash: add4b54adb02db09536f4e56a7f039c46245c182
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97963564"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>チュートリアル:Azure Functions アプリで動的な構成を使用する

App Configuration .NET 構成プロバイダーは、アプリケーションのアクティビティによって動的にもたらされる構成のキャッシュと更新をサポートします。 このチュートリアルでは、自分が作成するコードに、構成の動的更新を実装する方法について説明します。 これは、クイックスタートで紹介されている Azure Functions アプリに基づいています。 先に進む前に、[Azure App Configuration を使用した Azure Functions アプリの作成](./quickstart-azure-functions-csharp.md)を完了しておいてください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * App Configuration ストアへの変更に合わせて構成を更新するように Azure Functions アプリを設定する。
> * 最新の構成を Azure Functions の呼び出しに挿入する。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) と **Azure 開発** ワークロード
- [Azure Functions ツール](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- [Azure App Configuration を使用した Azure Functions アプリの作成](./quickstart-azure-functions-csharp.md)に関するクイックスタートを終えていること

## <a name="reload-data-from-app-configuration"></a>App Configuration からデータを再度読み込む

1. *Startup.cs* を開き、`ConfigureAppConfiguration` メソッドを更新します。 

   `ConfigureRefresh` メソッドは、アプリケーション内で更新がトリガーされるたびに変更の有無がチェックされる設定を登録します。この後の手順で `_configurationRefresher.TryRefreshAsync()` を追加しますが、そのタイミングで更新がトリガーされます。 `refreshAll` パラメーターが指定されているので、App Configuration プロバイダーは、登録された設定に変更が検出されるたびに構成全体をリロードします。

    更新の対象として登録されるすべての設定は、キャッシュの有効期限が既定で 30 秒になります。 これは `AzureAppConfigurationRefreshOptions.SetCacheExpiration` メソッドを呼び出すことで更新できます。

    ```csharp
    public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
    {
        builder.ConfigurationBuilder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   // Load all keys that start with `TestApp:`
                   .Select("TestApp:*")
                   // Configure to reload configuration if the registered 'Sentinel' key is modified
                   .ConfigureRefresh(refreshOptions =>
                      refreshOptions.Register("TestApp:Settings:Sentinel", refreshAll: true));
        });
    }
    ```

   > [!TIP]
   > App Configuration にあるキーと値の複数のペアを更新する際、すべての変更が済むまでは、アプリケーションに構成をリロードさせたくないのが普通でしょう。 **Sentinel** キーを登録し、その他の構成変更がすべて完了したときにだけ、そのキーを更新してください。 そうすることで、アプリケーション内の構成の一貫性を確保しやすくなります。

2. 依存関係の挿入を通じて Azure App Configuration サービスを利用できるように `Configure` メソッドを更新します。

    ```csharp
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.Services.AddAzureAppConfiguration();
    }
    ```

3. *Function1.cs* を開いて、次の名前空間を追加します。

    ```csharp
    using System.Linq;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

   `IConfigurationRefresherProvider` のインスタンスを依存関係の挿入を通じて取得し、そこから `IConfigurationRefresher` のインスタンスを取得できるように、コンストラクターを更新します。

    ```csharp
    private readonly IConfiguration _configuration;
    private readonly IConfigurationRefresher _configurationRefresher;

    public Function1(IConfiguration configuration, IConfigurationRefresherProvider refresherProvider)
    {
        _configuration = configuration;
        _configurationRefresher = refresherProvider.Refreshers.First();
    }
    ```

4. `Run` メソッドを更新し、Functions 呼び出しの最初に `TryRefreshAsync` メソッドを使用して構成を更新するよう伝えます。 キャッシュの有効期間に達していなければ、何も実行されません。 現在の Functions 呼び出しをブロックせずに構成を更新したい場合は、`await` オペレーターを削除してください。 その場合は、後続の Functions 呼び出しが、更新された値を取得するようになります。

    ```csharp
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await _configurationRefresher.TryRefreshAsync(); 

        string keyName = "TestApp:Settings:Message";
        string message = _configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>関数をローカルでテストする

1. **ConnectionString** という名前の環境変数に、アプリ構成ストアへのアクセス キーを設定します。 Windows コマンド プロンプトを使用する場合は、次のコマンドを実行してコマンド プロンプトを再起動し、変更が反映されるようにします。

    ```console
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell を使用する場合は、次のコマンドを実行します。

    ```powershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    macOS または Linux を使用する場合は、次のコマンドを実行します。

    ```console
    export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. 関数をテストするには、F5 キーを押します。 メッセージが表示されたら、Visual Studio からの要求に同意し、**Azure Functions Core (CLI)** ツールをダウンロードしてインストールします。 また、ツールで HTTP 要求を処理できるように、ファイアウォールの例外を有効にすることが必要になる場合もあります。

3. Azure Functions のランタイムの出力から、関数の URL をコピーします。

    ![クイック スタート: VS での関数のデバッグ](./media/quickstarts/function-visual-studio-debugging.png)

4. HTTP 要求の URL をブラウザーのアドレス バーに貼り付けます。 関数によって返されたローカルの GET 要求に対するブラウザーでの応答を次の図に示します。

    ![クイック スタート: ローカル環境での関数の起動](./media/quickstarts/dotnet-core-function-launch-local.png)

5. [Azure portal](https://portal.azure.com) にサインインします。 **[すべてのリソース]** を選択し、クイックスタートで作成した App Configuration ストアを選択します。

6. **[構成エクスプローラー]** を選択して次のキーの値を更新します。

    | Key | 値 |
    |---|---|
    | TestApp:Settings:Message | Azure App Configuration からのデータ - 更新済み |

   次に、Sentinel キーを作成するか、Sentinel キーが既に存在している場合は、その値を変更します。その例を次に示します。

    | Key | [値] |
    |---|---|
    | TestApp:Settings:Sentinel | v1 |


7. ブラウザーを数回更新します。 30 秒後、キャッシュされた設定の有効期限が切れると、更新された値と共に、Functions 呼び出しの応答がページに表示されます。

    ![クイックスタート: ローカル環境での関数の更新](./media/quickstarts/dotnet-core-function-refresh-local.png)

> [!NOTE]
> このチュートリアルで使用したコード例は、[App Configuration の GitHub リポジトリ](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction)からダウンロードできます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、App Configuration から動的に構成設定を更新できるように Azure Functions アプリを設定しました。 App Configuration へのアクセスを効率化する Azure マネージド ID を使用する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [マネージド ID の統合](./howto-integrate-azure-managed-service-identity.md)
