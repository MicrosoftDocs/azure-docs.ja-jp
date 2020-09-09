---
title: レシート - Form Recognizer
titleSuffix: Azure Cognitive Services
description: Form Recognizer API を使用したレシート分析に関連する概念 (使用法と制限) について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: fd0a782fc0c54cf14db9cac07712dea6d8f2e523
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2020
ms.locfileid: "88751973"
---
# <a name="receipt-concepts"></a>レシートの概念

Azure Form Recognizer では、その事前構築済みモデルの 1 つを使用してレシートを分析できます。 レシート API では、英語で書かれたレシートから重要な情報 (マーチャント名、取引日時、合計金額、品目など) を抽出します。 

## <a name="understanding-receipts"></a>レシートについて 

企業や個人の多くは、事業経費の報告、払い戻し、監査、税金、予算、マーケティング、またはその他の目的にかかわらず、依然としてレシートからデータを手動で抽出する方法に頼っています。 多くの場合、こうしたシナリオでは、検証のために物理的なレシートの画像が必要になります。  

これらのレシートからデータを自動的に抽出する処理は、一筋縄ではいかない可能性があります。 レシートがしわくちゃになっていて読み取るのが難しかったり、レシートの印刷または手書き部分やスマートフォン画像の品質が低かったりする場合があります。 また、レシートのテンプレートとフィールドは、市場、リージョン、マーチャントなどによって大きく変わることがあります。 データ抽出とフィールド検出の両方におけるこうした課題によって、レシート処理は独特の問題となっています。  

光学式文字認識 (OCR) と事前構築済みレシート モデルを使用することにより、レシート API ではこうしたレシート処理シナリオが可能になり、レシートからマーチャント、チップ、合計、品目などのデータを抽出できます。 この API を使用すれば、モデルのトレーニングは不要となり、レシートを Analyze Receipt API に送信するだけで、データが抽出されます。

![レシートの例](./media/contoso-receipt-small.png)

## <a name="what-does-the-receipt-api-do"></a>レシート API の機能 

事前構築済みのレシート API では、レシート (レストラン、小売業者、または食料品店で通常取得されるレシートの類) の内容を抽出します。

### <a name="fields-extracted"></a>抽出されるフィールド

* マーチャント名 
* マーチャントの住所 
* マーチャントの電話番号 
* トランザクション日時 
* トランザクション時間 
* 小計 
* 税 
* 合計 
* ヒント 
* 品目の抽出 (品目数、品目の価格、品目名など)

### <a name="additional-features"></a>その他の機能

レシート API では、次の情報も返されます。

* レシートの種類 (明細書、クレジット カードなど)
* フィールドの信頼度 (各フィールドから関連付けられた信頼度の値が返されます)
* OCR の未加工のテキスト (レシート全体の OCR 抽出テキスト出力)
* 値、行、単語ごとの境界ボックス

## <a name="input-requirements"></a>入力の要件

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>サポート対象のロケール 

* **事前構築済みレシート v2.0** (GA) では、EN-US ロケールでのレシートがサポートされています
* **事前構築済みレシート v2.1-preview.1** (パブリック プレビュー) では、次の EN レシート ロケールのサポートが追加されています。 
  * EN-AU 
  * EN-CA 
  * EN-GB 
  * EN-IN 

  > [!NOTE]
  > 言語の入力 
  >
  > 事前構築済みレシート v2.1-preview.1 には、追加の英語圏市場のレシート ロケールを指定するための省略可能な要求パラメーターがあります。 オーストラリア (EN-AU)、カナダ (EN-CA)、英国 (EN-GB)、インド (EN-IN) の英語で書かれた各レシートについては、ロケールを指定して、改善された結果を得ることができます。 v2.1-preview.1 でロケールが指定されていない場合、このモデルは既定で EN-US モデルになります。
  
 ### <a name="input-requirements"></a>入力要件 

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-receipt-operation"></a>Analyze Receipt の操作

[Analyze Receipt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeReceiptAsync) では、レシートの画像または PDF を入力として受け取り、目的の値やテキストを抽出します。 この呼び出しにより、`Operation-Location` という応答ヘッダー フィールドが返されます。 `Operation-Location` 値は、次の手順で使用される結果 ID を含む URL です。

|応答ヘッダー| 結果の URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/56a36454-fc4d-4354-aa07-880cfbf0064f` |

## <a name="the-get-analyze-receipt-result-operation"></a>Get Analyze Receipt Result の操作

2 番目の手順では、[Get Analyze Receipt Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/GetAnalyzeReceiptResult) 操作を呼び出します。 この操作では、Analyze Receipt 操作によって作成された結果 ID を入力として受け取ります。 これにより、次の設定可能な値を持つ **status** フィールドが含まれた JSON 応答が返されます。 **succeeded** の値が返されるまで、この操作を対話形式で呼び出します。 1 秒あたりの要求数 (RPS) を超えないようにするために、間隔は 3 - 5 秒あけてください。

|フィールド| 型 | 設定可能な値 |
|:-----|:----:|:----|
|status | string | notStarted: 分析操作は開始されていません。 |
| |  | running: 分析操作が進行中です。 |
| |  | failed: 分析操作は失敗しました。 |
| |  | succeeded: 分析操作は成功しました。 |

**status** フィールドの値が **succeeded** の場合、JSON 応答にはレシート解釈とテキスト認識の結果が含まれます。 レシート解釈の結果は名前付きフィールド値のディクショナリとして編成され、各値には、抽出されたテキスト、正規化された値、境界ボックス、信頼および対応する単語要素が含まれます。 テキスト認識の結果は行と単語の階層として編成され、テキスト、境界ボックス、信頼情報が含まれます。

![レシート結果の例](./media/contoso-receipt-2-information.png)

### <a name="sample-json-output"></a>サンプル JSON 出力

成功した JSON 応答の例を次に示します。"readResults" ノードには、認識されたすべてのテキストが格納されます。 テキストは、まずページごとに整理され、そのうえで行ごと、さらに個々の単語ごとに整理されます。 "documentResults" ノードには、モデルによって検出された名刺固有の値が格納されます。 名、姓、会社名など、大切なキーと値のペアが存在する場所です。

```json
{ 
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{ 
    "version":"2.0.0",
    "readResults":[ 
      { 
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[ 
          { 
            "text":"Contoso",
            "boundingBox":[ 
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[ 
              { 
                "text":"Contoso",
                "boundingBox":[ 
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[ 
      { 
        "docType":"prebuilt:receipt",
        "pageRange":[ 
          1,
          1
        ],
        "fields":{ 
          "ReceiptType":{ 
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{ 
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[ 
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[ 
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{ 
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[ 
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{ 
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[ 
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{ 
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[ 
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{ 
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[ 
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[ 
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{ 
            "type":"array",
            "valueArray":[ 
              { 
                "type":"object",
                "valueObject":{ 
                  "Quantity":{ 
                    "type":"number",
                    "text":"1",
                    "boundingBox":[ 
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[ 
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{ 
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[ 
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[ 
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{ 
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[ 
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[ 
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{ 
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[ 
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[ 
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{ 
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[ 
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[ 
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{ 
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[ 
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[ 
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{ 
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[ 
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[ 
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```


## <a name="customer-scenarios"></a>顧客シナリオ  

レシート API で抽出されたデータは、さまざまな作業を行うために使用できます。 次に、顧客がレシート API を使用して行った作業の例をいくつかご紹介します。 

### <a name="business-expense-reporting"></a>事業経費の報告  

事業経費のファイリングでは、多くの場合、レシートの画像からデータを手動で入力することに時間を費やす必要があります。 レシート API があれば、抽出されたフィールドを使用して、このプロセスを部分的に自動化し、レシートをすばやく分析することができます。  

レシート API には単純な JSON 出力があるため、抽出されたフィールド値をさまざまな方法で使用できます。 社内経費アプリケーションと統合すると、経費報告書に自動的に入力できます。 このシナリオの詳細については、Acumatica 社がレシート API を活用して[経費報告を苦労の少ないプロセスにしている](https://customers.microsoft.com/en-us/story/762684-acumatica-partner-professional-services-azure)方法に関するページをご覧ください。  

### <a name="auditing-and-accounting"></a>監査と会計 

レシート API の出力を使用すると、経費報告および払い戻しプロセスのさまざまな時点で多数の経費について分析を行うこともできます。 手動による監査または迅速な承認のために、レシートを処理して選別することができます。  

また、レシートの出力はビジネスまたは個人用の一般簿記にも役立ちます。 レシート API を使用すると、未加工のレシート画像/PDF データを実行可能なデジタル出力に変換できます。

### <a name="consumer-behavior"></a>消費者の行動 

レシートには、消費者の行動やショッピングの傾向を分析するために使用できる有益なデータが含まれています。

また、レシート API は [AI Builder の領収書処理機能](https://docs.microsoft.com/ai-builder/prebuilt-receipt-processing)にも採用されています。

## <a name="next-steps"></a>次のステップ

- クイックスタートに従って、[Python でのレシート API のクイックスタート](./quickstarts/python-receipts.md)を開始します。
- [Form Recognizer REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer/api) について学習します。
- [Form Recognizer](overview.md) の詳細を確認します。

