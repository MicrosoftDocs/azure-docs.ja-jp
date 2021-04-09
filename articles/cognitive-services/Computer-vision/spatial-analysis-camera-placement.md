---
title: 空間分析カメラの配置
titleSuffix: Azure Cognitive Services
description: 空間分析で使用するカメラを設定する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: 4c2d1cd1c73b377d85501fd31d0f5a1893df5183
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102487425"
---
# <a name="camera-placement-guide"></a>カメラ配置ガイド

この記事では、空間分析 (パブリック プレビュー) のカメラ配置に関する推奨事項について説明します。 これには、含まれているすべての操作に対する高さ、角度、およびカメラと焦点間の距離に関する具体的な推奨事項に加えて、一般的なガイドラインが含まれます。 

> [!NOTE]
> このガイドは、Axis M3045-V カメラ用に設計されています。 このカメラでは、解像度 1920 x 1080、106 度の水平視野、59 度の垂直視野、および修正された 2.8 mm の焦点距離を使用しています。 以下の原則はすべてのカメラに適用されますが、カメラの高さやカメラと焦点間の距離に関する具体的なガイドラインは、他のカメラで使用するために調整する必要があります。 

## <a name="general-guidelines"></a>一般的なガイドライン

空間分析用にカメラを配置するときは、次の一般的なガイドラインを考慮してください。

* **光源の高さ。** 照明器具がカメラをブロックしないように、器具の下にカメラを配置します。
* **障害物。** カメラのビューを遮らないようにするためには、電柱、看板、棚、壁、および既存の LP カメラなどの障害物に注意します。
* **環境の逆光。** 屋外の逆光はカメラの画質に影響します。 大きな逆光の状態を回避するには、外部に面した窓とグラスドアにカメラを向けないようにします。
* **ローカルのプライバシー規則および規制。** ローカルの規制によって、カメラがとらえることができる内容が制限される場合があります。 カメラを配置する前に、ローカルの規則および規制を理解してください。
* **建造物。** HVAC、スプリンクラー、および既存の配線によって、カメラのハード マウントが制限される場合があります。
* **ケーブルの管理方法。** 計画されたカメラのマウント場所からパワー オーバー オンインターネット (PoE) スイッチにイーサネット ケーブルをルーティングできることを確認します。

## <a name="height-focal-point-distance-and-angle"></a>高さ、焦点距離、および角度

空間分析用にカメラを設置する方法を決定する際には、次の 3 つの点を考慮する必要があります。
- カメラの高さ
- カメラと焦点間の距離
- 床面に対するカメラの角度

また、可能であれば、カメラの視野に関連して、大多数の人が歩く方向 (人の歩く方向) についても理解しておくことも重要です。 システムのパフォーマンスを向上させるためには、この方向が重要です。

![ある方向に歩く人の画像](./media/spatial-analysis/person-walking-direction.png)

次の図は、人の歩く方向の立面図を示しています。

![立面図および平面図](./media/spatial-analysis/person-walking-direction-diagram.png)

## <a name="camera-height"></a>カメラの高さ

一般的に、カメラは地上から 12 ～ 14 フィートにマウントする必要があります。 フェイス マスクの検出には、カメラを地面から 8 から 12 フィートに位置に設置することをお勧めします。 この範囲でのカメラの取り付けを計画するときは、カメラの視野に影響を与える可能性のある障害物 (たとえば、棚、つるし照明、つるし看板、ディスプレイなど) を考慮し、必要に応じて高さを調整します。

## <a name="camera-to-focal-point-distance"></a>カメラと焦点間の距離

_カメラと焦点間の距離_ とは、地表で測定した、焦点 (またはカメラ画像の中心) からカメラまでの直線距離です。

![カメラと焦点間の距離](./media/spatial-analysis/camera-focal-point.png)

この距離は、床面で測定されます。

![カメラと焦点間の距離を床から測定する方法](./media/spatial-analysis/camera-focal-point-floor-plane.png)

上からの場合、次のようになります。

![カメラと焦点間の距離を上から測定する方法](./media/spatial-analysis/camera-focal-point-above.png)

次の表を使用して、特定のマウントの高さに基づいて、焦点からのカメラの距離を決定します。 これらの距離は、最適な配置を行うためのものです。 この表では、天井によって高さが制限されることがあるため、12'-14' の推奨事項の下に指示が示されています。 フェイス マスク検出の場合、推奨されるカメラから焦点までの距離 (最小または最大) は、カメラの高さが 8 フィートから 12 フィートの場合、4 フィートから 10 フィートです。

| カメラの高さ | カメラと焦点間の距離 (最小/最大) |  
| ------------- | ---------------------------------------- |  
| 8'            | 4.6'-8'                                  |  
| 10'           | 5.8'-10'                                 |  
| 12'           | 7'-12'                                   |  
| 14'           | 8'-14''                                  |  
| 16'           | 9.2'-16'                                 |  
| 20'           | 11.5'-20'                                |  

次の図は、最も近いカメラと焦点間の距離から最も遠いカメラと焦点間の距離までのカメラの視野をシミュレートしています。

| 最も近い                                      | 最も遠い                                      |  
| -------------------------------------------- | --------------------------------------------- |  
| ![カメラと焦点間の最も近い距離](./media/spatial-analysis/focal-point-closest.png) | ![カメラと焦点間の最も遠い距離](./media/spatial-analysis/focal-point-farthest.png) |  

## <a name="camera-angle-mounting-ranges"></a>カメラの角度のマウント範囲

このセクションでは、許容可能なカメラの角度のマウント範囲について説明します。 これらのマウント範囲は、最適な配置のために許容可能な範囲を示します。

### <a name="line-configuration"></a>ライン構成

**cognitiveservices.vision.spatialanalysis-personcrossingline** 操作の場合、精度を最大にするために最適なカメラのマウント角度は +/-5°です。

フェイス マスク検出の場合、カメラの高さが 8 フィートから 12 フィートの場合、+/-30 度が最適なカメラ取り付け角度です。

次の図は、**cognitiveservices.vision.spatialanalysis-personcrossingline** を使用して、出入り口での入出のカウントを行うために、左端 (-) および右端 (+) のマウント角度の推奨事項を使用してカメラの視野をシミュレートしています。

| 左端の視野                | 右端の視野                |  
| ---------------------------- | ----------------------------- |  
| ![左端のカメラ角度](./media/spatial-analysis/camera-angle-left.png) | ![右端のカメラ角度](./media/spatial-analysis/camera-angle-right.png) |  

次の図は、カメラの配置と上からのマウント角度を示しています。

![鳥瞰図](./media/spatial-analysis/camera-angle-top.png)

### <a name="zone-configuration"></a>ゾーンの構成

カバーされた領域の大きさが十分であることを保証するには、カメラを地面から 10 フィート以上に配置することをお勧めします。 

ゾーンが壁や棚などの障害物の隣にある場合は、次の図に示すように、許容可能な 120 度の角度範囲内で、ターゲットから指定された距離でカメラをマウントします。

![許容可能なカメラ角度](./media/spatial-analysis/camera-angle-acceptable.png)

次の図は、棚の横にある領域の左および右のカメラの視野のシミュレーションを示しています。

| 左側の視野        | 右側の視野        |  
| ---------------- | ----------------- |  
| ![左側の視野](./media/spatial-analysis/end-cap-left.png) | ![右側の視野](./media/spatial-analysis/end-cap-right.png) |  

#### <a name="queues"></a>キュー

**cognitiveservices.vision.spatialanalysis-personcount**、**cognitiveservices.vision.spatialanalysis-persondistance** および **cognitiveservices.vision.spatialanalysis-personcrossingpolygon** スキルを使用して、キューを監視することができます。 キューのデータ品質を最適にするため、キュー内の人間の遮蔽を最小限に抑え、キューの場所が時間の経過と共に一貫していることを確認するためには、格納式ベルト バリアをお勧めします。

![格納式ベルト キュー](./media/spatial-analysis/retractable-belt-queue.png)

この種類のバリアは、システムからの洞察の精度を最大限に高めるために、キューの形成のために不透明なバリアをお勧めします。

キューには、直線とジグザグの 2 種類があります。

次の図は、直線キューの推奨事項を示しています。

![直線キューの推奨事項](./media/spatial-analysis/camera-angle-linear-queue.png)

次の図は、直線キューの左および右のカメラの視野のシミュレーションを示しています。 キューの反対側にカメラを取り付けることができます。

| 左側の視野                          | 右側の視野                          |  
| ---------------------------------- | ----------------------------------- |  
| ![直線キューの左側の角度](./media/spatial-analysis/camera-angle-linear-left.png) | ![直線キューの右側の角度](./media/spatial-analysis/camera-angle-linear-right.png) |  

ジグザグキューの場合、次の図に示すように、カメラをキューのラインの方向に直接配置しないようにすることをお勧めします。 図に示す 4 つのカメラ位置の例では、それぞれの方向に +/-15 度の許容誤差を持つ理想的な視野が示されています。

次の図では、ジグザグ キューの理想的な場所に配置されたカメラからの視野をシミュレートしています。

| 視野 1        | 視野 2        |  
| ------------- | ------------- |  
| ![視野 1](./media/spatial-analysis/camera-angle-ideal-location-right.png) | ![視野 2](./media/spatial-analysis/camera-angle-ideal-location-left.png) |  

| 視野 3 |  視野 4 |  
| ---- | ----  |
| ![視野 3](./media/spatial-analysis/camera-angle-ideal-location-back.png) |  ![視野 4](./media/spatial-analysis/camera-angle-ideal-location-back-left.png) | 

##### <a name="organic-queues"></a>オーガニック キュー

オーガニック キューのラインはオーガニックに生じます。 キューが 2、3 人を超えて生じ、ラインがゾーン定義内で生じる場合は、この種のキューは許容できます。 キューの長さが一般に 2、3 人を超える場合は、格納式ベルト バリアを使用して、キューの方向を導き、ゾーン定義内のラインの形式を確認することをお勧めします。

## <a name="next-steps"></a>次のステップ

* [人数カウント Web アプリをデプロイする](spatial-analysis-web-app.md)
* [空間分析操作の構成](./spatial-analysis-operations.md)
* [ロギングおよびトラブルシューティング](spatial-analysis-logging.md)
* [ゾーンとラインの配置ガイド](spatial-analysis-zone-line-placement.md)
