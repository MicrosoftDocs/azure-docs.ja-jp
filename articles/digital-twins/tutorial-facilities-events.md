---
title: チュートリアル:Azure Digital Twins 空間からのイベントをキャプチャする | Microsoft Docs
description: このチュートリアルの手順を使用して、Azure Digital Twins を Logic Apps に統合して空間から通知を受け取る方法について説明します。
services: digital-twins
author: dsk-2015
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 12/18/2018
ms.author: dkshir
ms.openlocfilehash: 524ca96687e9395b65ec513326ad0fd4f7c6d429
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57528907"
---
# <a name="tutorial-receive-notifications-from-your-azure-digital-twins-spaces-by-using-logic-apps"></a>チュートリアル:Logic Apps を使用して Azure Digital Twins 空間から通知を受け取る

Azure Digital Twins インスタンスをデプロイし、空間をプロビジョニングして、特定の条件を監視するためのカスタム関数を実装すると、監視対象の条件が発生したときにメールでオフィス管理者に通知できます。

[最初のチュートリアル](tutorial-facilities-setup.md)では、架空の建物の空間グラフを構成しました。 この建物の部屋には、モーション、二酸化炭素、温度のセンサーが備わっています。 [2 番目のチュートリアル](tutorial-facilities-udf.md)では、グラフをプロビジョニングしたほか、これらのセンサーの値を監視し、部屋が空室で温度と二酸化炭素が快適な範囲にあるときに通知をトリガーするユーザー定義関数をプロビジョニングしました。 

このチュートリアルでは、これらの通知を Azure Logic Apps と統合して、そのような部屋が使用可能なときにメールを送信する方法について説明します。 オフィス管理者は、この情報を使用して、生産性が最も高まる会議室を従業員が予約するのを支援できます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Event Grid にイベントを統合する。
> * Logic Apps を使用してイベントを通知する。

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、お客様が Azure Digital Twins の設定の[構成](tutorial-facilities-setup.md)と[プロビジョニング](tutorial-facilities-udf.md)を完了していることを前提としています。 次に進む前に、以下が準備されていることを確認します。

- [Azure アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Digital Twins のインスタンスが実行中であること。
- [Digital Twins C# のサンプル](https://github.com/Azure-Samples/digital-twins-samples-csharp)がダウンロードされ、作業マシン上に展開されていること。
- サンプルを実行する開発マシンに [.NET Core SDK バージョン 2.1.403 以上](https://www.microsoft.com/net/download)がインストールされていること。 適切なバージョンがインストールされていることを確認するには、`dotnet --version` を実行します。
- 通知メールを送信するための Office 365 アカウント。

## <a name="integrate-events-with-event-grid"></a>Event Grid にイベントを統合する

このセクションでは、Azure Digital Twins インスタンスからイベントを収集してそれを Logic Apps などの[イベント ハンドラー](../event-grid/event-handlers.md)にリダイレクトするよう、[Event Grid](../event-grid/overview.md) を設定します。

### <a name="create-an-event-grid-topic"></a>Event Grid トピックを作成する

[Event Grid トピック](../event-grid/concepts.md#topics)は、ユーザー定義関数によって生成されたイベントをルーティングするためのインターフェイスを提供します。 

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. 左側のウィンドウで、**[リソースの作成]** を選びます。 

1. **[Event Grid トピック]** を見つけて選択します。 **作成**を選択します。

1. Event Grid トピックの**名前**を入力し、**サブスクリプション**を選択します。 Digital Twins インスタンスに対して使用または作成した**リソース グループ**と**場所**を選択します。 **作成**を選択します。 

    ![Event Grid トピックを作成する](./media/tutorial-facilities-events/create-event-grid-topic.png)

1. リソース グループから Event Grid トピックに移動します。**[概要]** を選択し、**[トピック エンドポイント]** の値を一時ファイルにコピーします。 この URL は次のセクションで必要になります。 

1. **[アクセス キー]** を選択し、**YOUR_KEY_1** と **YOUR_KEY_2** を一時ファイルにコピーします。 これらの値は、次のセクションでエンドポイントを作成するために必要になります。

    ![Event Grid キー](./media/tutorial-facilities-events/event-grid-keys.png)

### <a name="create-an-endpoint-for-the-event-grid-topic"></a>Event Grid トピックのエンドポイントを作成する

1. コマンド ウィンドウで、Digital Twins サンプルの **occupancy-quickstart\src** フォルダーに移動していることを確認します。

1. Visual Studio Code エディターで **actions\createEndpoints.yaml** ファイルを開きます。 次の内容が含まれていることを確認します。

    ```yaml
    - type: EventGrid
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_Event_Grid
      secondaryConnectionString: Secondary_connection_string_for_your_Event_Grid
      path: Event_Grid_Topic_Path
    ```

1. プレースホルダー `Primary_connection_string_for_your_Event_Grid` を **YOUR_KEY_1** の値に置き換えます。

1. プレースホルダー `Secondary_connection_string_for_your_Event_Grid` を **YOUR_KEY_2** の値に置き換えます。

1. プレースホルダー `Event_Grid_Topic_Path` を Event Grid トピックのパスに置き換えます。 このパスは、**[トピック エンドポイント]** URL から **https://** と末尾のリソース パスを削除することで取得できます。 これは *yourEventGridName.yourLocation.eventgrid.azure.net* のような形式になります。

    > [!IMPORTANT]
    > 引用符なしですべての値を入力します。 YAML ファイル内のコロンの後に少なくとも 1 つの空白文字があることを確認してください。 YAML ファイルの内容は、[このツール](https://onlineyamltools.com/validate-yaml)のようなオンライン YAML 検証ツールを使用して検証することもできます。

1. ファイルを保存して閉じます。 コマンド ウィンドウで次のコマンドを実行し、メッセージが表示されたらサインインします。 

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   このコマンドを実行すると、Event Grid のエンドポイントが作成されます。 

   ![Event Grid のエンドポイント](./media/tutorial-facilities-events/dotnet-create-endpoints.png)

## <a name="notify-events-with-logic-apps"></a>Logic Apps を使用してイベントを通知する

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) サービスを使用すると、他のサービスから受信したイベントに対する自動化されたタスクを作成できます。 このセクションでは、[Event Grid トピック](../event-grid/overview.md)を利用して、空間センサーからルーティングされたイベントに関するメール通知を作成するよう Logic Apps を設定します。

1. [Azure portal](https://portal.azure.com) の左側のウィンドウで、**[リソースの作成]** を選択します。

1. 新しい**ロジック アプリ** リソースを見つけて選択します。 **作成**を選択します。

1. ロジック アプリ リソースの**名前**を入力し、**サブスクリプション**、**リソース グループ**、**場所**を選択します。 **作成**を選択します。

    ![Logic Apps リソースを作成](./media/tutorial-facilities-events/create-logic-app.png)

1. Logic Apps リソースがデプロイされたらこれを開き、**[ロジック アプリ デザイナー]** ウィンドウを開きます。 

1. **[When an Event Grid event occurs]\(Event Grid イベントが発生したとき\)** トリガーを選択します。 メッセージが表示されたら、Azure アカウントを使用して目的のテナントにサインインします。 メッセージが表示されたら、目的の Event Grid リソースへの **[アクセスを許可]** を選択します。 **[続行]** をクリックします。

1. **[When a resource event occurs (Preview)]\(リソース イベントが発生したとき (プレビュー)\)** ウィンドウで、次の操作を行います。 
   
   a. Event Grid トピックを作成するときに使用した**サブスクリプション**を選択します。

   b. **[リソース タイプ]** で **[Microsoft.EventGrid.Topics]** を選択します。

   c. **[リソース名]** のドロップダウン ボックスで目的の Event Grid リソースを選択します。

   ![ロジック アプリ デザイナー ウィンドウ](./media/tutorial-facilities-events/logic-app-resource-event.png)

1. **[新しいステップ]** を選択します。

1. **[アクションの選択]** ウィンドウで、次の操作を行います。

   a. 「**JSON の解析**」という語句を検索し、**[JSON の解析]** アクションを選択します。

   b. **[Content]\(コンテンツ\)** フィールドで、**[動的なコンテンツ]** の一覧から **[Body]\(本文\)** を選択します。

   c. **[サンプルのペイロードを使用してスキーマを生成する]** を選択します。 次の JSON ペイロードを貼り付け、**[完了]** を選択します。

    ```JSON
    {
    "id": "32162f00-a8f1-4d37-aee2-9312aabba0fd",
    "subject": "UdfCustom",
    "data": {
      "TopologyObjectId": "20efd3a8-34cb-4d96-a502-e02bffdabb14",
      "ResourceType": "Space",
      "Payload": "\"Air quality is poor.\"",
      "CorrelationId": "32162f00-a8f1-4d37-aee2-9312aabba0fd"
    },
    "eventType": "UdfCustom",
    "eventTime": "0001-01-01T00:00:00Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/a382ee71-b48e-4382-b6be-eec7540cf271/resourceGroups/HOL/providers/Microsoft.EventGrid/topics/DigitalTwinEventGrid"
    }
    ```

    このペイロードには架空の値が含まれています。 Logic Apps によってこのサンプル ペイロードが使用され、"*スキーマ*" が生成されます。

    ![Event Grid に対する Logic Apps の [JSON の解析] ウィンドウ](./media/tutorial-facilities-events/logic-app-parse-json.png)

1. **[新しいステップ]** を選択します。

1. **[アクションの選択]** ウィンドウで、次の操作を行います。

   a. **[アクション]** の一覧から **[Control]\(コントロール\) > [条件]** を選択するか、**[条件]** を検索します。 

   b. 最初の **[値の選択]** テキスト ボックスで、**[JSON の解析]** ウィンドウの **[動的なコンテンツ]** の一覧で **[eventType]** を選択します。

   c. 2 番目の **[値の選択]** ボックスに「`UdfCustom`」と入力します。

   ![選択された条件](./media/tutorial-facilities-events/logic-app-condition.png)

1. **[true の場合]** ウィンドウで、次の操作を行います。

   a. **[アクションの追加]** を選択し、**[Office 365 Outlook]** を選択します。

   b. **[アクション]** の一覧で、**[メールの送信]** を選択します。 **[サインイン]** をクリックし、自分のメール アカウントの資格情報を使用します。 メッセージが表示されたら、**[アクセスを許可]** を選択します。

   c. **[To]\(送信先\)** ボックスに、通知を受け取るためのメール ID を入力します。 **[件名]** にテキスト「**空間における空気の質の悪化に関する Digital Twins 通知**」を入力し、 **[JSON の解析]** の **[動的なコンテンツ]** の一覧で **[TopologyObjectId]** を選択します。

   d. 同じウィンドウの **[Body]\(本文\)** に、「**室内で空気の質の悪化が検出されました。温度を調整する必要があります**」のようなテキストを入力します。 **[動的なコンテンツ]** の一覧の要素を自由に使用して詳しい説明を追加してください。

   ![Logic Apps で [電子メールの送信] を選択](./media/tutorial-facilities-events/logic-app-send-email.png)

1. **[ロジック アプリ デザイナー]** ウィンドウの最上部にある **[保存]** ボタンを選択します。

1. コマンド ウィンドウで Digital Twins サンプルの **device-connectivity** フォルダーに移動し、`dotnet run` を実行して、センサー データをシミュレートします。

数分後には、この Logic Apps リソースからメール通知を受信するようになります。 

   ![電子メール通知](./media/tutorial-facilities-events/logic-app-notification.png)

これらのメールの受信を停止するには、ポータルで目的の Logic Apps リソースに移動し、**[概要]** ウィンドウを選択します。 **[無効]** を選択します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

この時点で Azure Digital Twins の探索を中止する場合は、このチュートリアルで作成されたリソースを削除してかまいません。

1. [Azure portal](https://portal.azure.com) の左側のメニューにある **[すべてのリソース]** をクリックし、目的の Digital Twins リソース グループを選択して **[削除]** を選択します。

    > [!TIP]
    > ご自分の Digital Twins インスタンスの削除で問題が発生していた場合は、サービス更新が修正と共にロールアウトされています。 ご自分のインスタンスの削除を再試行してください。

2. 必要に応じて、作業マシン上のサンプル アプリケーションを削除します。

## <a name="next-steps"></a>次の手順

センサー データの視覚化、傾向の分析、異常の特定を行う方法について学習するために、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [チュートリアル:Time Series Insights を使用して Azure Digital Twins 空間からのイベントを視覚化および分析する](tutorial-facilities-analyze.md)

また、Azure Digital Twins の空間インテリジェンス グラフとオブジェクト モデルについて詳しく学習することもできます。

> [!div class="nextstepaction"]
> [Digital Twins オブジェクト モデルと空間インテリジェンス グラフを理解する](concepts-objectmodel-spatialgraph.md)
