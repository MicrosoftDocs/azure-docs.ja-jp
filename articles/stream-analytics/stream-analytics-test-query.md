---
title: "Azure Stream Analytics クエリのテスト | Microsoft Docs"
description: "Stream Analytics ジョブでクエリをテストする方法"
keywords: "クエリ テスト, クエリのトラブルシューティング"
documentation center: 
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
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 5e7bab0b0c3222ba093a93dc2d15f1e41898e62c
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---
# <a name="test-azure-stream-analytics-queries-in-the-azure-portal"></a>Azure Portal で Azure Stream Analytics クエリをテストする

Azure Stream Analytics では、ジョブを開始したり停止したりすることなく、Azure Portal でクエリをテストできます。

## <a name="test-the-input"></a>入力をテストする

1. サンプル入力データでテストするには、入力のいずれかを右クリックし、**[ファイルからサンプル データをアップロードする]** を選択します。

    ![Stream Analytics クエリ エディターでクエリをテストする](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

2. アップロードが完了したら、**[テスト]** をクリックして、用意したサンプル データでこのクエリをテストします。

    ![Stream Analytics クエリ エディターでサンプル データをテストする](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

クエリの出力はブラウザーに表示されます。後で使用するためにテストの出力を保存する場合は、結果のダウンロードのためのリンクを利用できます。 これで、クエリを簡単に何度も編集してテストを繰り返し行い、出力がどのように変わるかを確認することができます。

![Stream Analytics クエリ エディターのサンプル出力](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

クエリで複数の出力を使用している場合には、両方の出力結果を個別に確認できるほか、それらを簡単に切り替えることができます。

ブラウザーに表示された結果に問題がなければ、クエリを保存し、ジョブを開始して、エラーを発生させずにイベントを処理できます。

## <a name="get-help"></a>問い合わせ

さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

