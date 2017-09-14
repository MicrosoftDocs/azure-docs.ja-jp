---
title: "Azure Service Bus のトピックとサブスクリプションの概要 | Microsoft Docs"
description: "Service Bus メッセージングのトピックとサブスクリプションを使った C# コンソール アプリケーションを作成します。"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/30/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 9401ada519f600b0d2817f06a396e16607a24129
ms.contentlocale: ja-jp
ms.lasthandoff: 07/01/2017


---
<a id="get-started-with-service-bus-topics" class="xliff"></a>

# Service Bus トピックの概要

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

<a id="what-will-be-accomplished" class="xliff"></a>

## 作業内容

このチュートリアルに含まれる手順は次のとおりです。

1. Azure ポータルを使用して Service Bus 名前空間を作成する。
2. Azure Portal を使用して Service Bus トピックを作成する。
3. そのトピックに対する Service Bus サブスクリプションを Azure Portal で作成する。
4. トピックにメッセージを送信するためのコンソール アプリケーションを作成する。
5. サブスクリプションからメッセージを受信するためのコンソール アプリケーションを作成する。

<a id="prerequisites" class="xliff"></a>

## 前提条件

1. [Visual Studio 2015 以降](http://www.visualstudio.com)。 このチュートリアルの例では、Visual Studio 2017 を使用します。
2. Azure サブスクリプション。

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

<a id="1-create-a-namespace-using-the-azure-portal" class="xliff"></a>

## 1.Azure ポータルを使用した名前空間の作成

Service Bus メッセージング名前空間を既に作成している場合は、「[Azure Portal を使用したトピックの作成](#2-create-a-topic-using-the-azure-portal)」セクションに進んでください。

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

<a id="2-create-a-topic-using-the-azure-portal" class="xliff"></a>

## 2.Azure Portal を使用したトピックの作成

1. [Azure Portal][azure-portal] にログオンします。
2. ポータルの左側のナビゲーション ウィンドウで、**[Service Bus]** をクリックします (**[Service Bus]** が表示されていない場合は **[More Services (その他のサービス)]** をクリックします)。
3. トピックを作成する名前空間をクリックします。 名前空間の [概要] ブレードが表示されます。
   
    ![トピックを作成する][createtopic1]
4. **[Service Bus 名前空間]** ブレードで **[トピック]** をクリックし、**[トピックの追加]** をクリックします。
   
    ![トピックの選択][createtopic2]
5. トピックの名前を入力し、**[パーティション分割を有効にする]** オプションをオフにします。 他のオプションは既定値のままにしてください。
   
    ![Select New][createtopic3]
6. ブレードの下部にある **[作成]**をクリックします。

<a id="3-create-a-subscription-to-the-topic" class="xliff"></a>

## 3.トピックに対するサブスクリプションの作成

1. 手順 1. で作成した名前空間をポータル リソース ウィンドウでクリックし、手順 2. で作成したトピックの名前をクリックします。
2. 概要ウィンドウの上部で、**[サブスクリプション]** の横にあるプラス記号をクリックし、このトピックにサブスクリプションを追加します。

    ![サブスクリプションの作成][createtopic4]

3. サブスクリプションの名前を入力します。 他のオプションは既定値のままにしてください。

<a id="4-send-messages-to-the-topic" class="xliff"></a>

## 4.トピックにメッセージを送信する

トピックにメッセージを送信するために、Visual Studio を使用して C# コンソール アプリケーションを記述します。

<a id="create-a-console-application" class="xliff"></a>

### コンソール アプリケーションの作成

Visual Studio を起動し、新しい**コンソール アプリ (.NET Framework)** プロジェクトを作成します。

<a id="add-the-service-bus-nuget-package" class="xliff"></a>

### Service Bus NuGet パッケージの追加

1. 新しく作成したプロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。
2. **[参照]** タブをクリックし、**Microsoft Azure Service Bus** を検索して、**[WindowsAzure.ServiceBus]** の項目を選択します。 **[インストール]** をクリックし、インストールが完了したら、このダイアログ ボックスを閉じます。
   
    ![Select a NuGet package][nuget-pkg]

<a id="write-some-code-to-send-a-message-to-the-topic" class="xliff"></a>

### トピックにメッセージを送信するコードの記述

1. Program.cs ファイルの先頭に次の `using` ステートメントを追加します。
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
2. `Main` メソッドに次のコードを追加します。 名前空間の作成時に取得した接続文字列を `connectionString` 変数に設定し、トピックの作成時に使用した名前を `topicName` に設定します。
   
    ```csharp
    var connectionString = "<your connection string>";
    var topicName = "<your topic name>";
   
    var client = TopicClient.CreateFromConnectionString(connectionString, topicName);
    var message = new BrokeredMessage("This is a test message!");

    Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
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

    namespace tsend
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";
                var topicName = "<your topic name>";

                var client = TopicClient.CreateFromConnectionString(connectionString, topicName);
                var message = new BrokeredMessage("This is a test message!");

                Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
                Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

                client.Send(message);

                Console.WriteLine("Message successfully sent! Press ENTER to exit program");
                Console.ReadLine();
            }
        }
    }
    ```
3. プログラムを実行し、Azure Portal を確認します。名前空間の **[概要]** ブレードでトピックの名前をクリックしてください。 トピックの **[要点]** ブレードが表示されます。 ブレードの下部付近に表示されているサブスクリプション一覧で、各サブスクリプションの **[メッセージ数]** の値が 1 になっていることがわかります。 この値は、メッセージを取得しないまま送信側アプリケーションを実行するたびに 1 ずつ増えます (メッセージの取得については次のセクションで説明します)。 アプリからトピック/サブスクリプションにメッセージを追加するたびに、トピックの最新のサイズによって、**[要点]** ブレードの **[現在]** の値も増えている点に注目してください。
   
      ![メッセージ サイズ][topic-message]

<a id="5-receive-messages-from-the-subscription" class="xliff"></a>

## 5.サブスクリプションからメッセージを受信する

1. 送信したメッセージを受信するには、前の手順で説明した送信側アプリケーションと同じように、新しいコンソール アプリケーションを作成し、Service Bus NuGet パッケージへの参照を追加します。
2. Program.cs ファイルの先頭に次の `using` ステートメントを追加します。
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
3. `Main` メソッドに次のコードを追加します。 名前空間の作成時に取得した接続文字列を `connectionString` 変数に設定し、トピックの作成時に使用した名前を `topicName` に設定します。
   
    ```csharp
    var connectionString = "<your connection string>";
    var topicName = "<your topic name>";
   
    var client = SubscriptionClient.CreateFromConnectionString(connectionString, topicName, "<your subscription name>");
   
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
   
    namespace GettingStartedWithTopics
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";;
          var topicName = "<your topic name>";
   
          var client = SubscriptionClient.CreateFromConnectionString(connectionString, topicName, "<your subscription name>");
   
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
4. プログラムを実行し、もう一度ポータルを確認します。 **[メッセージ数]** と **[現在]** の値が 0 になっていることがわかります。
   
    ![トピックの長さ][topic-message-receive]

お疲れさまでした。 以上、トピックとサブスクリプションを作成してメッセージを送信し、そのメッセージを受信する方法について説明しました。

<a id="next-steps" class="xliff"></a>

## 次のステップ

Service Bus メッセージングのさらに高度な機能を紹介する[サンプルが含まれる GitHub リポジトリ](https://github.com/Azure/azure-service-bus/tree/master/samples)を参照してください。

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/nuget-package.png
[topic-message]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message.png
[topic-message-receive]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message-receive.png
[createtopic1]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic1.png
[createtopic2]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic2.png
[createtopic3]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic3.png
[createtopic4]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic4.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
[azure-portal]: https://portal.azure.com

