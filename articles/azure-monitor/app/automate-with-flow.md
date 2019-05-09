---
title: Microsoft Flow を使って Azure Application Insights のプロセスを自動化する
description: Microsoft Flow を利用し、Application Insights コネクタを使って反復可能なプロセスを迅速に自動化する方法を説明します。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: mbullwin
ms.openlocfilehash: 15299be83758c157bf3bc7d9fb27b50763b9148e
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438939"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Microsoft Flow 対応のコネクタを使って Azure Application Insights のプロセスを自動化する

お使いのサービスが正常に機能しているかを確認するために、利用統計情報に対して同じクエリを繰り返し実行していませんか。 これらのクエリを自動化して傾向や異常を検出し、関連する独自のワークフローを構築する方法を確認しましょう。 これには、Microsoft Flow 対応の Azure Application Insights コネクタが最適です。

この統合によって、一行のコードも記述せずに、膨大な数のプロセスを自動化できるようになりました。 Application Insights のアクションを使ってフローを作成すると、このフローが Application Insights Analytics クエリを自動的に実行します。 

また、その他のアクションを追加することもできます。 Microsoft Flow によって、多数のアクションが利用可能になります。 たとえば、Microsoft Flow を使って、自動的に電子メール通知を送信したり、Azure DevOps のバグを作成したりできます。 また、Microsoft Flow に対応したコネクタで使用できる多数の[テンプレート](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights)の 1 つを使うこともできます。 これらのテンプレートによって、フローを作成するプロセスを高速化できます。 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="create-a-flow-for-application-insights"></a>Application Insights のフローの作成

このチュートリアルでは、Analytics 自動クラスター アルゴリズムを使うフローを作成して、Web アプリケーションのデータの属性をグループ化する方法を説明します。 このフローは結果を自動的にメールで送信しますが、これは、Microsoft Flow と Application Insights Analytics を共に利用する方法の一例にすぎません。 

### <a name="step-1-create-a-flow"></a>手順 1: フローを作成する
1. [Microsoft Flow](https://flow.microsoft.com) にサインインし、**[マイ フロー]** を選択します。
2. **[新規]** をクリックして、次に **[一から作成する]** をクリックします。

    ![新しいフローを一から作成する](./media/automate-with-flow/1createflow.png)

### <a name="step-2-create-a-trigger-for-your-flow"></a>手順 2:フローのトリガーを作成する
1. [ビルドイン] タブで、**[スケジュール]** を選択し、**[スケジュール - 繰り返し]** を選択します。

    ![[ビルドイン] でスケジュールを選択](./media/automate-with-flow/2schedule.png)

1. **[間隔]** ボックスで「**1**」を入力し、**[頻度]** ボックスで **[日]** を選択します。
2. **[新しいステップ]** をクリックします。

    ![頻度と間隔を入力して、スケジュールの繰り返しを設定します。](./media/automate-with-flow/3schedulerecurrence.png)


### <a name="step-3-add-an-application-insights-action"></a>手順 3:Application Insights のアクションを追加する
1. **Azure Application Insights** を検索します。
2. **[Azure Application Insights - Visualize Analytics query]\(Azure Application Insights - Visualize Analytics クエリ\)** をクリックします。
 
    ![アクションを選択します。Azure Application Insights Visualize Analytics クエリ](./media/automate-with-flow/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>手順 4:Application Insights リソースに接続する

この手順を完了するには、お使いのリソースのアプリケーション ID と API キーが必要です。 以下の図に示すように、Azure ポータルから ID と キーを取得できます。

![Azure ポータルのアプリケーション ID](./media/automate-with-flow/5apiaccess.png)

![Azure portal の API キー](./media/automate-with-flow/6apikey.png)

- 接続名、アプリケーション ID、API キーを指定します。

    ![Microsoft Flow の接続ウィンドウ](./media/automate-with-flow/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>手順 5: Analytics クエリとグラフの種類を指定する
この例のクエリでは、前日に失敗したリクエストを選択して、処理の一環として発生した例外と関連付けています。 operation_Id 識別子に基づいて、Analytics によって関連付けられます。 その後、自動クラスター アルゴリズムを使って、クエリによる結果のセグメント化が行われます。 

独自のクエリを作成するときは、クエリをフローに追加する前に、必ず Analytics で正常に動作していることを確認してください。

- 次の Analytics クエリを追加して、グラフの種類として [HTML の表] を選択します。 **[新しいステップ]** を選択します。

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
    
    ![Analytics クエリの設定ウィンドウ](./media/automate-with-flow/8query.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>手順 6: 電子メールを送信するフローを設定する

1. **Office 365 Outlook** を検索します。
2. **[Office 365 Outlook - 電子メールの送信]** をクリックします。

    ![Office 365 Outlook の選択ウィンドウ](./media/automate-with-flow/9outlookaction.png)

1. **[電子メールの送信]** ウィンドウで、次の操作を行います。

   a. 受信者の電子メール アドレスを入力します。

   b. 電子メールの件名を入力します。

   c. **[本文]** ボックス内の任意の場所をクリックし、右に展開する動的なコンテンツのメニューで **[本文]** を選択します。

   d. **[詳細オプションを表示する]** をクリックします。

    ![Office 365 Outlook の設定](./media/automate-with-flow/10sendemailbody.png)

1. 動的なコンテンツのメニューで、以下を実行します。

    a. **[添付ファイル名]** を選択します。

    b. **[添付ファイルの内容]** を選択します。
    
    c. **[Is HTML]\(HTML にする\)** ボックスで、**[はい]** を選択します。

    ![Office 365 の電子メール設定ウィンドウ](./media/automate-with-flow/11emailattachment.png)

### <a name="step-7-save-and-test-your-flow"></a>手順 7: フローを保存してテストする
- **[フロー名]** ボックスにフローの名前を追加し、**[保存]** をクリックします。

    ![フローに名前を付けて保存](./media/automate-with-flow/12nameflow.png)

トリガーがこのアクションを実行するまで待機するか、または、[必要に応じてトリガーを実行する](https://flow.microsoft.com/blog/run-now-and-six-more-services/)ことで、すぐにフローを実行できます。

フローを実行すると、電子メールの一覧に指定した受信者は、以下のような電子メール メッセージを受信します。

![電子メールのサンプル](./media/automate-with-flow/flow9.png)


## <a name="next-steps"></a>次の手順

- [Analytics クエリ](../../azure-monitor/log-query/get-started-queries.md)の作成についての詳細を見る
- [Microsoft Flow](https://ms.flow.microsoft.com)についての詳細を見る



<!--Link references-->





