---
title: "Azure Time Series Insights 環境へのイベント ソースの追加 | Microsoft Docs"
description: "このチュートリアルでは、Time Series Insights 環境にイベント ソースを接続します"
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: omravi
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: ffa2eaf3680e68ac14aabf49b6308caeb173fd43
ms.contentlocale: ja-jp
ms.lasthandoff: 07/01/2017

---

# <a name="create-an-event-source-for-your-time-series-insights-environment-using-the-ibiza-portal"></a>Ibiza ポータルを使用して Time Series Insights 環境のイベント ソースを作成する

Time Series Insights のイベント ソースは、Azure Event Hubs のようなイベント ブローカーから派生しています。 Time Series Insights は直接イベント ソースに接続するため、ユーザーがコードを 1 行も記述する必要なく、データ ストリームを取り込みます。 現在 Time Series Insights では、Azure Event Hubs と Azure IoT Hub がサポートされています。 将来的にはさらにイベント ソースが追加される予定です。

## <a name="steps-to-add-an-event-source-to-your-environment"></a>環境にイベント ソースを追加する手順

1.  [Ibiza ポータル](https://portal.azure.com)にサインインします。
2.  Ibiza ポータルの左側のメニューで [すべてのリソース] をクリックします。
3.  Time Series Insights 環境を選択します。

  ![Time Series Insights のイベント ソースの作成](media/add-event-source/getstarted-create-event-source-1.png)

4.  [イベント ソース] を選択し、[+ 追加] をクリックします。

  ![Time Series Insights のイベント ソースの作成 - 詳細](media/add-event-source/getstarted-create-event-source-2.png)

5.  イベント ソースの名前を指定します。 この名前は、このイベント ソースからのイベントすべてに関連付けられ、クエリ時に使用できます。
6.  現在のサブスクリプションに含まれるイベント ハブ リソースの一覧からイベント ハブを選択するか、 [イベント ハブ設定を手動で行う] インポート オプションを選択して別のサブスクリプションにあるイベント ハブを指定します。 イベントは JSON 形式で発行されている必要があります。
7.  イベント ハブでの読み取りアクセス許可があるポリシーを選択します。
8.  イベント ハブ コンシューマー グループを指定します。

  > [!IMPORTANT]
  > このコンシューマー グループがその他のサービス (Stream Analytics ジョブや別の Time Series Insights 環境など) で使用されていないことを確認してください。 コンシューマー グループが他のサービスで使用されている場合、この環境および他のサービスの読み取り操作は悪影響を受けます。 "$Default" をコンシューマー グループとして使用している場合、他の閲覧者によって再利用される可能性があります。

9.  [作成] をクリックします。

イベント ソースの作成後、Time Series Insights は自動的に環境へのデータのストリーミングを開始します。

## <a name="next-steps"></a>次のステップ

* イベント ソースに[イベントを送信する](time-series-insights-send-events.md)
* [Time Series Insights ポータル](https://insights.timeseries.azure.com)で環境を表示する

