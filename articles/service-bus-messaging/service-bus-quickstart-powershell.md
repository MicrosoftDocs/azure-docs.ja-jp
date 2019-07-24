---
title: クイック スタート - Azure PowerShell を使用して Service Bus キューを作成する | Microsoft Docs
description: このクイック スタートでは、Azure PowerShell を使用して Service Bus キューを作成する方法について説明します。 その後、サンプル アプリケーションを使用してキューとの間でメッセージを送受信します。
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/10/2019
ms.author: spelluru
ms.openlocfilehash: 31b1b852c92ad671564fd54520af3f3a23b3e3c2
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/11/2019
ms.locfileid: "59499803"
---
# <a name="quickstart-use-azure-powershell-to-create-a-service-bus-queue"></a>クイック スタート:Azure PowerShell を使用して Service Bus キューを作成する
このクイック スタートでは、Service Bus キューとの間でメッセージを送受信し、PowerShell を使用してその名前空間内にメッセージング名前空間とキューを作成し、その名前空間に対する承認資格情報を取得する方法について説明します。 次に、[.NET Standard ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)を使用して、このキューからメッセージを送受信する方法について説明します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]


## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下がインストールされていることを確認してください。

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント][] を作成してください。 
- [Visual Studio 2017 Update 3 (バージョン 15.3, 26730.01)](https://www.visualstudio.com/vs) 以降。 キューとの間でメッセージを送受信するサンプルを Visual Studio を使用して作成します。 このサンプルの目的は、ポータルで作成したキューをテストすることです。 
- [NET Core SDK](https://www.microsoft.com/net/download/windows) バージョン 2.0 以降。

このクイック スタートでは、Azure PowerShell の最新バージョンを実行していることを前提にしています。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストールと構成][]に関するページを参照してください。 Azure Cloud Shell を使い慣れている方は、そちらをご使用ください。お使いのマシンに Azure PowerShell をインストールする必要はありません。 Azure Cloud Shell の詳細については、「[Azure Cloud Shell の概要](../cloud-shell/overview.md)」を参照してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

1. Service Bus PowerShell モジュールをまだインストールしていない場合は、まずインストールします。

   ```azurepowershell-interactive
   Install-Module Az.ServiceBus
   ```

2. 次のコマンドを実行して、Azure にサインインします。

   ```azurepowershell-interactive
   Login-AzAccount
   ```

3. 次のコマンドを発行して、現在のサブスクリプション コンテキストを設定するか、現在アクティブなサブスクリプションを表示します。

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "MyAzureSubName" 
   Get-AzContext
   ```

## <a name="provision-resources"></a>リソースをプロビジョニングする

PowerShell プロンプトから次のコマンドを発行して、Service Bus リソースをプロビジョニングします。 すべてのプレースホルダーを適切な値に置き換えてください。

```azurepowershell-interactive
# Create a resource group 
New-AzResourceGroup –Name my-resourcegroup –Location eastus

# Create a Messaging namespace
New-AzServiceBusNamespace -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Location eastus

# Create a queue 
New-AzServiceBusQueue -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Name queue-name -EnablePartitioning $False

# Get primary connection string (required in next step)
Get-AzServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
```

`Get-AzServiceBusKey` コマンドレットを実行した後、接続文字列および選択したキュー名をコピーし、メモ帳などの一時的な場所に貼り付けます。 これは、次のステップで必要になります。

## <a name="send-and-receive-messages"></a>メッセージを送受信する

名前空間とキューを作成し、必要な資格情報を用意すれば、メッセージを送信および受信する準備が整いました。 [こちらの GitHub サンプル フォルダー](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveQuickStart)でコードを調べることができます。

コードを実行するには、次の手順を実行します。

1. 次のコマンドを発行して、[Service Bus の GitHub リポジトリ](https://github.com/Azure/azure-service-bus/)を複製します。

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

3. サンプル フォルダー `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveQuickStart\BasicSendReceiveQuickStart` に移動します。

4. まだ接続文字列を取得していない場合は、次の PowerShell コマンドレットを使用して取得します。 `my-resourcegroup`  と  `namespace-name` を実際の値に置き換えます。 

   ```azurepowershell-interactive
   Get-AzServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
   ```

5. PowerShell プロンプトで、次のコマンドを入力します。

   ```shell
   dotnet build
   ```

6. `bin\Debug\netcoreapp2.0` フォルダーに移動します。

7. 次のコマンドを入力してプログラムを実行します。 `myConnectionString` を以前に取得した値に、`myQueueName` を作成したキューの名前に置き換えます。

   ```shell
   dotnet BasicSendReceiveQuickStart.dll -ConnectionString "myConnectionString" -QueueName "myQueueName"
   ``` 

8. キューに送信され、次にキューから受信される 10 個のメッセージを観察します。

   ![プログラムの出力](./media/service-bus-quickstart-powershell/dotnet.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

次のコマンドを実行して、リソース グループ、名前空間、およびすべての関連リソースを削除します。

```powershell-interactive
Remove-AzResourceGroup -Name my-resourcegroup
```

## <a name="understand-the-sample-code"></a>サンプル コードを理解する

このセクションでは、サンプル コードの処理内容の詳細について説明します。 

### <a name="get-connection-string-and-queue"></a>接続文字列とキューを取得する

接続文字列とキュー名は、コマンド ライン引数として `Main()` メソッドに渡されます。 `Main()` は、これらの値を保持する 2 つの文字列変数を宣言します。

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string QueueName = "";

    for (int i = 0; i < args.Length; i++)
    {
        var p = new Program();
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i+1]}");
            ServiceBusConnectionString = args[i + 1]; 
        }
        else if(args[i] == "-QueueName")
        {
            Console.WriteLine($"QueueName: {args[i+1]}");
            QueueName = args[i + 1];
        }                
    }

    if (ServiceBusConnectionString != "" && QueueName != "")
        MainAsync(ServiceBusConnectionString, QueueName).GetAwaiter().GetResult();
    else
    {
        Console.WriteLine("Specify -Connectionstring and -QueueName to execute the example.");
        Console.ReadKey();
    }                            
}
```
 
`Main()` メソッドは、非同期メッセージ ループ `MainAsync()` を開始します。

### <a name="message-loop"></a>メッセージ ループ

MainAsync() メソッドで、コマンド ライン引数を使用してキュー クライアントを作成し、`RegisterOnMessageHandlerAndReceiveMessages()` という受信メッセージ ハンドラーを呼び出し、一連のメッセージを送信します。

```csharp
static async Task MainAsync(string ServiceBusConnectionString, string QueueName)
{
    const int numberOfMessages = 10;
    queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

    Console.WriteLine("======================================================");
    Console.WriteLine("Press any key to exit after receiving all the messages.");
    Console.WriteLine("======================================================");

    // Register QueueClient's MessageHandler and receive messages in a loop
    RegisterOnMessageHandlerAndReceiveMessages();

    // Send Messages
    await SendMessagesAsync(numberOfMessages);

    Console.ReadKey();

    await queueClient.CloseAsync();
}
```

`RegisterOnMessageHandlerAndReceiveMessages()` メソッドで、いくつかのメッセージ ハンドラー オプションを設定し、次にキュー クライアントの `RegisterMessageHandler()` メソッドを呼び出し、受信を開始します。

```csharp
static void RegisterOnMessageHandlerAndReceiveMessages()
{
    // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
    var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
    {
        // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
        // Set it according to how many messages the application wants to process in parallel.
        MaxConcurrentCalls = 1,

        // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
        // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
        AutoComplete = false
    };

    // Register the function that will process messages
    queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
} 
```

### <a name="send-messages"></a>メッセージを送信する

メッセージの作成と送信操作は、`SendMessagesAsync()` メソッドで行われます。

```csharp
static async Task SendMessagesAsync(int numberOfMessagesToSend)
{
    try
    {
        for (var i = 0; i < numberOfMessagesToSend; i++)
        {
            // Create a new message to send to the queue
            string messageBody = $"Message {i}";
            var message = new Message(Encoding.UTF8.GetBytes(messageBody));

            // Write the body of the message to the console
            Console.WriteLine($"Sending message: {messageBody}");

            // Send the message to the queue
            await queueClient.SendAsync(message);
        }
    }
    catch (Exception exception)
    {
        Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
    }
}
```

### <a name="process-messages"></a>メッセージを処理する

`ProcessMessagesAsync()` メソッドで、メッセージの受信の確認、処理、および完了を行います。

```csharp
static async Task ProcessMessagesAsync(Message message, CancellationToken token)
{
    // Process the message
    Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

    // Complete the message so that it is not received again.
    await queueClient.CompleteAsync(message.SystemProperties.LockToken);
}
```

## <a name="next-steps"></a>次の手順

この記事では、キューでメッセージを送受信するために必要な Service Bus 名前空間と他のリソースを作成しました。 メッセージを送受信するコードの作成についてさらに詳しく学習するには、「**メッセージを送受信する**」セクションのチュートリアルに進んでください。 

> [!div class="nextstepaction"]
> [メッセージを送受信する](service-bus-dotnet-get-started-with-queues.md)

[無料アカウント]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Azure PowerShell のインストールと構成]: /powershell/azure/install-Az-ps
