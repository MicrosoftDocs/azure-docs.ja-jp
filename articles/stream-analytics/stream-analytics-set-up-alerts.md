---
title: "Stream Analytics のクエリのアラートを設定する | Microsoft Docs"
description: "Stream Analytics のアラートについて理解する"
keywords: "アラートを設定する"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 9952e2cf-b335-4a5c-8f45-8d3e1eda2e20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: c8463e68600edefecd0f3bdd19c13b13f676b82b
ms.lasthandoff: 11/17/2016


---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Azure Stream Analytics ジョブのアラートを設定する
## <a name="introduction-monitor-page"></a>概要: 監視ページ
メトリックが指定した条件に達したときにアラートをトリガーするルールを設定できます。

例: "過去 15 分間の出力イベントが 100 未満の場合は電子メール ID: xyz@company.com に電子メール通知を送信する"。

ルールは、ポータルでメトリックに対して設定することも、操作ログのデータに対して [プログラムによって](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) 構成することもできます。

## <a name="set-up-alerts-through-the-azure-classic-portal"></a>Azure クラシック ポータルを使用してアラートを設定する
Azure クラシック ポータルでアラートを設定する方法は 2 つあります。  

1. Stream Analytics ジョブの **[監視]** タブ  
2. Management Services の操作ログ  

## <a name="set-up-alert-through-the-monitor-tab-of-the-job-in-the-portal"></a>ポータルでジョブの [監視] タブを使用してアラートを設定する
1. [監視] タブでメトリックを選択し、ダッシュボードの下部にある **[ルールの追加]** をクリックし、ルールを設定します。  
   
   ![ダッシュボード](./media/stream-analytics-set-up-alerts/01-stream-analytics-set-up-alerts.png)  
2. アラートの名前と説明を定義します。  
   
   ![ルールの作成](./media/stream-analytics-set-up-alerts/02-stream-analytics-set-up-alerts.png)  
3. しきい値、アラート評価ウィンドウ、およびアラートのアクションを入力します。  
   
   ![条件の定義](./media/stream-analytics-set-up-alerts/03-stream-analytics-set-up-alerts.png)  

## <a name="set-up-alerts-through-the-operations-logs"></a>操作ログを使用してアラートを設定する
1. **Azure クラシック ポータル** で、Management Services の [[アラート]](https://manage.windowsazure.com)タブに移動します。  
2.  **[ルールの追加]**  
   
   ![条件](./media/stream-analytics-set-up-alerts/04-stream-analytics-set-up-alerts.png)  
3. アラートの名前と説明を定義します。 [サービスの種類] として [Stream Analytics] を選択し、[サービス名] としてジョブの名前を選択します。  
   
   ![アラートの定義](./media/stream-analytics-set-up-alerts/05-stream-analytics-set-up-alerts.png)  

## <a name="set-up-alerts-in-the-azure-portal"></a>Azure Portal でアラートを設定する
Azure Portal で、アラートを有効にする Stream Analytics ジョブを参照し、 **[監視]** セクションをクリックします。  表示された **[メトリック]** ブレードで、**[アラートの追加]** コマンドをクリックします。

  ![Azure portal setup](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

アラート ルールに名前を付け、通知メールに表示される説明を選択できます。

[メトリック] を選択する場合は、メトリックの条件としきい値を選択します。

  ![Azure portal select metric](./media/stream-analytics-set-up-alerts/07-stream-analytics-set-up-alerts.png)  

Azure Portal でのアラートの構成の詳細については、「[アラート通知の受信](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)」を参照してください。  

## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)


