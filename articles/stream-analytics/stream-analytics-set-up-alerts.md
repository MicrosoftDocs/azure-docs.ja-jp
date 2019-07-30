---
title: Azure Stream Analytics ジョブの監視アラートを設定する
description: この記事では、Azure Portal を使って Azure Stream Analytics ジョブの監視とアラートを設定する方法について説明します。
services: stream-analytics
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 0fd489d856a16953a5a450a347c9737fe440ad28
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621770"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Azure Stream Analytics ジョブのアラートを設定する

Azure Stream Analytics ジョブが問題を生じることなく常に実行している状態を確保するためには、ジョブを監視することが大切です。 この記事では、監視が必要とされる一般的なシナリオについてのアラートの設定方法について説明します。 

ポータル経由で Operation Logs データからのメトリックに関するルールを定義できます。[プログラムで](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a)定義することもできます。

## <a name="set-up-alerts-in-the-azure-portal"></a>Azure Portal でアラートを設定する
### <a name="get-alerted-when-a-job-stops-unexpectedly"></a>ジョブが予期せず停止したときにアラートを受け取る

次の例では、ジョブがエラー状態になったときのアラートを設定する方法を説明しています。 このアラートは、あらゆるジョブに推奨されます。

1. Azure Portal で、アラートを作成する Stream Analytics ジョブを開きます。

2. **[ジョブ]** ページで、 **[監視]** セクションに移動します。  

3. **[メトリック]** を選択し、 **[新しいアラート ルール]** を選択します。

   ![Azure portal での Stream Analytics のアラートの設定](./media/stream-analytics-set-up-alerts/stream-analytics-set-up-alerts.png)  

4. 該当する Stream Analytics ジョブ名が **[リソース]** に自動的に表示されます。 **[条件の追加]** をクリックし、 **[シグナル ロジックの構成]** の **[All Administrative operations]\(すべての管理操作\)** を選択します。

   ![Stream Analytics アラートのシグナル名を選択する](./media/stream-analytics-set-up-alerts/stream-analytics-condition-signal.png)  

5. **[シグナル ロジックの構成]** で、 **[イベントのレベル]** を **[すべて]** に変更し、 **[状態]** を **[失敗]** に変更します。 **[イベント開始者]** は空欄のままで、 **[完了]** を選択します。

   ![Stream Analytics アラートのシグナル ロジックの構成](./media/stream-analytics-set-up-alerts/stream-analytics-configure-signal-logic.png) 

6. 既存のアクション グループを選択するか、新しいグループを作成します。 この例では、**TIDashboardGroupActions** というアクション グループを新たに作成し、Azure Resource Manager の**所有者**ロールを有するユーザーにメールを送信する **[電子メール]** アクションを設定しています。

   ![Azure Streaming Analytics ジョブのアラートの設定](./media/stream-analytics-set-up-alerts/stream-analytics-add-group-email-action.png)

7. **[リソース]** 、 **[条件]** 、 **[アクション グループ]** がすべて入力されている必要があります。 起動するアラートの順に、定義されている条件が満たされる必要がある点に注意してください。 たとえば、直近 15 分間のメトリックの平均値を 5 分ごとに測定できます。

   ![Stream Analytics のアラート ルールを作成する](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule-2.png)

   **[アラートの詳細]** に **[アラート ルール名]** 、 **[説明]** 、 **[リソース グループ]** を追加し、 **[アラート ルールの作成]** をクリックして、Stream Analytics ジョブ用のルールを作成します。

   ![Stream Analytics のアラート ルールを作成する](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule.png)
   
## <a name="scenarios-to-monitor"></a>監視するシナリオ

Stream Analytics ジョブのパフォーマンス監視には、以下のアラートが推奨されます。 これらのメトリックを、1 分間隔で直近 5 分間にわたって評価することをお勧めします。

|メトリック|条件|時間の集計|Threshold|是正措置|
|-|-|-|-|-|
|SU 使用率 (%)|より大きい|最大値|80|SU 使用率 (%) が増加する要因は複数あります。 クエリの並列処理を使用してスケーリングするか、ストリーミング ユニット数を増やすことができます。 詳細については、「[Azure Stream Analytics でのクエリの並列処理の活用](stream-analytics-parallelization.md)」を参照してください。|
|実行時エラー|より大きい|合計|0|アクティビティ ログまたは診断ログを観察して、入力、クエリ、出力を適宜変更してください。|
|透かしの遅延|より大きい|最大値|このメトリックの直近 15 分間の平均値が到着遅延許容期間 (秒単位) を超えたとき。 到着遅延許容期間を変更していない場合の既定値は 5 秒に設定されています。|SU 数を増やすか、クエリの並列処理を試してみてください。 SU の詳細については、「[ストリーミング ユニットの理解と調整](stream-analytics-streaming-unit-consumption.md#how-many-sus-are-required-for-a-job)」を参照してください。 クエリの並列処理の詳細については、「[Azure Stream Analytics でのクエリの並列処理の活用](stream-analytics-parallelization.md)」を参照してください。|
|逆シリアル化の入力エラー|より大きい|合計|0|アクティビティ ログまたは診断ログを観察して、入力を適宜変更してください。 診断ログの詳細については、[診断ログを使用した Azure Stream Analytics のトラブルシューティング](stream-analytics-job-diagnostic-logs.md)に関するページを参照してください。|

## <a name="get-help"></a>問い合わせ

Azure Portal でのアラートの構成の詳細については、「[アラート通知の受信](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)」を参照してください。  

さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

