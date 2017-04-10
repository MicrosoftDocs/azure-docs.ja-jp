---
title: ".NET Framework を使用して Azure Event Hubs にイベントを送信する | Microsoft Docs"
description: ".NET Framework で Event Hubs にイベントを送信してみる"
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 5c326a025a4276ae9b1a777439ed6c728d3e7103
ms.lasthandoff: 03/09/2017


---
# <a name="send-events-to-azure-event-hubs-using-the-net-framework"></a>.NET Framework を使用して Azure Event Hubs にイベントを送信する

## <a name="introduction"></a>はじめに
Event Hubs は、接続されているデバイスとアプリケーションからの大量のイベント データ (テレメトリ) を処理するサービスです。 Event Hubs にデータを収集した後、ストレージ クラスターを使用してデータを格納したり、リアルタイムの分析プロバイダーを使用して転送できます。 この大規模なイベントの収集と処理の機能は、モノのインターネット (IoT) など最新アプリケーション アーキテクチャの重要なコンポーネントです。

このチュートリアルでは、[Azure Portal](https://portal.azure.com) を使用してイベント ハブを作成する方法を説明します。 また、.NET Framework を使用して、C# で記述されたコンソール アプリケーションを使ってイベント ハブにイベントを送信する方法も示します。 .NET Framework を使用してイベントを受信するには、[.NET Framework を使用してイベントを受信する方法](event-hubs-dotnet-framework-getstarted-receive-eph.md)に関する記事を参照するか、左側の目次で適切な受信側の言語をクリックしてください。

このチュートリアルを完了するには、次のものが必要です。

* [Microsoft Visual Studio 2015 以上](http://visualstudio.com)。 このチュートリアルのスクリーンショットには、Visual Studio 2017 が使用されています。
* アクティブな Azure アカウント。 アカウントがない場合は、無料アカウントを数分で作成できます。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/free/)を参照してください。

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs 名前空間とイベント ハブを作成する

最初のステップでは、[Azure Portal](https://portal.azure.com) を使用して Event Hubs 型の名前空間を作成し、アプリケーションがイベント ハブと通信するために必要な管理資格情報を取得します。 名前空間とイベント ハブを作成するには、[この記事](event-hubs-create.md)の手順を踏み、次のステップに進みます。

## <a name="create-a-console-application"></a>コンソール アプリケーションの作成
このセクションでは、Windows コンソール アプリを記述して、Event Hub にイベントを送信します。

1. Visual Studio で、 **コンソール アプリケーション** プロジェクト テンプレートを使用して、新しい Visual C# のデスクトップ アプリ プロジェクトを作成します。 プロジェクトの名前として「 **Sender**」と入力します。
   
    ![](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp1.png)
2. ソリューション エクスプローラーで **[Sender]** プロジェクトを右クリックし、**[ソリューションの NuGet パッケージの管理]** をクリックします。 
3. **[参照]** タブをクリックして、`Microsoft Azure Service Bus` を検索します。 **[インストール]**をクリックして、使用条件に同意します。 
   
    ![](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp2.png)
   
    Visual Studio によりパッケージのダウンロードとインストールが実行され、 [Azure Service Bus ライブラリ NuGet パッケージ](https://www.nuget.org/packages/WindowsAzure.ServiceBus)への参照が追加されます。
4. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。
   
    ```csharp
    using System.Threading;
    using Microsoft.ServiceBus.Messaging;
    ```
5. **Program** クラスに次のフィールドを追加し、前のセクションで作成した Event Hub の名前と、先ほど保存した名前空間レベルの接続文字列を値に代入します。
   
    ```csharp
    static string eventHubName = "{Event Hub name}";
    static string connectionString = "{send connection string}";
    ```
6. **Program** クラスに次のメソッドを追加します。
   
    ```csharp
    static void SendingRandomMessages()
    {
        var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
        while (true)
        {
            try
            {
                var message = Guid.NewGuid().ToString();
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                Console.ResetColor();
            }
   
            Thread.Sleep(200);
        }
    }
    ```
   
    このメソッドは、200 ミリ秒の遅延時間でイベント ハブにイベントを継続的に送信します。
7. 最後に、 **Main** メソッドに次の行を追加します。
   
    ```csharp
    Console.WriteLine("Press Ctrl-C to stop the sender process");
    Console.WriteLine("Press Enter to start now");
    Console.ReadLine();
    SendingRandomMessages();
    ```
8. プログラムを実行し、エラーがないことを確認します。
  
お疲れさまでした。 メッセージを Event Hub に送信しました。

## <a name="next-steps"></a>次のステップ
イベント ハブを作成し、データを送信する実用的なアプリケーションが構築できたので、次のシナリオに移動します。

* [イベント プロセッサ ホストを使用してイベントを受信する](event-hubs-dotnet-framework-getstarted-receive-eph.md)
* [イベント プロセッサ ホスト リファレンス](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)
* [Event Hubs の概要](event-hubs-what-is-event-hubs.md)

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png


