---
title: サポートされている組み込みの Azure Maps マップ スタイル
description: Azure Maps でサポートされる組み込みのマップ スタイル (道路、blank_accessible、衛星、satellite_road_labels、road_shaded_relief、night など) について説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: f6ce2966d7e91bd0fc6a23ad2605316da4e2342e
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037730"
---
# <a name="azure-maps-supported-built-in-map-styles"></a>Azure Maps でサポートされている組み込みのマップ スタイル

Azure Maps は、次に説明するように、いくつかの異なる組み込みマップ スタイルをサポートしています。

## <a name="road"></a>道路

**道路** マップは、道路を表示する標準のマップです。 自然および人工的な特徴、およびそれらの特徴についてのラベルも表示されます。

![道路マップ スタイル](./media/supported-map-styles/road.png)

**該当する API:**

* [マップ画像](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [マップ タイル](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK マップ コントロール
* Android マップ コントロール
* Power BI 視覚エフェクト

## <a name="blank-and-blank_accessible"></a>blank と blank_accessible

**blank** と **blank_accessible** マップ スタイルでは、データを視覚化するための空白のキャンバスが提供されます。 基本マップが表示されていない場合でも、**blank_accessible** スタイルでは、マップの場所の詳細を含むスクリーン リーダーの更新が引き続き提供されます。

> [!Note]
> Web SDK では、マップの DIV 要素の CSS `background-color` スタイルを設定することによって、マップの背景色を変更できます。

**該当する API:**

* Web SDK マップ コントロール

## <a name="satellite"></a>衛星

**衛星**スタイルは、衛星画像と航空映像の組み合わせです。

![衛星タイル マップ スタイル](./media/supported-map-styles/satellite.png)

**該当する API:**

* [衛星タイル](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Web SDK マップ コントロール
* Android マップ コントロール
* Power BI 視覚エフェクト

## <a name="satellite_road_labels"></a>satellite_road_labels

このマップ スタイルは、衛星画像と航空映像の上に道路とラベルが重ねて表示されたハイブリッドです。

![satellite_road_labels マップ スタイル](./media/supported-map-styles/satellite-road-labels.png)

**該当する API:**

* Web SDK マップ コントロール
* Android マップ コントロール
* Power BI 視覚エフェクト

## <a name="grayscale_dark"></a>grayscale_dark

**グレースケール ダーク**は、道路マップ スタイルの濃いバージョンです。

![gray_scale マップ スタイル](./media/supported-map-styles/grayscale-dark.png)

**該当する API:**

* [マップ画像](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [マップ タイル](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK マップ コントロール
* Android マップ コントロール
* Power BI 視覚エフェクト

## <a name="grayscale_light"></a>grayscale_light

**グレースケール ライト**は、道路マップ スタイルの薄いバージョンです。

![グレースケール ライト マップ スタイル](./media/supported-map-styles/grayscale-light.png)

**該当する API:**
* Web SDK マップ コントロール
* Android マップ コントロール
* Power BI 視覚エフェクト

## <a name="night"></a>night

**night** は、道路マップ スタイルの色の濃いバージョンで、道路と記号が色付きです。

![ナイト マップ スタイル](./media/supported-map-styles/night.png)

**該当する API:**

* Web SDK マップ コントロール
* Android マップ コントロール
* Power BI 視覚エフェクト

## <a name="road_shaded_relief"></a>road_shaded_relief

**道路の影付きレリーフ**は、地球の輪郭で完成する、Azure Maps の主要スタイルです。

![影付きレリーフ マップ スタイル](./media/supported-map-styles/shaded-relief.png)

**該当する API:**

* [マップ タイル](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK マップ コントロール
* Android マップ コントロール
* Power BI 視覚エフェクト

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** は、他のスタイルと比較してコントラストが強いダーク マップ スタイルです。

![コントラストの強いダーク マップ スタイル](./media/supported-map-styles/high-contrast-dark.png)

**該当する API:**

* Web SDK マップ コントロール
* Power BI 視覚エフェクト

## <a name="next-steps"></a>次のステップ

Azure Maps でマップ スタイルを設定する方法について説明します。

> [!div class="nextstepaction"]
> [マップ スタイルを選択する](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
