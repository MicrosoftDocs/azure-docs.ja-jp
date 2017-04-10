---
title: "Stream Analytics のストリーミング ジョブを開始する方法 | Microsoft Docs"
description: "Azure Stream Analytics のストリーミング ジョブを実行する方法 | ラーニング パス セグメント"
keywords: "ストリーミング ジョブ"
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 9d46950f-2b69-49ce-a567-df558c5dd820
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 903f5bc3ea4bfd4087fecb030703d31fc42582ae
ms.lasthandoff: 11/17/2016


---
# <a name="how-to-run-a-streaming-job-in-azure-stream-analytics"></a>Azure Stream Analytics でストリーミング ジョブを実行する方法
ジョブの入力、クエリ、および出力をすべて指定したら、Stream Analytics ジョブを開始できます。

ジョブを開始するには:

1. Azure クラシック ポータルのジョブ ダッシュボードから、ページの下部にある **[開始]** をクリックします。
   
   ![[ジョブの開始] ボタン](./media/stream-analytics-run-a-job/1-stream-analytics-run-a-job.png)  
   
   Azure Portal で、ジョブ ページの上部にある **[開始]** をクリックします。
   
   ![Azure portal Start job Button](./media/stream-analytics-run-a-job/4-stream-analytics-run-a-job.png)  
2. このジョブが出力の生成をいつ開始するかを決定する **[出力の開始]** 値を指定します。 過去に開始されたことがないジョブの既定の設定は **[ジョブの開始時刻]** です。ここでは、ジョブはデータの処理を直ちに開始します。 **[カスタム]** を使用して、過去の時刻 (履歴データを使用します) または未来の時刻 (その時刻まで処理を遅らせます) を指定することもできます。 ジョブがこれまでに開始されて停止されている場合は、最後の出力時刻からジョブを再開してデータの損失を回避するための **[最後に停止した時刻]** オプションを使用できます。  
   
   ![開始ストリーミング ジョブ時刻](./media/stream-analytics-run-a-job/2-stream-analytics-run-a-job.png)  
   
   ![Azure portal Start streaming job Time](./media/stream-analytics-run-a-job/5-stream-analytics-run-a-job.png)  
3. 選択内容を確認します。 ジョブが開始されると、ジョブの状態が *[開始中]* に変わり、まもなく *[実行中]* に移行します。 **Notification Hub** で**開始**操作の進行状況を監視できます。
   
   ![ストリーミング ジョブの進捗状況](./media/stream-analytics-run-a-job/3-stream-analytics-run-a-job.png)  
   
   ![Azure portal streaming job progress](./media/stream-analytics-run-a-job/6-stream-analytics-run-a-job.png)  

## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)


