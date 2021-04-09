---
title: チュートリアル:.NET Core アプリでプッシュ更新による動的な構成を使用する
titleSuffix: Azure App Configuration
description: このチュートリアルでは、プッシュ更新を使用して、.NET Core アプリの構成データを動的に更新する方法を学習します
services: azure-app-configuration
documentationcenter: ''
author: abarora
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 07/25/2020
ms.author: abarora
ms.openlocfilehash: 977982bf1a36b4b85524df2513f2272fe4a8d1bf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101701520"
---
# <a name="tutorial-use-dynamic-configuration-using-push-refresh-in-a-net-core-app"></a>チュートリアル:.NET Core アプリでプッシュ更新による動的な構成を使用する

App Configuration .NET Core クライアント ライブラリでは、アプリケーションを再起動させることなく必要に応じて構成を更新できます。 次の 2 つの方法のどちらかまたは両方を使用して、App Configuration 内の変更を検出するようにアプリケーションを構成することができます。

1. ポーリング モデル: ポーリングを使用して構成の変更を検出する既定の動作です。 キャッシュされた設定値の有効期限が切れた後、次に `TryRefreshAsync` または `RefreshAsync` を呼び出すと、構成が変更されているかどうかをチェックするための要求がサーバーに送信され、必要に応じて、更新された構成がプルされます。

1. プッシュ モデル: [App Configuration のイベント](./concept-app-configuration-event.md)を使用して構成の変更を検出します。 キー値の変更イベントを Azure Event Grid に送信するよう App Configuration を一度設定すれば、アプリケーションは、それらのイベントを使用できるので、構成を最新の状態に保つために必要な要求の総数は最小限で済みます。 アプリケーションは、Event Grid から直接イベントをサブスクライブするか、Webhook、Azure 関数、Service Bus トピックなど、[サポートされているイベント ハンドラー](../event-grid/event-handlers.md)のいずれかを使用してイベントをサブスクライブすることができます。

これらのイベントは、アプリケーションが Event Grid から直接サブスクライブできるほか、Web hook を介したり、Azure Service Bus にイベントを転送したりすることでサブスクライブすることができます。 Azure Service Bus SDK には、メッセージ ハンドラーを登録するための API が用意されています。HTTP エンドポイントがないアプリケーションや、イベント グリッドに絶えず変更をポーリングすることが好ましくないアプリケーションでは、この API を利用することで、このプロセスを単純化することができます。

このチュートリアルでは、自分が作成するコードに、プッシュ更新を使用して構成の動的更新を実装する方法について説明します。 これは、クイック スタートで紹介されているアプリに基づいています。 先に進む前に、[App Configuration を使用した .NET Core アプリの作成](./quickstart-dotnet-core-app.md)を完了しておいてください。

このチュートリアルの手順は、任意のコード エディターを使用して実行できます。 推奨のエディターは [Visual Studio Code](https://code.visualstudio.com/) です (Windows、macOS、および Linux プラットフォームで使用できます)。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * App Configuration から Service Bus トピックに構成の変更イベントを送信するようにサブスクリプションを設定する
> * App Configuration への変更に合わせて構成を更新するように .NET Core アプリを設定する。
> * 最新の構成をアプリケーションに取り込む。

## <a name="prerequisites"></a>前提条件

このチュートリアルを実行するには、[.NET Core SDK](https://dotnet.microsoft.com/download) をインストールします。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-azure-service-bus-topic-and-subscription"></a>Azure Service Bus のトピックとサブスクリプションを設定する

このチュートリアルでは、App Configuration に絶えず変更をポーリングすることが望ましくないアプリケーションで構成変更の検出を単純化するために、Service Bus と Event Grid の統合を使用します。 Azure Service Bus SDK には、メッセージ ハンドラーを登録するための API が用意されています。この API を使用することで、App Configuration で変更が検出されたときに構成を更新することができます。 「[Quickstart:Azure portal を使用して Service Bus のトピックとサブスクリプションを作成するクイックスタート](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)の手順に従って、サービス バスの名前空間、トピック、サブスクリプションを作成します。

リソースの作成後、以下の環境変数を追加します。 アプリケーション コードで構成変更のイベント ハンドラーを登録する際に使用します。

| Key | 値 |
|---|---|
| ServiceBusConnectionString | サービス バスの名前空間の接続文字列 |
| ServiceBusTopic | Service Bus トピックの名前 |
| ServiceBusSubscription | Service Bus サブスクリプションの名前 |

## <a name="set-up-event-subscription"></a>イベント サブスクリプションの設定

1. Azure portal で App Configuration リソースを開き、[`Events`] ペインの [`+ Event Subscription`] をクリックします。

    ![App Configuration イベント](./media/events-pane.png)

1. `Event Subscription` と `System Topic` の名前を入力します。

    ![イベント サブスクリプションの作成](./media/create-event-subscription.png)

1. [`Endpoint Type`] に [`Service Bus Topic`] を選択し、Service Bus トピックを選択して、[`Confirm Selection`] をクリックします。

    ![イベント サブスクリプションの Service Bus エンドポイント](./media/event-subscription-servicebus-endpoint.png)

1. [`Create`] をクリックしてイベント サブスクリプションを作成します。

1. [`Events`] ペインの [`Event Subscriptions`] をクリックして、サブスクリプションが正しく作成されたことを確認します。

    ![App Configuration イベント サブスクリプション](./media/event-subscription-view.png)

> [!NOTE]
> 構成の変更をサブスクライブするとき、1 つまたは複数のフィルターを使用することで、アプリケーションに送信されるイベントの数を減らすことができます。 これらは、[Event Grid サブスクリプション フィルター](../event-grid/event-filtering.md)または [Service Bus サブスクリプション フィルター](../service-bus-messaging/topic-filters.md)として構成できます。 たとえばサブスクリプション フィルターを使用すると、特定の文字列で始まるキーの変更イベントだけをサブスクライブすることができます。

## <a name="register-event-handler-to-reload-data-from-app-configuration"></a>App Configuration からデータを再度読み込むためのイベント ハンドラーを登録する

*Program.cs* を開き、ファイルを次のコードで更新します。

```csharp
using Microsoft.Azure.ServiceBus;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Configuration.AzureAppConfiguration;
using System;
using System.Diagnostics;
using System.Text;
using System.Text.Json;
using System.Threading.Tasks;

namespace TestConsole
{
    class Program
    {
        private const string AppConfigurationConnectionStringEnvVarName = "AppConfigurationConnectionString"; // e.g. Endpoint=https://{store_name}.azconfig.io;Id={id};Secret={secret}
        private const string ServiceBusConnectionStringEnvVarName = "ServiceBusConnectionString"; // e.g. Endpoint=sb://{service_bus_name}.servicebus.windows.net/;SharedAccessKeyName={key_name};SharedAccessKey={key}
        private const string ServiceBusTopicEnvVarName = "ServiceBusTopic";
        private const string ServiceBusSubscriptionEnvVarName = "ServiceBusSubscription";

        private static IConfigurationRefresher _refresher = null;

        static async Task Main(string[] args)
        {
            string appConfigurationConnectionString = Environment.GetEnvironmentVariable(AppConfigurationConnectionStringEnvVarName);

            IConfiguration configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(appConfigurationConnectionString);
                    options.ConfigureRefresh(refresh =>
                        refresh
                            .Register("TestApp:Settings:Message")
                            .SetCacheExpiration(TimeSpan.FromDays(30))  // Important: Reduce poll frequency
                    );

                    _refresher = options.GetRefresher();
                }).Build();

            RegisterRefreshEventHandler();
            var message = configuration["TestApp:Settings:Message"];
            Console.WriteLine($"Initial value: {configuration["TestApp:Settings:Message"]}");

            while (true)
            {
                await _refresher.TryRefreshAsync();

                if (configuration["TestApp:Settings:Message"] != message)
                {
                    Console.WriteLine($"New value: {configuration["TestApp:Settings:Message"]}");
                    message = configuration["TestApp:Settings:Message"];
                }
                
                await Task.Delay(TimeSpan.FromSeconds(1));
            }
        }

        private static void RegisterRefreshEventHandler()
        {
            string serviceBusConnectionString = Environment.GetEnvironmentVariable(ServiceBusConnectionStringEnvVarName);
            string serviceBusTopic = Environment.GetEnvironmentVariable(ServiceBusTopicEnvVarName);
            string serviceBusSubscription = Environment.GetEnvironmentVariable(ServiceBusSubscriptionEnvVarName);
            SubscriptionClient serviceBusClient = new SubscriptionClient(serviceBusConnectionString, serviceBusTopic, serviceBusSubscription);

            serviceBusClient.RegisterMessageHandler(
                handler: (message, cancellationToken) =>
               {
                   string messageText = Encoding.UTF8.GetString(message.Body);
                   JsonElement messageData = JsonDocument.Parse(messageText).RootElement.GetProperty("data");
                   string key = messageData.GetProperty("key").GetString();
                   Console.WriteLine($"Event received for Key = {key}");

                   _refresher.SetDirty();
                   return Task.CompletedTask;
               },
                exceptionReceivedHandler: (exceptionargs) =>
                {
                    Console.WriteLine($"{exceptionargs.Exception}");
                    return Task.CompletedTask;
                });
        }
    }
}
```

更新対象に登録されたキーと値に関して、キャッシュされている値をダーティとして設定するには、[SetDirty](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.iconfigurationrefresher.setdirty) メソッドを使用します。 これにより、次回 `RefreshAsync` または `TryRefreshAsync` を呼び出したときに、キャッシュされている値が App Configuration との間で再度確認され、必要に応じて値が更新されます。

複数のインスタンスが同時に更新されるとき、スロットルが生じる可能性を小さくするために、キャッシュされている値がダーティとしてマークされるまでには、ランダムな待ち時間が追加されます。 キャッシュされている値がダーティとしてマークされるまでの最大待ち時間の既定値は 30 秒ですが、オプションの `TimeSpan` パラメーターを `SetDirty` メソッドに渡すことで、この値はオーバーライドすることができます。

> [!NOTE]
> プッシュ更新を使用して App Configuration への要求の数を減らすためには、`cacheExpiration` パラメーターに適切な値を指定して `SetCacheExpiration(TimeSpan cacheExpiration)` を呼び出すことが大切です。 これはプル更新に使用されるキャッシュの有効期限を制御する手段であり、イベント サブスクリプションまたは Service Bus サブスクリプションに関する問題が発生した場合のセーフティ ネットとして使用することができます。 推奨値は `TimeSpan.FromDays(30)` です。

## <a name="build-and-run-the-app-locally"></a>アプリをビルドしてローカルで実行する

1. **AppConfigurationConnectionString** という名前の環境変数に、App Configuration ストアへのアクセス キーを設定します。 Windows コマンド プロンプトを使用する場合は、次のコマンドを実行してコマンド プロンプトを再起動し、変更が反映されるようにします。

    ```console
     setx AppConfigurationConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell を使用する場合は、次のコマンドを実行します。

    ```powershell
     $Env:AppConfigurationConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    macOS または Linux を使用する場合は、次のコマンドを実行します。

    ```console
     export AppConfigurationConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. 次のコマンドを実行して、コンソール アプリをビルドします。

    ```console
     dotnet build
    ```

1. ビルドが正常に完了したら、次のコマンドを実行して、アプリをローカルで実行します。

    ```console
     dotnet run
    ```

    ![プッシュ更新の実行 (更新前)](./media/dotnet-core-app-pushrefresh-initial.png)

1. [Azure portal](https://portal.azure.com) にサインインします。 **[すべてのリソース]** を選択し、クイック スタートで作成した App Configuration ストア インスタンスを選択します。

1. **[Configuration Explorer]\(構成エクスプローラー)** を選択して次のキーの値を更新します。

    | Key | 値 |
    |---|---|
    | TestApp:Settings:Message | Azure App Configuration からのデータ - 更新済み |

1. 30 秒待ちます。その間に、イベントが処理されて構成が更新されます。

    ![プッシュ更新の実行 (更新後)](./media/dotnet-core-app-pushrefresh-final.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、App Configuration から動的に構成設定を更新できるように .Net Core アプリを設定しました。 App Configuration へのアクセスを効率化する Azure マネージド ID を使用する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [マネージド ID の統合](./howto-integrate-azure-managed-service-identity.md)