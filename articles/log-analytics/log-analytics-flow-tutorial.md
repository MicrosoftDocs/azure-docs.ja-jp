---
title: "Microsoft Flow で Azure Log Analytics のプロセスを自動化する"
description: "Azure Log Analytics コネクタを使用して、Microsoft Flow で反復可能なプロセスを迅速に自動化する方法について説明します。"
services: log-analytics
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: log-analytics
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 4955f90de06cd720d78c5bb60c0adcd7dc633245
ms.contentlocale: ja-jp
ms.lasthandoff: 08/10/2017

---

# <a name="automate-log-analytics-processes-with-the-connector-for-microsoft-flow"></a>Microsoft Flow 対応のコネクタを使用して Log Analytics のプロセスを自動化する
[Microsoft Flow](https://ms.flow.microsoft.com) を使用すると、各種サービス用の何百ものアクションを使用して自動化されたワークフローを作成できます。 あるアクションの出力は別のアクションへの入力として使用できます。これにより、さまざまなサービス間の統合を作成できます。  Microsoft Flow 対応の Azure Log Analytics コネクタを使用すると、Log Analytics のログ検索機能によって取得されたデータが含まれるワークフローを構築できます。

たとえば、Microsoft Flow で Office 365 からの電子メール通知の Log Analytics データを使用して、Visual Studio Team Services でバグを作成したり、Slack のメッセージを投稿したりできます。  簡単なスケジュールまたは接続されたサービスのアクション (メールやツイートを受信したときなど) からワークフローをトリガーできます。  


> [!NOTE]
> Microsoft Flow 対応の Azure Log Analytics コネクタでは、ワークスペースを新しい Log Analytics のクエリ言語にアップグレードする必要があります。 新しい言語の詳細、およびワークスペースのアップグレード手順については、「[Upgrade your Azure Log Analytics workspace to new log search (新しいログ検索への Azure Log Analytics ワークスペースのアップグレード)](log-analytics-log-search-upgrade.md)」を参照してください。  

この記事のチュートリアルには、Log Analytics のログ検索の結果を自動的に送信するフローを作成する方法について説明します。これは、Microsoft Flow における Log Analytics の使用方法の一例です。 


## <a name="step-1-create-a-flow"></a>手順1 : フローを作成する
1. [Microsoft Flow](http://flow.microsoft.com) にサインインし、**[マイ フロー]** を選択します。
2. **[一から作成]** をクリックします。

## <a name="step-2-create-a-trigger-for-your-flow"></a>手順 2: フローのトリガーを作成する
1. **[Search hundreds of connectors and triggers]\(何百ものコネクタやトリガーを検索する\)** をクリックします。
2. 検索ボックスに「**スケジュール**」と入力します。
3. **[スケジュール]** を選択し、**[スケジュール - 繰り返し]** を選択します。
4. **[頻度]** ボックスで **[日]** を選択し、**[間隔]** ボックスに「**1**」と入力します。<br><br>![Microsoft Flow のトリガー ダイアログ ボックス](media/log-analytics-flow-tutorial/flow01.png)


## <a name="step-3-add-a-log-analytics-action"></a>手順 3: Log Analytics のアクションを追加する
1. **[+ 新しいステップ]** をクリックし、**[アクションの追加]** をクリックします。
2. **Log Analytics** を検索します。
3. **[Azure Log Analytics – Run query and visualize results]\(Azure Log Analytics – クエリを実行して結果を視覚化する\)** をクリックします。<br><br>![Log Analytics のクエリ実行ウィンドウ](media/log-analytics-flow-tutorial/flow02.png)

## <a name="step-4-configure-the-log-analytics-action"></a>手順 4: Log Analytics のアクションを構成する

1. ワークスペースの詳細を指定します (サブスクリプション ID、リソース グループ、ワークスペース名など)。
2. **[クエリ]** ボックスで、次の Log Analytics のクエリを追加します。  これはサンプル クエリです。データを返すその他のものに置き換えることもできます。
```
    Event
    | where EventLevelName == "Error" 
    | where TimeGenerated > ago(1day)
    | summarize count() by Computer
    | sort by Computerindow. 
```

2. **[グラフの種類]** ボックスで、**[HTML の表]** を選択します。<br><br>![Log Analytics のアクション](media/log-analytics-flow-tutorial/flow03.png)

## <a name="step-5-configure-the-flow-to-send-email"></a>手順 5: 電子メールを送信するフローを構成する

1. **[新しいステップ]** をクリックし、**[アクションの追加]** をクリックします。
2. **Office 365 Outlook** を検索します。
3. **[Office 365 Outlook – Send an email]\(Office 365 Outlook – 電子メールの送信\)** をクリックします。<br><br>![Office 365 Outlook の選択ウィンドウ](media/log-analytics-flow-tutorial/flow04.png)

4. **[宛先]** ウィンドウに受信者の電子メール アドレスを指定し、メールの件名を **[件名]** に入力します。
5. **[本文]** ボックスの任意の場所をクリックします。  前のアクションの値が入った **[動的なコンテンツ]** ウィンドウが開きます。  
6. **[本文]** を選択します。  これは Log Analytics アクションのクエリの結果です。
6. **[詳細オプションを表示する]** をクリックします。
7. **[Is HTML]\(HTML にする\)** ボックスで、**[はい]** を選択します。<br><br>![Office 365 の電子メール設定ウィンドウ](media/log-analytics-flow-tutorial/flow05.png)

## <a name="step-6-save-and-test-your-flow"></a>手順 6: フローを保存してテストする
1. **[フロー名]** ボックスにフローの名前を追加し、**[フローの作成]** をクリックします。<br><br>![フローを保存する](media/log-analytics-flow-tutorial/flow06.png)
2. フローが作成され、指定したスケジュール通りに 1 日後に実行されます。 
3. フローをすぐにテストするには、**[今すぐ実行]** をクリックして **[フローの実行]** をクリックします。<br><br>![フローを実行する](media/log-analytics-flow-tutorial/flow07.png)
3. フローが完了したら、指定した受信者のメールを確認します。  次のような本文のメールを受信します。<br><br>![電子メールのサンプル](media/log-analytics-flow-tutorial/flow08.png)


## <a name="next-steps"></a>次のステップ

- [Log Analytics のログ検索](log-analytics-log-search-new.md)についてさらに学習します。
- [Microsoft Flow](https://ms.flow.microsoft.com)についての詳細を見る




