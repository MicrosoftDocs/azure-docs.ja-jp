---
title: Python で REST 呼び出しを使用して意図を取得する
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/03/2020
ms.openlocfilehash: bc266cba20e72edea54a71028dc98b75dbd77cd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91545638"
---
[リファレンス ドキュメント](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c08) | [サンプル](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/python-predict-with-rest/predict.py)

## <a name="prerequisites"></a>前提条件

* [Python 3.6](https://www.python.org/downloads/) 以降。
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-pizza-app"></a>Pizza アプリを作成する

[!INCLUDE [Create pizza app](get-started-get-intent-create-pizza-app.md)]

## <a name="get-intent-from-the-prediction-endpoint"></a>予測エンドポイントから意図を取得する

Python を使用して、[予測エンドポイント](https://aka.ms/luis-apim-v3-prediction)のクエリを実行し、予測結果を取得します。

1. このコード スニペットを `predict.py` というファイルにコピーします。

    [!code-python[Code snippet](~/cognitive-services-quickstart-code/python/LUIS/python-predict-with-rest/predict.py)]

1. `YOUR-` で始まる値を実際の値に置き換えます。

    |Information|目的|
    |--|--|
    |`YOUR-APP-ID`|アプリ ID。 LUIS ポータルのアプリの [アプリケーションの設定] ページにあります。
    |`YOUR-PREDICTION-KEY`|32 文字の予測キー。 LUIS ポータルのアプリの [Azure リソース] ページにあります。
    |`YOUR-PREDICTION-ENDPOINT`| 予測 URL エンドポイント。 LUIS ポータルのアプリの [Azure リソース] ページにあります。<br>たとえば、「 `https://westus.api.cognitive.microsoft.com/` 」のように入力します。|

1. `requests` 依存関係をインストールします。 `requests` ライブラリは、HTTP 要求を行うために使用されます。

    ```console
    pip install requests
    ```

1. 次のコンソール コマンドを使用してスクリプトを実行します。

    ```console
    python predict.py
    ```

1. JSON として返される予測応答を確認します。

    ```console
    {'query': 'I want two large pepperoni pizzas on thin crust please', 'prediction': {'topIntent': 'ModifyOrder', 'intents': {'ModifyOrder': {'score': 1.0}, 'None': {'score': 8.55e-09}, 'Greetings': {'score': 1.82222226e-09}, 'CancelOrder': {'score': 1.47272727e-09}, 'Confirmation': {'score': 9.8125e-10}}, 'entities': {'Order': [{'FullPizzaWithModifiers': [{'PizzaType': ['pepperoni pizzas'], 'Size': [['Large']], 'Quantity': [2], 'Crust': [['Thin']], '$instance': {'PizzaType': [{'type': 'PizzaType', 'text': 'pepperoni pizzas', 'startIndex': 17, 'length': 16, 'score': 0.9978157, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}], 'Size': [{'type': 'SizeList', 'text': 'large', 'startIndex': 11, 'length': 5, 'score': 0.9984481, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}], 'Quantity': [{'type': 'builtin.number', 'text': 'two', 'startIndex': 7, 'length': 3, 'score': 0.999770939, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}], 'Crust': [{'type': 'CrustList', 'text': 'thin crust', 'startIndex': 37, 'length': 10, 'score': 0.933985531, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}]}}], '$instance': {'FullPizzaWithModifiers': [{'type': 'FullPizzaWithModifiers', 'text': 'two large pepperoni pizzas on thin crust', 'startIndex': 7, 'length': 40, 'score': 0.90681237, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}]}}], 'ToppingList': [['Pepperoni']], '$instance': {'Order': [{'type': 'Order', 'text': 'two large pepperoni pizzas on thin crust', 'startIndex': 7, 'length': 40, 'score': 0.9047088, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}], 'ToppingList': [{'type': 'ToppingList', 'text': 'pepperoni', 'startIndex': 17, 'length': 9, 'modelTypeId': 5, 'modelType': 'List Entity Extractor', 'recognitionSources': ['model']}]}}}}
    ```

    読みやすくするために書式設定された JSON 応答:

    ```JSON
    {
      'query': 'I want two large pepperoni pizzas on thin crust please',
      'prediction': {
        'topIntent': 'ModifyOrder',
        'intents': {
          'ModifyOrder': {
            'score': 1.0
          },
          'None': {
            'score': 8.55e-9
          },
          'Greetings': {
            'score': 1.82222226e-9
          },
          'CancelOrder': {
            'score': 1.47272727e-9
          },
          'Confirmation': {
            'score': 9.8125e-10
          }
        },
        'entities': {
          'Order': [
            {
              'FullPizzaWithModifiers': [
                {
                  'PizzaType': [
                    'pepperoni pizzas'
                  ],
                  'Size': [
                    [
                      'Large'
                    ]
                  ],
                  'Quantity': [
                    2
                  ],
                  'Crust': [
                    [
                      'Thin'
                    ]
                  ],
                  '$instance': {
                    'PizzaType': [
                      {
                        'type': 'PizzaType',
                        'text': 'pepperoni pizzas',
                        'startIndex': 17,
                        'length': 16,
                        'score': 0.9978157,
                        'modelTypeId': 1,
                        'modelType': 'Entity Extractor',
                        'recognitionSources': [
                          'model'
                        ]
                      }
                    ],
                    'Size': [
                      {
                        'type': 'SizeList',
                        'text': 'large',
                        'startIndex': 11,
                        'length': 5,
                        'score': 0.9984481,
                        'modelTypeId': 1,
                        'modelType': 'Entity Extractor',
                        'recognitionSources': [
                          'model'
                        ]
                      }
                    ],
                    'Quantity': [
                      {
                        'type': 'builtin.number',
                        'text': 'two',
                        'startIndex': 7,
                        'length': 3,
                        'score': 0.999770939,
                        'modelTypeId': 1,
                        'modelType': 'Entity Extractor',
                        'recognitionSources': [
                          'model'
                        ]
                      }
                    ],
                    'Crust': [
                      {
                        'type': 'CrustList',
                        'text': 'thin crust',
                        'startIndex': 37,
                        'length': 10,
                        'score': 0.933985531,
                        'modelTypeId': 1,
                        'modelType': 'Entity Extractor',
                        'recognitionSources': [
                          'model'
                        ]
                      }
                    ]
                  }
                }
              ],
              '$instance': {
                'FullPizzaWithModifiers': [
                  {
                    'type': 'FullPizzaWithModifiers',
                    'text': 'two large pepperoni pizzas on thin crust',
                    'startIndex': 7,
                    'length': 40,
                    'score': 0.90681237,
                    'modelTypeId': 1,
                    'modelType': 'Entity Extractor',
                    'recognitionSources': [
                      'model'
                    ]
                  }
                ]
              }
            }
          ],
          'ToppingList': [
            [
              'Pepperoni'
            ]
          ],
          '$instance': {
            'Order': [
              {
                'type': 'Order',
                'text': 'two large pepperoni pizzas on thin crust',
                'startIndex': 7,
                'length': 40,
                'score': 0.9047088,
                'modelTypeId': 1,
                'modelType': 'Entity Extractor',
                'recognitionSources': [
                  'model'
                ]
              }
            ],
            'ToppingList': [
              {
                'type': 'ToppingList',
                'text': 'pepperoni',
                'startIndex': 17,
                'length': 9,
                'modelTypeId': 5,
                'modelType': 'List Entity Extractor',
                'recognitionSources': [
                  'model'
                ]
              }
            ]
          }
        }
      }
    }
    ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートを使用して完了したときに、ファイル システムからファイルを削除します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [発話の追加とトレーニング](../get-started-get-model-rest-apis.md)