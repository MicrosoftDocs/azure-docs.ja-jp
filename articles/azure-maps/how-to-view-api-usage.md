---
title: Azure Maps の API 使用状況を表示する方法 | Microsoft Docs
description: ポータルで Azure Maps の API 呼び出しに対するメトリックを表示する方法について説明します。
author: walsehgal
ms.author: v-musehg
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 716f9fbc02905a8981771c2fd9dd2be0baeb8196
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67460190"
---
# <a name="view-azure-maps-api-usage"></a>Azure Maps の API 使用状況を表示する

この記事では、[ポータル](https://portal.azure.com)で Azure Maps アカウントにおける API 使用状況のメトリックを表示する方法について示します。 メトリックは、カスタマイズ可能な時間の長さに従って便利なグラフ形式で示されます。

## <a name="view-metric-snapshot"></a>メトリックのスナップショットを表示する

Maps アカウントの **[概要]** ページでいくつかの共通メトリックを表示できます。 現在、選択可能な時間の長さにわたって*要求の合計数*、*合計エラー数*、および*可用性*が表示されています。

![Azure Maps メトリックの概要](media/how-to-view-api-usage/portal-overview.png)

特定の分析に対してこれらのグラフをカスタマイズする場合は、次のセクションに進みます。

## <a name="view-detailed-metrics"></a>詳細なメトリックを表示する

1. [ポータル](https://portal.azure.com)で Azure サブスクリプションにサインインします。

2. 左側にある **[すべてのリソース]** メニュー アイコンをクリックして、 *[Azure Maps アカウント]* に移動します。

3. ご利用の Maps アカウントが開いたら、左側の **[メトリック]** メニューをクリックします。

4. **[メトリック]** ウィンドウで、次のいずれかを選びます。

   1. **[可用性]** : 期間にわたって API 可用性の*平均*を示します。
   2. **[使用状況]** : 使用状況におけるご自分のアカウントの*割合*を示します。

      ![Azure Maps メトリック ウィンドウ](media/how-to-view-api-usage/portal-metrics.png)

5. 次に、 **[過去 24 時間] (自動)** をクリックして、 *[時間の範囲]* を選択できます。 既定では、時間の範囲は 24 時間に設定されています。 クリックすると、選択可能な時間の範囲がすべて表示されます。 また、 *[時間の粒度]* を選択したり、同じドロップダウン内の *[ローカル]* または *[GMT]* として時間を示すように選んだりすることができます。 **[Apply]** をクリックします。

    ![Azure Maps メトリックの時間の範囲](media/how-to-view-api-usage/time-range.png)

6. メトリックを追加すると、そのメトリックに関連するプロパティから**フィルターを追加**して、グラフを表示するプロパティの値を選択することができます。

    ![Azure Maps メトリックのフィルター](media/how-to-view-api-usage/filter.png)

7. また、選択したメトリック プロパティに基づいてメトリックに**分割を適用**することもできます。 これにより、グラフを複数のグラフに分割できます。プロパティの値ごとにグラフ 1 つです。 次の図にある各グラフの色は、グラフの下に表示されるプロパティの値に対応しています。

    ![Azure Maps メトリックの分割](media/how-to-view-api-usage/splitting.png)

8. 上部の **[メトリックの追加]** ボタンをクリックするだけで、同じグラフ上で複数のメトリックを確認することもできます。

## <a name="next-steps"></a>次の手順

使用状況を追跡する必要がある Azure Maps の API の詳細については、以下を参照してください。
> [!div class="nextstepaction"] 
> [Azure Maps Web SDK のハウツー](how-to-use-map-control.md)

> [!div class="nextstepaction"] 
> [Azure Maps Android SDK のハウツー](how-to-use-android-map-control-library.md)

> [!div class="nextstepaction"]
> [Azure Maps REST API ドキュメント](https://docs.microsoft.com/rest/api/maps)
