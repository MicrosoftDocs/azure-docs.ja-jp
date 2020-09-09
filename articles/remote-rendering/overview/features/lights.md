---
title: シーンのライティング
description: 光源の説明とプロパティ
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: e33e012480c876dc5befbb93404bdb131ea9329a
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "84022148"
---
# <a name="scene-lighting"></a>シーンのライティング

既定では、リモートでレンダリングされるオブジェクトは、[スカイライト](sky.md)を使用して照射されます。 ほとんどのアプリケーションではこれで十分ですが、さらに光源をシーンに追加することができます。

> [!IMPORTANT]
> [PBR 素材](pbr-materials.md) のみが光源の影響を受けます。 [色素材](color-materials.md) は、常に完全に明るく見えます。

> [!NOTE]
> キャスト シャドウは現在サポートされていません。 Azure Remote Rendering は、必要に応じて複数の GPU を利用して、大量のジオメトリをレンダリングするように最適化されています。 シャドウ キャストの従来のアプローチは、このようなシナリオではうまく機能しません。

## <a name="common-light-component-properties"></a>一般的な照明コンポーネントのプロパティ

すべてのライトの種類は、抽象基本クラス `LightComponent` から派生し、次のプロパティを共有します。

* **Color:** [ガンマ空間](https://en.wikipedia.org/wiki/SRGB)における照明の色。 アルファは無視されます。

* **Intensity:** 照明の明るさ。 ポイント ライトとスポット ライトでは、強度によって光がどの程度届くかも定義されます。

## <a name="point-light"></a>ポイント ライト

Azure Remote Rendering では、`PointLightComponent` は単一点から光を発するだけでなく、より柔らかい光源をシミュレートするため、小さい球体や小さなチューブからも光を発することができます。

### <a name="pointlightcomponent-properties"></a>PointLightComponent プロパティ

* **Radius:** 既定の半径は 0 です。この場合、照明はポイント ライトとして機能します。 半径が 0 より大きい場合は、球体の光源として機能し、反射の光源の外観を変更します。

* **Length:** `Length` と `Radius` の両方が 0 以外の場合、照明はチューブ状のライトとして機能します。 これは、ネオン チューブをシミュレートするために使用できます。

* **AttenuationCutoff:** (0, 0) の場合、ライトの減衰はその `Intensity` のみに依存します。 ただし、照明の強度が線形的に 0 にスケーリングされるカスタムの最小距離と最大距離を指定することができます。 この機能を使用すると、特定の照明の影響範囲を小さくすることができます。

* **ProjectedCubemap:** 有効な [キューブ マップ](../../concepts/textures.md)に設定されている場合、テクスチャは光源の周囲のジオメトリに投影されます。 キューブ マップの色は、ライトの色に応じて変調されます。

## <a name="spot-light"></a>スポット ライト

`SpotLightComponent` は `PointLightComponent` に似ていますが、照明は円錐の形状に制限されています。 円錐の向きは、*所有者エンティティの負の Z 軸*によって定義されます。

### <a name="spotlightcomponent-properties"></a>SpotLightComponent プロパティ

* **Radius:** `PointLightComponent` の場合と同じです。

* **SpotAngleDeg:** この間隔は、円錐の内角と外角 (度単位) を定義します。 内角内の範囲は、最大の明るさで照らされます。 外角に光の減衰を適用し、半影表現を生み出します。

* **FalloffExponent:** 円錐の内角と外角の間の光の減衰の度合いを定義します。 値を大きくすると、より鋭く遷移します。 既定の 1.0 では、線形遷移になります。

* **AttenuationCutoff:** `PointLightComponent` の場合と同じです。

* **Projected2dTexture:** 有効な [2D テクスチャ](../../concepts/textures.md)に設定されている場合、光が当たるジオメトリにイメージが投影されます。 テクスチャの色は、ライトの色に応じて変調されます。

## <a name="directional-light"></a>指向性ライト

`DirectionalLightComponent` は、無限に離れた場所にある光源をシミュレートします。 光は、*所有者エンティティの負の Z 軸*の方向に照射されます。 エンティティの位置は無視されます。

追加のプロパティはありません。

## <a name="performance-considerations"></a>パフォーマンスに関する考慮事項

光源は、レンダリングのパフォーマンスに大きな影響を与えます。 アプリケーションで必要な場合にのみ、慎重に使用してください。 静的な指向性コンポーネントを含む静的なグローバル光源の条件は、[カスタムのスカイ テクスチャ](sky.md)で実現できます。追加のレンダリングコストは発生しません。

## <a name="next-steps"></a>次のステップ

* [素材](../../concepts/materials.md)
* [スカイ](sky.md)
