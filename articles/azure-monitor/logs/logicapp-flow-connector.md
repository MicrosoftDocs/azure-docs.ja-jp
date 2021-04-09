---
title: Azure Logic Apps と Power Automate で Azure Monitor ログを使用する
description: Azure Monitor コネクタを使用することで、Azure Logic Apps と Power Automate を使って、反復可能なプロセスを迅速に自動化する方法について説明します。
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/13/2020
ms.openlocfilehash: 4a25d7a23d486c8ce22fa433cc1ead390726facc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048864"
---
# <a name="azure-monitor-logs-connector-for-logic-apps-and-power-automate"></a>Logic Apps および Power Automate の Azure Monitor Logs コネクタ
[Azure Logic Apps](../../logic-apps/index.yml) と [Power Automate](https://flow.microsoft.com) を使用すると、各種サービス用の何百ものアクションを使用して自動化されたワークフローを作成できます。 Azure Monitor Logs コネクタを使用すると、Azure Monitor 内の Log Analytics ワークスペースまたは Application Insights アプリケーションからデータを取得するワークフローを作成できます。 この記事では、コネクタに含まれるアクションについて説明し、このデータを使用してワークフローを作成するためのチュートリアルを提供します。

たとえば、ロジック アプリを作成して、Office 365 からの電子メール通知の Azure Monitor ログ データを使用したり、Azure DevOps でバグを作成したり、Slack メッセージを投稿したりできます。  簡単なスケジュールまたは接続されたサービスのアクション (メールやツイートを受信したときなど) からワークフローをトリガーできます。 

## <a name="connector-limits"></a>コネクタの制限
Azure Monitor Logs コネクタには次の制限があります。
* 最大データ サイズ:16 MB
* クエリ応答の最大サイズ 100 MB
* レコードの最大数:500,000
* クエリの最大タイムアウト 110 秒。
* 現在、コネクタと [ログ] ページで同じグラフ ライブラリが使用されていないため、グラフの視覚エフェクトは、[ログ] ページでは使用できますが、[コネクタ] ページにはありません。

使用するデータとクエリのサイズによっては、コネクタがその上限に達し、エラーが発生することがあります。 このような問題は、実行頻度を増やし、照会するデータを減らすようにトリガーの繰り返しを調整することで回避できます。 返すレコードと列が少なくなるようにデータを集計するクエリを使用できます。

## <a name="actions"></a>Actions
次の表では、Azure Monitor Logs コネクタに含まれるアクションについて説明します。 両方とも、Log Analytics ワークスペースまたは Application Insights アプリケーションに対してログ クエリを実行できます。 違いは、データが返される方法です。

> [!NOTE]
> Azure Monitor Logs コネクタは、[Azure Log Analytics コネクタ](/connectors/azureloganalytics/)および [Azure Application Insights コネクタ](/connectors/applicationinsights/)に置き換わるものです。 このコネクタはその他のものと同じ機能を提供し、Log Analytics ワークスペースまたは Application Insights アプリケーションに対してクエリを実行する場合に推奨される方法です。


| アクション | 説明 |
|:---|:---|
| [クエリの実行と結果の一覧表示](/connectors/azuremonitorlogs/#run-query-and-list-results) | 各行を独自のオブジェクトとして返します。 このアクションは、残りのワークフローで各行を個別に操作する場合に使用します。 アクションの後には、通常、[For each アクティビティ](../../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)が続きます。 |
| [クエリの実行と結果の視覚化](/connectors/azuremonitorlogs/#run-query-and-visualize-results) | 結果セット内のすべての行を 1 つの書式設定されたオブジェクトとして返します。 このアクションは、結果をメールで送信するなど、残りのワークフローで結果セットをまとめて使用する場合に使用します。  |

## <a name="walkthroughs"></a>チュートリアル
次のチュートリアルでは、Azure Logic Apps での Azure Monitor コネクタの使用方法について説明します。 これらの同じ例を Power Automate で実行できます。唯一の違いは、初期ワークフローの作成と完了時の実行方法です。 ワークフローおよびアクションの構成は、どちらも同じです。 作業を開始する場合は、「[Power Automate でテンプレートからフローを作成する](/power-automate/get-started-logic-template)」を参照してください。


### <a name="create-a-logic-app"></a>ロジック アプリの作成

Azure portal で **[Logic Apps]** に移動し、 **[追加]** をクリックします。 新しいロジック アプリを格納する **サブスクリプション**、**リソース グループ**、**リージョン** を選択し、一意の名前を付けます。 **[Log Analytics]** 設定を有効にすると、「[Azure Monitor ログを設定し、Azure Logic Apps の診断データを収集する](../../logic-apps/monitor-logic-apps-log-analytics.md)」で説明されているように、ランタイム データおよびイベントに関する情報を収集できます。 この設定は、Azure Monitor Logs コネクタを使用する場合には不要です。

![ロジック アプリを作成する](media/logicapp-flow-connector/create-logic-app.png)


**[確認および作成]** 、 **[作成]** の順にクリックします。 デプロイが完了したら、 **[リソースに移動]** をクリックし、 **[Logic Apps デザイナー]** を開きます。

### <a name="create-a-trigger-for-the-logic-app"></a>ロジック アプリのトリガーを作成する
**[一般的なトリガーで開始する]** の下で、 **[繰り返し]** を選択します。 これにより、一定の間隔で自動的に実行されるロジック アプリが作成されます。 ワークフローが 1 日 1 回実行されるように、アクションの **[頻度]** ボックスで、 **[日]** を選択し、 **[間隔]** ボックスに「**1**」と入力します。

![繰り返しアクション](media/logicapp-flow-connector/recurrence-action.png)

## <a name="walkthrough-mail-visualized-results"></a>チュートリアル:メールの視覚化された結果
次のチュートリアルでは、Azure Monitor ログ クエリの結果を電子メールで送信するロジック アプリを作成する方法について説明します。 

### <a name="add-azure-monitor-logs-action"></a>Azure Monitor Logs アクションの追加
**[+ 新しいステップ]** をクリックして、繰り返しアクションの後に実行するアクションを追加します。 **[アクションを選択してください]** の下で「**azure monitor**」と入力して、 **[Azure Monitor Logs]** を選択します。

![Azure Monitor Logs のアクション](media/logicapp-flow-connector/select-azure-monitor-connector.png)

**[Azure Log Analytics – Run query and visualize results]\(Azure Log Analytics – クエリを実行して結果を視覚化する\)** をクリックします。

![ロジック アプリ デザイナーのステップに追加される新しいアクションのスクリーンショット。 [アクションの選択] の下に Azure Monitor Logs が強調表示されています。](media/logicapp-flow-connector/select-query-action-visualize.png)


### <a name="add-azure-monitor-logs-action"></a>Azure Monitor Logs アクションの追加

Log Analytics ワークスペースの **サブスクリプション** と **リソース グループ** を選択します。 **[リソースの種類]** で *[Log Analytics ワークスペース]* を選択し、 **[リソース名]** でワークスペースの名前を選択します。

**[クエリ]** ウィンドウに次のログ クエリを追加します。  

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1day)
| summarize TotalErrors=count() by Computer
| sort by Computer asc   
```

**[時間の範囲]** で *[クエリに設定します]* を選択し、 **[グラフの種類]** で **[HTML Table]\(HTML テーブル\)** を選択します。
   
![[クエリを実行して結果を視覚化する] という名前の新しい Azure Monitor Logs アクションの設定のスクリーンショット。](media/logicapp-flow-connector/run-query-visualize-action.png)

メールは、現在の接続に関連付けられているアカウントによって送信されます。 別のアカウントを指定するには、 **[接続の変更]** をクリックします。

### <a name="add-email-action"></a>電子メール アクションの追加

**[+ 新しいステップ]** をクリックし、 **[+ アクションの追加]** をクリックします。 **[アクションの選択]** の下で「**outlook**」と入力して、 **[Office 365 Outlook]** を選択します。

![Outlook コネクタの選択](media/logicapp-flow-connector/select-outlook-connector.png)

**[メールの送信 (V2)]** を選択します。

![Office 365 Outlook の選択ウィンドウ](media/logicapp-flow-connector/select-mail-action.png)

**[本文]** ボックス内の任意の場所をクリックすると、 **[動的なコンテンツ]** ウィンドウが開き、ロジック アプリの前のアクションの値が表示されます。 **[もっと見る]** を選択し、Log Analytics アクションのクエリの結果である **[本文]** を選択します。

![本文を選択する](media/logicapp-flow-connector/select-body.png)

**[宛先]** ウィンドウに受信者の電子メール アドレスを指定し、メールの件名を **[件名]** に入力します。 

![メールのアクション](media/logicapp-flow-connector/mail-action.png)


### <a name="save-and-test-your-logic-app"></a>ロジック アプリを保存してテストする
**[保存]** をクリックし、ロジック アプリのテストの実行を行うために **[実行]** をクリックします。

![保存と実行](media/logicapp-flow-connector/save-run.png)


ロジック アプリが完了したら、指定した受信者のメールを確認します。  次のような本文のメールを受信します。

![電子メールのサンプル](media/logicapp-flow-connector/sample-mail.png)



## <a name="next-steps"></a>次のステップ

- [Azure Monitor のログ クエリ](./log-query-overview.md)についての詳細を見る。
- [Logic Apps](../../logic-apps/index.yml) についての詳細を見る
- [Power Automate](https://flow.microsoft.com) についての詳細を見る。