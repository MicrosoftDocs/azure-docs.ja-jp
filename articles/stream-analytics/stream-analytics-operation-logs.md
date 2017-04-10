---
title: "Stream Analytics の操作ログとサービス ログを使用したデバッグ | Microsoft Docs"
description: "Stream Analytics 操作ログの使用方法"
keywords: "サービス ログ"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: a2ed9676-f0bd-4398-87c8-a592779ac728
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e694eb8f4d658ff9410ae7be2bd4b69500ecbf20
ms.lasthandoff: 11/17/2016


---
# <a name="debug-stream-analytics-jobs-using-service-and-operation-logs"></a>サービスと操作ログを使用した Stream Analytics ジョブのデバッグ
すべての Azure サービスでは、管理操作に関連する詳細を記録するために操作ログ メッセージがユーザーに提供されます。 Azure Stream Analytics では、ジョブの状態、ジョブの進行状況、エラー メッセージを表示して入力から、処理、出力までのジョブの進行を追跡するなど、この情報をデバッグ目的に使用できます。

## <a name="find-operation-logs-in-the-azure-management-portal"></a>Azure 管理ポータルでの操作ログの検出
操作ログには 2 つの方法でアクセスできます。  

* Stream Analytics ジョブのダッシュボード  
* Azure クラシック ポータルの管理サービス  

## <a name="dashboard-of-the-stream-analytics-job"></a>Stream Analytics ジョブのダッシュボード
Stream Analytics ジョブの対応するログへのリンクが、ジョブの [ダッシュボード] タブに表示されます。 そのリンクをクリックすると、その特定のジョブの最新のログが表示されるようにフィルターが設定されます。

  ![管理サービス ログの選択](./media/stream-analytics-operation-logs/01-stream-analytics-operation-logs.png)  

## <a name="management-services"></a>管理サービス
Azure クラシック ポータルで Stream Analytics および他のサービスの操作ログに手動で移動するには、次の手順に従います。

1. **Azure クラシック ポータル** で [[管理サービス]](https://manage.windowsazure.com)をクリックします。
2. **[種類]** で **[Stream Analytics]** を選択し、**[サービス名]** でジョブの名前を選択します。  
   
   ![Stream Analytics の選択](./media/stream-analytics-operation-logs/02-stream-analytics-operation-logs.png)  

## <a name="find-audit-logs-in-the-azure-portal"></a>Azure Portal での監査ログの検出
Azure Portal で Stream Analytics ジョブの操作ログを見つけるには、**[参照]** をクリックし、**[監査ログ]** を選択します。

  ![Azure portal Select Stream Analytics](./media/stream-analytics-operation-logs/06-stream-analytics-operation-logs.png)  

これにより、サブスクリプション内のすべてのリソースに関して、過去 7 日間のイベントが表示されたブレードが開かれます。  **[フィルター]** コマンドをクリックして、特定のタイプまたは時間枠のイベントが表示されるようにフィルタリングできます。

  ![Azure portal Select Stream Analytics](./media/stream-analytics-operation-logs/07-stream-analytics-operation-logs.png)  

## <a name="get-log-details"></a>ログ詳細の取得
時間範囲と状態でフィルター処理して、ジョブのログを表示できます。

Microsoft Azure 管理ポータルで、選択したイベントについての詳細を表示するには、ウィンドウの下部にある **[詳細]** ボタンをクリックします。 

  ![詳細の選択](./media/stream-analytics-operation-logs/03-stream-analytics-operation-logs.png)  

Azure Portal で、ログ エントリをクリックするとその中に詳細なイベントが表示されます。

  ![Azure portal Select Details](./media/stream-analytics-operation-logs/08-stream-analytics-operation-logs.png)  

そこから、イベントをクリックして **[詳細]** ブレードを開くことができます。

  ![Azure portal Select Details](./media/stream-analytics-operation-logs/09-stream-analytics-operation-logs.png)  

## <a name="debug-a-failed-job"></a>失敗したジョブのデバッグ
Microsoft Azure 管理ポータルで [検索] アイコンをクリックし、「failed」と入力します。 エラーが発生したすべてのログの結果が表示されます。 

  ![失敗したジョブのデバッグ](./media/stream-analytics-operation-logs/04-stream-analytics-operation-logs.png)  

Azure Portal で、メッセージのレベルでフィルタリングして **[重大]** イベントを表示できます。

  ![Azure portal debug](./media/stream-analytics-operation-logs/10-stream-analytics-operation-logs.png)  

障害のいずれかを選択して **[詳細]** をクリックし、エラーの詳細を確認できます。  一部のエラー メッセージでは、問題を軽減する方法についての情報も提供されます。 

  ![操作の詳細](./media/stream-analytics-operation-logs/05-stream-analytics-operation-logs.png)  

[MSDN フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)で[サポート](https://azure.microsoft.com/support/options/)に問い合わせたり、チームに情報を提供したりする必要がある場合は、操作の詳細、特に**相関 ID** をメモしておいてください。 

## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)


