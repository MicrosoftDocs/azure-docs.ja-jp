---
title: 環境の作成 - Azure Time Series Insights | Microsoft Docs
description: Azure portal を使用して新しい Azure Time Series Insights 環境を作成する方法について説明します。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 61af7922318514a7b86a349d1970c59d4d168d85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95023328"
---
# <a name="create-a-new-azure-time-series-insights-gen1-environment-in-the-azure-portal"></a>Azure Portal で新しい Azure Time Series Insights Gen1 環境を作成する

> [!CAUTION]
> これは Gen1 の記事です。

この記事では、Azure Portal を使用して新しい Azure Time Series Insights 環境を作成する方法について説明します。

Azure Time Series Insights では、Azure IoT Hub や Event Hubs に送信されたデータの視覚化と照会を数分で開始することができ、大量の時系列データを数秒で照会できます。  モノのインターネット (IoT) 規模に対応するように設計されており、数テラバイトのデータを処理できます。

## <a name="steps-to-create-the-environment"></a>環境の作成手順

次の手順に従って、環境を作成します。

1. [Azure portal](https://portal.azure.com) にサインインする

1. **[+ リソースの作成]** ボタンを選択します。

1. **[モノのインターネット]** カテゴリを選択し、 **[Time Series Insights]** を選択します。

   [![Azure Time Series Insights 環境を作成する](media/time-series-insights-get-started/tsi-create-new-environment.png)](media/time-series-insights-get-started/tsi-create-new-environment.png#lightbox)

1. **[Time Series Insights]** ページで、 **[作成]** を選択します。

1. 必要なパラメーターを入力します。 次の表に各パラメーターを示します。

   [![Azure Time Series Insights リソース グループを作成する](media/time-series-insights-get-started/tsi-configure-and-create.png)](media/time-series-insights-get-started/tsi-configure-and-create.png#lightbox)

   設定|推奨値|説明
   ---|---|---
   環境名 | 一意の名前 | この名前は、[Time Series エクスプローラー](https://insights.timeseries.azure.com)でその環境を表します
   サブスクリプション | 該当するサブスクリプション | 複数のサブスクリプションがある場合、可能であれば、イベント ソースが含まれているサブスクリプションを選択します。 Azure Time Series Insights では、同じサブスクリプションに存在する Azure IoT Hub とイベント ハブのリソースを自動的に検出できます。
   Resource group | 新しいグループを作成するか、既存のグループを使用 | リソース グループとは、一緒に使用される Azure リソースのコレクションです。 既存のリソース グループ (イベント ハブまたは IoT ハブが含まれているグループなど) を選択できます。 このリソースが他のリソースに関連していない場合は、新しいリソース グループを作成することもできます。
   Location | イベント ソースに最も近い場所 | リージョン間およびゾーン間での帯域幅利用料の追加や、データをリージョンの外部に移動するときの待機時間の増加を回避するために、可能であれば、イベント ソース データがあるデータ センターの場所を選択します。
   Pricing tier | S1 | 必要なスループットを選択します。 コストと初期容量を最小限に抑えるために、S1 を選択します。
   容量 | 1 | 容量は、イングレス レート、ストレージ容量、選択した SKU に関連するコストに適用される乗数です。  環境の容量は、作成後に変更できます。 コストを最小限に抑えるために、容量として 1 を選択します。
  
1. **[作成]** をクリックして、プロビジョニング プロセスを開始します。 このプロセスには数分かかることがあります。

1. デプロイ プロセスを監視するために、 **[通知]** シンボル (ベルのアイコン) をクリックします。

   [![通知の確認](media/time-series-insights-get-started/tsi-deploy-notifications.png)](media/time-series-insights-get-started/tsi-deploy-notifications.png#lightbox)

1. リソースの **[概要]** で、デプロイの構成を確認します。

   [![Azure Time Series Insights の作成の [ダッシュボードにピン留めする]](media/time-series-insights-get-started/tsi-verify-deployment.png)](media/time-series-insights-get-started/tsi-verify-deployment.png#lightbox)

1. **(オプション)** 右上隅にある **ピン アイコン** を選択すると、Azure Time Series Insights 環境に簡単にアクセスできます。

## <a name="next-steps"></a>次のステップ

* 環境をセキュリティで保護するために、[データ アクセス ポリシーを定義](./concepts-access-policies.md)する。

* Azure Time Series Insights 環境に[イベント ハブ イベント ソースを追加](./how-to-ingest-data-event-hub.md)する。

* イベント ソースに[イベントを送信](time-series-insights-send-events.md)します。

* [Azure Time Series Insights エクスプローラー](https://insights.timeseries.azure.com)で自分の環境を表示します。