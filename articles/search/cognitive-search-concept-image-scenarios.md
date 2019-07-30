---
title: コグニティブ検索内の画像からテキストを処理して抽出する - Azure Search
description: Azure Search のコグニティブ検索パイプラインで、画像内のテキストやその他の情報を処理し、抽出する方法について説明します。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 589f8c8f11138b4fb5c3c3096229e28c633efb0d
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423012"
---
#  <a name="how-to-process-and-extract-information-from-images-in-cognitive-search-scenarios"></a>コグニティブ検索のシナリオで画像からの情報を処理し、抽出する方法

コグニティブ検索では、画像や画像ファイルを操作するための複数の機能を利用できます。 ドキュメント クラッキングを行う際には、*imageAction* パラメーターを使用して、英数字が含まれている写真や絵からテキストを抽出することができます (停止標識から「止まれ」の文字を抽出するなど)。 また、画像のテキスト表現を生成することもできます (たんぽぽの写真から、「たんぽぽ」や「黄色」といいたテキストを生成するなど)。 さらに、画像に関するメタデータを抽出することもできます (サイズなど)。

この記事では、画像の処理について詳しく説明すると共に、コグニティブ検索パイプラインでの画像の操作方法について、ガイダンスを提供します。

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>正規化された画像の取得

ドキュメント クラッキングでは、ファイルに埋め込まれた画像や画像ファイルを操作するための、新しいインデクサー構成パラメーター セットを使用できます。 これらのパラメーターは、下流の処理で画像を正規化するために使用されます。 画像を正規化することで、画像の画一性が高まります。 大きい画像は、高さと幅の最大値に応じてサイズ変更され、扱いやすくなります。 向きについてのメタデータがある画像は、画像の回転が垂直方向の読み込み用に調整されます。 メタデータの調整結果は、各画像用に作成された複合型に取得されます。 

画像の正規化をオフにすることはできません。 画像を反復処理するスキルでは、正規化された画像が受け付けられます。 インデクサーで画像の正規化を有効にするには、そのインデクサーにスキルセットがアタッチされている必要があります。

| 構成パラメーター | 説明 |
|--------------------|-------------|
| imageAction   | 見つかった埋め込み画像や画像ファイルに対してアクションを実行しない場合は、"none" に設定します。 <br/>"GenerateNormalizedImages" に設定すると、ドキュメント クラッキングの際、正規化された画像の配列が生成されます。<br/>"generateNormalizedImagePerPage" に設定すると、正規化された画像の配列が生成され、データ ソース内の PDF は、各ページが 1 つの出力画像にレンダリングされます。  PDF 以外のファイルの種類については、機能は "generateNormalizedImages" の場合と同じです。<br/>"none" ではないすべてのオプションについては、画像が *normalized_images* フィールドで公開されます。 <br/>既定値は "none" です。 この構成は、BLOB データ ソースにのみ関連します ("dataToExtract" が "contentAndMetadata" に設定されている場合)。 <br/>特定のドキュメントから最大 1,000 個の画像が抽出されます。 ドキュメントに 1,000 を超える画像がある場合は、最初の 1,000 が抽出され、警告が生成されます。 |
|  normalizedImageMaxWidth | 生成された正規化画像の最大幅 (ピクセル単位)。 既定値は 2000 です。|
|  normalizedImageMaxHeight | 生成された正規化画像の最大の高さ (ピクセル単位)。 既定値は 2000 です。|

> [!NOTE]
> *imageAction* プロパティを "none" 以外の値に設定した場合、 *parsingMode* プロパティを "default" 以外に設定することはできなくなります。  インデクサー構成では、これら 2 つのプロパティのうち、いずれか 1 つだけを既定値以外の値に設定できます。

**parsingMode** パラメーターを `json` に設定するか (各 BLOB を 1 つのドキュメントとしてインデックスを付ける場合)、または `jsonArray` に設定します (BLOB に JSON 配列が含まれ、配列の各要素を個別のドキュメントとして扱う必要がある場合)。

正規化された画像の最大幅と最大高さの既定値 (2000 ピクセル) は、 [OCR スキル](cognitive-search-skill-ocr.md)と[画像分析スキル](cognitive-search-skill-image-analysis.md)でサポートされる最大サイズに基づいています。 最大の制限値を引き上げると、より大きい画像で処理が失敗する可能性があります。


ImageAction は、[インデクサー定義](https://docs.microsoft.com/rest/api/searchservice/create-indexer)で次のように指定します。

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":
  {
    "configuration": 
    {
        "dataToExtract": "contentAndMetadata",
        "imageAction": "generateNormalizedImages"
    }
  }
}
```

*imageAction* を "none" 以外の値に設定すると、新しい *normalized_images* フィールドに画像の配列が格納されます。 各画像は、次のメンバーを含んだ複合型になります。

| 画像のメンバー       | 説明                             |
|--------------------|-----------------------------------------|
| data               | JPEG 形式の正規化画像を BASE64 でエンコードした文字列。   |
| width              | 正規化画像の幅 (ピクセル単位)。 |
| height             | 正規化画像の高さ (ピクセル単位)。 |
| originalWidth      | 正規化する前の、画像の元の幅。 |
| originalHeight      | 正規化する前の、画像の元の高さ。 |
| rotationFromOriginal |  正規化された画像の作成時に発生した、反時計回りの回転角度。 0 ～ 360 度の値になります。 このステップでは、カメラやスキャナーで生成された画像からのメタデータが読み取られます。 通常は、90 度の倍数になります。 |
| contentOffset |画像が抽出された位置の、content フィールド内での文字オフセット。 このフィールドは、埋め込み画像があるファイルにのみ適用されます。 |

 *normalized_images* のサンプル値:
```json
[
  {
    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
    "width": 500,
    "height": 300,
    "originalWidth": 5000,  
    "originalHeight": 3000,
    "rotationFromOriginal": 90,
    "contentOffset": 500  
  }
]
```

## <a name="image-related-skills"></a>画像関連のスキル

画像を入力として取得するコグニティブ スキルは、組み込みで 2 つ用意されています:[OCR](cognitive-search-skill-ocr.md) と[画像分析](cognitive-search-skill-image-analysis.md)です。 

現在のところ、これらのスキルは、ドキュメント クラッキング ステップから生成された画像に対してのみ機能します。 そのため、サポートされている入力は `"/document/normalized_images"` のみです。

### <a name="image-analysis-skill"></a>画像分析スキル

[画像分析スキル](cognitive-search-skill-image-analysis.md)では､画像の内容に基づいて、さまざまな視覚的特徴のセットを抽出できます。 たとえば､画像からキャプションを生成したり､タグを生成したり､セレブリティやランドマークを特定したりできます。

### <a name="ocr-skill"></a>OCR スキル

[OCR スキル](cognitive-search-skill-ocr.md)では、JPG、PNG、ビットマップなどの画像ファイルからテキストを抽出できます。 テキストのほかに、レイアウト情報を抽出することもできます。 このレイアウト情報によって、特定された各文字列の境界ボックスが提供されます。

## <a name="embedded-image-scenario"></a>埋め込み画像のシナリオ

一般的なシナリオとして挙げられるのは、すべてのファイル コンテンツ (テキストと画像由来テキストの両方) を含んだ、1 つの文字列を作成するケースです。これは次の手順で実行されます。  

1. [Normalized_images を抽出します](#get-normalized-images)
1. `"/document/normalized_images"` を入力として使用し、OCR スキルを実行します
1. それらの画像のテキスト表現を、ファイルから抽出された未加工のテキストとマージします。 [テキスト マージ](cognitive-search-skill-textmerger.md)スキルを使用することで、両方のテキスト チャンクを 1 つの大きな文字列に統合できます。

次に示すのは、ドキュメントのテキスト コンテンツを含んだ、*merged_text* フィールドを作成するスキルセットの例です。 これには、各埋め込み画像から OCR 処理されたテキストも含まれています。 

#### <a name="request-body-syntax"></a>要求本文の構文
```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
        "description": "Extract text (plain and structured) from image.",
        "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
        "context": "/document/normalized_images/*",
        "defaultLanguageCode": "en",
        "detectOrientation": true,
        "inputs": [
          {
            "name": "image",
            "source": "/document/normalized_images/*"
          }
        ],
        "outputs": [
          {
            "name": "text"
          }
        ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```

merged_text フィールドが作成されたら、それを検索可能フィールドとしてインデクサー定義内にマップできます。 ファイルのすべてのコンテンツ (画像のテキストを含む) が検索可能になります。

## <a name="visualize-bounding-boxes-of-extracted-text"></a>抽出されたテキストの境界ボックスの視覚化

もう 1 つの一般的なシナリオとして、検索結果のレイアウト情報を視覚化するケースが挙げられます。 たとえば、特定のテキストが画像内のどこで検出されたかを、検索結果の一部として強調表示することができます。

OCR ステップは正規化された画像に対して実行されるため、レイアウト座標は正規化された画像領域上のものになります。 正規化された画像を表示する場合、座標の存在は通常問題にはなりませんが、状況によっては、元の画像を表示する必要性が生じることもあるでしょう。 その場合は、レイアウト内の各座標点を、元の画像の座標系に変換してください。 

正規化された座標を元の座標空間にに変換する必要がある場合は、次のアルゴリズムを使用できます。

```csharp
        /// <summary>
        ///  Converts a point in the normalized coordinate space to the original coordinate space.
        ///  This method assumes the rotation angles are multiples of 90 degrees.
        /// </summary>
        public static Point GetOriginalCoordinates(Point normalized,
                                    int originalWidth,
                                    int originalHeight,
                                    int width,
                                    int height,
                                    double rotationFromOriginal)
        {
            Point original = new Point();
            double angle = rotationFromOriginal % 360;

            if (angle == 0 )
            {
                original.X = normalized.X;
                original.Y = normalized.Y;
            } else if (angle == 90)
            {
                original.X = normalized.Y;
                original.Y = (width - normalized.X);
            } else if (angle == 180)
            {
                original.X = (width -  normalized.X);
                original.Y = (height - normalized.Y);
            } else if (angle == 270)
            {
                original.X = height - normalized.Y;
                original.Y = normalized.X;
            }

            double scalingFactor = (angle % 180 == 0) ? originalHeight / height : originalHeight / width;
            original.X = (int) (original.X * scalingFactor);
            original.Y = (int)(original.Y * scalingFactor);

            return original;
        }
```

## <a name="see-also"></a>関連項目
+ [インデクサーの作成 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
+ [画像分析スキル](cognitive-search-skill-image-analysis.md)
+ [OCR スキル](cognitive-search-skill-ocr.md)
+ [テキスト マージ スキル](cognitive-search-skill-textmerger.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [エンリッチされたフィールドをマップする方法](cognitive-search-output-field-mapping.md)
