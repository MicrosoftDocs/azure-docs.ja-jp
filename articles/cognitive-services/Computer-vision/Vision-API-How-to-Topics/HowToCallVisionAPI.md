---
title: 例:画像分析 API を呼び出す - Computer Vision
titlesuffix: Azure Cognitive Services
description: Azure Cognitive Services で REST を使用して Computer Vision API を呼び出す方法について説明します。
services: cognitive-services
author: kellydf
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 03/21/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 0e2767660edf2a9dbcb8617b07a6b9f71fedb743
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60011245"
---
# <a name="example-how-to-call-the-computer-vision-api"></a>例:Computer Vision API を呼び出す方法

このガイドでは、REST を使用して Computer Vision API を呼び出す方法を示します。 サンプルは、Computer Vision API クライアント ライブラリを使用する C# と HTTP POST/GET 呼び出しの両方で記述されています。 以下に焦点を当てます。

- "タグ"、"説明"、および "カテゴリ" の取得方法。
- "ドメイン固有" 情報 (有名人) の取得方法。

## <a name="prerequisites"></a>前提条件

- ローカルに保存された画像への画像 URL またはパス。
- サポートされる入力方法:アプリケーション/オクテット ストリーム形式の未加工画像バイナリまたは画像 URL
- サポートされる画像形式: JPEG、PNG、GIF、BMP
- 画像ファイルのサイズ:4 MB 未満
- 画像サイズ: 50 x 50 ピクセルよりも大きい
  
以下の例では、次の機能が示されています。

1. 画像の分析と、返されるタグの配列と説明の取得。
2. ドメイン固有モデル (具体的には "有名人" モデル) を使用した画像の分析と、JSON 内の対応する結果の再調整。

機能は次のように分類されます。

- **オプション 1:** スコープされた分析 - 特定のモデルのみを分析する
- **オプション 2:** 高度な分析 - 追加情報を提供するために、[86 カテゴリ分類](../Category-Taxonomy.md)を使用して分析する
  
## <a name="authorize-the-api-call"></a>API 呼び出しを承認する

Computer Vision API への呼び出しでは、毎回サブスクリプション キーが必要です。 このキーは、クエリ文字列パラメーターによって渡すか、要求ヘッダー内で指定する必要があります。

無料試用版のキーを取得するには、「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)」を参照してください。 または、[Cognitive Services アカウントの作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)に関するページの手順に従って、Computer Vision をサブスクライブし、キーを取得します。

1. クエリ文字列を使用してサブスクリプション キーを渡す場合は、次の Computer Vision API の例を参照してください。

```https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>```

1. サブスクリプション キーは HTTP 要求ヘッダーで渡すこともできます。

```ocp-apim-subscription-key: <Your subscription key>```

1. クライアント ライブラリを使用する場合、サブスクリプション キーは VisionServiceClient のコンストラクターを介して渡されます。

```var visionClient = new VisionServiceClient("Your subscriptionKey");```

## <a name="upload-an-image-to-the-computer-vision-api-service-and-get-back-tags-descriptions-and-celebrities"></a>画像を Computer Vision API サービスにアップロードし、タグ、説明、有名人を取得する

Computer Vision API の呼び出しを実行する基本的な方法は、画像を直接アップロードすることです。 これを行うには、コンテンツの種類を application/octet-stream と指定し、同時にデータの読み取りは画像ファイルからであることを指定して、"POST" 要求を送信します。 "タグ" と "説明" については、すべての Computer Vision API 呼び出しでこのアップロード方法と同じです。 唯一の違いは、ユーザーが指定するクエリ パラメーターです。 

特定の画像の "タグ" と "説明" を取得する方法を次に示します。

**オプション 1:** "タグ" の一覧と 1 つの "説明" を取得する

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```

```csharp
using Microsoft.ProjectOxford.Vision;
using Microsoft.ProjectOxford.Vision.Contract;
using System.IO;

AnalysisResult analysisResult;
var features = new VisualFeature[] { VisualFeature.Tags, VisualFeature.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.AnalyzeImageAsync(fs, features);
}
```

**オプション 2:** "タグ" の一覧のみ、または "説明" の一覧のみを取得する

###### <a name="tags-only"></a>タグのみ: 

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/tag&subscription-key=<Your subscription key>
var analysisResult = await visionClient.GetTagsAsync("http://contoso.com/example.jpg");
```

###### <a name="description-only"></a>説明のみ: 

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/describe&subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.DescribeAsync(fs);
}
```

### <a name="get-domain-specific-analysis-celebrities"></a>ドメイン固有の分析 (有名人) を取得する

**オプション 1:** スコープされた分析 - 特定のモデルのみを分析する
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

このオプションでは、他のすべてのクエリ パラメーター {visualFeatures, details} は有効ではありません。 サポートされているすべてのモデルを表示する場合は、次のコマンドを使用します。

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.0/models 
var models = await visionClient.ListModelsAsync();
```

**オプション 2:** 高度な分析 - 追加情報を提供するために、[86 カテゴリ分類](../Category-Taxonomy.md)を使用して分析する

1 つ以上のドメイン固有モデルからの詳細に加え、汎用的な画像分析を取得するアプリケーションでは、v1 API をモデルのクエリ パラメーターを使用して拡張します。

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?details=celebrities
```

このメソッドが呼び出されると、86 カテゴリ分類子が最初に呼び出されます。 いずれかのカテゴリが既知/一致モデルのカテゴリと一致した場合、分類子呼び出しの 2 番目のパスが発生します。 たとえば、"details=all" または "details" に ‘celebrities’ が含まれている場合は、86 カテゴリ分類子の後に有名人モデルが呼び出され、そのカテゴリに属する人物が結果に含まれます。 これは、オプション 1 に比べて、有名人に関心があるユーザーにとって待機時間が増加します。

ここでは、v1 のすべてのクエリ パラメーターは同じように動作します。  visualFeatures=categories が指定されない場合は、暗黙的に有効になります。

## <a name="retrieve-and-understand-the-json-output-for-analysis"></a>分析の JSON 出力を取得して解釈する

次に例を示します。

```json
{  
  "tags":[  
    {  
      "name":"outdoor",
      "score":0.976
    },
    {  
      "name":"bird",
      "score":0.95
    }
  ],
  "description":{  
    "tags":[  
      "outdoor",
      "bird"
    ],
    "captions":[  
      {  
        "text":"partridge in a pear tree",
        "confidence":0.96
      }
    ]
  }
}
```

フィールド | Type | コンテンツ
------|------|------|
Tags  | `object` | tags 配列の最上位オブジェクト
tags[].Name | `string`  | タグ分類子からのキーワード
tags[].Score    | `number`  | 信頼度スコア (0 と 1 の間)。
description  | `object` | description の最上位オブジェクト。
description.tags[] |    `string`    | タグの一覧。  キャプションを生成する機能で信頼度が不足している場合、おそらく呼び出し元が入手できる情報はタグのみになります。
description.captions[].text | `string`  | 画像を説明する語句。
description.captions[].confidence   | `number`  | 語句の信頼度。

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>ドメイン固有モデルの JSON 出力を取得して解釈する

**オプション 1:** スコープされた分析 - 特定のモデルのみを分析する

出力はタグの配列であり、その例は次の例のようになります。

```json
{  
  "result":[  
    {  
      "name":"golden retriever",
      "score":0.98
    },
    {  
      "name":"Labrador retriever",
      "score":0.78
    }
  ]
}
```

**オプション 2:** 高度な分析 - 追加情報を提供するために、86 カテゴリ分類を使用して分析する

オプション 2 (高度な分析) を使用するドメイン固有モデルでは、カテゴリの戻り値の型が拡張されます。 例を次に示します。

```json
{  
  "requestId":"87e44580-925a-49c8-b661-d1c54d1b83b5",
  "metadata":{  
    "width":640,
    "height":430,
    "format":"Jpeg"
  },
  "result":{  
    "celebrities":[  
      {  
        "name":"Richard Nixon",
        "faceRectangle":{  
          "left":107,
          "top":98,
          "width":165,
          "height":165
        },
        "confidence":0.9999827
      }
    ]
  }
}
```

categories フィールドは、元の分類内の[86 カテゴリ](../Category-Taxonomy.md)の 1 つまたは複数の一覧です。 アンダー スコアで終わるカテゴリは、そのカテゴリとその子 (たとえば、有名人モデルでは people_ だけでなく people_group も) 一致することにも注意してください。

フィールド   | Type  | コンテンツ
------|------|------|
categories | `object`   | 最上位レベルのオブジェクト
categories[].name    | `string` | 86カテゴリ分類の名前
categories[].score  | `number`  | 信頼度スコア (0 と 1 の間)。
categories[].detail  | `object?`      | 省略可能な詳細オブジェクト

複数のカテゴリと一致する場合 (たとえば、model=celebrities のときに 86 カテゴリ分類子がpeople_ と people_young を返す場合)、詳細は、最も汎用性が高いレベルの一致 (この例では people_) に添付されます。

## <a name="errors-responses"></a>エラー応答

これらは vision.analyze と同一ですが、NotSupportedModel エラー (HTTP 400) が追加されています。これは、オプション 1 とオプション 2 のどちらのシナリオでも返される可能性があります。 オプション 2 (高度な分析) では、詳細に指定されたモデルのいずれかが認識されない場合は、有効なモデルが 1 つ以上ある場合でも、API は NotSupportedModel を返します。  ユーザーは、listModels を呼び出して、サポートされているモデルを確認できます。

## <a name="next-steps"></a>次の手順

REST API を使用するには、[Computer Vision API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)を参照してください。
