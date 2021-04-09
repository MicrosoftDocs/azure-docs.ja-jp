---
title: Azure Media Services でサムネイル スプライトを生成する | Microsoft Docs
description: このトピックでは、Azure Media Services でサムネイル スプライトを生成する方法を示します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: ce66b6f605b10f65ec8a98d14c682928c7b21321
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012243"
---
# <a name="generate-a-thumbnail-sprite"></a>サムネイル スプライトを生成する

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Media Encoder Standard を使用して、サムネイル スプライトを生成できます。サムネイル スプライトは、1 つの (大きい) 画像につなぎ合わされた複数の解像度の小さいサムネイルを含む JPEG ファイルと VTT ファイルがまとめられたものです。 この VTT ファイルでは、各サムネイルが表す入力ビデオでの時間範囲と、大きい JPEG ファイル内でのそのサムネイルのサイズと座標が指定されています。 ビデオ プレーヤーは、VTT ファイルとスプライト画像を使用して、"ビジュアル" なシークバーを表示し、ビデオのタイムライン上を前後に移動するときに視覚的なフィードバックを閲覧者に提供します。

Media Encoder Standard を使用してサムネイル スプライトを生成するためのプリセットは以下のとおりです。

1. JPG サムネイル画像形式を使用する必要があります。
2. タイムスタンプまたは % 値 (およびフレーム数ではない) として、開始/ステップ/範囲値を指定する必要があります。 
    
    1. タイムスタンプと % の値を混在させることができます

3. 1 以上の正数の SpriteColumn 値を持つ必要があります。

    1. SpriteColumn が M >= 1 に設定されている場合、出力画像は M 列の四角形です。 #2 で生成されたサムネイルの数が M の正確な倍数でない場合、最後の行は不完全であり、黒ピクセルのままになります。  

たとえば次のようになります。

```json
{
    "Version": 1.0,
    "Codecs": [
    {
      "Start": "00:00:01",
      "Type": "JpgImage",
      "Step": "5%",
      "Range": "100%",
      "JpgLayers": [
        {
          "Type": "JpgLayer",
          "Width": "10%",
          "Height": "10%",
          "Quality": 90
        }
      ],
      "SpriteColumn": 10
    }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
   ]
}
```

## <a name="known-issues"></a>既知の問題

1.  1 行の画像のスプライト画像を生成することはできません (SpriteColumn = 1 の結果は 1 列の画像)。
2.  スプライト画像を妥当なサイズの JPEG 画像にチャンクすることは、まだサポートされていません。 そのため、結果の合成サムネイル スプライトが約 8 M ピクセル以下になるように、サムネイルの数とサイズを制限するよう注意する必要があります。
3.  Azure Media Player は、Microsoft Edge、Chrome、および Firefox ブラウザーでスプライトをサポートしています。 VTT 解析は IE11 ではサポートされていません。

## <a name="next-steps"></a>次のステップ

[コンテンツのエンコード](media-services-encode-asset.md)
