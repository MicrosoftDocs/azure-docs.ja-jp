---
title: WebJobs SDK の概要
description: イベント ドリブンのバックグラウンド処理のための WebJobs SDK の概要。 Azure サービスとサード パーティのサービス内のデータにアクセスする方法を説明します。
author: ggailey777
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 4a22602dd9638b981cfe3d8bae9b5cdaacbf90dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91652042"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>イベント ドリブンのバックグラウンド処理で Azure WebJobs SDK の使用を開始する

この記事では、Visual Studio 2019 を使用し、Azure WebJobs SDK プロジェクトを作成し、それをローカルで実行し、[Azure App Service](overview.md) にデプロイする方法について説明します。 バージョン 3.x の WebJobs SDK は、.NET Core と .NET Framework の両方のコンソール アプリをサポートしています。 WebJobs SDK の使用方法については、「[イベント ドリブンのバックグラウンド処理に Azure WebJobs SDK を使用する方法](webjobs-sdk-how-to.md)」を参照してください。

この記事では、.NET Core コンソール アプリとして Webjobs をデプロイする方法を示します。 .NET Framework コンソール アプリケーションとして Webjobs をデプロイするには、「[.NET Framework コンソール アプリとしての WebJobs](webjobs-dotnet-deploy-vs.md#webjobs-as-net-framework-console-apps)」を参照してください。 .NET Framework だけをサポートする WebJobs SDK バージョン 2.x に興味がある場合、「[Visual Studio を使用して Web ジョブを開発してデプロイする - Azure App Service](webjobs-dotnet-deploy-vs.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* **Azure 開発** ワークロードと共に [Visual Studio 2019 をインストール](/visualstudio/install/)します。 Visual Studio は既にあるものの、必要なワークロードがない場合は、 **[ツール] > [Get Tools and Features]\(ツールと機能の取得\)** を選択してワークロードを追加してください。

* WebJobs SDK プロジェクトを Azure に発行するには、[Azure アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)が必要です。

## <a name="create-a-project"></a>プロジェクトの作成

1. Visual Studio で、 **[新しいプロジェクトの作成]** を選択します。

2. **[コンソール アプリ (.NET Core)]** を選択します。

3. プロジェクトに *WebJobsSDKSample* という名前を付け、 **[作成]** を選択します。

   ![[新しいプロジェクト] ダイアログ](./media/webjobs-sdk-get-started/new-project.png)

## <a name="webjobs-nuget-packages"></a>WebJobs NuGet パッケージ

1. `Microsoft.Azure.WebJobs` を含む、[`Microsoft.Azure.WebJobs.Extensions` NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions/)の最新の安定した 3.x バージョンをインストールします。

     **パッケージ マネージャー コンソール** コマンドを次に示します。

     ```powershell
     Install-Package Microsoft.Azure.WebJobs.Extensions -version <3_X_VERSION>
     ```

    このコマンドの `<3_X_VERSION>` は、サポートされているバージョンのパッケージに置き換えてください。 

## <a name="create-the-host"></a>ホストを作成する

ホストは関数のランタイム コンテナーであり、トリガーをリッスンし、関数を呼び出します。 以下の手順では、[`IHost`](/dotnet/api/microsoft.extensions.hosting.ihost) を実装するホストを作成します。これは、ASP.NET Core での汎用ホストです。

1. *Program.cs* に、次の `using` ステートメントを追加します。

    ```cs
    using System.Threading.Tasks;
    using Microsoft.Extensions.Hosting;
    ```

1. `Main` メソッドを次のコードに置き換えます。

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

ASP.NET Core では、ホストの構成は [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) インスタンスでメソッドを呼び出すことによって設定します。 詳しくは、「[.NET での汎用ホスト](/aspnet/core/fundamentals/host/generic-host)」をご覧ください。 `ConfigureWebJobs` 拡張メソッドでは、WebJobs ホストが初期化されます。 `ConfigureWebJobs` では、特定の WebJobs 拡張を初期化し、その拡張のプロパティを設定します。  

## <a name="enable-console-logging"></a>コンソール ログ記録の有効化

ここでは、[ASP.NET Core ログ記録フレームワーク](/aspnet/core/fundamentals/logging)を使用するコンソール ログ記録を設定します。

1. `Microsoft.Extensions.Logging` を含む、[`Microsoft.Extensions.Logging.Console` NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)の最新の安定したバージョンをインストールします。

   **パッケージ マネージャー コンソール** コマンドを次に示します。

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version <3_X_VERSION>
   ```
   このコマンドの `<3_X_VERSION>` は、サポートされている 3.x バージョンのパッケージに置き換えてください。

1. *Program.cs* で、`using` ステートメントを追加します。

   ```cs
   using Microsoft.Extensions.Logging;
   ```

1. [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) で [`ConfigureLogging`](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) メソッドを呼び出します。 [`AddConsole`](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole) メソッドでは、構成にコンソールのログ記録が追加されます。

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    `Main` メソッドは次のようになります。

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

    この更新で次が行われます。

    * [ダッシュボード ログ記録](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs)を無効にします。 ダッシュボードはレガシ監視ツールであり、ダッシュボード ログ記録は高スループットの実稼働シナリオにはお勧めしません。
    * 既定の[フィルター](webjobs-sdk-how-to.md#log-filtering)を使用してコンソールのプロバイダーを追加します。

これで、Azure Storage キューへのメッセージ到着によってトリガーされる関数を追加することができます。

## <a name="install-the-storage-binding-extension"></a>Storage バインディング拡張機能をインストールする

バージョン 3.x より、WebJobs SDK で必要となるストレージ バインディング拡張機能を明示的にインストールする必要があります。 以前のバージョンでは、ストレージ バインディングは SDK に含まれていました。

1. [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet パッケージの最新の安定バージョンであるバージョン 3.x をインストールします。 

    **パッケージ マネージャー コンソール** コマンドを次に示します。

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version <3_X_VERSION>
    ```
    
    このコマンドの `<3_X_VERSION>` は、サポートされているバージョンのパッケージに置き換えてください。 

2. `ConfigureWebJobs` 拡張メソッド内で、[`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) インスタンスの `AddAzureStorage` メソッドを呼び出し、ストレージ拡張機能を初期化します。 この段階で、`ConfigureWebJobs` メソッドは次の例のようになります。

    ```cs
    builder.ConfigureWebJobs(b =>
                    {
                        b.AddAzureStorageCoreServices();
                        b.AddAzureStorage();
                    });
    ```

## <a name="create-a-function"></a>関数を作成する

1. プロジェクトを右クリックし、 **[追加]**  > 、 **[新しいアイテム]** の順に選択し、 **[クラス]** を選択し、新しい C# クラス ファイルに *Functions.cs* という名前を付け、 **[追加]** を選択します。

1. Functions.cs で、生成されたテンプレートを次のコードに置き換えます。
    
    ```cs
    using Microsoft.Azure.WebJobs;
    using Microsoft.Extensions.Logging;
    
    namespace WebJobsSDKSample
    {
        public class Functions
        {
            public static void ProcessQueueMessage([QueueTrigger("queue")] string message, ILogger logger)
            {
                logger.LogInformation(message);
            }
        }
    }
    ```

   `QueueTrigger` 属性は、`queue` と呼ばれる Azure Storage キューに新しいメッセージが書き込まれたときに、この関数を呼び出すようランタイムに通知します。 キュー メッセージの内容は、`message` パラメーター内のメソッド コードに提供されます。 メソッドの本文では、トリガー データを処理します。 この例では、コードはメッセージをログに記録するだけです。

   `message` パラメーターは文字列である必要はありません。 JSON オブジェクト、バイト配列、または [CloudQueueMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) オブジェクトにバインドすることもできます。 [キュー トリガーの使用方法](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#usage)に関するページをご覧ください。 バインドの種類 (キュー、BLOB、テーブルなど) ごとに、バインドできる異なる種類のパラメーター セットがあります。

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

ローカルで実行する Azure ストレージ エミュレーターには、WebJobs SDK に必要な機能がすべてあるわけではありません。 そのため、ここでは、Azure にストレージ アカウントを作成し、それを使用するようにプロジェクトを構成します。 ストレージ アカウントが既にある場合、手順 6 までスキップします。

1. Visual Studio で **サーバー エクスプローラー** を開き、Azure にサインインします。 **[Azure]** ノードを右クリックし、 **[Microsoft Azure サブスクリプションへの接続]** を選択します。

   ![Azure へのサインイン](./media/webjobs-sdk-get-started/sign-in.png)

1. **サーバー エクスプローラー** の **Azure** ノードで **[Storage]** を右クリックし、 **[ストレージ アカウントの作成]** をクリックします。

   ![[ストレージ アカウントの作成] メニュー](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. **[ストレージ アカウントの作成]** ダイアログ ボックスで、ストレージ アカウントの一意の名前を入力します。

1. App Service アプリを作成したのと同じ **リージョン** または近くのリージョンを選択します。

1. **［作成］** を選択します

   ![ストレージ アカウントの作成](./media/webjobs-sdk-get-started/create-storage-account.png)

1. **サーバー エクスプローラー** の **[Storage]** ノードで、新しいストレージ アカウントを選択します。 **[プロパティ]** ウィンドウで、 **[接続文字列]** 値フィールドの右側にある省略記号 ( **...** ) を選択します。

   ![接続文字列の省略記号](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. 接続文字列をコピーし、この値をいつでももう一度コピーできる任意の場所に保存します。

   ![接続文字列のコピー](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-to-run-locally"></a>ローカル環境で実行するようにストレージを構成する

WebJobs SDK では、Azure のアプリケーション設定内でストレージの接続文字列が検索されます。 ローカル環境で実行すると、ローカル構成ファイルまたは環境変数内でこの値が検索されます。

1. プロジェクトを右クリックし、 **[追加]**  > 、 **[新しいアイテム]** の順に選択し、 **[JavaScript JSON 構成ファイル]** を選択し、新しいファイルに *appsettings.json* という名前を付け、 **[追加]** を選択します。 

1. 新しいファイルで、次の例のように `AzureWebJobsStorage` フィールドを追加します。

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. *{storage connection string}* を先ほどコピーした接続文字列で置き換えます。

1. ソリューション エクスプローラーで *appsettings.json* ファイルを選択し、 **[プロパティ]** ウィンドウで **[出力ディレクトリにコピー]** を **[新しい場合はコピーする]** に設定します。

後で、Azure App Service のアプリに同じ接続文字列のアプリ設定を追加します。

## <a name="test-locally"></a>ローカルでテストする

ここでは、プロジェクトをローカルでビルドおよび実行し、キュー メッセージを作成して関数をトリガーします。

1. **Ctrl キーを押しながら F5 キー** を押してプロジェクトを実行します。

   コンソールは、ランタイムが関数を見つけ、キュー メッセージのトリガーを待機していることを示します。 v3.x のホストでは、次の出力が生成されます。

   ```console
    info: Microsoft.Azure.WebJobs.Hosting.JobHostService[0]
          Starting JobHost
    info: Host.Startup[0]
          Found the following functions:
          WebJobsSDKSample.Functions.ProcessQueueMessage

    info: Host.Startup[0]
          Job host started
    Application started. Press Ctrl+C to shut down.
    Hosting environment: Development
    Content root path: C:\WebJobsSDKSample\WebJobsSDKSample\bin\Debug\netcoreapp2.1\
   ```

1. コンソール ウィンドウを閉じます。

1. Visual Studio の **サーバー エクスプローラー** で、新しいストレージ アカウントのノードを展開し、 **[キュー]** を右クリックします。

1. **[キューの作成]** を選択します。

1. キューの名前として「*キュー*」と入力し、 **[OK]** を選択します。

   ![キューを作成して "queue" という名前を付けているスクリーンショット。 ](./media/webjobs-sdk-get-started/create-queue.png)

1. 新しいキューのノードを右クリックし、 **[キューの表示]** を選択します。

1. **メッセージの追加** アイコンを選択します。

   ![メッセージの追加アイコンが強調表示されているスクリーンショット。](./media/webjobs-sdk-get-started/create-queue-message.png)

1. **[メッセージの追加]** ダイアログで、「*Hello World!* 」を **[メッセージ テキスト]** として入力し、 **[OK]** を選択します。 これでキューにメッセージが入りました。

   ![キューの作成](./media/webjobs-sdk-get-started/hello-world-text.png)

1. プロジェクトを再度実行します。

   `ProcessQueueMessage` 関数で `QueueTrigger` 属性を使用したので、WeJobs SDK ランタイムは起動時にキュー メッセージをリッスンします。 "*キュー*" という名前のキューで新しいキュー メッセージを検索し、関数を呼び出します。

   [キュー ポーリング指数バックオフ](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#polling-algorithm)により、ランタイムがメッセージを検索して関数を呼び出すのに 2 分ほどかかる場合があります。 [開発モード](webjobs-sdk-how-to.md#host-development-settings)で実行すると、この待機時間を短縮できます。

   コンソール出力は次のようになります。

   ```console
    info: Function.ProcessQueueMessage[0]
          Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=2c319369-d381-43f3-aedf-ff538a4209b8)
    info: Function.ProcessQueueMessage[0]
          Trigger Details: MessageId: b00a86dc-298d-4cd2-811f-98ec39545539, DequeueCount: 1, InsertionTime: 1/18/2019 3:28:51 AM +00:00
    info: Function.ProcessQueueMessage.User[0]
          Hello World!
    info: Function.ProcessQueueMessage[0]
          Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=2c319369-d381-43f3-aedf-ff538a4209b8)
   ```

1. コンソール ウィンドウを閉じます。 

1. [キュー] ウィンドウに戻り、ウィンドウを更新します。 ローカルで実行されている関数によって処理されたため、メッセージが消えます。 

## <a name="add-application-insights-logging"></a>Application Insights ログの追加

プロジェクトを Azure で実行する場合、コンソール出力を表示して関数の実行を監視することはできません。 お勧めする監視ソリューションは [Application Insights](../azure-monitor/app/app-insights-overview.md) です。 詳しくは、「[Azure Functions を監視する](../azure-functions/functions-monitoring.md)」をご覧ください。

ここでは、Azure にデプロイする前に次のタスクを実行して Application Insights のログ記録を設定します。

* 使用する App Service アプリと Application Insights インスタンスがあることをご確認ください。
* Application Insights インスタンスおよび先ほど作成したストレージ アカウントを使用するように App Service アプリを構成します。
* Application Insights へのログ記録用のプロジェクトを設定します。

### <a name="create-app-service-app-and-application-insights-instance"></a>App Service アプリと Application Insights のインスタンスの作成

1. 使用できる App Service アプリがまだない場合は、[作成](quickstart-dotnet-framework.md)します。 アプリを作成するときに、接続されている Application Insights リソースも作成できます。 これを行うと、アプリ内で自動的に `APPINSIGHTS_INSTRUMENTATIONKEY` が設定されます。

1. 使用できる Application Insights リソースがまだない場合は、[作成](../azure-monitor/app/create-new-resource.md )します。 **[アプリケーションの種類]** を **[全般]** に設定し、「**インストルメンテーション キーをコピー**」の後のセクションをスキップします。

1. 使用する Application Insights リソースが既にある場合は、[インストルメンテーション キーをコピー](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key)します。

### <a name="configure-app-settings"></a>アプリケーションの設定の構成 

1. Visual Studio の **サーバー エクスプローラー** で、 **[Azure]** の下の **[App Service]** ノードを展開します。

1. App Service アプリがあるリソース グループを展開し、App Service アプリを右クリックします。

1. **[設定の表示]** を選択します。

1. **[接続文字列]** ボックスで、次のエントリを追加します。

   |名前  |接続文字列  |データベースの種類|
   |---------|---------|------|
   |AzureWebJobsStorage | {先ほどコピーした Storage 接続文字列}|Custom|

1. **[アプリケーション設定]** ボックスに Application Insights インストルメンテーション キーがない場合は、先ほどコピーしたキーを追加します (App Service アプリを作成する方法によっては、インストルメンテーション キーが既にある場合があります)。

   |名前  |値  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {instrumentation key} |

1. *{instrumentation key}* を、使用している Application Insights リソースからのインストルメンテーション キーで置き換えます。

1. **[保存]** を選択します。

1. Application Insights の接続がプロジェクトに追加されて、ローカル環境で実行できるようになります。 次の例のように、*appsettings.json* ファイルで `APPINSIGHTS_INSTRUMENTATIONKEY` フィールドを追加します。

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}",
        "APPINSIGHTS_INSTRUMENTATIONKEY": "{instrumentation key}"
    }
    ```

    *{instrumentation key}* を、使用している Application Insights リソースからのインストルメンテーション キーで置き換えます。

1. 変更を保存します。

### <a name="add-application-insights-logging-provider"></a>Application Insights ログ プロバイダーの追加

[Application Insights](../azure-monitor/app/app-insights-overview.md) のログ記録を利用するには、ログ記録のコードを次のように更新します。

* 既定の[フィルタリング](webjobs-sdk-how-to.md#log-filtering)で Application Insights ログ プロバイダーを追加します。 ローカルで実行しているときは、情報レベルとそれより高いレベルのログがすべて、コンソールと Application Insights の両方に出力されます。
* ホストの終了時にログ出力がフラッシュされるように、[LoggerFactory](./webjobs-sdk-how-to.md#logging-and-monitoring) オブジェクトを `using` ブロックに配置します。

1. [NuGet パッケージ `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/) の最新の安定した 3.x バージョンをインストールします

   **パッケージ マネージャー コンソール** コマンドを次に示します。

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <3_X_VERSION>
   ```
    このコマンドの `<3_X_VERSION>` は、サポートされているバージョンのパッケージに置き換えてください。

1. *Program.cs* を開き、`Main` メソッド内のコードを次のコードに置き換えます。

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.UseEnvironment(EnvironmentName.Development);
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                    b.AddAzureStorage();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();

                    // If the key exists in settings, use it to enable Application Insights.
                    string instrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                    if (!string.IsNullOrEmpty(instrumentationKey))
                    {
                        b.AddApplicationInsightsWebJobs(o => o.InstrumentationKey = instrumentationKey);
                    }
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

    先にアプリ設定に追加したキーを利用し、Application Insights プロバイダーがログ記録に追加されます。

## <a name="test-application-insights-logging"></a>Application Insights ログ記録のテスト

ここでは、もう一度ローカル環境で実行して、ログ データが Application Insights とコンソールに記録されることを確認します。

1. 「*Hello App Insights!* 」をメッセージ テキストとして入力すること以外は [前](#test-locally)に行ったときと同様に、Visual Studio の **サーバー エクスプローラー** を使用してキュー メッセージを 作成します。

1. プロジェクトを実行します。

   WebJobs SDK がキュー メッセージを処理し、コンソール ウィンドウにログが表示されます。

1. コンソール ウィンドウを閉じます。

1. [Azure Portal](https://portal.azure.com/) に移動し、Application Insights リソースを表示します。 **Application Insights** を探して選択します。

1. Application Insights インスタンスを選択します。

1. **[検索]** を選択します。

   ![[検索] の選択](./media/webjobs-sdk-get-started/select-search.png)

1. "*Hello App Insights!* " メッセージが表示されない場合は、 **[更新]** を数分ごとに定期的に選択します (Application Insights クライアントが処理するログをフラッシュするのに少し時間がかかるので、ログはすぐには表示されません)。

   ![Application Insights のログ](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. コンソール ウィンドウを閉じます。

## <a name="deploy-to-azure"></a><a name="deploy-as-a-webjob"></a>Azure にデプロイする

デプロイ中、関数を実行するアプリ サービス インスタンスを作成します。 .NET Core コンソール アプリを Azure で App Service に発行すると、Web ジョブとして自動的に実行されます。 発行に関する詳細については、「[Visual Studio を使用して Web ジョブを開発してデプロイする](webjobs-dotnet-deploy-vs.md)」を参照してください。

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)]

## <a name="trigger-the-function-in-azure"></a>Azure で関数をトリガーする

1. ローカルで実行していないことを確認します (コンソール ウィンドウがまだ開いている場合は閉じます)。 そうしないと、作成するキュー メッセージが最初にローカル インスタンスによって処理される可能性があります。

1. Visual Studio の **[キュー]** ページで、前と同様にキューにメッセージを追加します。

1. **[キュー]** ページを更新すると、Azure で実行されている関数によって処理されたため、新しいメッセージが表示されなくなります。

   > [!TIP]
   > Azure でテストするときに、[開発モード](webjobs-sdk-how-to.md#host-development-settings)を使用して、キュー トリガー関数がすぐに呼び出されることを確認し、[キュー ポーリング指数バックオフ](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#polling-algorithm)による遅延を回避します。

### <a name="view-logs-in-application-insights"></a>Application Insights のログの表示

1. [Azure Portal](https://portal.azure.com/) を開き、Application Insights リソースに移動します。

1. **[検索]** を選択します。

1. "*Hello Azure!* " メッセージが表示されない場合は、 **[更新]** を数分ごとに定期的に選択します

   Web ジョブで実行されている関数からのログを確認します。これには、前のセクションで入力した "*Hello Azure!* " テキストが含まれます。

## <a name="add-an-input-binding"></a>入力バインディングの追加

入力バインディングでは、データを読み取るコードを簡略化します。 この例では、キュー メッセージは BLOB 名になり、BLOB 名を使用して Azure Storage 内で BLOB を検索し、読み取ります。

1. *Functions.cs* で、`ProcessQueueMessage` メソッドを次のコードに置き換えます。

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   このコードでは、`queueTrigger` は[バインディング式](../azure-functions/functions-bindings-expressions-patterns.md)なので、実行時に別の値に解決されます。  実行時には、キュー メッセージの内容が含まれます。

1. `using` を追加します。

   ```cs
   using System.IO;
   ```

1. ストレージ アカウントで BLOB コンテナーを作成します。

   a. Visual Studio の **サーバー エクスプローラー** で、ストレージ アカウントのノードを展開し、 **[BLOB]** を右クリックして **[BLOB コンテナーの作成]** を選択します。

   b. **[BLOB コンテナーの作成]** ダイアログで、コンテナー名として「*container*」を入力し、 **[OK]** をクリックします。

1. BLOB コンテナーに *Program.cs* ファイルをアップロードします (このファイルは、ここでは例として使用しています。任意のテキスト ファイルをアップロードし、そのファイルの名前でキュー メッセージを作成できます)。

   a. **サーバー エクスプローラー** で、作成したコンテナーのノードをダブルクリックします。

   b. **[コンテナー]** ウィンドウで **アップロード** ボタンをクリックします。

   ![BLOB のアップロード ボタン](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. *Program.cs* を検索して選択し、 **[OK]** を選択します。

1. *Program.cs* をメッセージのテキストとして、以前に作成したキューにキュー メッセージを作成します。

   ![キュー メッセージ Program.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. プロジェクトをローカルで実行します。

   キュー メッセージによって関数がトリガーされ、BLOB が読み取られて、その長さがログに記録されます。 コンソール出力は次のようになります。

   ```console
   Found the following functions:
   ConsoleApp1.Functions.ProcessQueueMessage
   Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   Blob name:Program.cs
   Size: 532 bytes
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   ```

## <a name="add-an-output-binding"></a>出力バインディングを追加する

出力バインディングでは、データを書き込むコードを簡略化します。 この例では、サイズをログに記録する代わりに BLOB のコピーを書き込むことで、前の例を変更します。 BLOB ストレージのバインドは、前にインストールした Azure Storage の拡張機能パッケージに含まれます。

1. `ProcessQueueMessage` メソッドを次のコードに置き換えます。

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       [Blob("container/copy-{queueTrigger}", FileAccess.Write)] Stream outputBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
       myBlob.CopyTo(outputBlob);
   }
   ```

1. *Program.cs* をメッセージのテキストとして別のキュー メッセージを作成します。

1. プロジェクトをローカルで実行します。

   キュー メッセージによって関数がトリガーされ、BLOB が読み取られ、その長さがログに記録されて、新しい BLOB が作成されます。 コンソール出力は同じですが、BLOB コンテナー ウィンドウに移動して **[更新]** を選択すると、*copy-Program.cs* という名前の新しい BLOB が表示されます。

## <a name="republish-the-updates-to-azure"></a>Azure に更新プログラムを再発行する

1. **ソリューション エクスプローラー** で、プロジェクトを右クリックし、 **[発行]** を選択します。

1. **[発行]** ダイアログ ボックスで、現在のプロファイルが選択されていることを確認し、 **[発行]** を選択します。 発行の結果は **[出力]** ウィンドウに詳しく表示されます。
 
1. ファイルを再び BLOB コンテナーにアップロードし、アップロードしたファイルの名前になっているキューにメッセージを追加することで Azure で関数を検証します。 キューからメッセージが削除されたことと、BLOB コンテナーにファイルのコピーが作成されたことを確認できます。 

## <a name="next-steps"></a>次のステップ

この記事では、WebJobs SDK 3.x プロジェクトを作成、実行、デプロイする方法について説明しました。

> [!div class="nextstepaction"]
> [WebJobs SDK の詳細を確認してください](webjobs-sdk-how-to.md)