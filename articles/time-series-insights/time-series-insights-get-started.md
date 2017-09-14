---
title: "Azure Time Series Insights 環境の作成 | Microsoft Docs"
description: "このチュートリアルでは、Time Series Insights 環境を作成してイベント ソースに接続し、数分でイベント データを分析できるように準備する方法について学習します。"
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
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: eb710795916a2d7beea75a6408a0982fb4dc8750
ms.contentlocale: ja-jp
ms.lasthandoff: 04/26/2017

---

# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Azure Portal で新しい Time Series Insights 環境を作成する

Time Series Insights 環境は、受信とストレージの容量を備えた Azure リソースです。 導入ユーザーは、Azure Portal を使用して、必要な容量を備えた環境をプロビジョニングします。

## <a name="steps-to-create-the-environment"></a>環境の作成手順

次の手順に従って、環境を作成します。

1.  [Azure Portal](https://portal.azure.com) にサインインします。
2.  左上隅のプラス記号 ("+") をクリックします。
3.  検索ボックスで「Time Series Insights」を検索します。

  ![Time Series Insights 環境の作成](media/get-started/getstarted-create-environment1.png)

4.  [Time Series Insights] を選択し、[作成] をクリックします。

  ![Time Series Insights リソース グループの作成](media/get-started/getstarted-create-environment2.png)

5.  環境名を指定します。 この名前は、[Time Series エクスプローラー](https://insights.timeseries.azure.com)でその環境を表します。
6.  サブスクリプションを選択します。 イベント ソースが含まれているものを選択します。 Time Series Insights では、同じサブスクリプションに存在する Azure IoT Hub とイベント ハブのリソースを自動検出できます。
7.  リソース グループを選択または作成します。 リソース グループとは、一緒に使用される Azure リソースのコレクションです。
8.  ホストする場所を選択します。 データ センター間でのデータの移動を回避するために、イベント ソースが含まれている場所を選択してください。
9.  価格レベルを選択します。
10. 容量を選択します。 環境の容量は、作成後に変更できます。
11. 環境を作成します。 サインインするときに常にアクセスしやすいように、作成した環境をダッシュボードにピン留めすることもできます。

  ![Time Series Insights の作成の [ダッシュボードにピン留めする]](media/get-started/getstarted-create-environment3.png)

## <a name="next-steps"></a>次のステップ

* [Time Series Insights ポータル](https://insights.timeseries.azure.com)で環境にアクセスするために[データ アクセス ポリシーを定義する](time-series-insights-data-access.md)
* [イベント ソースを作成する](time-series-insights-add-event-source.md)
* イベント ソースに[イベントを送信する](time-series-insights-send-events.md)

