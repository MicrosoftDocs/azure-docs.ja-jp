---
title: Logic Apps を使用して Azure Application Insights プロセスを自動化する
description: Application Insights コネクタをお使いのロジック アプリに追加することで、反復可能なプロセスを迅速に自動化する方法を説明します。
ms.topic: conceptual
ms.date: 03/11/2019
ms.openlocfilehash: d7ff75be3cb847235405a740df4a20803cdc87b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589927"
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Logic Apps を使って Application Insights のプロセスを自動化する

お使いのサービスが正常に機能しているかを確認するために、利用統計情報に対して同じクエリを繰り返し実行していませんか。 これらのクエリを自動化して傾向や異常を検出し、関連する独自のワークフローを構築する方法を確認しましょう。 これには、Logic Apps 対応の Azure Application Insights コネクタが最適なツールです。

> [!NOTE]
> Azure Application Insights コネクタは、API キーを必要とする代わりに Azure Active Directory に統合された[Azure Monitor コネクタ](../logs/logicapp-flow-connector.md)に置き換えられ、Log Analytics ワークスペースからのデータの取得も可能になりました。

この統合によって、一行のコードも記述せずに、膨大な数のプロセスを自動化できます。 Application Insights コネクタを使ってロジック アプリを作成し、どの Application Insights プロセスでも迅速に自動化できます。 

また、その他のアクションを追加することもできます。 Azure App Service の Logic Apps 機能により、多数のアクションが利用可能になります。 たとえば、ロジック アプリを使用して、自動的に電子メール通知を送信したり、Azure DevOps のバグを作成したりできます。 また、多数の[テンプレート](../../logic-apps/logic-apps-create-logic-apps-from-templates.md)の 1 つを使って、ロジック アプリの作成プロセスをスピードアップすることもできます。 

## <a name="create-a-logic-app-for-application-insights"></a>Application Insights のロジック アプリの作成

このチュートリアルでは、Analytics 自動クラスター アルゴリズムを使うロジック アプリを作成して、Web アプリケーションのデータの属性をグループ化する方法を説明します。 このフローは結果を自動的にメールで送信しますが、これは、Application Insights Analytics と Logic Apps を共に利用する方法の一例にすぎません。 

### <a name="step-1-create-a-logic-app"></a>手順 1:ロジック アプリを作成します
1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[リソースの作成]** をクリックし、 **[Web + モバイル]** 、 **[ロジック アプリ]** の順に選択します。

    ![新しいロジック アプリのウィンドウ](./media/automate-with-logic-apps/1createlogicapp.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>手順 2:ロジック アプリのトリガーを作成する
1. **ロジック アプリ デザイナー** ウィンドウで、 **[一般的なトリガーで開始する]** の下の **[繰り返し]** を選択します。

    ![ロジック アプリ デザイナー ウィンドウ](./media/automate-with-logic-apps/2logicappdesigner.png)

1. **[間隔]** ボックスに「**1**」と入力し、 **[頻度]** ボックスで **[日]** を選択します。

    ![ロジック アプリ デザイナーの [繰り返し] ウィンドウ](./media/automate-with-logic-apps/3recurrence.png)

### <a name="step-3-add-an-application-insights-action"></a>手順 3:Application Insights のアクションを追加する
1. **[新しいステップ]** をクリックします。

1. **[アクションの選択]** 検索ボックスで、「**Azure Application Insights**」と入力します。

1. **[アクション]** の下で、 **[Azure Application Insights - Visualize Analytics クエリ]** をクリックします。

    ![ロジック アプリ デザイナーの [アクションの選択] ウィンドウ](./media/automate-with-logic-apps/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>手順 4:Application Insights リソースに接続する

この手順を完了するには、お使いのリソースのアプリケーション ID と API キーが必要です。 以下の図に示すように、Azure ポータルから ID と キーを取得できます。

![スクリーンショットには、[API キーの作成] ボタンが選択された Azure portal の [API アクセス] ページが示されています。](./media/automate-with-logic-apps/5apiaccess.png)

![Azure ポータルのアプリケーション ID](./media/automate-with-logic-apps/6apikey.png)

接続名、アプリケーション ID、API キーを指定します。

![ロジック アプリ デザイナーのフロー接続ウィンドウ](./media/automate-with-logic-apps/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>手順 5:Analytics クエリとグラフの種類を指定する
次の例のクエリでは、前日に失敗したリクエストを選択して、処理の一環として発生した例外と関連付けています。 失敗したクエリは、operation_Id 識別子に基づいて、Analytics によって関連付けられます。 その後、自動クラスター アルゴリズムを使って、クエリによる結果のセグメント化が行われます。 

独自のクエリを作成するときは、クエリをフローに追加する前に、必ず Analytics で正常に動作していることを確認してください。

1. **[クエリ]** ボックスで、次の Analytics クエリを追加します。

    ```
    requests
    | where timestamp > ago(1d)
    | where success == "False"
    | project name, operation_Id
    | join ( exceptions
        | project problemId, outerMessage, operation_Id
    ) on operation_Id
    | evaluate autocluster()
    ```

1. **[グラフの種類]** ボックスで、 **[HTML の表]** を選択します。

    ![Analytics クエリの設定ウィンドウ](./media/automate-with-logic-apps/8query.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>手順 6:電子メールを送信するようにロジック アプリを設定する

1. **[新しいステップ]** をクリックします。

1. 検索ボックスに、「**Office 365 Outlook**」と入力します。

1. **[Office 365 Outlook - 電子メールの送信]** をクリックします。

    ![[Office 365 Outlook] の選択内容](./media/automate-with-logic-apps/9sendemail.png)

1. **[電子メールの送信]** ウィンドウで、次の操作を行います。

   a. 受信者の電子メール アドレスを入力します。

   b. 電子メールの件名を入力します。

   c. **[本文]** ボックス内の任意の場所をクリックし、右に展開する動的なコンテンツのメニューで **[本文]** を選択します。
    
   d. **[新しいパラメーターの追加]** ドロップダウンをクリックし、[添付ファイル] と [HTML] を選択します。

      ![スクリーンショットには、[本文] ボックスが強調表示された [メールの送信] ウィンドウと、右側の [本文] が強調表示された [動的なコンテンツ] メニューが示されています。](./media/automate-with-logic-apps/10emailbody.png)

      ![Office 365 Outlook の設定](./media/automate-with-logic-apps/11emailparameter.png)

1. 動的なコンテンツのメニューで、以下を実行します。

    a. **[添付ファイル名]** を選択します。

    b. **[添付ファイルの内容]** を選択します。
    
    c. **[Is HTML]\(HTML にする\)** ボックスで、 **[はい]** を選択します。

      ![Office 365 の電子メール設定画面](./media/automate-with-logic-apps/12emailattachment.png)

### <a name="step-7-save-and-test-your-logic-app"></a>手順 7:ロジック アプリを保存してテストする
* **[保存]** をクリックして変更を保存します。

トリガーがロジック アプリを実行するまで待機するか、または、 **[実行]** をクリックしてすぐにロジック アプリを実行できます。

![ロジック アプリの作成画面](./media/automate-with-logic-apps/13save.png)

作成したロジック アプリを実行すると、電子メールの一覧に指定した受信者は、以下のような電子メールを受信します。

![ロジック アプリの電子メール メッセージ](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>次のステップ

- [Analytics クエリ](../logs/get-started-queries.md)の作成についての詳細を見る
- [Logic Apps](../../logic-apps/logic-apps-overview.md) の詳細を見る



<!--Link references-->

