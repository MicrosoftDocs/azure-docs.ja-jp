---
title: Azure Service Bus と Event Grid の統合の例 | Microsoft Docs
description: この記事では、Service Bus メッセージングと Event Grid の統合の例を紹介します。
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: spelluru
ms.openlocfilehash: 4e1ea3d822c8b032617b7f202f1c176aeb966210
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58436780"
---
# <a name="azure-service-bus-to-azure-event-grid-integration-examples"></a>Azure Service Bus と Azure Event Grid の統合の例

Azure 関数とロジック アプリはどちらも、Azure Event Grid からのイベントの受信に基づいてメッセージを受信します。この記事では、Azure 関数とロジック アプリの設定方法について説明します。 次の作業を行います。
 
* Event Grid からのイベントの初期フローをデバッグして確認するための、簡単なテスト Azure 関数を作成します。 この手順は、他の作業を行うかどうかに関係なく実行します。
* Event Grid イベントに基づいて Azure Service Bus メッセージを受信して処理する Azure 関数を作成します。
* Azure App Service の Logic Apps 機能を利用します。

作成する例では、Service Bus トピックに 2 つのサブスクリプションがあることを前提としています。 また、一方の Service Bus サブスクリプションについてのみ、イベントを送信するように Event Grid サブスクリプションが作成されていることを前提とします。 

この例では、Service Bus トピックにメッセージを送信した後、その Service Bus サブスクリプションのイベントが生成されているかどうかを確認します。 関数アプリまたはロジック アプリは、Service Bus サブスクリプションからメッセージを受信して完了します。

## <a name="prerequisites"></a>前提条件
開始する前に、次の 2 つのセクションの手順が完了していることを確認してください。

### <a name="create-a-service-bus-namespace"></a>Service Bus 名前空間を作成する

Service Bus Premium 名前空間を作成し、2 つのサブスクリプションを持った Service Bus トピックを作成します。

### <a name="send-a-message-to-the-service-bus-topic"></a>Service Bus トピックにメッセージを送信する

メッセージを Service Bus トピックに送信するには、お好きな方法を使用できます。 この手順の最後にあるサンプル コードは、Visual Studio 2017 の使用を前提としています。

1. [GitHub の azure-service-bus リポジトリ](https://github.com/Azure/azure-service-bus/)を複製します。

1. Visual Studio で *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* フォルダーに移動し、*SBEventGridIntegration.sln* ファイルを開きます。

1. **MessageSender** プロジェクトに移動し、**[Program.cs]** を選択します。

   ![8][]

1. トピック名と接続文字列を入力し、次のコンソール アプリケーション コードを実行します。

    ```CSharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```

## <a name="set-up-a-test-function"></a>テスト関数をセットアップする

完全なシナリオを実行する前に、少なくとも小さなテスト関数をセットアップします。これを使用して、送信されているイベントのデバッグと観察を行えます。

1. Azure Portal で、新しい Azure Functions アプリケーションを作成します。 Azure Functions の基礎については、[Azure Functions のドキュメント](https://docs.microsoft.com/azure/azure-functions/)を参照してください。

1. 新たに作成した関数の正符号 (+) を選択して、HTTP トリガー関数を追加します。

    ![2][]
    
    **[用意されている関数を使ってすぐに使用を開始する]** ウィンドウが表示されます。

    ![3][]

1. **[webhook + API]** ボタンを選択し、**[CSharp]** を選択して、**[この関数を作成する]** を選択します。
 
1. この関数に次のコードを貼り付けます。

    ```CSharp
    #r "Newtonsoft.Json"
    using System.Net;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info("C# HTTP trigger function processed a request.");
        // parse query parameter
        var content = req.Content;
    
        string jsonContent = await content.ReadAsStringAsync(); 
        log.Info($"Received Event with payload: {jsonContent}");
    
    IEnumerable<string> headerValues;
    if (req.Headers.TryGetValues("Aeg-Event-Type", out headerValues))
    {
    var validationHeaderValue = headerValues.FirstOrDefault();
    if(validationHeaderValue == "SubscriptionValidation")
    {
    var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
         var code = events[0].Data["validationCode"];
         return req.CreateResponse(HttpStatusCode.OK,
         new { validationResponse = code });
    }
    }
    
        return jsonContent == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + jsonContent);
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }
    ```

1. **[保存および実行]** を選択します。

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Event Grid による関数と名前空間の接続

このセクションでは、関数と Service Bus 名前空間を関連付けます。 たとえば、Azure Portal を使用します。 PowerShell または Azure CLI を使用してこの手順を行う方法については、[Azure Service Bus と Azure Event Grid の統合の概要](service-bus-to-event-grid-integration-concept.md)に関するページを参照してください。

Azure Event Grid サブスクリプションを作成するには、次の手順に従います。
1. Azure Portal で、該当する名前空間に移動し、左側のウィンドウで **[Event Grid]** を選択します。  
    該当する名前空間のウィンドウが開き、右側のウィンドウに 2 つの Event Grid サブスクリプションが表示されます。

    ![20][]

1. **[イベント サブスクリプション]** を選びます。  
    **[イベント サブスクリプション]** ウィンドウが表示されます。 次の画像は、フィルターを適用せずに、Azure 関数または webhook をサブスクライブするためのフォームを示しています。

    ![21][]

1. ここに示したようにフォームに必要事項を入力します。**[サフィックス フィルター]** ボックスに、関連するフィルターを忘れずに入力してください。

1. **作成**を選択します。

1. 「前提条件」セクションで説明したとおりメッセージを Service Bus トピックに送信し、Azure Functions の監視機能を使用して、イベントが送信されていることを確認します。

次の手順では、関数と Service Bus 名前空間を関連付けます。 たとえば、Azure Portal を使用します。 PowerShell または Azure CLI を使用してこの手順を行う方法については、[Azure Service Bus と Azure Event Grid の統合の概要](service-bus-to-event-grid-integration-concept.md)に関するページを参照してください。

![9][]

### <a name="receive-messages-by-using-azure-functions"></a>Azure Functions を使用してメッセージを受信する

前のセクションでは、テストとデバッグの簡単なシナリオを確認し、イベントが送信されていることを確認しました。 

このセクションでは、イベントを受信した後に、メッセージを受信して処理する方法について説明します。

Azure Functions 内の Service Bus の関数では、新しい Event Grid の統合がまだネイティブにサポートされていません。そのため、次の例に示したように Azure 関数を自分で追加することになります。

1. 前提条件で開いたのと同じ Visual Studio ソリューションで、**ReceiveMessagesOnEvent.cs** を選択します。 

    ![10][]

1. 次のコードに実際の接続文字列を入力します。

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```

1. 「テスト関数をセットアップする」セクションで作成した Azure 関数の発行プロファイルを Azure Portal でダウンロードします。

    ![11][]

1. Visual Studio で **SBEventGridIntegration** を右クリックして **[発行]** を選択します。 

1. 先ほどダウンロードした発行プロファイルの **[発行]** ウィンドウで、**[プロファイルのインポート]** を選択し、**[発行]** を選択します。

    ![12][]

1. 新しい Azure 関数を発行した後、その新しい Azure 関数を指す新しい Azure Event Grid サブスクリプションを作成します。  
    **[次の値で終わる]** ボックスで、必ず正しいフィルターを適用してください。実際の Service Bus のサブスクリプション名を指定する必要があります。

1. 先ほど作成した Azure Service Bus トピックにメッセージを送信し、Azure Portal で Azure Functions ログを監視し、イベントが送信されていることとメッセージが受信されていることを確認します。

    ![12-1][]

### <a name="receive-messages-by-using-logic-apps"></a>Logic Apps を使用してメッセージを受信する

次の手順に従い、Azure Service Bus と Azure Event Grid にロジック アプリを接続します。

1. Azure Portal で新しいロジック アプリを作成し、**Event Grid** を開始アクションとして選択します。

    ![13][]

    Logic Apps デザイナー ウィンドウが表示されます。

    ![14][]

1. 次の手順に従って、該当する情報を追加します。

    a. **[リソース名]** ボックスには、独自の名前空間名を入力します。 

    b. **[詳細設定オプション]** の **[サフィックス フィルター]** ボックスに、ご利用のサブスクリプションのフィルターを入力します。

1. トピック サブスクリプションからメッセージを受信する Service Bus 受信アクションを追加します。  
    最終的なアクションを次の図に示します。

    ![15][]

1. 次の図に示すように、完全なイベントを追加します。

    ![16][]

1. ロジック アプリを保存し、「前提条件」セクションで説明したとおり Service Bus トピックにメッセージを送信します。  
    ロジック アプリの実行を観察します。 実行に関するデータをさらに表示するには、**[概要]** を選択し、**[実行の履歴]** でデータを確認します。

    ![17][]

    ![18][]

## <a name="next-steps"></a>次の手順

* [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/) について学習します。
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) について学習します。
* [Azure App Service の Logic Apps 機能](https://docs.microsoft.com/azure/logic-apps/)について学習します。
* Azure Service Bus の詳細については、[こちら](https://docs.microsoft.com/azure/service-bus/)を参照してください。


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
