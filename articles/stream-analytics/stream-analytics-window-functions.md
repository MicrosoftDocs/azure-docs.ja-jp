---
title: "Stream Analytics ウィンドウ関数の概要 | Microsoft Docs"
description: "Stream Analytics の 3 つのウィンドウ関数 (タンブリング、ホッピング、スライディング) について説明します。"
keywords: "タンブリング ウィンドウ, スライディング ウィンドウ, ホッピング ウィンドウ"
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 0d8d8717-5d23-43f0-b475-af078ab4627d
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 561a3b3f9c90274cfff95ab27c6cd745582f15e0
ms.lasthandoff: 11/17/2016


---
# <a name="introduction-to-stream-analytics-window-functions"></a>Stream Analytics ウィンドウ関数の概要
リアルタイム ストリーミング シナリオでは、多くの場合、テンポラル ウィンドウに含まれるデータでのみ操作を実行する必要があります。 ウィンドウ化関数のネイティブ サポートは Azure Stream Analytics の主要機能で、複雑なストリーム処理のジョブで開発者の生産性に大きな変化をもたらします。 Stream Analytics により、開発者は、[**タンブリング**](https://msdn.microsoft.com/library/dn835055.aspx)、[**ホッピング**](https://msdn.microsoft.com/library/dn835041.aspx)、[**スライディング**](https://msdn.microsoft.com/library/dn835051.aspx) ウィンドウを使用して、ストリーミング データで一時的な操作を実行できます。 すべての [ウィンドウ](https://msdn.microsoft.com/library/dn835019.aspx) 操作が、ウィンドウ **終了** 時に結果を出力していることに注意してください。 ウィンドウの出力は、使用される集計関数に基づく単一のイベントになります。 イベントにはウィンドウ終了のタイム スタンプが提供され、すべてのウィンドウ関数が固定長で定義されています。 最後に、すべてのウィンドウ関数を [**GROUP BY**](https://msdn.microsoft.com/library/dn835023.aspx) 句で使用する必要があることにも注意してください。

![Stream Analytics ウィンドウ関数の概念](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>タンブリング ウィンドウ
タンブリング ウィンドウ関数は、次の例のように、データ ストリームを個別の時間セグメントに分割して、関数を実行するときに使用します。 タンブリング ウィンドウの主な差別化要素は、重複しないで繰り返される点です。1 つのイベントが複数のタンブリング ウィンドウに属することはできません。

![Stream Analytics ウィンドウ関数 (タンブリング) の概要](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>ホッピング ウィンドウ
ホッピング ウィンドウ関数は、一定の期間だけ前に進みます。 重複できるタンブリング ウィンドウと考えると簡単で、イベントは複数のホッピング ウィンドウ結果セットに属することができます。 ホッピング ウィンドウをタンブリング ウィンドウと同じにするには、ホップ サイズに、ウィンドウ サイズと同じ値を指定します。 

![Stream Analytics ウィンドウ関数 (ホッピング) の概要](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>スライディング ウィンドウ
タンブリング ウィンドウまたはホッピング ウィンドウとは異なり、スライディング ウィンドウ関数は、イベント発生時に出力 **のみ** を生成します。 すべてのウィンドウに少なくとも 1 つのイベントがあり、€ (イプシロン) ごとに継続的に前に移動します。 ホッピング ウィンドウのように、イベントは複数のスライディング ウィンドウに属することができます。

![Stream Analytics ウィンドウ関数 (スライディング) の概要](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="getting-help-with-window-functions"></a>ウィンドウ関数に関するヘルプ
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)


