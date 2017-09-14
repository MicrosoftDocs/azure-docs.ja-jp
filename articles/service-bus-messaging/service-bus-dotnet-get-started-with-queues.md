---
title: "Azure Service Bus キューの使用 | Microsoft Docs"
description: "Service Bus メッセージング キューを使った C# コンソール アプリケーションを作成します。"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/26/2017
ms.author: sethm
ms.translationtype: HT
ms.sourcegitcommit: b309108b4edaf5d1b198393aa44f55fc6aca231e
ms.openlocfilehash: 99a377db6341d90d263b98e14227db61dd9beabd
ms.contentlocale: ja-jp
ms.lasthandoff: 08/15/2017

---
# <a name="get-started-with-service-bus-queues"></a>Service Bus キューの使用
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

## <a name="what-will-be-accomplished"></a>作業内容
このチュートリアルに含まれる手順は次のとおりです。

1. Azure Portal を使用して Service Bus 名前空間を作成する。
2. Azure Portal を使用して Service Bus キューを作成する。
3. メッセージを送信するコンソール アプリケーションを記述する。
4. 直前の手順で送信されたメッセージを受信するためのコンソール アプリケーションを作成する。

## <a name="prerequisites"></a>前提条件
1. [Visual Studio 2015 以降](http://www.visualstudio.com)。 このチュートリアルの例では、Visual Studio 2017 を使用します。
2. Azure サブスクリプション。

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1.Azure Portal を使用した名前空間の作成
Service Bus メッセージング名前空間を既に作成している場合は、「[Azure Portal を使用したキューの作成](#2-create-a-queue-using-the-azure-portal)」セクションに進んでください。

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-queue-using-the-azure-portal"></a>2.Azure Portal を使用したキューの作成
Service Bus キューを既に作成している場合は、「[キューへのメッセージの送信](#3-send-messages-to-the-queue)」セクションに進んでください。

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="3-send-messages-to-the-queue"></a>3.キューへのメッセージの送信
キューにメッセージを送信するために、Visual Studio を使用して C# コンソール アプリケーションを記述します。

### <a name="create-a-console-application"></a>コンソール アプリケーションの作成

Visual Studio を起動し、新しい**コンソール アプリ (.NET Framework)** プロジェクトを作成します。

### <a name="add-the-service-bus-nuget-package"></a>Service Bus NuGet パッケージの追加
1. 新しく作成したプロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。
2. **[参照]** タブをクリックし、**Microsoft Azure Service Bus** を検索して、**[WindowsAzure.ServiceBus]** の項目を選択します。 **[インストール]** をクリックし、インストールが完了したら、このダイアログ ボックスを閉じます。
   
    ![Select a NuGet package][nuget-pkg]

### <a name="write-some-code-to-send-a-message-to-the-queue"></a>キューにメッセージを送信するコードの記述
1. Program.cs ファイルの先頭に次の `using` ステートメントを追加します。
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
2. `Main` メソッドに次のコードを追加します。 名前空間の作成時に取得した接続文字列を `connectionString` 変数に設定し、キューの作成時に使用したキュー名を `queueName` に設定します。
   
    ```csharp
    var connectionString = "<your connection string>";
    var queueName = "<your queue name>";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
    var message = new BrokeredMessage("This is a test message!");

    Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

    client.Send(message);

    Console.WriteLine("Message successfully sent! Press ENTER to exit program");
    Console.ReadLine();
    ```
   
    Program.cs ファイルは次のようになります。
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.ServiceBus.Messaging;

    namespace qsend
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";
                var queueName = "<your queue name>";

                var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
                var message = new BrokeredMessage("This is a test message!");

                Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

                client.Send(message);

                Console.WriteLine("Message successfully sent! Press ENTER to exit program");
                Console.ReadLine();
            }
        }
    }
    ```
3. プログラムを実行し、Azure Portal を確認します。名前空間の **[概要]** ブレードでキューの名前をクリックしてください。 **[要点]** ブレードが表示されます。 **[アクティブなメッセージ数]** の値が 1 になっていることに注目してください。 この値は、メッセージを取得しないまま送信側アプリケーションを実行するたびに 1 ずつ増えます。 アプリからキューにメッセージを追加するたびにキューの最新のサイズも増えている点に注目してください。
   
      ![メッセージ サイズ][queue-message]

## <a name="4-receive-messages-from-the-queue"></a>4.キューからメッセージを受け取る

1. 送信したメッセージを受信するには、前の手順で説明した送信側アプリケーションと同じように、新しいコンソール アプリケーションを作成し、Service Bus NuGet パッケージへの参照を追加します。
2. Program.cs ファイルの先頭に次の `using` ステートメントを追加します。
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
3. `Main` メソッドに次のコードを追加します。 名前空間の作成時に取得した接続文字列を `connectionString` 変数に設定し、キューの作成時に使用したキュー名を `queueName` に設定します。
   
    ```csharp
    var connectionString = "<your connection string>";
    var queueName = "<your queue name>";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
    client.OnMessage(message =>
    {
      Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
      Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
    });
   
    Console.WriteLine("Press ENTER to exit program");
    Console.ReadLine();
    ```
   
    Program.cs ファイルは次のようになります。
   
    ```csharp
    using System;
    using Microsoft.ServiceBus.Messaging;
   
    namespace GettingStartedWithQueues
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";;
          var queueName = "<your queue name>";
   
          var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
          client.OnMessage(message =>
          {
            Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
            Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
          });

          Console.WriteLine("Press ENTER to exit program");   
          Console.ReadLine();
        }
      }
    }
    ```
4. プログラムを実行し、もう一度ポータルを確認します。 **[アクティブなメッセージ数]** と **[現在]** の値が 0 になっていることがわかります。
   
    ![Queue length][queue-message-receive]

お疲れさまでした。 これで、キューを作成し、メッセージを送信して、メッセージを受信しました。

## <a name="next-steps"></a>次のステップ

Service Bus メッセージングのさらに高度な機能を紹介する[サンプルが含まれる GitHub リポジトリ](https://github.com/Azure/azure-service-bus/tree/master/samples)を参照してください。

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

