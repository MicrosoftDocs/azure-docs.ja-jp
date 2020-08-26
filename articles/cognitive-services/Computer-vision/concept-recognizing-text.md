---
title: 光学式文字認識 (OCR) - Computer Vision
titleSuffix: Azure Cognitive Services
description: 印刷や手書きのテキストが含まれる画像やドキュメントの、Computer Vision API を使用した光学式文字認識 (OCR) に関連する概念です。
services: cognitive-services
author: PatrickFarley
manager: netahw
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 9f9ebff77f54d86c3c4ed45fb5190de1900934e9
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88207224"
---
# <a name="optical-character-recognition-ocr"></a>光学式文字認識 (OCR)

Azure の Computer Vision API には、画像から印刷または手書きのテキストを抽出する光学式文字認識 (OCR) 機能が含まれています。 ナンバー プレートの写真やシリアル番号の付いたコンテナーなどの画像からも、ドキュメント (請求書、明細書、財務報告書、記事など) からもテキストを抽出することができます。 

## <a name="read-api"></a>Read API 

Computer Vision の [Read API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) は、印刷されたテキスト (複数の言語)、手書きのテキスト (英語のみ)、数字、通貨記号を、画像や複数ページの PDF ドキュメントから抽出する、Azure の最新の OCR テクノロジです。 これは、テキストの多い画像や、混合言語を含む複数ページの PDF ドキュメントからテキストを抽出するように最適化されています。 同じ画像またはドキュメントで、印刷と手書き両方のテキストの検出がサポートされています。

![OCR で画像やドキュメントからテキストを抽出して構造化された出力に変換するしくみ](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>入力の要件
Read API の**読み取り**操作では、イメージやドキュメントを入力値として受け取ります。 これには次の要件があります。

* サポートされているファイル形式: JPEG、PNG、BMP、PDF、TIFF
* PDF と TIFF については、最大 2,000 ページまで処理されます。 Free レベルのサブスクライバーの場合は、最初の 2 ページだけが処理されます。
* ファイル サイズは 50 MB 未満でなければならず、寸法は 50 x 50 ピクセル以上 10,000 x 10,000 ピクセル以下です。
* PDF の寸法は、17 x 17 インチ以下である必要があります (リーガル サイズまたは A3 サイズ以下の用紙に対応します)。

> [!NOTE]
> **言語の入力** 
>
> [読み取り操作](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005)には、言語に対する省略可能な要求パラメーターがあります。 これは、ドキュメント内のテキストの BCP-47 言語コードです。 読み取りでは、言語の自動識別と多言語ドキュメントがサポートされるため、言語コードの指定は、その特定の言語としてドキュメントを処理するように強制する場合にのみ行ってください。

## <a name="the-read-operation"></a>読み取り操作

[読み取り操作](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005)は、画像または PDF ドキュメントを入力として受け取り、非同期でテキストを抽出します。 この呼び出しにより、`Operation-Location` という応答ヘッダー フィールドが返されます。 `Operation-Location` 値は、次の手順で使用する操作 ID を含む URL です。

|応答ヘッダー| 結果の URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.0/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-read-results-operation"></a>読み取り結果の取得操作

2 番目の手順では、[読み取り結果の取得](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750)操作を呼び出します。 この操作は、読み取り操作によって作成された操作 ID を入力として受け取ります。 これにより、次の設定可能な値を持つ **status** フィールドが含まれた JSON 応答が返されます。 **succeeded** の値が返されるまで、この操作を対話形式で呼び出します。 1 秒あたりの要求 (RPS) レートを超えないようにするために、1 秒から 2 秒の間隔を使用してください。

|フィールド| 型 | 設定可能な値 |
|:-----|:----:|:----|
|status | string | notStarted: 操作は開始されていません。 |
| |  | running: 操作を処理しています。 |
| |  | failed: 操作は失敗しました。 |
| |  | succeeded: 操作は成功しました。 |

> [!NOTE]
> Free レベルでは、要求レートは 1 分あたり 20 回の呼び出しに制限されます。 有料レベルでは、10 RPS (1 秒あたりの要求) が許可されており、これはリクエストに応じて増加できます。 1 秒あたりの要求 (RPS) レートの引き上げをリクエストするには、Azure サポート チャネルまたはお客様のアカウント チームを使用してください。

**status** フィールドに **succeeded** 値が指定されている場合、JSON 応答には、画像またはドキュメントから抽出されたテキスト コンテンツが含まれます。 JSON の応答では、認識された単語の元の行グループが維持されます。 抽出されたテキスト行とその境界ボックスの座標が含まれます。 各テキスト行には、抽出されたすべての単語と、その座標および信頼度スコアが含まれています。

### <a name="sample-json-output"></a>サンプル JSON 出力

成功した JSON 応答の例を次に示します。

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 0.8551,
        "width": 2661,
        "height": 1901,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              67,
              646,
              2582,
              713,
              2580,
              876,
              67,
              821
            ],
            "text": "The quick brown fox jumps",
            "words": [
              {
                "boundingBox": [
                  143,
                  650,
                  435,
                  661,
                  436,
                  823,
                  144,
                  824
                ],
                "text": "The",
                "confidence": 0.958
              }
            ]
          }
        ]
      }
    ]
  }
}
```

C# と REST API を使用した OCR の実装については、[印刷されたテキストと手書きテキストの抽出](./QuickStarts/CSharp-hand-text.md)に関するクイックスタートに従ってください。

## <a name="language-support"></a>言語のサポート

### <a name="printed-text"></a>印刷されたテキスト
[Read 3.0 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) の印刷テキストの抽出でサポートされている言語は、英語、スペイン語、ドイツ語、フランス語、イタリア語、ポルトガル語、オランダ語です。 

[Read 3.1 API パブリック プレビュー](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005)では、簡体字中国語のサポートが追加されています。 お客様のシナリオでさらに多くの言語をサポートする必要がある場合は、「[OCR API](#ocr-api)」セクションを参照してください。 

OCR でサポートされている言語の完全な一覧については、[サポートされている言語](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr)に関する記事をご覧ください。

### <a name="handwritten-text"></a>手書きのテキスト
現在、読み取り操作の手書きテキストの抽出でサポートされているのは、英語だけです。

## <a name="integration-options"></a>統合オプション

### <a name="use-the-rest-api-or-client-sdk"></a>REST API またはクライアント SDK を使用する
[Read 3.x REST API](./QuickStarts/CSharp-hand-text.md) は、統合が簡単で、すぐに生産性を上げることができるため、ほとんどのお客様にとって推奨される選択肢です。 Azure と Computer Vision サービスがスケール、パフォーマンス、データ セキュリティ、コンプライアンスのニーズに対応する一方で、お客様は顧客のニーズを満たすことに集中できます。

### <a name="use-containers-for-on-premise-deployment"></a>オンプレミス デプロイにコンテナーを使用する
[Read 2.0 Docker コンテナー (プレビュー)](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers) を使用すると、独自のローカル環境に新しい OCR 機能をデプロイできます。 コンテナーは、特定のセキュリティ要件とデータ ガバナンス要件に適しています。

## <a name="read-ocr-examples"></a>OCR の例を読み取る

### <a name="text-from-images"></a>画像からのテキスト

次の Read API の出力は、さまざまなテキスト角度、色、フォントが含まれる、画像から抽出されたテキストを示しています。

![抽出されたテキストが一覧表示された、さまざまな色と角度を持つ複数の単語の画像](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>ドキュメントからのテキスト

Read API は、PDF ドキュメントを入力として取ることもできます。

![抽出されたテキストが一覧表示された請求書ドキュメント](./Images/text-from-pdf-example.png)

### <a name="handwritten-text"></a>手書きのテキスト

読み取り操作では、画像から手書きのテキストが抽出されます (現在は英語のみ)。

![抽出されたテキストが一覧表示された手書きのメモの画像](./Images/handwritten-example.png)

### <a name="printed-text"></a>印刷されたテキスト

読み取り操作では、複数の異なる言語で印刷されたテキストを抽出できます。

![抽出されたテキストが一覧表示されたスペイン語の教科書の画像](./Images/supported-languages-example.png)

### <a name="mixed-language-documents"></a>混合言語ドキュメント

Read API は、一般に混合言語ドキュメントと呼ばれる、複数の異なる言語を含む画像とドキュメントをサポートしています。 これは、テキスト コンテンツを抽出する前に、ドキュメント内の各テキスト行を検出された言語に分類することによって機能します。

![抽出されたテキストが一覧表示された、複数言語の語句の画像](./Images/mixed-language-example.png)

## <a name="ocr-api"></a>OCR API

[OCR API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) では、古い認識モデルが使用されており、画像のみがサポートされ、同期的に実行されて、検出されたテキストは直ちに返されます。 [OCR でサポートされている言語](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr)と Read API に関する記事をご覧ください。

## <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

Cognitive Services 全般に言えることですが、読み取り/OCR サービスを使用する開発者は、顧客データに関する Microsoft のポリシーに注意する必要があります。 詳細については、[Microsoft セキュリティ センター](https://www.microsoft.com/trust-center/product-overview)の Cognitive Services のページを参照してください。

> [!NOTE]
> Computer Vison 2.0 RecognizeText 操作は非推奨になる予定であり、この記事で取り上げている新しい Read API がその代わりになります。 既存顧客の皆様には、[読み取り操作をご利用いただくように](upgrade-api-versions.md)お願いします。

## <a name="next-steps"></a>次のステップ

- [Read 3.0 REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) について学習します。
- 簡体字中国語のサポートが追加された [Read 3.1 パブリック プレビュー REST API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005) について学習します。
- [テキストの抽出](./QuickStarts/CSharp-hand-text.md)に関するクイックスタートに従い、C#、Java、JavaScript、または Python と REST API を使用して、OCR を実装します。
