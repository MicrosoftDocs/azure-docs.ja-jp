---
title: 繰り返しトリガーを使用して繰り返しタスクをスケジュール設定する - Azure Logic Apps
description: Azure Logic Apps で繰り返しトリガーを使用して自動化された繰り返しタスクおよびワークフローをスケジュール設定して実行する
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 0bd7262daf23f205552e46bc3ca2802cf35f85db
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914453"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Azure Logic Apps で繰り返しトリガーを使用して繰り返しタスクおよびワークフローを作成、スケジュール設定、および実行する

特定のスケジュールでタスク、プロセス、またはジョブを定期的に実行するには、組み込みの **[Recurrence - Schedule] (繰り返し - スケジュール)** トリガーを使用してロジック アプリ ワークフローを開始できます。 ワークフローを開始するための日付と時刻やタイム ゾーン、およびそのワークフローを反復処理するための繰り返しを設定できます。 繰り返しが何らかの理由で失敗した場合、このトリガーは、次のスケジュール設定された間隔で繰り返しを続行します。 トリガーとアクションの組み込みのスケジュール設定の詳細については、[Azure Logic Apps での自動化された繰り返しタスクおよびワークフローのスケジュール設定と実行](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)に関するページを参照してください。

このトリガーがサポートするいくつかのパターンを、より高度な繰り返しや複雑なスケジュールと共に次に示します。

* 直ちに実行し、*n* 秒、分、時間、日、週、または月ごとに繰り返す。

* 特定の日付と時刻に開始して実行し、*n* 秒、分、時間、日、週、または月ごとに繰り返す。

* 毎日 1 回または複数回実行する (8:00 AM と 5:00 PM など)。

* 毎週特定の曜日 (土曜日と日曜日など) に実行する。

* 毎週特定の曜日の特定の時刻 (月曜日から金曜日の 8:00 AM と 5:00 PM など) に実行する。

このトリガーとスライディング ウィンドウ トリガーの違いについて、または繰り返しワークフローのスケジュール設定の詳細については、[Azure Logic Apps での自動化された繰り返しタスク、プロセス、およびワークフローのスケジュール設定と実行](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)に関するページを参照してください。

> [!TIP]
> ロジック アプリをトリガーし、将来 1 回だけ実行する場合は、「[ジョブを 1 回だけ実行する](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)できます。

* [ロジック アプリ](../logic-apps/logic-apps-overview.md)に関する基本的な知識。 ロジック アプリを初めて使用する場合は、[初めてのロジック アプリを作成する方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)を学習してください。

## <a name="add-recurrence-trigger"></a>繰り返しトリガーを追加する

1. [Azure Portal](https://portal.azure.com) にサインインします。 空のロジック アプリを作成します。

1. ロジック アプリ デザイナーが表示されたら、検索ボックスに、フィルターとして「繰り返し」と入力します。 トリガーの一覧から、ロジック アプリ ワークフロー内の最初の手順として次のトリガーを選択します。**定期的なアイテム**

   !["繰り返し" トリガーを選択する](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. 繰り返しの間隔と頻度を設定します。 この例では、これらのプロパティを設定して、ワークフローを毎週実行します。

   ![間隔と頻度の設定](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | プロパティ | 必須 | JSON での名前 | 種類 | 説明 |
   |----------|----------|-----------|------|-------------|
   | **間隔** | はい | interval | 整数 | ワークフローの実行間隔を、[頻度] に指定された単位に基づいて表す正の整数。 間隔の最小値と最大値は次のとおりです。 <p>- Month: 1 から 16 か月 </br>- Day: 1 から 500 日 </br>- Hour: 1 から 12,000 時間 </br>- Minute: 1 から 72,000 分 </br>- Second: 1 ～ 9,999,999 秒<p>たとえば間隔が 6 で、頻度が "月" である場合は、繰り返しは 6 か月ごとになります。 |
   | **頻度** | はい | frequency | string | 繰り返しの時間の単位: **[秒]** 、 **[分]** 、 **[時間]** 、 **[日]** 、 **[週]** 、または **[月]** |
   ||||||

   その他のスケジュール設定オプションについては、 **[新しいパラメーターの追加]** 一覧を開きます。 
   選択したオプションはすべて、選択後にトリガーに表示されます。

   ![詳細スケジュール オプション](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | プロパティ | 必須 | JSON での名前 | 種類 | 説明 |
   |----------|----------|-----------|------|-------------|
   | **タイム ゾーン** | いいえ | timeZone | string | 開始時刻を指定したときに限り適用されます。このトリガーに [UTC オフセット](https://en.wikipedia.org/wiki/UTC_offset)を指定することはできないためです。 適用するタイム ゾーンを選択してください。 |
   | **開始時刻** | いいえ | startTime | string | 開始日時を次の形式で指定します。 <p>YYYY-MM-DDThh:mm:ss (タイム ゾーンを選択した場合) <p>または <p>YYYY-MM-DDThh:mm:ssZ (タイム ゾーンを選択しなかった場合) <p>たとえば、2017 年 9 月 18 日午後 2:00 にする場合は、"2017-09-18T14:00:00" と指定し、太平洋標準時などのタイム ゾーンを選択します。 または、タイム ゾーンなしで「2017-09-18T14:00:00Z」と指定します。 <p>**注:** この開始時刻には、最大で 49 年先の時刻を指定できます。また、[UTC の日付と時刻の形式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) (ただし、[UTC オフセット](https://en.wikipedia.org/wiki/UTC_offset)を除く) で[日付と時刻に関する ISO 8601 規格](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)に従っている必要があります。 タイム ゾーンを選択しない場合は、末尾にスペースを入れず、アルファベットの "Z" を追加してください。 この "Z" は、同等の[航海時間](https://en.wikipedia.org/wiki/Nautical_time)を表します。 <p>単純なスケジュールでは、開始時刻と最初の実行時刻が一致するのに対して、複雑なスケジュールでは、トリガーが作動するのは開始時刻以降となります。 "[*開始日時の使用方法を具体的に教えてください*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time)" |
   | **設定曜日** | いいえ | weekDays | 文字列または文字列配列 | [週] を選択した場合は、ワークフローを実行するときに **[月曜日]** 、 **[火曜日]** 、 **[水曜日]** 、 **[木曜日]** 、 **[金曜日]** 、 **[土曜日]** 、および **[日曜日]** のうちの 1 日以上を選択できます。 |
   | **設定時刻 (時間)** | いいえ | hours | 整数または整数配列 | [日] または [週] を選択した場合は、ワークフローを実行する時刻として 0 ～ 23 の 1 つ以上の整数を選択できます。 <p><p>たとえば、[10]、[12]、[14] を指定した場合は、時刻として午前 10 時、午後 12 時、および午後 2 時が指定されますが、時刻の分は繰り返しの開始時刻に基づいて計算されます。 時刻の分を設定するには、 **[設定時刻 (分)]** プロパティの値を指定します。 |
   | **設定時刻 (分)** | いいえ | minutes | 整数または整数配列 | [日] または [週] を選択した場合、ワークフローを実行する時刻 (分) として 0 ～ 59 の整数を選択できます (複数選択可)。 <p>たとえば上の例で指定した時を使用し、分の要素に「30」を指定した場合、実行時刻は 10:30 AM、12:30 PM、2:30 PM となります。 |
   |||||

   たとえば、今日が 2017 年 9 月 4 日の月曜日だとします。 次の繰り返しトリガーは、開始日時である 2017 年 9 月 18 日 (月曜日) 午前 8:00 PST *より前*に起動されることはありません。 ただし、繰り返しのスケジュールは月曜日の 10:30 AM、12:30 PM、2:30 PM にのみ設定されています。 したがって、最初にトリガーが作動してロジック アプリのワークフロー インスタンスが作成される時刻は 10:30 AM となります。 開始時刻の動作について詳しくは、[開始時刻の例](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time)を参照してください。

   その後は、同じ日の 12:30 PM と 2:30 PM に実行されます。 繰り返しのたびに、ワークフロー インスタンスが新しく作成されます。 その後は、次の月曜日に再び、スケジュール全体が最初から繰り返されます。 "[*その他の繰り返しの例*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)"

   ![詳細スケジュールの例](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > 指定した繰り返しのプレビューが表示されるのは、トリガーの頻度として [日] または [週] を選択したときだけです。

1. 次に、他のアクションを使用して残りのワークフローを構築します。 追加できるその他のアクションについては、「[Azure Logic Apps のコネクタ](../connectors/apis-list.md)」を参照してください。

## <a name="workflow-definition---recurrence"></a>ワークフロー定義 - 繰り返し

ロジック アプリの基になるワークフロー定義 (JSON を使用します) では、選択したオプションを使用して[繰り返しトリガーの定義](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)を表示できます。 この定義を表示するには、デザイナーのツールバーで **[コード ビュー]** を選択します。 デザイナーに戻るには、デザイナーのツールバーで **[デザイナー]** を選択します。

この例は、繰り返しトリガーの定義が基になるワークフロー定義でどのように表示されるかを示しています。

``` json
"triggers": {
   "Recurrence": {
      "type": "Recurrence",
      "recurrence": {
         "frequency": "Week",
         "interval": 1,
         "schedule": {
            "hours": [
               10,
               12,
               14
            ],
            "minutes": [
               30
            ],
            "weekDays": [
               "Monday"
            ]
         },
         "startTime": "2017-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

## <a name="next-steps"></a>次の手順

* [遅延アクションを使用してワークフローを一時停止する](../connectors/connectors-native-delay.md)
* [Logic Apps のコネクタ](../connectors/apis-list.md)
