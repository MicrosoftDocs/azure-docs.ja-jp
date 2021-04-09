---
title: Azure Functions を使用して Event Grid 経由で Service Bus のイベントを処理する
description: この記事では、Azure Functions を使用して Event Grid 経由で Service Bus のイベントを処理する手順について説明します。
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: afc0a5bf9b83363d1f4baab955b55148fe3a8498
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95818482"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions"></a>チュートリアル:Azure Functions を使用して、Azure Event Grid 経由で受信した Azure Service Bus のイベントに応答する
このチュートリアルでは、Azure Functions と Azure Logic Apps を使用して、Azure Event Grid 経由で受信した Azure Service Bus イベントに応答する方法について説明します。 

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * Service Bus 名前空間を作成する
> * サンプル アプリケーションを準備してメッセージを送信する
> * Service Bus トピックにメッセージを送信する
> * Logic Apps を使用してメッセージを受信する
> * Azure でテスト関数を設定する
> * Event Grid による関数と名前空間の接続
> * Azure Functions を使用してメッセージを受信する

[!INCLUDE [service-bus-event-grid-prerequisites](../../includes/service-bus-event-grid-prerequisites.md)]

## <a name="additional-prerequisites"></a>追加の前提条件
[Visual Studio 2019](https://www.visualstudio.com/vs) をインストールし、**Azure の開発** ワークロードを追加します。 このワークロードには、Visual Studio での Azure Functions プロジェクトの作成、ビルド、デプロイに必要な **Azure Function Tools** が含まれています。 

## <a name="deploy-the-function-app"></a>関数アプリをデプロイする 

>[!NOTE]
> Azure Functions アプリの作成とデプロイについて詳しくは、「[Visual Studio を使用する Azure Functions の開発](../azure-functions/functions-develop-vs.md)」を参照してください。

1. **SBEventGridIntegration.sln** ソリューションの **FunctionApp1** プロジェクトから **ReceiveMessagesOnEvent.cs** ファイルを開きます。 
1. `<SERCICE BUS NAMESPACE - CONNECTION STRING>` を Service Bus 名前空間の接続文字列に置き換えます。 これは、同じソリューションにある **MessageSender** プロジェクトの **Program.cs** ファイルで使用したものと同じである必要があります。 
1. **[FunctionApp1]** を右クリックし、 **[発行]** を選択します。 
1. **[発行]** ページで **[開始]** を選択します。 これらの手順は実際に表示される内容と異なる場合もありますが、発行の手順に大きな違いはありません。 
1. **発行** ウィザードの **[Target]\(ターゲット\)** ページで、 **[Target]\(ターゲット\)** に **[Azure]** を選択します。 
1. **[特定のターゲット]** ページで **[Azure Function App (Windows)]** を選択します。 
1. **[Functions インスタンス]** ページで **[新しい Azure 関数の作成]** を選択します。 
1. **[Function App (Windows)]** ページで、以下の手順に従います。
    1. 関数アプリの **名前** を入力します。
    1. Azure **サブスクリプション** を選択します。
    1. 既存の **リソース グループ** を選択するか、新しいリソース グループを作成します。 このチュートリアルでは、Service Bus 名前空間があるリソース グループを選択します。 
    1. App Service の **プランの種類** を選択します。
    1. **場所** を選択します。 Service Bus 名前空間と同じ場所を選択します。 
    1. 既存の **Azure Storage** を選択するか、 **[New]\(新規作成\)** を選択して、関数アプリで使用される新しいストレージ アカウントを作成します。 
    1. **[作成]** を選択して関数アプリを作成します。 
1. **発行** ウィザードの **[Functions インスタンス]** ページに戻って **[完了]** を選択します。 
1. Visual Studio の **[発行]** ページで、 **[発行]** を選択して関数アプリを Azure に発行します。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/publish-function-app.png" alt-text="関数アプリを発行する":::    
1. **[出力]** ウィンドウに表示されるビルドと発行のメッセージを見て、どちらも成功したことを確認します。 
1. 次に、 **[発行]** ページで **[Azure portal での管理]** を選択します。 
1. Azure portal の **[関数アプリ]** ページで、左側のメニューにある **[関数]** を選択し、2 つの関数が表示されていることを確認します。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/functions.png" alt-text="Event Grid トリガーと HTTP トリガーを処理する 2 つの関数":::

    > [!NOTE]
    > `EventGridTriggerFunction` では [Event Grid トリガー](../azure-functions/functions-bindings-event-grid-trigger.md)が、`HTTPTriggerFunction` では [HTTP トリガー](../azure-functions/functions-bindings-http-webhook-trigger.md)が使用されます。
1. 一覧から **[EventGridTriggerFunction]** を選択します。 HTTP トリガーを使用するよりも有利な点がいくつかあるため、Azure Functions では Event Grid トリガーの使用をお勧めします。 詳細については、「[Event Grid イベントに対するイベント ハンドラーとしての Azure 関数](../event-grid/handler-functions.md)」を参照してください。
1. **[EventGridTriggerFunction]** の **[関数]** ページで、左側のメニューの **[監視]** を選択します。 
1. **[構成]** を選択し、呼び出しログをキャプチャするように Application Insights を構成します。 Event Grid から Service Bus のイベントを受信したら、このページを使用して関数の実行を監視します。 
1. **[Application Insights]** ページでリソースの名前を入力し、リソースの **場所** を選択して、 **[OK]** を選択します。 
1. 上部 (階層リンク メニュー) の **EventGridTriggerFunction** 関数を選択して、 **[関数]** ページに戻ります。 
1. **[監視]** ページが表示されていることを確認します。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/monitor-before.png" alt-text="関数の [監視] ページ (関数の呼び出し前)":::
    
    このページは、Web ブラウザーのタブで開いたままにしておいてください。 後でこのページを最新の情報に更新すると、この関数の呼び出しが表示されます。

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Event Grid による関数と名前空間の接続
このセクションでは、Azure portal を使用して、関数と Service Bus 名前空間を関連付けます。 

Azure Event Grid サブスクリプションを作成するには、次の手順に従います。

1. Azure portal で、該当する名前空間に移動し、左側のウィンドウで **[イベント]** を選択します。 該当する名前空間のウィンドウが開き、右側のウィンドウに 2 つの Event Grid サブスクリプションが表示されます。 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png" alt-text="Service Bus - [イベント] ページ":::
2. ツール バーの **[+ イベント サブスクリプション]** を選択します。 
3. **[イベント サブスクリプションの作成]** ページで、次の手順を実行します。
    1. サブスクリプションの **名前** を入力します。 
    2. **システム トピック** の **名前** を入力します。 システム トピックは、Azure Storage アカウントや Azure Service Bus などの Azure リソースに関して作成されたトピックです。 システム トピックの詳細については、[システム トピックの概要](../event-grid/system-topics.md)に関するページを参照してください。
    2. **[エンドポイントのタイプ]** に **[Azure Function]** を選択し、 **[エンドポイントの選択]** をクリックします。 

        ![Service Bus - Event Grid サブスクリプション](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. **[Azure 関数の選択]** ページで、サブスクリプション、リソース グループ、関数アプリ、スロット、関数を選択し、 **[選択の確認]** を選択します。 

        ![関数 - エンドポイントの選択](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. **[イベント サブスクリプションの作成]** ページの **[フィルター]** タブに切り替えて、次の作業を行います。
        1. **[サブジェクト フィルタリングを有効にする]** を選択します。
        2. 前に作成した Service Bus トピック (**S1**) に対するサブスクリプションの名前を入力します。
        3. **[作成]** ボタンを選択します。 

            ![イベント サブスクリプションのフィルター](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. **[イベント]** ページの **[イベント サブスクリプション]** タブで、一覧にイベント サブスクリプションが表示されていることを確認します。

    ![一覧のイベント サブスクリプション](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="monitor-the-functions-app"></a>関数アプリを監視する
先ほど Service Bus トピックに送信したメッセージは、サブスクリプション (S1) に転送されます。 サブスクリプションにあるメッセージは、Event Grid によって Azure 関数に転送されます。 このチュートリアルの手順では、関数が呼び出されたことを確かめ、ログに記録された情報メッセージを確認します。 

1. 自分の Azure 関数アプリのページで、 **[監視]** タブに切り替えます (まだアクティブになっていない場合)。 Service Bus トピックにポストされた各メッセージのエントリが表示されます。 それらの表示されない場合は、数分待ってから、ページを最新の情報に更新してください。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-monitor.png" alt-text="関数の [監視] ページ (呼び出し後)":::
2. 一覧から呼び出しを選択して、詳細を表示します。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/invocation-details.png" alt-text="関数の呼び出しの詳細" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::

    メッセージが送信されたときに、 **[監視]** ページの **[ログ]** タブを使用して、ログ情報を確認することもできます。 ある程度の待ち時間が発生する可能性があります。ログに記録されたメッセージが表示されるまで、数分お待ちください。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-logs.png" alt-text="関数のログ" lightbox="./media/service-bus-to-event-grid-integration-example/function-logs.png":::

## <a name="troubleshoot"></a>トラブルシューティング
しばらく待って最新の情報に更新しても、関数の呼び出しが表示されない場合は、以下の手順に従います。 

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