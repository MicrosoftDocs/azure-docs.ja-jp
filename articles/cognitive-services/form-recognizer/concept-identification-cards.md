---
title: ID - Form Recognizer
titleSuffix: Azure Cognitive Services
description: Form Recognizer 事前構築済み ID API を使用した ID ドキュメントからのデータ抽出に関連する概念について説明します。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 01a73e7940f88a3eb6e040f26d255448294cab18
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467949"
---
# <a name="form-recognizer-prebuilt-identification-card-id-model"></a>Form Recognizer 事前構築済み ID カード (ID) モデル

Azure Form Recognizer により、事前構築済み ID モデルを使用して政府機関の ID カード (ID) の情報を分析し、抽出することができます。 強力な[光学式文字認識 (OCR)](../computer-vision/concept-recognizing-text.md) 機能と ID 認識機能を組み合わせて、世界各国のパスポートと米国の運転免許証 (50 州すべてとワシントン D.C.) から重要な情報を抽出することができます。 ID API により、これらの ID ドキュメントから、名、姓、生年月日、ドキュメント番号などの重要な情報が抽出されます。 この API は、Form Recognizer v2.1 プレビューで、クラウド サービスおよびオンプレミスのコンテナーとして使用できます。

## <a name="what-does-the-id-service-do"></a>ID サービスの機能 

事前構築済み ID サービスにより、世界各国のパスポートと米国の運転免許証から主要な値が抽出され、整理され、構造化された JSON 応答で返されます。 

![運転免許証のサンプル](./media/id-example-drivers-license.JPG)

![パスポートのサンプル](./media/id-example-passport-result.JPG)

### <a name="fields-extracted"></a>抽出されるフィールド

|名前| Type | 説明 | 値 | 
|:-----|:----|:----|:----|
|  国 | country | ISO 3166 標準に準拠した国番号 | "USA" | 
|  DateOfBirth | date | YYYY-MM-DD 形式の DOB | "1980-01-01" | 
|  DateOfExpiration | date | YYYY-MM-DD 形式の有効期限日 | "2019-05-05" |  
|  DocumentNumber | string | 関連するパスポート番号、運転免許証番号など | "340020013" |  
|  FirstName | string | 該当する場合は、抽出された名とミドルネームのイニシャル | "JENNIFER" | 
|  LastName | string | 抽出された姓 | "BROOKS" |   
|  Nationality | country | ISO 3166 標準に準拠した国番号 | "USA" |
|  Sex | gender | "M"、"F"、"X" という値が抽出される可能性があります | "F" | 
|  MachineReadableZone | object | それぞれ 44 文字の 2 行を含む抽出されたパスポート MRZ | "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816" |
|  DocumentType | string | ドキュメントの種類 (例: パスポート、運転免許証) | "passport" |  
|  Address | string | 抽出された住所 (運転免許証) | "123 STREET ADDRESS YOUR CITY WA 99999-1234"|
|  リージョン | string | 抽出された地域、州、都道府県など (運転免許証のみ) | "Washington" | 

### <a name="additional-features"></a>その他の機能

ID API からは次の情報も返されます。

* フィールドの信頼度 (各フィールドから関連付けられた信頼度の値が返されます)
* OCR の未加工のテキスト (レシート全体の OCR 抽出テキスト出力)
* 米国の運転免許証で抽出された各フィールドの境界ボックス
* パスポートの機械読み取り可能ゾーンの境界ボックス (MRZ)

  > [!NOTE]
  > 事前構築済み ID により、ID の信頼性は検出されません
  >
  > Form Recognizer 事前構築済み ID により、ID データから重要なデータが抽出されます。 ただし、元の ID ドキュメントの有効性や信頼性は検出されません。 

## <a name="try-it-out"></a>試してみる

Form Recognizer IDs サービスを試してみるには、オンラインのサンプル UI ツールにアクセスしてください。

> [!div class="nextstepaction"]
> [事前構築済みモデルを試す](https://fott-preview.azurewebsites.net/)

## <a name="input-requirements"></a>入力の要件

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-id-types"></a>サポートされる ID の種類  

* **Pre-built IDs v2.1-preview.3** 世界各国のパスポートと米国の運転免許証から主要な値が抽出されます。 

  > [!NOTE]
  > ID の種類のサポート 
  >
  > 現在サポートされている ID の種類として、世界各国のパスポートと米国の運転免許証があります。 Microsoft では、ID のサポートを世界各国の他の ID ドキュメントに拡張することを積極的に模索しています。

## <a name="post-analyze-id-document"></a>ID 分析ドキュメントの投稿

[ID 分析](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5f74a7daad1f2612c46f5822)操作に ID の画像または PDF を入力として渡すと、目的の値が抽出されます。 この呼び出しにより、`Operation-Location` という応答ヘッダー フィールドが返されます。 `Operation-Location` 値は、次の手順で使用される結果 ID を含む URL です。

|応答ヘッダー| 結果の URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="get-analyze-id-document-result"></a>ID 分析ドキュメント結果の取得

<!---
Need to update this with updated APIM links when available
-->

2 つ目の手順により、[**ID 分析ドキュメント結果の取得**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeFormResult)を呼び出します。 この操作には、ID 分析操作によって作成された結果 ID を入力として渡します。 これにより、次の設定可能な値を持つ **status** フィールドが含まれた JSON 応答が返されます。 **succeeded** の値が返されるまで、この操作を対話形式で呼び出します。 1 秒あたりの要求数 (RPS) を超えないようにするために、間隔は 3 - 5 秒あけてください。

|フィールド| 型 | 設定可能な値 |
|:-----|:----:|:----|
|status | string | notStarted: 分析操作は開始されていません。 |
| |  | running: 分析操作が進行中です。 |
| |  | failed: 分析操作は失敗しました。 |
| |  | succeeded: 分析操作は成功しました。 |

**status** フィールドの値が **succeeded** の場合、JSON 応答にはレシート解釈とテキスト認識の結果が含まれます。 ID の結果は名前付きフィールドの値の辞書として編成され、各値には、抽出されたテキスト、正規化された値、境界ボックス、信頼度、対応する単語要素が含まれます。 テキスト認識の結果は行と単語の階層として編成され、テキスト、境界ボックス、信頼情報が含まれます。

![レシート結果の例](./media/id-example-passport-result.JPG)

### <a name="sample-json-output"></a>サンプル JSON 出力

成功した JSON 応答の例を次に示します。`readResults` ノードには、認識されたすべてのテキストが格納されます。 テキストは、まずページごとに整理され、そのうえで行ごと、さらに個々の単語ごとに整理されます。 `documentResults` ノードには、モデルによって検出された ID 値が格納されます。 このノードには、名、姓、ドキュメント番号などの便利なキーと値のペアがあります。

```json
{ 
   "status": "succeeded",
  "createdDateTime": "2021-03-04T22:29:33Z",
  "lastUpdatedDateTime": "2021-03-04T22:29:36Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
     {
        "page": 1,
        "angle": 0.3183,
        "width": 549,
        "height": 387,
        "unit": "pixel",
        "lines": [
          {
            "text": "PASSPORT",
            "boundingBox": [
              57,
              10,
              120,
              11,
              119,
              22,
              57,
              22
            ],
            "words": [
              {
                "text": "PASSPORT",
                "boundingBox": [
                  57,
                  11,
                  119,
                  11,
                  118,
                  23,
                  57,
                  22
                ],
                "confidence": 0.994
              }
            ],
          ...
      }
    ],
    
     "documentResults": [
      {
        "docType": "prebuilt:idDocument:passport",
        "docTypeConfidence": 0.995,
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "Country": {
            "type": "country",
            "valueCountry": "USA",
            "text": "USA"
          },
          "DateOfBirth": {
            "type": "date",
            "valueDate": "1980-01-01",
            "text": "800101"
          },
          "DateOfExpiration": {
            "type": "date",
            "valueDate": "2019-05-05",
            "text": "190505"
          },
          "DocumentNumber": {
            "type": "string",
            "valueString": "340020013",
            "text": "340020013"
          },
          "FirstName": {
            "type": "string",
            "valueString": "JENNIFER",
            "text": "JENNIFER"
          },
          "LastName": {
            "type": "string",
            "valueString": "BROOKS",
            "text": "BROOKS"
          },
          "Nationality": {
            "type": "country",
            "valueCountry": "USA",
            "text": "USA"
          },
          "Sex": {
            "type": "gender",
            "valueGender": "F",
            "text": "F"
          },
          "MachineReadableZone": {
            "type": "object",
            "text": "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816",
            "boundingBox": [
              16,
              314.1,
              504.2,
              317,
              503.9,
              363,
              15.7,
              360.1
            ],
            "page": 1,
            "confidence": 0.384,
            "elements": [
              "#/readResults/0/lines/33/words/0",
              "#/readResults/0/lines/33/words/1",
              "#/readResults/0/lines/33/words/2",
              "#/readResults/0/lines/33/words/3",
              "#/readResults/0/lines/33/words/4",
              "#/readResults/0/lines/34/words/0"
            ]
          },
          "DocumentType": {
            "type": "string",
            "text": "passport",
            "confidence": 0.995
          }
        }
      }
    ]
  }
}
```


## <a name="next-steps"></a>次のステップ

- [Form Recognizer サンプル UI](https://fott-preview.azurewebsites.net/) で、独自の ID とサンプルを試してみてください。
- 選択した開発言語で Form Recognizer を使用して ID 処理アプリの作成を始めるには、[Form Recognizer のクイックスタート](quickstarts/client-library.md)を完了します。

## <a name="see-also"></a>関連項目

* [**Form Recognizer とは**](./overview.md)
* [**REST API リファレンス ドキュメント**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)
