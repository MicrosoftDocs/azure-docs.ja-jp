---
title: "Azure Stream Analytics によるイベントの順序と遅延の処理 | Microsoft Docs"
description: "データ ストリーム中の順序を逸脱したイベントまたは遅延イベントが Azure Stream Analytics によって処理されるしくみを説明します"
keywords: "順序の逸脱, 遅延, イベント"
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: cf9a43fbb82a32c92d66f25809916d3ccde1a20d
ms.contentlocale: ja-jp
ms.lasthandoff: 09/14/2017

---
# <a name="azure-stream-analytics-event-order-handling"></a>Azure Stream Analytics によるイベントの順序の処理

イベントのテンポラル データ ストリームでは、各イベントにタイムスタンプが割り当てられます。 Azure Stream Analytics は、到着時刻またはアプリケーション時間を使用して、各イベントにタイムスタンプを割り当てます。 

**System.Timestamp** 列には、イベントに割り当てられたタイムスタンプが含まれます。 イベントがソースに到達すると、入力ソースに到着時刻が割り当てられます。 到着時刻は、イベント ハブ入力では **EventEnqueuedTime** になり、BLOB 入力では [BLOB の最終更新時刻](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3)になります。 アプリケーション時間は、イベントの生成時に割り当てられ、ペイロードの一部になります。 

アプリケーション時間でイベントを処理するには、SELECT クエリで TIMESTAMP BY 句を使用します。 TIMESTAMP BY 句がない場合は、イベントは到着時間で処理されます。 到着時間にアクセスするには、イベント ハブの場合は **EventEnqueuedTime** プロパティを、BLOB 入力の場合は **BlobLastModified** プロパティを使用します。 

Azure Stream Analytics では、出力はタイムスタンプ順に生成され、順序を逸脱したデータを扱うためのいくつかの設定が備わっています。

![Stream Analytics のイベントの処理](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

順序どおりでない入力ストリームは、次のいずれかに当てはまります。
* 並べ替えられた (そのため*遅延*した)。
* ユーザーが指定したポリシーに従って、システムによって調整された。

Stream Analytics では、アプリケーション時間で処理する場合、遅延と順序を逸脱したイベントは許容されます。

**到着遅延許容期間**

* 到着遅延許容期間の設定は、アプリケーション時間で処理する場合にのみ実行できます。 それ以外の場合は無視されます。
* 到着遅延許容期間は、到着時刻とアプリケーション時間の差の最大値です。 アプリケーション時間が *(到着時刻 - 到着遅延)* よりも前の時刻である場合は、*(到着時刻 - 到着遅延)* に設定されます。
* 同じ入力ストリームの複数のパーティションまたは複数の入力ストリームが組み合わされる場合、すべてのパーティションが新しいデータを待機する最大時間が到着遅延許容期間になります。 

簡単に言うと、到着遅延は、イベントが生成されてから、入力ソース側でイベントが受信されるまでの最大遅延です。
到着遅延許容期間に基づく調整は最初に実行され、誤順序の許容期間に基づく調整は 2 番目に実行されます。 **System.Timestamp** 列には、イベントに割り当てられた最後のタイムスタンプが含まれます。

**誤順序の許容期間**

* 順序を逸脱しているものの、「誤順序の許容期間」内であるイベントは、*タイムスタンプに基づいて並べ替え*られます。 
* 許容範囲を超えたイベントは、*削除*または*調整*されます。
    * **調整**: 許容される最新の時刻に届いたかのように調整されます。 
    * **削除**: 破棄されます。

「誤順序の許容期間」内に届いたイベントを並べ替えるために、クエリの出力は*誤順序の許容期間*だけ遅延されます。

**例**

到着遅延許容期間 = 10 分<br/>
誤順序の許容期間 = 3 分<br/>
アプリケーション時間による処理<br/>

イベント:

イベント 1 _アプリケーション時間_ = 00:00:00、_到着時刻_ = 00:10:01、_System.Timestamp_ = 00:00:01、(_到着時刻_ - _アプリケーション時間_) は到着遅延許容期間を超えているため、調整されます。

イベント 2 _アプリケーション時間_ = 00:00:01、_到着時刻_ = 00:10:01、_System.Timestamp_ = 00:00:01、アプリケーション時間は到着遅延期間の範囲内であるため、調整されません。

イベント 3 _アプリケーション時間_ = 00:10:00、_到着時刻_ = 00:10:02、_System.Timestamp_ = 00:10:00、アプリケーション時間は到着遅延期間の範囲内であるため、調整されません。

イベント 4 _アプリケーション時間_ = 00:09:00、_到着時刻_ = 00:10:03、_System.Timestamp_ = 00:09:00、アプリケーション時間は到着遅延許容期間の範囲内であるため、元のタイムスタンプで承認されます。

イベント 5 _アプリケーション時間_ = 00:06:00、_到着時刻_ = 00:10:04、_System.Timestamp_ = 00:07:00、アプリケーション時間は誤順序の許容期間よりも古いため、調整されます。



## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、[Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics クエリ言語リファレンス](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics の管理 REST API リファレンス](https://msdn.microsoft.com/library/azure/dn835031.aspx)

