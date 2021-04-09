---
title: Azure Portal とトピック/サブスクリプションを使用して在庫を更新する
description: このチュートリアルでは、トピックとサブスクリプションからメッセージを送受信する方法と、.NET を使用してフィルター ルールを追加および使用する方法について説明します。
author: spelluru
ms.author: spelluru
ms.date: 10/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: d0a94f346f9d3cf7a05a1ca6e1b37d4d008f3e75
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179665"
---
# <a name="tutorial-update-inventory-using-azure-portal-and-topicssubscriptions"></a>チュートリアル: Azure Portal とトピック/サブスクリプションを使用して在庫を更新する

Microsoft Azure Service Bus は、アプリケーションとサービスの間で情報を送信するマルチテナント クラウド メッセージング サービスです。 非同期操作により、柔軟なブローカー メッセージング、構造化された先入れ先出し型 (FIFO) のメッセージング、および発行/購読機能が可能になります。 このチュートリアルでは、公開/サブスクライブ チャネルで Azure Portal と .NET を使用して、小売在庫シナリオで Service Bus トピックとサブスクリプションを使用する方法について説明します。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * Azure Portal を使用して、Service Bus トピックとそのトピックへの 1 つ以上のサブスクリプションを作成する
> * .NET コードを使用してトピック フィルターを追加する
> * 異なる内容の 2 つのメッセージを作成する
> * メッセージを送信し、所定のサブスクリプションに到着したことを確認する
> * サブスクリプションからメッセージを受信する

このシナリオの例は、複数の小売店の在庫品の更新です。 このシナリオでは、各店舗または一連の店舗が、在庫品を更新するためのメッセージを受け取ります。 このチュートリアルでは、サブスクリプションとフィルターを使用してこのシナリオを実装する方法を示します。 まず、3 つのサブスクリプションを持つトピックを作成し、いくつかのルールとフィルターを追加してから、トピックとサブスクリプションからメッセージを送受信します。

![topic](./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png)

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント][]を作成できます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下がインストールされていることを確認してください。

- [Visual Studio 2017 Update 3 (バージョン 15.3, 26730.01)](https://www.visualstudio.com/vs) 以降。
- [NET Core SDK](https://www.microsoft.com/net/download/windows) バージョン 2.0 以降。

## <a name="service-bus-topics-and-subscriptions"></a>Service Bus トピックとサブスクリプション

[トピックへの各サブスクリプション](service-bus-messaging-overview.md#topics)は、各メッセージのコピーを受信できます。 トピックは完全にプロトコルであり、意味的には Service Bus キューと互換性があります。 Service Bus のトピックは、フィルターの条件を持つさまざまな選択ルールをサポートしています。メッセージのプロパティを設定または変更するオプションのアクションもあります。 ルールが一致するたびに、メッセージが生成されます。 ルール、フィルター、およびアクションの詳細については、こちらの[リンク](topic-filters.md)を参照してください。

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]



## <a name="create-filter-rules-on-subscriptions"></a>サブスクリプションに対してフィルター ルールを作成する

名前空間とトピック/サブスクリプションがプロビジョニングされ、必要な資格情報を持っている場合、サブスクリプションに対してフィルター ルールを作成し、メッセージを送受信できる状態です。 [こちらの GitHub サンプル フォルダー](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters)でコードを調べることができます。

## <a name="send-and-receive-messages"></a>メッセージを送受信する

コードを実行するには、次の手順を実行します。

1. コマンド プロンプトまたは PowerShell プロンプトで、次のコマンドを発行して [Service Bus GitHub リポジトリ](https://github.com/Azure/azure-service-bus/)を複製します。

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. サンプル フォルダー `azure-service-bus\samples\DotNet\Azure.Messaging.ServiceBus\BasicSendReceiveTutorialwithFilters` に移動します。

3. このチュートリアルの「管理資格情報の取得」セクションで、メモ帳にコピーした接続文字列を取得します。 前のセクションで作成したトピックの名前も必要です。

4. コマンド プロンプトで、次のコマンドを入力します。

   ```shell
   dotnet build
   ```

5. `BasicSendReceiveTutorialwithFilters\bin\Debug\netcoreapp3.1` フォルダーに移動します。

6. 次のコマンドを入力してプログラムを実行します。 `myConnectionString` を以前に取得した値に、`myTopicName` を作成したトピックの名前に置き換えます。

   ```shell
   dotnet BasicSendReceiveTutorialwithFilters.dll -ConnectionString "myConnectionString" -TopicName "myTopicName"
   ``` 
7. コンソールの指示に従って、まずフィルター作成を選択します。 フィルターの作成処理には、既定のフィルターの削除が含まれています。 PowerShell または CLI を使用する場合は、既定のフィルターを削除する必要はありませんが、コードで削除する場合は、既定のフィルターを削除する必要があります。 コンソール コマンド 1 と 3 は、以前に作成したサブスクリプションに対するフィルターを管理するために役立ちます。

   - 1 の実行: 既定のフィルターを削除します。
   - 2 の実行: 独自のフィルターを追加します。
   - 3 の実行: 必要に応じて独自のフィルターを削除します。 この操作では、既定のフィルターは再作成されません。

     ![2 の出力例](./media/service-bus-tutorial-topics-subscriptions-portal/create-rules.png)

8. フィルターの作成後は、メッセージを送信できます。 4 キーを押して、トピックに送信されている 10 個のメッセージを観察します。

    ![出力の送信](./media/service-bus-tutorial-topics-subscriptions-portal/send-output.png)

9. 5 キーを押して、受信中のメッセージを観察します。 10 個のメッセージが表示されない場合は、m キーを押してメニューを表示し、もう一度 5 キーを押します。

    ![出力を受信する](./media/service-bus-tutorial-topics-subscriptions-portal/receive-output.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、名前空間とトピックを削除します。 これを行うには、ポータルでこれらのリソースを選択し、 **[削除]** をクリックします。

## <a name="understand-the-sample-code"></a>サンプル コードを理解する

このセクションでは、サンプル コードの処理内容の詳細について説明します。

### <a name="get-connection-string-and-topic"></a>接続文字列とトピックを取得する

コードはまず一連の変数を宣言し、プログラムの残りの処理を実行します。

```csharp
string ServiceBusConnectionString;
string TopicName;

static string[] Subscriptions = { "S1", "S2", "S3" };
static IDictionary<string, string[]> SubscriptionFilters = new Dictionary<string, string[]> {
    { "S1", new[] { "StoreId IN('Store1', 'Store2', 'Store3')", "StoreId = 'Store4'"} },
    { "S2", new[] { "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'" } },
    { "S3", new[] { "sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')" } }
};
// You can have only have one action per rule and this sample code supports only one action for the first filter, which is used to create the first rule. 
static IDictionary<string, string> SubscriptionAction = new Dictionary<string, string> {
    { "S1", "" },
    { "S2", "" },
    { "S3", "SET sys.Label = 'SalesEvent'"  }
};
static string[] Store = { "Store1", "Store2", "Store3", "Store4", "Store5", "Store6", "Store7", "Store8", "Store9", "Store10" };
static string SysField = "sys.To";
static string CustomField = "StoreId";
static int NrOfMessagesPerStore = 1; // Send at least 1.
```

接続文字列とトピック名は、次のようにコマンド ライン パラメーターを介して渡され、次に `Main()` メソッドで読み取られます。

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string TopicName = "";

    for (int i = 0; i < args.Length; i++)
    {
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i + 1]}");
            ServiceBusConnectionString = args[i + 1]; // Alternatively enter your connection string here.
        }
        else if (args[i] == "-TopicName")
        {
            Console.WriteLine($"TopicName: {args[i + 1]}");
            TopicName = args[i + 1]; // Alternatively enter your queue name here.
        }
    }

    if (ServiceBusConnectionString != "" && TopicName != "")
    {
        Program P = StartProgram(ServiceBusConnectionString, TopicName);
        P.PresentMenu().GetAwaiter().GetResult();
    }
    else
    {
        Console.WriteLine("Specify -Connectionstring and -TopicName to execute the example.");
        Console.ReadKey();
    }
}
```

### <a name="remove-default-filters"></a>既定のフィルターを削除する

サブスクリプションを作成すると、Service Bus でサブスクリプションごとに既定のフィルターが作成されます。 このフィルターを使用すると、トピックに送信されたすべてのメッセージを受信できます。 カスタム フィルターを使用する場合は、次のコードのように既定のフィルターを削除できます。

```csharp
private async Task RemoveDefaultFilters()
{
    Console.WriteLine($"Starting to remove default filters.");

    try
    {
        var client = new ServiceBusAdministrationClient(ServiceBusConnectionString);
        foreach (var subscription in Subscriptions)
        {
            await client.DeleteRuleAsync(TopicName, subscription, CreateRuleOptions.DefaultRuleName);
            Console.WriteLine($"Default filter for {subscription} has been removed.");
        }

        Console.WriteLine("All default Rules have been removed.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="create-filters"></a>フィルターの作成

次のコードは、このチュートリアルで定義したカスタム フィルターを追加します。

```csharp
private async Task CreateCustomFilters()
{
    try
    {
        for (int i = 0; i < Subscriptions.Length; i++)
        {
            var client = new ServiceBusAdministrationClient(ServiceBusConnectionString);
            string[] filters = SubscriptionFilters[Subscriptions[i]];
            if (filters[0] != "")
            {
                int count = 0;
                foreach (var myFilter in filters)
                {
                    count++;

                    string action = SubscriptionAction[Subscriptions[i]];
                    if (action != "")
                    {
                        await client.CreateRuleAsync(TopicName, Subscriptions[i], new CreateRuleOptions
                        {
                            Filter = new SqlRuleFilter(myFilter),
                            Action = new SqlRuleAction(action),
                            Name = $"MyRule{count}"
                        });
                    }
                    else
                    {
                        await client.CreateRuleAsync(TopicName, Subscriptions[i], new CreateRuleOptions
                        {
                            Filter = new SqlRuleFilter(myFilter),
                            Name = $"MyRule{count}"
                        });
                    }
                }
            }

            Console.WriteLine($"Filters and actions for {Subscriptions[i]} have been created.");
        }

        Console.WriteLine("All filters and actions have been created.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="remove-your-custom-created-filters"></a>作成したカスタム フィルターを削除する

サブスクリプションに対するすべてのフィルターを削除するには、次のコードでその方法を確認してください。

```csharp
private async Task CleanUpCustomFilters()
{
    foreach (var subscription in Subscriptions)
    {
        try
        {
            var client = new ServiceBusAdministrationClient(ServiceBusConnectionString);
            IAsyncEnumerator<RuleProperties> rules = client.GetRulesAsync(TopicName, subscription).GetAsyncEnumerator();
            while (await rules.MoveNextAsync())
            {
                await client.DeleteRuleAsync(TopicName, subscription, rules.Current.Name);
                Console.WriteLine($"Rule {rules.Current.Name} has been removed.");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }
    Console.WriteLine("All default filters have been removed.\n");

    await PresentMenu();
}
```

### <a name="send-messages"></a>メッセージを送信する

トピックにメッセージを送信する処理は、メッセージをキューに送信する処理に似ています。 この例は、タスク一覧と非同期処理を使用してメッセージを送信する方法を示しています。

```csharp
public async Task SendMessages()
{
    try
    {
        await using var client = new ServiceBusClient(ServiceBusConnectionString);
        var taskList = new List<Task>();
        for (int i = 0; i < Store.Length; i++)
        {
            taskList.Add(SendItems(client, Store[i]));
        }

        await Task.WhenAll(taskList);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }
    Console.WriteLine("\nAll messages sent.\n");
}

private async Task SendItems(ServiceBusClient client, string store)
{
    // create the sender
    ServiceBusSender tc = client.CreateSender(TopicName);

    for (int i = 0; i < NrOfMessagesPerStore; i++)
    {
        Random r = new Random();
        Item item = new Item(r.Next(5), r.Next(5), r.Next(5));

        // Note the extension class which is serializing an deserializing messages
        ServiceBusMessage message = item.AsMessage();
        message.To = store;
        message.ApplicationProperties.Add("StoreId", store);
        message.ApplicationProperties.Add("Price", item.GetPrice().ToString());
        message.ApplicationProperties.Add("Color", item.GetColor());
        message.ApplicationProperties.Add("Category", item.GetItemCategory());

        await tc.SendMessageAsync(message);
        Console.WriteLine($"Sent item to Store {store}. Price={item.GetPrice()}, Color={item.GetColor()}, Category={item.GetItemCategory()}"); ;
    }
}
```

### <a name="receive-messages"></a>メッセージを受信する

メッセージはタスク一覧を介して再び受信され、コードはバッチ処理を使用します。 バッチ処理を使用して送受信することはできますが、この例はバッチ受信方法のみを示しています。 実際には、ループから抜けることはありませんが、ループを維持し、1 分などのより長い時間間隔を設定します。 この期間はブローカーへの受信呼び出しが開かれたままになり、メッセージが到達した場合は即座に返され、新しい受信呼び出しが発行されます。 この概念は、*長いポーリング* と呼ばれます。 より一般的なオプションは、この[クイック スタート](service-bus-quickstart-portal.md)やリポジトリの他のいくつかのサンプルで見られる受信ポンプです。

```csharp
public async Task Receive()
{
    var taskList = new List<Task>();
    for (var i = 0; i < Subscriptions.Length; i++)
    {
        taskList.Add(this.ReceiveMessages(Subscriptions[i]));
    }

    await Task.WhenAll(taskList);
}

private async Task ReceiveMessages(string subscription)
{
    await using var client = new ServiceBusClient(ServiceBusConnectionString);
    ServiceBusReceiver receiver = client.CreateReceiver(TopicName, subscription);

    // In reality you would not break out of the loop like in this example but would keep looping. The receiver keeps the connection open
    // to the broker for the specified amount of seconds and the broker returns messages as soon as they arrive. The client then initiates
    // a new connection. So in reality you would not want to break out of the loop. 
    // Also note that the code shows how to batch receive, which you would do for performance reasons. For convenience you can also always
    // use the regular receive pump which we show in our Quick Start and in other github samples.
    while (true)
    {
        try
        {
            //IList<Message> messages = await receiver.ReceiveAsync(10, TimeSpan.FromSeconds(2));
            // Note the extension class which is serializing an deserializing messages and testing messages is null or 0.
            // If you think you did not receive all messages, just press M and receive again via the menu.
            IReadOnlyList<ServiceBusReceivedMessage> messages = await receiver.ReceiveMessagesAsync(maxMessages: 100);

            if (messages.Any())
            {
                foreach (ServiceBusReceivedMessage message in messages)
                {
                    lock (Console.Out)
                    {
                        Item item = message.As<Item>();
                        IReadOnlyDictionary<string, object> myApplicationProperties = message.ApplicationProperties;
                        Console.WriteLine($"StoreId={myApplicationProperties["StoreId"]}");
                        if (message.Subject != null)
                        {
                            Console.WriteLine($"Subject={message.Subject}");
                        }
                        Console.WriteLine(
                            $"Item data: Price={item.GetPrice()}, Color={item.GetColor()}, Category={item.GetItemCategory()}");
                    }

                    await receiver.CompleteMessageAsync(message);
                }
            }
            else
            {
                break;
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }
}
```

> [!NOTE]
> Service Bus リソースは、[Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/) で管理できます。 Service Bus Explorer を使用すると、ユーザーは Service Bus 名前空間に接続し、簡単な方法でメッセージング エンティティを管理できます。 このツールには、インポート/エクスポート機能や、トピック、キュー、サブスクリプション、リレー サービス、通知ハブ、イベント ハブをテストする機能などの高度な機能が用意されています。 

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Portal を使用してリソースをプロビジョニングした後、Service Bus のトピックとそのサブスクリプションからメッセージを送受信しました。 以下の方法を学習しました。

> [!div class="checklist"]
> * Azure Portal を使用して、Service Bus トピックとそのトピックへの 1 つ以上のサブスクリプションを作成する
> * .NET コードを使用してトピック フィルターを追加する
> * 異なる内容の 2 つのメッセージを作成する
> * メッセージを送信し、所定のサブスクリプションに到着したことを確認する
> * サブスクリプションからメッセージを受信する

メッセージの送受信の他の例については、[GitHub の Service Bus サンプル](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted)から始めてください。

Service Bus の公開/サブスクライブ機能の使用方法の詳細については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Event Grid を使用したイベントへの応答](service-bus-to-event-grid-integration-example.md)

[無料アカウント]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Azure portal]: https://portal.azure.com/

[connection-string]: ./media/service-bus-tutorial-topics-subscriptions-portal/connection-string.png
[service-bus-flow]: ./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png
