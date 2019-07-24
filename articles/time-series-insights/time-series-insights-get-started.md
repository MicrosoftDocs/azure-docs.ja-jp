---
title: Azure Time Series Insights 環境の作成 | Microsoft Docs
description: この記事では、Azure Portal を使用して新しい Time Series Insights 環境を作成する方法について説明します。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.custom: seodec18
ms.openlocfilehash: b18ae44a140d77c24d8b5741bc09740767d1344f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58075954"
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Azure Portal で新しい Time Series Insights 環境を作成する
この記事では、Azure Portal を使用して新しい Time Series Insights 環境を作成する方法について説明します。

Time Series Insights では、Azure IoT Hub や Event Hubs に送信されたデータの視覚化と照会を数分で開始することができ、大量の時系列データを数秒で照会できます。  モノのインターネット (IoT) 規模に対応するように設計されており、数テラバイトのデータを処理できます。

## <a name="steps-to-create-the-environment"></a>環境の作成手順
次の手順に従って、環境を作成します。

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. **[+ 新規]** をクリックします。

3. **[モノのインターネット]** カテゴリを選択し、**[Time Series Insights]** を選択します。

   ![Time Series Insights 環境の作成](media/time-series-insights-get-started/1-new-tsi.png)

4. **[Time Series Insights]** ページで、**[作成]** を選択します。

5. 必要なパラメーターを入力します。 次の表に各パラメーターを示します。
   
   ![Time Series Insights リソース グループの作成](media/time-series-insights-get-started/2-create-tsi.png)
   
   Setting|推奨値|説明
   ---|---|---
   環境名 | 一意の名前 | この名前は、[Time Series エクスプローラー](https://insights.timeseries.azure.com)でその環境を表します。
   サブスクリプション | 該当するサブスクリプション | 複数のサブスクリプションがある場合、可能であれば、イベント ソースが含まれているサブスクリプションを選択します。 Time Series Insights では、同じサブスクリプションに存在する Azure IoT Hub とイベント ハブのリソースを自動検出できます。
   リソース グループ | 新しいグループを作成するか、既存のグループを使用 | リソース グループとは、一緒に使用される Azure リソースのコレクションです。 既存のリソース グループ (イベント ハブまたは IoT ハブが含まれているグループなど) を選択できます。 このリソースが他のリソースに関連していない場合は、新しいリソース グループを作成することもできます。
   Location | イベント ソースに最も近い場所 | リージョン間およびゾーン間での帯域幅利用料の追加や、データをリージョンの外部に移動するときの待機時間の増加を回避するために、可能であれば、イベント ソース データがあるデータ センターの場所を選択します。
   価格レベル  | S1 | 必要なスループットを選択します。 コストと初期容量を最小限に抑えるために、S1 を選択します。
   容量 | 1 | 容量は、イングレス レート、ストレージ容量、選択した SKU に関連するコストに適用される乗数です。  環境の容量は、作成後に変更できます。 コストを最小限に抑えるために、容量として 1 を選択します。 
  
6. 今後、Time Series 環境にアクセスしやすいように、**[ダッシュボードにピン留めする]** をオンにします。

   ![Time Series Insights の作成の [ダッシュボードにピン留めする]](media/time-series-insights-get-started/3-pin-create.png)

7. **[作成]** をクリックして、プロビジョニング プロセスを開始します。 このプロセスには数分かかることがあります。

8. デプロイ プロセスを監視するために、**[通知]** シンボル (ベルのアイコン) をクリックします。

   ![通知の確認](media/time-series-insights-get-started/4-notifications.png)

デプロイが成功したら、**[リソースに移動]** を選択して、他のプロパティの構成、データ アクセス ポリシーによるセキュリティの設定、イベント ソースの追加などの操作を行うことができます。

## <a name="next-steps"></a>次の手順
* 環境をセキュリティで保護するために、[データ アクセス ポリシーを定義](time-series-insights-data-access.md)する。
* Azure Time Series Insights 環境に[イベント ハブ イベント ソースを追加](time-series-insights-how-to-add-an-event-source-eventhub.md)する。 
* イベント ソースに[イベントを送信する](time-series-insights-send-events.md)。
* [Time Series Insights エクスプローラー](https://insights.timeseries.azure.com)で環境を表示します。
