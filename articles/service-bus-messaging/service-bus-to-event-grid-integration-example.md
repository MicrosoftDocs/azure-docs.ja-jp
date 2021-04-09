---
title: Service Bus のイベントを Event Grid 経由で Azure Logic Apps を使用して処理する
description: この記事では、Service Bus のイベントを Event Grid 経由で Azure Logic Apps を使用して処理する手順について取り上げます。
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 10/16/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: 93375f6047fbe4eda2132e024dab0e067e83ccf1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95999027"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-logic-apps"></a>チュートリアル:Azure Event Grid 経由で受信した Azure Service Bus のイベントに Azure Logic Apps を使用して応答する
このチュートリアルでは、Azure Logic Apps を使用して、Azure Event Grid 経由で受信した Azure Service Bus イベントに応答する方法について説明します。 

[!INCLUDE [service-bus-event-grid-prerequisites](../../includes/service-bus-event-grid-prerequisites.md)]

## <a name="receive-messages-by-using-logic-apps"></a>Logic Apps を使用してメッセージを受信する
この手順では、Service Bus イベントを Azure Event Grid 経由で受信する Azure ロジック アプリを作成します。 

1. Azure portal で、ロジック アプリを作成します。
    1. **[+ リソースの作成]** 、 **[統合]** 、 **[ロジック アプリ]** の順に選択します。 
    2. **[ロジック アプリ - 作成]** ページで、ロジック アプリの **名前** を入力します。
    3. Azure **サブスクリプション** を選択します。 
    4. **[リソース グループ]** では **[既存のものを使用]** を選択し、以前に作成済みの、他のリソース (Azure 関数、Service Bus 名前空間など) に使用したリソース グループを選択します。 
    5. ロジック アプリの **場所** を選択します。 
    6. **[確認および作成]** を選択します。 
    1. **[確認および作成]** ページで **[作成]** を選択してロジック アプリを作成します。 
1. **[Logic Apps デザイナー]** ページの **[テンプレート]** で、 **[空のロジック アプリ]** を選択します。 
1. デザイナーで、次の手順を実行します。
    1. **Event Grid** を検索します。 
    2. **[リソース イベントが発生したとき - Azure Event Grid]** を選択します。 

        ![Logic Apps デザイナー - Event Grid トリガーの選択](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. **[サインイン]** を選択して、Azure 資格情報を入力し、 **[Allow Access]\(アクセスを許可する\)** を選択します。 
5. **[When a resource event occurs]\(リソース イベントが発生したとき\)** ページで、次の手順を実行します。
    1. Azure サブスクリプションを選択します。 
    2. **[リソースの種類]** で、 **[Microsoft.ServiceBus.Namespaces]** を選択します。 
    3. **[リソース名]** で、Service Bus 名前空間を選択します。 
    4. **[新しいパラメーターの追加]** を選択し、 **[サフィックス フィルター]** を選択します。 
    5. **[サフィックス フィルター]** で、Service Bus トピックの 2 つ目のサブスクリプションの名前を入力します。 
        ![Logic Apps デザイナー - イベントの構成](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. デザイナーで **[+ 新しいステップ]** を選択し、次の手順を実行します。
    1. **Service Bus** を検索します。
    2. 一覧から **[Service Bus]** を選択します。 
    3. **[アクション]** 一覧から **[メッセージを取得します]** を選択します。 
    4. **[トピック サブスクリプションからメッセージを取得する (ピークロック)]** を選択します。 

        ![Logic Apps デザイナー - メッセージの取得アクション](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. **接続名** を入力します。 次に例を示します。「**Get messages from the topic subscription**」。そして、Service Bus 名前空間を選択します。 

        ![Logic Apps デザイナー - Service Bus 名前空間の選択](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. **RootManageSharedAccessKey** を選び、 **[作成]** を選択します。

        ![Logic Apps デザイナー - 共有アクセス キーの選択](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    8. **トピック** と **サブスクリプション** を選択します。 
    
        ![トピックとサブスクリプションを選択する場所を示すスクリーンショット。](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. **[+ 新しいステップ]** を選択し、次の手順を実行します。 
    1. **[Service Bus]** を選びます。
    2. アクションの一覧から **[トピック サブスクリプション内のメッセージを完了する]** を選択します。 
    3. Service Bus **トピック** を選択します。
    4. トピックに対する 2 つ目の **サブスクリプション** を選択します。
    5. **[メッセージのロック トークン]** で、 **[動的なコンテンツ]** から **[ロック トークン]** を選択します。 

        ![Logic Apps デザイナー - メッセージを完成させる](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Logic Apps デザイナーのツールバーの **[保存]** を選択して、ロジック アプリを保存します。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/save-logic-app.png" alt-text="ロジック アプリを保存する":::
1. まだテスト メッセージをトピックに送信していない場合は、「[Service Bus トピックにメッセージを送信する](#send-messages-to-the-service-bus-topic)」セクションの手順に従って、トピックにメッセージを送信します。 
1. お使いのロジック アプリの **[概要]** ページに切り替えます。 送信されたメッセージの **[実行の履歴]** に、ロジック アプリの実行が表示されます。 ロジック アプリの実行が確認できるまでに数分かかる場合があります。 ツール バーの **[Refresh]\(最新の情報に更新\)** を選択して、ページを最新の情報に更新してください。 

    ![Logic Apps デザイナー - ロジック アプリの実行](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)
1. ロジック アプリの実行を選択して詳細を確認します。 for ループで 5 つのメッセージが処理されていることに注目してください。 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/logic-app-run-details.png" alt-text="ロジック アプリの実行の詳細":::    

## <a name="troubleshoot"></a>トラブルシューティング
しばらく待って最新の情報に更新しても呼び出しが表示されない場合は、次の手順に従います。 

1. メッセージが Service Bus トピックに到達したことを確認します。 **[Service Bus トピック]** ページの **受信メッセージ** カウンターに注目してください。 この場合、**MessageSender** アプリケーションを 2 回実行したので、10 件のメッセージが表示されています (各実行につき 5 件のメッセージ)。

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/topic-incoming-messages.png" alt-text="[Service Bus トピック] ページ - 受信メッセージ":::    
1. Service Bus サブスクリプションに **アクティブなメッセージがない** ことを確認します。 
    このページにイベントが 1 つも表示されない場合、 **[Service Bus Subscription]\(Service Bus サブスクリプション\)** ページに **[アクティブなメッセージ数]** が表示されていないことを確認してください。 このカウンターの数値がゼロより大きい場合は、なんらかの理由により、サブスクリプションのメッセージがハンドラー関数 (イベント サブスクリプション ハンドラー) に転送されていません。 イベント サブスクリプションが適切に設定されていることを確認してください。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/subscription-active-message-count.png" alt-text="Service Bus サブスクリプション側におけるアクティブなメッセージ数":::    
1. また、Service Bus 名前空間の **[イベント]** ページに **配信済みイベント** が表示されます。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-page.png" alt-text="[イベント] ページ - 配信済みイベント" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::
1. イベントが配信されたことは、 **[イベント サブスクリプション]** ページでも確認できます。 このページには、 **[イベント]** ページでイベント サブスクリプションを選択することによってアクセスできます。 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-delivered-events.png" alt-text="[イベント サブスクリプション] ページ - 配信済みイベント":::
## <a name="next-steps"></a>次のステップ

* [Azure Event Grid](../event-grid/index.yml) について学習します。
* [Azure Functions](../azure-functions/index.yml) について学習します。
* [Azure App Service の Logic Apps 機能](../logic-apps/index.yml)について学習します。
* Azure Service Bus の詳細については、[こちら](/azure/service-bus/)を参照してください。


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