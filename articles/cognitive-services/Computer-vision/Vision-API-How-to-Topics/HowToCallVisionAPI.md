---
title: Computer Vision API を呼び出す
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services で REST API を使用して Computer Vision API を呼び出す方法について説明します。
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 417ff7ac345b9a83b3d3f4c50e9fd141d74bc99c
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103542"
---
# <a name="call-the-computer-vision-api"></a>Computer Vision API を呼び出す

この記事では、REST API を使用して Computer Vision API を呼び出す方法を紹介します。 サンプルは、Computer Vision API クライアント ライブラリを使用する C# と HTTP POST (または GET) 呼び出しの両方で記述されています。 この記事では、次の内容を重点的に取り上げます。

- タグ、説明、カテゴリを取得する
- ドメイン固有情報 ("有名人") を取得する

## <a name="prerequisites"></a>前提条件

- ローカルに保存された画像の URL またはパス
- サポートされている入力方法: application/octet-stream 形式または画像 URL 形式の未加工画像バイナリ
- サポートされる画像形式: JPEG、PNG、GIF、BMP
- 画像ファイルのサイズ:4 MB 以下
- 画像サイズ: 50 &times; 50 ピクセル以上
  
この記事の例では、次の機能のデモンストレーションを行います。

* 画像を分析して、タグの配列と説明を取得する
* ドメイン固有モデル (具体的には "有名人" モデル) を使用して画像を分析し、対応する結果を JSON で取得する

これらの機能には、次のオプションがあります。

- **オプション 1**:スコープされた分析 - 特定のモデルのみを分析する
- **オプション 2**:高度な分析 - 追加情報を提供するために、[86 カテゴリ分類](../Category-Taxonomy.md)を使用して分析する
  
## <a name="authorize-the-api-call"></a>API 呼び出しを承認する

Computer Vision API への呼び出しでは、毎回サブスクリプション キーが必要です。 このキーは、クエリ文字列パラメーターを通じて渡すか、または要求ヘッダーで指定する必要があります。

無料試用版キーを入手するには、次のいずれかを実行します。
* 「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)」ページに移動します。 
* [Cognitive Services アカウントの作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)ページにアクセスして Computer Vision をサブスクライブします。

サブスクリプション キーは、次のいずれかの方法で渡すことができます。

* 次の Computer Vision API の例のように、クエリ文字列で渡します。

  ```
  https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
  ```

* HTTP 要求ヘッダーで指定します。

  ```
  ocp-apim-subscription-key: <Your subscription key>
  ```

* クライアント ライブラリを使用する場合、ComputerVisionClient のコンストラクターでキーを渡し、クライアントのプロパティでリージョンを指定します。

    ```
    var visionClient = new ComputerVisionClient(new ApiKeyServiceClientCredentials("Your subscriptionKey"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com"
    }
    ```

## <a name="upload-an-image-to-the-computer-vision-api-service"></a>Computer Vision API サービスに画像をアップロードする

Computer Vision API の呼び出しを実行する基本的な方法は、画像を直接アップロードして、タグ、説明、有名人を取得することです。 これを行うには、バイナリ画像を HTTP 本文に追加して、画像から読み取るデータと共に "POST" 要求を送信します。 アップロード方法は、すべての Computer Vision API 呼び出しで共通です。 唯一の違いは、指定するクエリ パラメーターです。 

特定の画像について、次のいずれかのオプションを使用してタグと説明を取得します。

### <a name="option-1-get-a-list-of-tags-and-a-description"></a>オプション 1:タグの一覧と説明を取得する

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```

```csharp
using System.IO;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

ImageAnalysis imageAnalysis;
var features = new VisualFeatureTypes[] { VisualFeatureTypes.Tags, VisualFeatureTypes.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageAnalysis = await visionClient.AnalyzeImageInStreamAsync(fs, features);
}
```

### <a name="option-2-get-a-list-of-tags-only-or-a-description-only"></a>オプション 2:タグの一覧のみまたは説明のみを取得する

タグのみの場合は、次を実行します。

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/tag?subscription-key=<Your subscription key>
var tagResults = await visionClient.TagImageAsync("http://contoso.com/example.jpg");
```

説明のみの場合は、次を実行します。

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/describe?subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageDescription = await visionClient.DescribeImageInStreamAsync(fs);
}
```

## <a name="get-domain-specific-analysis-celebrities"></a>ドメイン固有の分析 (有名人) を取得する

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>オプション 1:スコープされた分析 - 特定のモデルのみを分析する
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

このオプションでは、他のすべてのクエリ パラメーター {visualFeatures, details} は有効ではありません。 サポートされているすべてのモデルを表示する場合は、次のコマンドを使用します。

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.0/models 
var models = await visionClient.ListModelsAsync();
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-86-categories-taxonomy"></a>オプション 2:高度な分析 - 追加情報を提供するために、86 カテゴリ分類を使用して分析する

1 つ以上のドメイン固有モデルからの詳細に加え、汎用的な画像分析を取得するアプリケーションでは、モデルのクエリ パラメーターを使用して v1 API を拡張します。

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?details=celebrities
```

このメソッドを呼び出すときは、まず [86 カテゴリ](../Category-Taxonomy.md)分類子を呼び出します。 いずれかのカテゴリが既知の (対応する) モデルのカテゴリと一致した場合、分類子呼び出しの 2 番目のパスが発生します。 たとえば、"details=all" の場合、または "details" に "celebrities" が含まれている場合、86 カテゴリ分類子を呼び出した後に有名人モデルを呼び出します。 結果には、そのカテゴリに属する人物が含まれます。 有名人に関心があるユーザーにとって、この方法は、オプション 1 に比べて待ち時間が長くなります。

このケースでは、v1 のすべてのクエリ パラメーターが同じように動作します。 visualFeatures=categories を指定しなかった場合は、これが暗黙的に有効になります。

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

フィールド | 種類 | コンテンツ
------|------|------|
Tags  | `object` | タグの配列の最上位オブジェクト。
tags[].Name | `string`  | タグ分類子からのキーワード。
tags[].Score    | `number`  | 信頼度スコア (0 と 1 の間)。
description  | `object` | description の最上位オブジェクト。
description.tags[] |    `string`    | タグの一覧。  キャプションを生成する機能で信頼度が不足している場合、おそらく呼び出し元が入手できる情報はタグのみになります。
description.captions[].text | `string`  | 画像を説明する語句。
description.captions[].confidence   | `number`  | 語句の信頼度スコア。

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>ドメイン固有モデルの JSON 出力を取得して解釈する

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>オプション 1:スコープされた分析 - 特定のモデルのみを分析する

次の例に示すように、出力はタグの配列になります。

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

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-the-86-categories-taxonomy"></a>オプション 2:高度な分析 - 追加情報を提供するために、"86 カテゴリ" 分類を使用して分析する

オプション 2 (高度な分析) を使用するドメイン固有モデルでは、次の例に示したように、カテゴリの戻り値の型が拡張されます。

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

categories フィールドは、元の分類内の[86 カテゴリ](../Category-Taxonomy.md)の 1 つまたは複数の一覧です。 アンダースコアで終わるカテゴリは、そのカテゴリとその子 (たとえば、有名人モデルでは "people_" と "people_group") に一致します。

フィールド   | 種類  | コンテンツ
------|------|------|
categories | `object`   | 最上位レベルのオブジェクト。
categories[].name    | `string` | 86 カテゴリ分類リストからの名前。
categories[].score  | `number`  | 信頼度スコア (0 と 1 の間)。
categories[].detail  | `object?`      | (省略可能) 詳細オブジェクト。

複数のカテゴリと一致する場合 (たとえば、model=celebrities のときに 86 カテゴリ分類子が "people_" と "people_young" を返す場合)、詳細は、最も汎用性が高いレベルの一致 (この例では "people_") に添付されます。

## <a name="error-responses"></a>エラー応答

これらのエラーは vision.analyze のエラーと同一ですが、NotSupportedModel エラー (HTTP 400) が追加されています。これは、オプション 1 とオプション 2 のどちらのシナリオでも返される可能性があります。 オプション 2 (高度な分析) では、詳細に指定されたモデルのいずれかが認識されない場合は、有効なモデルが 1 つ以上ある場合でも、API は NotSupportedModel を返します。 サポートされているモデルは、listModels を呼び出すことで確認できます。

## <a name="next-steps"></a>次の手順

REST API を使用するには、[Computer Vision API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)を参照してください。
