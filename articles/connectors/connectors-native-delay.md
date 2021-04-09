---
title: ワークフロー内の次のアクションの遅延
description: Azure Logic Apps で [遅延] または [延期期限] アクションを使用して、ロジック アプリ ワークフロー内の次のアクションの実行を待ちます。
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
tags: connectors
ms.openlocfilehash: 5348ade1ba6eec6cbd360849411b4520cb3c2b19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "74787338"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>Azure Logic Apps で次のアクションの実行を遅延させる

次のアクションを実行する前にロジック アプリを一定の時間だけ待たせるには、ロジック アプリのワークフロー内のアクションの前に組み込みの **[Delay - Schedule] (遅延 - スケジュール)** アクションを追加できます。 または、次のアクションを実行する前に特定の日付と時刻まで待つために、組み込みの **[Delay until - Schedule] (延期期限 - スケジュール)** アクションを追加できます。 アクションとトリガーの組み込みのスケジュール設定の詳細については、[Azure Logic Apps での自動化された繰り返しタスクおよびワークフローのスケジュール設定と実行](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)に関するページを参照してください。

* **[遅延]** : 次のアクションの実行の前に、指定された時間単位数 (秒、分、時間、日、週、月など) だけ待ちます。

* **[延期期限]** : 次のアクションの実行の前に、指定された日付と時刻まで待ちます。

これらのアクションを使用するためのいくつかの方法の例を次に示します。

* 電子メールで状態の更新情報を送信するのを平日まで待つ。

* 再開してデータを取得する前に、HTTP 呼び出しが完了するまでワークフローを遅延させます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)できます。

* [ロジック アプリ](../logic-apps/logic-apps-overview.md)に関する基本的な知識。 アクションを使用するには、最初にロジック アプリをトリガーで開始しておく必要があります。 遅延アクションを追加する前に、必要な任意のトリガーを使用し、他のアクションを追加できます。 このトピックでは、Office 365 Outlook トリガーを使用します。 ロジック アプリを初めて使用する場合は、[初めてのロジック アプリを作成する方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)を学習してください。

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>遅延アクションを追加する

1. ロジック アプリ デザイナーの、遅延アクションを追加するステップで **[新しいステップ]** を選択します。

   ステップの間に遅延アクションを追加するには、それらのステップを接続している矢印の上にポインターを移動します。 表示されるプラス記号 (+) を選択してから、 **[アクションの追加]** を選択します。

1. 検索ボックスに、フィルターとして「遅延」と入力します。 アクションの一覧から、次のアクションを選択します。 **[遅延]**

   ![[遅延] アクションを追加する](./media/connectors-native-delay/add-delay-action.png)

1. 次のアクションの実行の前に待つ時間を指定します。

   ![遅延の時間を設定する](./media/connectors-native-delay/delay-time-intervals.png)

   | プロパティ | JSON での名前 | 必須 | Type | 説明 |
   |----------|-----------|----------|------|-------------|
   | Count | count | はい | Integer | 遅延する時間の単位数 |
   | ユニット | unit | はい | String | 時間の単位 (`Second`、`Minute`、`Hour`、`Day`、`Week`、`Month` など) |
   ||||||

1. ワークフローで実行する他のすべてのアクションを追加します。

1. 完了したら、ロジック アプリを保存します。

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>延期期限アクションを追加する

1. ロジック アプリ デザイナーの、遅延アクションを追加するステップで **[新しいステップ]** を選択します。

   ステップの間に遅延アクションを追加するには、それらのステップを接続している矢印の上にポインターを移動します。 表示されるプラス記号 (+) を選択してから、 **[アクションの追加]** を選択します。

1. 検索ボックスに、フィルターとして「遅延」と入力します。 アクションの一覧から、次のアクションを選択します。 **[延期期限]**

   ![「延期期限」アクションの追加](./media/connectors-native-delay/add-delay-until-action.png)

1. ワークフローを再開するための終了の日付と時刻を指定します。

   ![遅延を終了するタイムスタンプを指定する](./media/connectors-native-delay/delay-until-timestamp.png)

   | プロパティ | JSON での名前 | 必須 | Type | 説明 |
   |----------|-----------|----------|------|-------------|
   | Timestamp | timestamp | はい | String | ワークフローを再開するための終了の日付と時刻であり、 <p>YYYY-MM-DDThh:mm:ssZ の形式を使用 <p>たとえば、2017 年 9 月 18 日午後 2:00 にする場合は、"2017-09-18T14:00:00Z" と指定します。 <p>**注:** この時間の形式は、[UTC の日付と時刻の形式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) (ただし、[UTC オフセット](https://en.wikipedia.org/wiki/UTC_offset)を除く) で [日付と時刻に関する ISO 8601 規格](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)に従っている必要があります。 タイム ゾーンがない場合は、最後にスペースなしで文字 "Z" を追加する必要があります。 この "Z" は、同等の[航海時間](https://en.wikipedia.org/wiki/Nautical_time)を表します。 |
   ||||||

1. ワークフローで実行する他のすべてのアクションを追加します。

1. 完了したら、ロジック アプリを保存します。

## <a name="next-steps"></a>次のステップ

* [繰り返しトリガーを使用して繰り返しタスクおよびワークフローを作成、スケジュール設定、および実行する](../connectors/connectors-native-recurrence.md)
* [Logic Apps のコネクタ](../connectors/apis-list.md)