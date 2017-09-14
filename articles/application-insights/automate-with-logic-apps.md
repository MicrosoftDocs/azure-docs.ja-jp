---
title: "Logic Apps を使って Azure Application Insights のプロセスを自動化する"
description: "Application Insights コネクタをお使いのロジック アプリに追加することで、反復可能なプロセスを迅速に自動化する方法を説明します。"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 06bfb75a07b7902fcf245271def5d40e9941a89e
ms.contentlocale: ja-jp
ms.lasthandoff: 07/08/2017

---

# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Logic Apps を使って Application Insights のプロセスを自動化する

お使いのサービスが正常に機能しているかを確認するために、利用統計情報に対して同じクエリを繰り返し実行していませんか。 これらのクエリを自動化して傾向や異常を検出し、関連する独自のワークフローを構築する方法を確認しましょう。 これには、Logic Apps 対応の Azure Application Insights コネクタ (プレビュー) が最適です。

この統合によって、一行のコードも記述せずに、膨大な数のプロセスを自動化できます。 Application Insights コネクタを使ってロジック アプリを作成し、どの Application Insights プロセスでも迅速に自動化できます。 

また、その他のアクションを追加することもできます。 Azure App Service の Logic Apps 機能により、多数のアクションが利用可能になります。 たとえば、ロジック アプリを使用して、自動的に電子メール通知を送信したり、Visual Studio Team Services のバグを作成したりできます。 また、多数の[テンプレート](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates)の 1 つを使って、ロジック アプリの作成プロセスをスピードアップすることもできます。 

## <a name="create-a-logic-app-for-application-insights"></a>Application Insights のロジック アプリの作成

このチュートリアルでは、Analytics 自動クラスター アルゴリズムを使うロジック アプリを作成して、Web アプリケーションのデータの属性をグループ化する方法を説明します。 このフローは結果を自動的にメールで送信しますが、これは、Application Insights Analytics と Logic Apps を共に利用する方法の一例にすぎません。 

### <a name="step-1-create-a-logic-app"></a>手順 1: ロジック アプリを作成する
1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. **[新規]** ペインで、**[Web + モバイル]**、**[ロジック アプリ]** の順に選択します。

    ![新しいロジック アプリのウィンドウ](./media/automate-with-logic-apps/logicapp1.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>手順 2: ロジック アプリのトリガーを作成する
1. **ロジック アプリ デザイナー** ウィンドウで、**[一般的なトリガーで開始する]** の下の **[繰り返し]** を選択します。

    ![ロジック アプリ デザイナー ウィンドウ](./media/automate-with-logic-apps/logicapp2.png)

2. **[頻度]** ボックスで **[日]** を選択し、**[間隔]** ボックスに「**1**」と入力します。

    ![ロジック アプリ デザイナーの [繰り返し] ウィンドウ](./media/automate-with-logic-apps/step2b.png)

### <a name="step-3-add-an-application-insights-action"></a>手順 3: Application Insights のアクションを追加する
1. **[新しいステップ]** をクリックし、**[アクションの追加]** をクリックします。

2. **[アクションの選択]** 検索ボックスで、「**Azure Application Insights**」と入力します。

3. **[アクション]** の下で、**[Azure Application Insights – Visualize Analytics query Preview]\(Azure Application Insights – Visualize Analytics クエリのプレビュー\)** をクリックします。

    ![ロジック アプリ デザイナーの [アクションの選択] ウィンドウ](./media/automate-with-logic-apps/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>手順 4: Application Insights リソースに接続する

この手順を完了するには、お使いのリソースのアプリケーション ID と API キーが必要です。 以下の図に示すように、Azure ポータルから ID と キーを取得できます。

![Azure ポータルのアプリケーション ID](./media/automate-with-logic-apps/appid.png) 

接続名、アプリケーション ID、API キーを指定します。

![ロジック アプリ デザイナーのフロー接続ウィンドウ](./media/automate-with-logic-apps/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>手順 5: Analytics クエリとグラフの種類を指定する
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

2. **[グラフの種類]** ボックスで、**[HTML の表]** を選択します。

    ![Analytics クエリの設定ウィンドウ](./media/automate-with-logic-apps/flow4.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>手順 6: 電子メールを送信するようにロジック アプリを設定する

1. **[新しいステップ]** をクリックし、**[アクションの追加]** を選択します。

2. 検索ボックスに、「**Office 365 Outlook**」と入力します。

3. **[Office 365 Outlook – Send an email]\(Office 365 Outlook – 電子メールの送信\)** をクリックします。

    ![[Office 365 Outlook] の選択内容](./media/automate-with-logic-apps/flow2b.png)

4. **[電子メールの送信]** ウィンドウで、次の操作を行います。

   a. 受信者の電子メール アドレスを入力します。

   b. 電子メールの件名を入力します。

   c. **[本文]** ボックス内の任意の場所をクリックし、右に展開する動的なコンテンツのメニューで **[本文]** を選択します。

   d. **[詳細オプションを表示する]** をクリックします。

      ![Office 365 Outlook の設定](./media/automate-with-logic-apps/flow5.png)

5. 動的なコンテンツのメニューで、以下を実行します。

    a. **[添付ファイル名]** を選択します。

    b. **[添付ファイルの内容]** を選択します。
    
    c. **[Is HTML]\(HTML にする\)** ボックスで、**[はい]** を選択します。

      ![Office 365 の電子メール設定画面](./media/automate-with-logic-apps/flow7.png)

### <a name="step-7-save-and-test-your-logic-app"></a>手順 7: ロジック アプリを保存してテストする
* **[保存]** をクリックして変更を保存します。

トリガーがロジック アプリを実行するまで待機するか、または、**[実行]** をクリックしてすぐにロジック アプリを実行できます。

![ロジック アプリの作成画面](./media/automate-with-logic-apps/step7.png)

作成したロジック アプリを実行すると、電子メールの一覧に指定した受信者は、以下のような電子メールを受信します。

![ロジック アプリの電子メール メッセージ](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>次のステップ

- [Analytics クエリ](app-insights-analytics-using.md)の作成についての詳細を見る
- [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps) の詳細を見る



<!--Link references-->






