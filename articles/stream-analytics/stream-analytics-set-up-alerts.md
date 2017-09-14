---
title: "Stream Analytics のクエリのアラートを設定する | Microsoft Docs"
description: "Stream Analytics のアラートについて理解する"
keywords: "アラートを設定する"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 9952e2cf-b335-4a5c-8f45-8d3e1eda2e20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/26/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: cdbd7da36f3eb2795fc4548915ebbf18200ca44d
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Azure Stream Analytics ジョブのアラートを設定する
## <a name="introduction-monitor-page"></a>概要: 監視ページ
メトリックが指定した条件に達したときにアラートをトリガーするルールを設定できます。 たとえば、次のような条件でアラートを設定できます。

`If there are zero input events in the last 5 minutes, send email notification to sa-admin@example.com`

ルールは、ポータルでメトリックに対して設定することも、操作ログのデータに対して [プログラムによって](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) 構成することもできます。

## <a name="set-up-alerts-in-the-azure-portal"></a>Azure Portal でアラートを設定する
1. Azure Portal で、アラートを作成する Stream Analytics ジョブを開きます。 

2. **[ジョブ]** ブレードで **[監視]** セクションをクリックします。  

3. **[メトリック]** ブレードで、**[アラートの追加]** コマンドをクリックします。

      ![Azure portal setup](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

4. 名前と説明を入力します。

5. セレクターを使用して、アラートを送信する条件を定義します。

6. アラートの送信先の情報を入力します。

      ![Azure Streaming Analytics ジョブのアラートの設定](./media/stream-analytics-set-up-alerts/stream-analytics-add-alert.png)  

Azure Portal でのアラートの構成の詳細については、「[アラート通知の受信](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)」を参照してください。  


## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)


