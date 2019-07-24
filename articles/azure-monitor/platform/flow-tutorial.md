---
title: Microsoft Flow で Azure Monitor ログのプロセスを自動化する
description: Azure Log Analytics コネクタを使用して、Microsoft Flow で反復可能なプロセスを迅速に自動化する方法について説明します。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
ms.service: log-analytics
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: bwren
ms.openlocfilehash: c3732dd2fa87b00eec38f88ab828605b33567235
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58123149"
---
# <a name="automate-azure-monitor-log-processes-with-the-connector-for-microsoft-flow"></a>Microsoft Flow のコネクタを使用して Azure Monitor ログのプロセスを自動化する
[Microsoft Flow](https://ms.flow.microsoft.com) を使用すると、各種サービス用の何百ものアクションを使用して自動化されたワークフローを作成できます。 あるアクションの出力は別のアクションへの入力として使用できます。これにより、さまざまなサービス間の統合を作成できます。  Microsoft Flow 用の Azure Log Analytics コネクタを使用すると、Azure Monitor の Log Analytics ワークスペースから、ログ クエリによって取得されるデータを含むワークフローを構築できます。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

たとえば、Microsoft Flow を使用して、Office 365 からの電子メール通知で Azure Monitor ログ データを使用すること、Azure DevOps でバグを作成すること、Slack のメッセージを投稿することが可能です。  簡単なスケジュールまたは接続されたサービスのアクション (メールやツイートを受信したときなど) からワークフローをトリガーできます。  

この記事のチュートリアルでは、Azure Monitor ログ クエリの結果を電子メールで自動送信するフローを作成する方法を示します。これは、Microsoft Flow で Log Analytics コネクタを利用できる方法の一例にすぎません。 


## <a name="step-1-create-a-flow"></a>手順 1:フローを作成する
1. [Microsoft Flow](https://flow.microsoft.com) にサインインし、**[マイ フロー]** を選択します。
2. **[一から作成]** をクリックします。

## <a name="step-2-create-a-trigger-for-your-flow"></a>手順 2:フローのトリガーを作成する
1. **[Search hundreds of connectors and triggers]\(何百ものコネクタやトリガーを検索する\)** をクリックします。
2. 検索ボックスに「**スケジュール**」と入力します。
3. **[スケジュール]** を選択し、**[スケジュール - 繰り返し]** を選択します。
4. **[頻度]** ボックスで **[日]** を選択し、**[間隔]** ボックスに「**1**」と入力します。<br><br>![Microsoft Flow のトリガー ダイアログ ボックス](media/flow-tutorial/flow01.png)


## <a name="step-3-add-a-log-analytics-action"></a>手順 3:Log Analytics のアクションを追加する
1. **[+ 新しいステップ]** をクリックし、**[アクションの追加]** をクリックします。
2. **Log Analytics** を検索します。
3. **[Azure Log Analytics – Run query and visualize results]\(Azure Log Analytics – クエリを実行して結果を視覚化する\)** をクリックします。<br><br>![Log Analytics のクエリ実行ウィンドウ](media/flow-tutorial/flow02.png)

## <a name="step-4-configure-the-log-analytics-action"></a>手順 4:Log Analytics のアクションを構成する

1. ワークスペースの詳細を指定します (サブスクリプション ID、リソース グループ、ワークスペース名など)。
2. **[クエリ]** ウィンドウに次のログ クエリを追加します。  これはサンプル クエリです。データを返すその他のものに置き換えることもできます。
   ```
    Event
    | where EventLevelName == "Error" 
    | where TimeGenerated > ago(1day)
    | summarize count() by Computer
    | sort by Computer
   ```

2. **[グラフの種類]** ボックスで、**[HTML の表]** を選択します。<br><br>![Log Analytics のアクション](media/flow-tutorial/flow03.png)

## <a name="step-5-configure-the-flow-to-send-email"></a>手順 5:電子メールを送信するフローを構成する

1. **[新しいステップ]** をクリックし、**[アクションの追加]** をクリックします。
2. **Office 365 Outlook** を検索します。
3. **[Office 365 Outlook – Send an email]\(Office 365 Outlook – 電子メールの送信\)** をクリックします。<br><br>![Office 365 Outlook の選択ウィンドウ](media/flow-tutorial/flow04.png)

4. **[宛先]** ウィンドウに受信者の電子メール アドレスを指定し、メールの件名を **[件名]** に入力します。
5. **[本文]** ボックスの任意の場所をクリックします。  前のアクションの値が入った **[動的なコンテンツ]** ウィンドウが開きます。  
6. **[本文]** を選択します。  これは Log Analytics アクションのクエリの結果です。
6. **[詳細オプションを表示する]** をクリックします。
7. **[Is HTML]\(HTML にする\)** ボックスで、**[はい]** を選択します。<br><br>![Office 365 の電子メール設定ウィンドウ](media/flow-tutorial/flow05.png)

## <a name="step-6-save-and-test-your-flow"></a>手順 6:自分のフローを保存してテストする
1. **[フロー名]** ボックスにフローの名前を追加し、**[フローの作成]** をクリックします。<br><br>![フローを保存する](media/flow-tutorial/flow06.png)
2. フローが作成され、指定したスケジュール通りに 1 日後に実行されます。 
3. フローをすぐにテストするには、**[今すぐ実行]** をクリックして **[フローの実行]** をクリックします。<br><br>![フローを実行する](media/flow-tutorial/flow07.png)
3. フローが完了したら、指定した受信者のメールを確認します。  次のような本文のメールを受信します。<br><br>![電子メールのサンプル](media/flow-tutorial/flow08.png)


## <a name="next-steps"></a>次の手順

- [Azure Monitor のログ クエリ](../log-query/log-query-overview.md)についての詳細を見る。
- [Microsoft Flow](https://ms.flow.microsoft.com)についての詳細を見る



