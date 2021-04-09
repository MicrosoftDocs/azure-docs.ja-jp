---
title: レシート - Form Recognizer
titleSuffix: Azure Cognitive Services
description: Form Recognizer API を使用したレシート分析に関連する概念 (使用法と制限) について説明します。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 81e8cd5cf4af8da76ae4eb09bed5a4ee0368da4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467359"
---
# <a name="form-recognizer-prebuilt-receipt-model"></a>Form Recognizer の事前構築済みレシート モデル

Azure Form Recognizer を使用すると、事前構築済みのレシート モデルを使用して、販売レシートから情報を分析して抽出できます。 強力な[光学式文字認識 (OCR)](../computer-vision/concept-recognizing-text.md) 機能と、ディープ ラーニング モデルを組み合わせて、英語で書かれたレシートから重要な情報を抽出します。

## <a name="understanding-receipts"></a>レシートについて

多くの企業と個人は、いまだにレシートから手動で抽出されたデータに依存しています。 これらのレシートからデータを自動的に抽出する処理は、複雑になる場合があります。 レシートはしわくちゃになっていたり、読み取るのが難しかったり、手書き部分があったり、低画質のスマートフォン画像が含まれていたりする場合があります。 また、レシートのテンプレートとフィールドは、市場、リージョン、マーチャントなどによって大きく変わることがあります。 データ抽出とフィールド検出におけるこうした課題によって、レシート処理は独特の問題となっています。  

レシート API は、光学式文字認識 (OCR) と、事前に構築された Microsoft のモデルを使用して、大量のレシート処理シナリオを可能にします。 レシート API を使用すると、モデルをトレーニングする必要がなくなります。 レシートの画像を Analyze Receipt API に送信すると、データが抽出されます。

![レシートの例](./media/receipts-example.jpg)


## <a name="what-does-the-receipt-service-do"></a>レシート サービスの機能 

事前構築済みのレシート サービスでは、レシート (レストラン、小売業者、または食料品店で通常取得されるレシートの類) の内容を抽出します。

### <a name="fields-extracted"></a>抽出されるフィールド

|名前| Type | 説明 | Text | 値 (標準化された出力) |
|:-----|:----|:----|:----| :----|
| ReceiptType | string | 販売レシートの種類 | Itemized |  |
| MerchantName | string | レシートを発行しているマーチャントの名前 | Contoso |  |
| MerchantPhoneNumber | phoneNumber | マーチャントの電話番号の一覧 | 987-654-3210 | +19876543210 |
| MerchantAddress | string | マーチャントの住所の一覧 | 123 Main St Redmond WA 98052 |  |
| TransactionDate | date | レシートが発行された日付 | June 06, 2019 | 2019-06-26  |
| TransactionTime | time | レシートが発行された時刻 | 4:49 PM | 16:49:00  |
| 合計 | 数値 | レシートの取引合計額 | $14.34 | 14.34 |
| 小計 | 数値 | レシートの小計 (多くの場合、税金が適用される前) | $12.34 | 12.34 |
| 税 | 数値 | レシートの税金 (多くの場合、消費税またはそれに相当する税金) | $2.00 | 2.00 |
| ヒント | 数値 | 購入者によって追加されたチップ | $1.00 | 1.00 |
| 項目 | オブジェクトの配列 | 抽出された品目 (名前、数量、単価、および合計価格) | |
| 名前 | string | 項目名 | Surface Pro 6 | |
| 数量 | 数値 | 各品目の数量 | 1 | |
| 価格 | 数値 | 各品目単位の個別価格 | $999.00 | 999.00 |
| 合計価格 | 数値 | 品目の合計価格 | $999.00 | 999.00 |

### <a name="additional-features"></a>その他の機能

レシート API では、次の情報も返されます。

* フィールドの信頼度 (各フィールドから関連付けられた信頼度の値が返されます)
* OCR の未加工のテキスト (レシート全体の OCR 抽出テキスト出力)
* 値、行、単語ごとの境界ボックス

## <a name="try-it-out"></a>試してみる

Form Recognizer レシート サービスを試すには、オンラインのサンプル UI ツールにアクセスしてください。

> [!div class="nextstepaction"]
> [事前構築済みモデルを試す](https://fott-preview.azurewebsites.net/)

## <a name="input-requirements"></a>入力の要件

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>サポート対象のロケール 

* **事前構築済みレシート v2.0** (GA) では、EN-US ロケールでのレシートがサポートされています
* **事前構築済みレシート v2.1-preview.3** (パブリック プレビュー) では、次の EN レシート ロケールのサポートが追加されています。 
  * EN-AU 
  * EN-CA 
  * EN-GB 
  * EN-IN 

  > [!NOTE]
  > 言語の入力 
  >
  > 事前構築済みレシート v2.1-preview.3 には、追加の英語圏市場のレシート ロケールを指定するための省略可能な要求パラメーターがあります。 オーストラリア (EN-AU)、カナダ (EN-CA)、英国 (EN-GB)、インド (EN-IN) の英語で書かれた各レシートについては、ロケールを指定して、改善された結果を得ることができます。 v2.1-preview.3 でロケールが指定されていない場合、このモデルは既定で EN-US モデルになります。


## <a name="the-analyze-receipt-operation"></a>Analyze Receipt の操作

[Analyze Receipt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeReceiptAsync) では、レシートの画像または PDF を入力として受け取り、目的の値やテキストを抽出します。 この呼び出しにより、`Operation-Location` という応答ヘッダー フィールドが返されます。 `Operation-Location` 値は、次の手順で使用される結果 ID を含む URL です。

|応答ヘッダー| 結果の URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/56a36454-fc4d-4354-aa07-880cfbf0064f` |

## <a name="the-get-analyze-receipt-result-operation"></a>Get Analyze Receipt Result の操作

2 番目の手順では、[Get Analyze Receipt Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeReceiptResult) 操作を呼び出します。 この操作では、Analyze Receipt 操作によって作成された結果 ID を入力として受け取ります。 これにより、次の設定可能な値を持つ **status** フィールドが含まれた JSON 応答が返されます。 **succeeded** の値が返されるまで、この操作を対話形式で呼び出します。 1 秒あたりの要求数 (RPS) を超えないようにするために、間隔は 3 - 5 秒あけてください。

|フィールド| 型 | 設定可能な値 |
|:-----|:----:|:----|
|status | string | notStarted: 操作は開始されていません。 |
| |  | running: 分析操作が進行中です。 |
| |  | failed: 分析操作は失敗しました。 |
| |  | succeeded: 分析操作は成功しました。 |

**status** フィールドの値が **succeeded** の場合、JSON 応答にはレシート解釈とテキスト認識の結果が含まれます。 レシート解釈の結果は名前付きフィールド値のディクショナリとして編成されます。 各値には、抽出されたテキスト、正規化された値、境界ボックス、信頼および対応する単語要素が含まれます。 テキスト認識の結果は行と単語の階層として編成され、テキスト、境界ボックス、信頼情報が含まれます。

![レシート結果の例](./media/contoso-receipt-2-information.png)

### <a name="sample-json-output"></a>サンプル JSON 出力


Get Analyze Receipt Result 操作への応答は、抽出されたすべての情報が含まれるレシートの構造化表現になります。  [サンプルのレシート ファイル](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-allinone.jpg)と、その構造化された出力である[サンプルのレシート出力](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/receipt-result.json)を参照してください。

成功した JSON 応答の例を次に示します。
* `"readResults"` ノードには、認識されたすべてのテキストが格納されます。 テキストは、まずページごとに整理され、そのうえで行ごと、さらに個々の単語ごとに整理されます。 
* `"documentResults"` ノードには、モデルによって検出された名刺固有の値が格納されます。 名、姓、会社名など、大切なキーと値のペアが存在する場所です。

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

レシート API は単純な JSON 出力であり、抽出されたフィールド値をさまざまな方法で使用できるようにします。 社内経費アプリケーションと統合すると、経費報告書に自動的に入力できます。 このシナリオの詳細については、Acumatica 社がレシート API を活用して[経費報告を苦労の少ないプロセスにしている](https://customers.microsoft.com/story/762684-acumatica-partner-professional-services-azure)方法に関するページをご覧ください。  

### <a name="auditing-and-accounting"></a>監査と会計

レシート API の出力を使用すると、経費報告および払い戻しプロセスのさまざまな時点で多数の経費について分析を行うこともできます。 手動による監査または迅速な承認のために、レシートを処理して選別することができます。  

また、レシートの出力はビジネスまたは個人用の一般簿記にも役立ちます。 レシート API を使用すると、未加工のレシート画像/PDF データを実行可能なデジタル出力に変換できます。

### <a name="consumer-behavior"></a>消費者の行動 

レシートには、消費者の行動やショッピングの傾向を分析するために使用できる有益なデータが含まれています。

また、レシート API は [AI Builder の領収書処理機能](/ai-builder/prebuilt-receipt-processing)にも採用されています。

## <a name="next-steps"></a>次のステップ

 選択した開発言語で Form Recognizer を使用してレシート処理アプリの作成を開始します。

> [!div class="nextstepaction"]
> [Form Recognizer のクイックスタートを完了する](quickstarts/client-library.md)

## <a name="see-also"></a>関連項目

* [Form Recognizer とは](overview.md)
* [Form Recognizer API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeReceiptAsync)
>
