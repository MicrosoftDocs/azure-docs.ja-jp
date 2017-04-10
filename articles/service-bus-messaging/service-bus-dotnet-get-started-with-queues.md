---
title: "Azure Service Bus キューを使用するプログラムを作成する | Microsoft Docs"
description: "Service Bus メッセージング用の C# コンソール アプリケーションを記述する方法"
services: service-bus-messaging
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 03/23/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: f92909e0098a543f99baf3df3197a799bc9f1edc
ms.openlocfilehash: 83649bdad1d369cdfe4edf3c2bdaa67180db8668
ms.lasthandoff: 03/01/2017


---
# <a name="get-started-with-service-bus-queues"></a>Service Bus キューの使用
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

## <a name="what-will-be-accomplished"></a>作業内容
このチュートリアルでは次の作業を行います。

1. Azure ポータルを使用して Service Bus 名前空間を作成する。
2. Azure ポータルを使用して Service Bus メッセージング キューを作成する。
3. メッセージを送信するコンソール アプリケーションを記述する。
4. メッセージを受信するコンソール アプリケーションを記述する。

## <a name="prerequisites"></a>前提条件
1. [Visual Studio 2015 以降](http://www.visualstudio.com)。 このチュートリアルの例では、Visual Studio 2015 を使用します。
2. Azure サブスクリプション。

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1.Azure ポータルを使用した名前空間の作成
Service Bus 名前空間を既に作成している場合は、「[Azure Portal を使用したキューの作成](#2-create-a-queue-using-the-azure-portal)」セクションに進んでください。

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-queue-using-the-azure-portal"></a>2.Azure ポータルを使用したキューの作成
Service Bus キューを既に作成している場合は、「[キューへのメッセージの送信](#3-send-messages-to-the-queue)」セクションに進んでください。

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="3-send-messages-to-the-queue"></a>3.キューへのメッセージの送信
キューにメッセージを送信するために、Visual Studio を使用して C# コンソール アプリケーションを記述します。

### <a name="create-a-console-application"></a>コンソール アプリケーションの作成

- Visual Studio を起動し、新しいコンソール アプリケーションを作成します。

### <a name="add-the-service-bus-nuget-package"></a>Service Bus NuGet パッケージの追加
1. 新しく作成したプロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。
2. **[参照]** タブをクリックし、"Microsoft Azure Service Bus" を検索して、**[Microsoft Azure Service Bus]** の項目を選択します。 **[インストール]** をクリックし、インストールが完了したら、このダイアログ ボックスを閉じます。
   
    ![Select a NuGet package][nuget-pkg]

### <a name="write-some-code-to-send-a-message-to-the-queue"></a>キューにメッセージを送信するコードの記述
1. Program.cs ファイルの先頭に次の using ステートメントを追加します。
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
2. `Main` メソッドに次のコードを追加し、**connectionString** 変数に名前空間の作成時に取得した接続文字列を設定して、**queueName** にキューの作成時に使用したキュー名を設定します。
   
    ```csharp
    var connectionString = "<Your connection string>";
    var queueName = "<Your queue name>";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
    var message = new BrokeredMessage("This is a test message!");
    client.Send(message);
    ```
   
    Program.cs は次のようになります。
   
    ```csharp
    using System;
    using Microsoft.ServiceBus.Messaging;
   
    namespace GettingStartedWithQueues
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "<Your connection string>";
                var queueName = "<Your queue name>";
   
                var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
                var message = new BrokeredMessage("This is a test message!");
   
                client.Send(message);
            }
        }
    }
    ```
3. プログラムを実行し、Azure ポータルを確認します。 名前空間の **[概要]** ブレードでキューの名前をクリックします。 **[アクティブなメッセージ数]** の値が 1 になっていることに注目してください。
   
      ![Message count][queue-message]

## <a name="4-receive-messages-from-the-queue"></a>4.キューからメッセージを受け取る
1. 前の手順で説明した送信アプリケーションと同じように、新しいコンソール アプリケーションを作成し、Service Bus NuGet パッケージへの参照を追加します。
2. Program.cs ファイルの先頭に次の `using` ステートメントを追加します。
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
3. `Main` メソッドに次のコードを追加し、**connectionString** 変数に名前空間の作成時に取得した接続文字列を設定して、**queueName** にキューの作成時に使用したキュー名を設定します。
   
    ```csharp
    var connectionString = "";
    var queueName = "samplequeue";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
    client.OnMessage(message =>
    {
      Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
      Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
    });
   
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
          var connectionString = "";
          var queueName = "samplequeue";
   
          var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
          client.OnMessage(message =>
          {
            Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
            Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
          });
   
          Console.ReadLine();
        }
      }
    }
    ```
4. プログラムを実行し、ポータルを確認します。 **[キューの長さ]** の値が 0 になっていることに注目してください。
   
    ![Queue length][queue-message-receive]

お疲れさまでした。 これで、キューを作成し、メッセージを送信して、メッセージを受信しました。

## <a name="next-steps"></a>次のステップ
Azure Service Bus メッセージングのさらに高度な機能を紹介する[サンプルが含まれる GitHub リポジトリ](https://github.com/Azure-Samples/azure-servicebus-messaging-samples)を参照してください。

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

