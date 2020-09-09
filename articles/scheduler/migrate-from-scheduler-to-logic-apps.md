---
title: Azure Scheduler から Azure Logic Apps への移行
description: 廃止予定の Azure Scheduler のジョブを Azure Logic Apps で置換する方法について説明します
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 02/29/2020
ms.openlocfilehash: dd61ac9751010d57cbf5b742a5081beb3ac560e9
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2020
ms.locfileid: "83826062"
---
# <a name="migrate-azure-scheduler-jobs-to-azure-logic-apps"></a>Azure Scheduler ジョブを Azure Logic Apps に移行する

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) は、[廃止される予定](#retire-date)の Azure Scheduler の後継です。 Scheduler で設定したジョブを使用し続けるには、この記事に従って、できるだけ早く Azure Logic Apps に移行してください。 
>
> Scheduler は Azure portal で利用できなくなりましたが、現時点では [REST API](/rest/api/scheduler) と [Azure Scheduler PowerShell コマンドレット](scheduler-powershell-reference.md)がまだ使用できるので、お客様はジョブとジョブ コレクションを管理することができます。

この記事では、Azure Scheduler ではなく Azure Logic Apps を使用して自動化されたワークフローを作成することで、1 回限りのジョブと定期的なジョブをスケジュール設定する方法を示します。 Logic Apps を使用してスケジュールされたジョブを作成すると、次のメリットが得られます。

* ビジュアル デザイナーと、Azure Blob Storage、Azure Service Bus、Office 365 Outlook、SAP などの数百のサービスの[すぐに使用できるコネクタ](../connectors/apis-list.md)を使用して、ジョブを構築します。

* スケジュールされた各ワークフローを最上級の Azure リソースとして管理します。 各ロジック アプリは個別の Azure リソースであるため、*ジョブ コレクション*の概念について心配する必要はありません。

* 1 つのロジック アプリを使用して、複数の 1 回限りのジョブを実行します。

* タイム ゾーンをサポートし、夏時間 (DST) に合わせて自動調整されるスケジュールを設定します。

詳細については、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照するか、[初めてのロジック アプリを作成する](../logic-apps/quickstart-create-first-logic-app-workflow.md)のクイック スタートで初めてのロジック アプリを作成してみてください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* HTTP 要求を送信することでロジック アプリをトリガーするには、[Postman デスクトップ アプリ](https://www.getpostman.com/apps)などのツールを使用します。

## <a name="migrate-by-using-a-script"></a>スクリプトを使用して移行する

Scheduler ジョブはそれぞれ固有であるため、Azure Logic Apps へのすべての Scheduler ジョブの移行に 1 つで対応できるツールは存在しません。 ただし、[このスクリプトを編集する](https://github.com/Azure/logicapps/tree/master/scripts/scheduler-migration)ことにより、ニーズを満たすことができます。

## <a name="schedule-one-time-jobs"></a>1 回限りのジョブをスケジュール設定する

ロジック アプリを 1 つだけ作成して、複数の 1 回限りのジョブを実行できます。

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーで空のロジック アプリを作成します。

   基本的な手順については、[クイック スタート: 初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

1. 検索ボックスに「`when a http request`」と入力して、要求トリガーを検索します。 トリガーの一覧から、**HTTP 要求の受信時**

   !["要求" トリガーの追加](./media/migrate-from-scheduler-to-logic-apps/request-trigger.png)

1. 要求トリガーに対して、必要に応じて JSON スキーマを指定できます。これは、ロジック アプリ デザイナーが、要求トリガーへの受信呼び出しに含まれる入力の構造を理解するのに役立ち、出力をこの後のワークフローで選択しやすくします。

   **[要求本文の JSON スキーマ]** ボックスにスキーマを入力します。次に例を示します。

   ![要求スキーマ](./media/migrate-from-scheduler-to-logic-apps/request-schema.png)

   スキーマはなくても JSON 形式のサンプル ペイロードがあれば、そのペイロードからスキーマを生成することができます。

   1. 要求トリガーで **[サンプルのペイロードを使用してスキーマを生成する]** を選択します。

   1. 次の例のように、 **[サンプルの JSON ペイロードを入力するか、貼り付けます]** でサンプル ペイロードを指定し、 **[完了]** を選択します。

      ![サンプル ペイロード](./media/migrate-from-scheduler-to-logic-apps/sample-payload.png)

      ```json
      {
         "runat": "2012-08-04T00:00Z",
         "endpoint": "https://www.bing.com"
      }
      ```

1. トリガーで、 **[次のステップ]** を選択します。

1. 検索ボックスに、フィルターとして「`delay until`」と入力します。 アクションの一覧で、アクション **[延期期限]**

   このアクションは、指定した日時までロジック アプリ ワークフローを一時停止します。

   ![「延期期限」アクションの追加](./media/migrate-from-scheduler-to-logic-apps/delay-until.png)

1. ロジック アプリのワークフローを開始するタイムスタンプを入力します。

   **[タイムスタンプ]** ボックス内をクリックすると、動的コンテンツ リストが表示され、必要に応じてトリガーから出力を選択できるようになります。

   ![「延期期限」の詳細を指定](./media/migrate-from-scheduler-to-logic-apps/delay-until-details.png)

1. [数百単位のすぐに使えるコネクタ](../connectors/apis-list.md)から選択して、実行するその他のアクションを追加できます。

   たとえば、URL に要求を送信する HTTP アクションや、Storage キュー、Service Bus キューまたは Service Bus トピックを使用するアクションを含めることができます。

   ![HTTP アクション](./media/migrate-from-scheduler-to-logic-apps/request-http-action.png)

1. 完了したら、ロジック アプリを保存します。

   ![ロジック アプリを保存する](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

   初めてロジック アプリを保存するときに、そのロジック アプリの要求トリガーの URL エンドポイントが **[HTTP POST の URL]** ボックスに表示されます。 ロジック アプリを呼び出して入力を処理するためにロジック アプリに送信する場合に、呼び出し先としてこの URL を使用します。

   ![要求トリガーのエンドポイント URL を保存する](./media/migrate-from-scheduler-to-logic-apps/request-endpoint-url.png)

1. 後で手動でロジック アプリをトリガーする要求を送信できるように、このエンドポイント URL をコピーして保存します。

## <a name="start-a-one-time-job"></a>1 回限りのジョブを開始する

1 回限りのジョブを手動で実行またはトリガーするには、ロジック アプリの要求トリガーのエンドポイント URL への呼び出しを送信します。 この呼び出しでは、送信する入力またはペイロードを指定します。これはスキーマを指定することで既に記述している可能性があります。

たとえば、Postman アプリを使用して、このサンプルと同じような設定で POST 要求を作成し、 **[送信]** を選択して要求を行うことができます。

| 要求メソッド | URL | Body | ヘッダー |
|----------------|-----|------|---------|
| **POST** | <*endpoint-URL*> | **raw** <p>**JSON(application/json)** <p>**[raw]** ボックスに、要求で送信するペイロードを入力します。 <p>**注**:この設定により、 **[ヘッダー]** 値が自動的に構成されます。 | **[キー]** :Content-Type <br>**値**: application/json |
|||||

![ロジック アプリを手動でトリガーする要求を送信する](./media/migrate-from-scheduler-to-logic-apps/postman-send-post-request.png)

呼び出しを送信すると、ロジック アプリからの応答が **[本文]** タブの **[raw]** ボックスの下に表示されます。 

<a name="workflow-run-id"></a>

> [!IMPORTANT]
>
> 後でジョブを取り消す場合は、 **[ヘッダー]** タブを選択します。応答で **x-ms-workflow-run-id** ヘッダー値を見つけてコピーします。 
>
> ![Response](./media/migrate-from-scheduler-to-logic-apps/postman-response.png)

## <a name="cancel-a-one-time-job"></a>1 回限りのジョブをキャンセルする

Logic Apps では、1 回限りのジョブはそれぞれ 1 つのロジック アプリの実行インスタンスとして実行されます。 1 回限りのジョブをキャンセルするには、Logic Apps REST API で[ワークフロー実行 - キャンセル](https://docs.microsoft.com/rest/api/logic/workflowruns/cancel)を使用できます。 トリガーへの呼び出しを送信するときに、[ワークフロー実行 ID](#workflow-run-id) を指定します。

## <a name="schedule-recurring-jobs"></a>定期的なジョブをスケジュール設定する

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーで空のロジック アプリを作成します。

   基本的な手順については、[クイック スタート: 初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

1. 検索ボックスに、フィルターとして「recurrence」と入力します。 トリガーの一覧から、**定期的なアイテム**

   !["繰り返し" トリガーの追加](./media/migrate-from-scheduler-to-logic-apps/recurrence-trigger.png)

1. 必要に応じて、より詳細なスケジュールを設定します。

   ![詳細なスケジュール](./media/migrate-from-scheduler-to-logic-apps/recurrence-advanced-schedule.png)

   詳細なスケジュール オプションの詳細については、[定期的に実行されるタスクとワークフローを Azure Logic Apps で作成、実行する方法](../connectors/connectors-native-recurrence.md)に関するページを参照してください。

1. [数百単位のすぐに使えるコネクタ](../connectors/apis-list.md)から選択し、実行するその他のアクションを追加できます。 トリガーで、 **[次のステップ]** を選択します。 目的のアクションを探して選択します。

   たとえば、URL に要求を送信する HTTP アクションや、Storage キュー、Service Bus キューまたは Service Bus トピックを使用するアクションを含めることができます。

   ![HTTP アクション](./media/migrate-from-scheduler-to-logic-apps/recurrence-http-action.png)

1. 完了したら、ロジック アプリを保存します。

   ![ロジック アプリを保存する](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

## <a name="advanced-setup"></a>詳細設定

ここでは、ジョブをカスタマイズできるその他の方法を示します。

### <a name="retry-policy"></a>再試行ポリシー

断続的なエラーが発生したときに、ロジック アプリでアクションが再実行を試行する方法を制御するために、各アクションの設定で[再試行ポリシー](../logic-apps/logic-apps-exception-handling.md#retry-policies)を設定できます。次に例を示します。

1. アクションの省略記号 ( **[...]** ) メニューを開き、 **[設定]** を選択します。

   ![アクションの設定を開く](./media/migrate-from-scheduler-to-logic-apps/action-settings.png)

1. 目的の再試行ポリシーを選択します。 各ポリシーの詳細については、「[Retry policies](../logic-apps/logic-apps-exception-handling.md#retry-policies)」(再試行ポリシー) を参照してください。

   ![再試行ポリシーの選択](./media/migrate-from-scheduler-to-logic-apps/retry-policy.png)

## <a name="handle-exceptions-and-errors"></a>例外とエラーを処理する

Azure Sheduler では、既定のアクションが実行に失敗した場合、エラー状態に対処する代替アクションを実行できます。 Azure Logic Apps でも、同じタスクを実行することができます。

1. ロジック アプリ デザイナーでは、処理したいアクションの上で、ポインターを手順の間の矢印の上に移動し、 **[並列分岐の追加]** を選択します。

   ![並列分岐を追加する](./media/migrate-from-scheduler-to-logic-apps/add-parallel-branch.png)

1. 代替アクションとして代わりに実行するアクションを見つけて選択します。

   ![並列アクションを追加する](./media/migrate-from-scheduler-to-logic-apps/add-parallel-action.png)

1. 代替アクションで、省略記号 ( **[...]** ) メニューを開き、 **[実行条件の構成]** を選択します。

   ![実行条件の構成](./media/migrate-from-scheduler-to-logic-apps/configure-run-after.png)

1. **[に成功しました]** プロパティのボックスをオフにします。 **[に失敗しました]** 、 **[がスキップされます]** 、および **[がタイムアウトしました]** のプロパティを選択します。

   !["実行条件" プロパティの設定](./media/migrate-from-scheduler-to-logic-apps/select-run-after-properties.png)

1. 完了したら、 **[完了]** をクリックします。

例外処理の詳細については、[エラーと例外処理 - RunAfter プロパティ](../logic-apps/logic-apps-exception-handling.md#control-run-after-behavior)に関するセクションを参照してください。

## <a name="faq"></a>よく寄せられる質問

<a name="retire-date"></a>

**Q**: Azure Scheduler が廃止になるのはいつですか。 <br>
**A**: Azure Scheduler は、2019 年 12 月 31 日に完全に廃止される予定です。 この日付より前に行う必要がある重要な手順と詳細なタイムラインについては、「[Scheduler のサービス停止日を 2019 年 12 月 31 日まで延長](https://azure.microsoft.com/updates/extending-retirement-date-of-scheduler/)」を参照してください。 一般的な更新については、[Azure の更新情報 - スケジューラ](https://azure.microsoft.com/updates/?product=scheduler)を参照してください。

**Q**: サービスの廃止後、ジョブ コレクションとジョブはどうなりますか。 <br>
**A**: Scheduler のすべてのジョブ コレクションとジョブが実行を停止され、システムから削除されます。

**Q**: Scheduler ジョブを Logic Apps に移行する前に、バックアップまたはその他のタスクを実行する必要がありますか。 <br>
**A**: ベスト プラクティスとして、作業は常にバックアップしてください。 Scheduler ジョブを削除または無効にする前に、作成したロジック アプリが期待どおりに実行されていることを確認します。

**Q**: ジョブを Scheduler から Logic Apps に移行するために役立つツールはありますか。 <br>
**A**: 各 Scheduler ジョブは独特であるため、すべてに対応できるツールは存在しません。 ただし、ニーズに基づき、[このスクリプトを編集して Azure Scheduler ジョブを Azure Logic Apps に移行する](https://github.com/Azure/logicapps/tree/master/scripts/scheduler-migration)ことができます。

**Q**: Scheduler ジョブを移行するためのサポートはどこで受けられますか。 <br>
**A**: サポートを受けるためのいくつかの方法を次に示します。

**Azure Portal**

Azure サブスクリプションに有料サポート プランがある場合は、Azure portal でテクニカル サポート要求を作成できます。 それ以外の場合は、さまざまなサポート オプションを選択できます。

1. [Azure portal](https://portal.azure.com) のメイン メニューで、 **[ヘルプとサポート]** を選択します。

1. **[サポート]** メニューの **[新しいサポート リクエスト]** を選択します。 リクエストに関して次の情報を入力します。

   | プロパティ | 値 |
   |---------|-------|
   | **問題の種類** | **テクニカル** |
   | **サブスクリプション** | <*ご使用の Azure サブスクリプション*> |
   | **サービス** | **[監視 + 管理]** の下で、 **[Scheduler]** を選択します。 **[Scheduler]** が見つからない場合は、まず **[すべてのサービス]** を選択します。 |
   ||| 

1. 必要なサポート オプションを選択します。 有料サポート プランがある場合は、 **[次へ]** を選択します。

**コミュニティ**

* [Azure Logic Apps に関する Microsoft Q&A 質問ページ](https://docs.microsoft.com/answers/topics/azure-logic-apps.html)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-scheduler)

## <a name="next-steps"></a>次のステップ

* [定期的に実行されるタスクとワークフローを Azure Logic Apps で作成する](../connectors/connectors-native-recurrence.md)