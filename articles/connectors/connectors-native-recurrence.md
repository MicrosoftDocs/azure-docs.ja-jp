---
title: 定期的なタスクとワークフローをスケジュール設定する
description: Azure Logic Apps で繰り返しトリガーを使用して自動化された繰り返しタスクおよびワークフローをスケジュール設定して実行する
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: 3749a7080bf17c020b48ae3ebc3cff3aa998eeef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100382295"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Azure Logic Apps で繰り返しトリガーを使用して繰り返しタスクおよびワークフローを作成、スケジュール設定、および実行する

特定のスケジュールでタスク、プロセス、またはジョブを定期的に実行するには、Azure Logic Apps でネイティブに実行される組み込みの **繰り返し** トリガーを使用してロジック アプリ ワークフローを開始できます。 ワークフローを開始するための日付と時刻やタイム ゾーン、およびそのワークフローを反復処理するための繰り返しを設定できます。 中断やワークフローの無効化などの何らかの理由で、実行されない繰り返しがトリガーで発生した場合、実行されなかった繰り返しはこのトリガーによって処理されず、スケジュールされている次の間隔で繰り返しが再開されます。 組み込みのスケジュール トリガーとアクションの詳細については、[Azure Logic Apps を使用した定期的な自動タスク、ワークフローのスケジュールと実行](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)に関するページを参照してください。

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

* Azure サブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* [ロジック アプリ](../logic-apps/logic-apps-overview.md)に関する基本的な知識。 ロジック アプリを初めて使用する場合は、[初めてのロジック アプリを作成する方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)を学習してください。

## <a name="add-the-recurrence-trigger"></a>繰り返しトリガーを追加する

1. [Azure portal](https://portal.azure.com) にサインインします。 空のロジック アプリを作成します。

1. ロジック アプリ デザイナーが表示されたら、検索ボックスに、フィルターとして「`recurrence`」と入力します。 トリガーの一覧から、ロジック アプリ ワークフロー内の最初の手順として次のトリガーを選択します。**定期的なアイテム**

   !["繰り返し" トリガーを選択する](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. 繰り返しの間隔と頻度を設定します。 この例では、これらのプロパティを設定して、ワークフローを毎週実行します。

   ![間隔と頻度の設定](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | プロパティ | JSON での名前 | 必須 | Type | 説明 |
   |----------|-----------|----------|------|-------------|
   | **間隔** | `interval` | はい | Integer | ワークフローの実行間隔を、[頻度] に指定された単位に基づいて表す正の整数。 間隔の最小値と最大値は次のとおりです。 <p>- Month: 1 から 16 か月 <br>- Week:1 から 71 週 <br>- Day: 1 から 500 日 <br>- Hour: 1 から 12,000 時間 <br>- Minute: 1 から 72,000 分 <br>- Second: 1 から 9,999,999 秒<p>たとえば間隔が 6 で、頻度が "月" である場合は、繰り返しは 6 か月ごとになります。 |
   | **頻度** | `frequency` | はい | String | 繰り返しの時間の単位: **[秒]** 、 **[分]** 、 **[時間]** 、 **[日]** 、 **[週]** 、または **[月]** |
   ||||||

   > [!IMPORTANT]
   > 繰り返しに特定の[開始日時](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time)が指定されていない場合は、トリガーの繰り返し設定にかかわらず、ロジック アプリを保存またはデプロイすると、直ちに最初の繰り返しが実行されます。 この動作を回避するには、最初の繰り返しを実行する開始日時を指定してください。
   >
   > 将来の繰り返しを実行する特定時刻など、その他の詳細なスケジュール オプションが繰り返しに指定されていない場合、それらの繰り返しは前回の実行時刻に基づいて行われます。 その結果、ストレージ呼び出し中の待機時間などの要因によって、それらの繰り返しの開始時刻がずれる可能性があります。 
   > ロジック アプリで繰り返しが行われないことがないようにするには (特に頻度が日単位以上の場合)、次のいずれかのオプションを試してください。
   > 
   > * **[設定時刻 (時間)]** と **[設定時刻 (分)]** という名前のプロパティ ( **[日]** と **[週]** の頻度でのみ使用可能) を使用して、繰り返しの開始日時と後続の繰り返しを実行する特定の時刻を指定します。
   > 
   > * 繰り返しトリガーではなく、[スライディング ウィンドウ トリガー](../connectors/connectors-native-sliding-window.md)を使用します。

1. 詳細スケジュール オプションを設定するには、 **[新しいパラメーターの追加]** 一覧を開きます。 選択したオプションはすべて、選択後にトリガーに表示されます。

   ![詳細スケジュール オプション](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | プロパティ | JSON での名前 | 必須 | Type | 説明 |
   |----------|-----------|----------|------|-------------|
   | **タイム ゾーン** | `timeZone` | いいえ | String | 開始時刻を指定したときに限り適用されます。このトリガーに [UTC オフセット](https://en.wikipedia.org/wiki/UTC_offset)を指定することはできないためです。 適用するタイム ゾーンを選択してください。 |
   | **[開始時刻]** | `startTime` | いいえ | String | 開始日と時刻を指定します。これには、最大で 49 年先の時刻を指定でき、また [UTC の日付と時刻の形式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) (ただし、[UTC オフセット](https://en.wikipedia.org/wiki/UTC_offset)を除く) で[日付と時刻に関する ISO 8601 規格](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)に従っている必要があります。 <p><p>YYYY-MM-DDThh:mm:ss (タイム ゾーンを選択した場合) <p>または <p>YYYY-MM-DDThh:mm:ssZ (タイム ゾーンを選択しなかった場合) <p>たとえば、2020 年 9 月 18 日午後 2:00 にする場合は、"2020-09-18T14:00:00" と指定し、太平洋標準時などのタイム ゾーンを選択します。 または、タイム ゾーンなしで「2020-09-18T14:00:00Z」と指定します。 <p><p>**重要:** タイム ゾーンを選択しない場合は、末尾にスペースを入れず、アルファベットの "Z" を追加してください。 この "Z" は、同等の[航海時間](https://en.wikipedia.org/wiki/Nautical_time)を表します。 タイム ゾーンの値を選択した場合は、**開始時刻** の値の末尾に "Z" を追加する必要はありません。 行った場合、"Z" は UTC 時刻形式を示すため、Logic Apps ではタイム ゾーンの値が無視されます。 <p><p>単純なスケジュールでは、開始時刻と最初の実行時刻が一致するのに対して、複雑なスケジュールでは、トリガーが作動するのは開始時刻以降となります。 "[*開始日時の使用方法を具体的に教えてください*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time)" |
   | **設定曜日** | `weekDays` | いいえ | 文字列または文字列配列 | [週] を選択した場合は、ワークフローを実行するときに **[月曜日]** 、 **[火曜日]** 、 **[水曜日]** 、 **[木曜日]** 、 **[金曜日]** 、 **[土曜日]** 、および **[日曜日]** のうちの 1 日以上を選択できます。 |
   | **設定時刻 (時間)** | `hours` | いいえ | 整数または整数配列 | [日] または [週] を選択した場合は、ワークフローを実行する時刻として 0 ～ 23 の 1 つ以上の整数を選択できます。 <p><p>たとえば、[10]、[12]、[14] を指定した場合は、時刻として午前 10 時、午後 12 時、および午後 2 時が指定されますが、時刻の分は繰り返しの開始時刻に基づいて計算されます。 特定の時刻 (たとえば、午前 10:00、午後 12:00、午後 2:00) を設定するには、 **[設定時刻 (分)]** という名前のプロパティを使用してこれらの値を指定します。 |
   | **設定時刻 (分)** | `minutes` | いいえ | 整数または整数配列 | [日] または [週] を選択した場合、ワークフローを実行する時刻 (分) として 0 ～ 59 の整数を選択できます (複数選択可)。 <p>たとえば上の例で指定した時を使用し、分の要素に「30」を指定した場合、実行時刻は 10:30 AM、12:30 PM、2:30 PM となります。 <p>**注**:トリガーされた実行のタイムスタンプが、スケジュールされた時刻から最大 1 分ずれることがあります。 後続のアクションに対し、タイムスタンプをスケジュールどおりに正確に渡す必要がある場合は、テンプレート式を使用してタイムスタンプを適宜変更してください。 詳細については、[式の日付と時刻の関数](../logic-apps/workflow-definition-language-functions-reference.md#date-time-functions)に関するページを参照してください。 |
   |||||

   たとえば、今日が 2020 年 9 月 4 日の金曜日だとします。 次の繰り返しトリガーは、開始日時である 2020 年 9 月 18 日 (金曜日) 午前 8:00 PST *より前* に起動されることはありません。 ただし、繰り返しのスケジュールは月曜日の 10:30 AM、12:30 PM、2:30 PM にのみ設定されています。 したがって、最初にトリガーが作動してロジック アプリのワークフロー インスタンスが作成される時刻は月曜日の 10:30 AM となります。 開始時刻の動作について詳しくは、[開始時刻の例](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time)を参照してください。

   その後は、同じ日の 12:30 PM と 2:30 PM に実行されます。 繰り返しのたびに、ワークフロー インスタンスが新しく作成されます。 その後は、次の月曜日に再び、スケジュール全体が最初から繰り返されます。 "[*その他の繰り返しの例*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)"

   ![詳細スケジュールの例](./media/connectors-native-recurrence/recurrence-trigger-advanced-schedule-options.png)

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
         "startTime": "2020-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

<a name="daylight-saving-standard-time"></a>

## <a name="trigger-recurrence-shift-between-daylight-saving-time-and-standard-time"></a>夏時間と標準時間の間でのトリガーの繰り返しシフト

組み込みトリガーの繰り返しでは、指定されたタイム ゾーンを含め、設定されたスケジュールが守られます。 タイム ゾーンを選択しないと、トリガーの実行時刻が夏時間 (DST) の影響を受ける可能性があります。たとえば、DST が開始されると開始時刻が 1 時間繰り上げられ、DST が終了すると 1 時間繰り下げられます。

このシフトを回避して、ロジック アプリが指定された開始時刻に実行されるようにするには、タイム ゾーンを必ず選択してください。 そうすることで、ロジック アプリの UTC 時刻も、季節の時間変化に合わせてシフトされます。 ただし、一部の時間枠では時間がシフトされるときに問題が発生することがあります。 詳細と例については、[夏時間と標準時間での繰り返し](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

* [遅延アクションを使用してワークフローを一時停止する](../connectors/connectors-native-delay.md)
* [Logic Apps のコネクタ](../connectors/apis-list.md)
