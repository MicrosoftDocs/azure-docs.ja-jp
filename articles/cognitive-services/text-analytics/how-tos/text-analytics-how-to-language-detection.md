---
title: Text Analytics REST API を使用して言語を検出する
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services の Text Analytics REST API を使用して言語を検出します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: 5b3893dce2d20b1de0a78f11263d880e262098d2
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2020
ms.locfileid: "84142386"
---
# <a name="example-detect-language-with-text-analytics"></a>例:Text Analytics を使用して言語を検出する

Azure Text Analytics REST API の[言語検出](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)機能では、各ドキュメントのテキスト入力が評価され、言語識別子と、分析の強度を示すスコアが返されます。

この機能は、言語が不明な任意のテキストを取集するコンテンツ ストアに役立ちます。 この分析の結果を解析して、入力ドキュメントでいずれの言語が使用されるかを特定できます。 応答では、モデルの信頼度が反映されたスコアも返されます。 スコアの値は 0 から 1 の間です。

言語検出機能では、さまざまな言語、変異形、方言のほか、一部の地方言語や文化言語も検出できます。 この機能の厳密な言語の一覧は公開されていません。

使用頻度の低い言語で表されるコンテンツがある場合、言語検出機能を試して、コードが返されるかどうか確認できます。 検出できない言語の応答は `unknown` です。

> [!TIP]
> Text Analytics には言語検出用の Linux ベースの Docker コンテナー イメージも用意されているため、データの近くに [Text Analytics コンテナーをインストールして実行](text-analytics-how-to-install-containers.md)することができます。

## <a name="preparation"></a>準備

JSON ドキュメントは、次の形式である必要があります: ID とテキスト。

ドキュメントのサイズは、ドキュメントごとに 5120 文字未満でなければなりません。 コレクションごとに最大 1000 個の項目 (ID) を含めることができます。 コレクションは、要求の本文で送信されます。 次に示すのは、言語検出のために送信するコンテンツの例です。

```json
    {
        "documents": [
            {
                "id": "1",
                "text": "This document is in English."
            },
            {
                "id": "2",
                "text": "Este documento está en inglés."
            },
            {
                "id": "3",
                "text": "Ce document est en anglais."
            },
            {
                "id": "4",
                "text": "本文件为英文"
            },
            {
                "id": "5",
                "text": "Этот документ на английском языке."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>手順 1:要求を構造化する

要求定義の詳細については、[Text Analytics API の呼び出し](text-analytics-how-to-call-api.md)に関するページを参照してください。 確認に便利なように、以下に再度、要点を示します。

+ POST 要求を作成します。 この要求の API ドキュメントを確認するには、[言語検出 API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) に関するページを参照してください。

+ 言語検出のための HTTP エンドポイントを設定します。 Azure 上の Text Analytics リソースまたはインスタンス化された [Text Analytics コンテナー](text-analytics-how-to-install-containers.md)のいずれかを使用します。 URL に `/text/analytics/v3.0/languages` を含める必要があります (例: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages`)。

+ Text Analytics 操作用の[アクセス キー](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)が含まれるように要求ヘッダーを設定します。

+ 要求本文で、この分析のために準備した JSON ドキュメントのコレクションを提供します。

> [!Tip]
> [Postman](text-analytics-how-to-call-api.md) を使用するか、[ドキュメント](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)に記載されている **API テスト コンソール**を開き、要求を構造化して POST でサービスに投稿します。

## <a name="step-2-post-the-request"></a>手順 2:要求を投稿する

要求が受信されると分析が実行されます。 分単位および秒単位で送信できる要求のサイズと数については、概要の「[data limits (データ制限)](../overview.md#data-limits)」セクションを参照してください。

サービスはステートレスであることを思い出してください。 ユーザーのアカウントに保存されるデータはありません。 結果はすぐに、応答で返されます。


## <a name="step-3-view-the-results"></a>手順 3:結果の確認

すべての POST 要求で、ID と検出されたプロパティを含む JSON 形式の応答が返されます。

出力はすぐに返されます。 JSON を受け入れるアプリケーションに結果をストリーミングすることも、出力をローカル システム上のファイルに保存することもできます。 次に、データの並べ替え、検索、および操作に使用できるアプリケーション内に出力をインポートします。

要求の例の結果は、次の JSON のようになります。 複数の項目を含む 1 つのドキュメントであることに注意してください。 出力は英語です。 言語識別子には、[ISO 639-1](https://www.iso.org/standard/22109.html) 形式のフレンドリ名と言語コードが含まれます。

正のスコア 1.0 は、分析の可能な最も高い信頼レベルを表します。

```json
{
    "documents": [
        {
            "id": "1",
            "detectedLanguage": {
                "name": "English",
                "iso6391Name": "en",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "2",
            "detectedLanguage": {
                "name": "Spanish",
                "iso6391Name": "es",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "3",
            "detectedLanguage": {
                "name": "French",
                "iso6391Name": "fr",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "4",
            "detectedLanguage": {
                "name": "Chinese_Simplified",
                "iso6391Name": "zh_chs",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "5",
            "detectedLanguage": {
                "name": "Russian",
                "iso6391Name": "ru",
                "confidenceScore": 1.0
            },
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

### <a name="ambiguous-content"></a>あいまいなコンテンツ

場合によっては、入力に基づいて言語を明確に区別するのが困難なことがあります。 `countryHint` パラメーターを使用して、2 文字の国/地域コードを指定できます。 API の既定では、既定の countryHint として "US" が使用されます。この動作を削除するには、この値を空の文字列 `countryHint = ""` に設定して、このパラメーターをリセットします。

たとえば、英語とフランス語の両方に共通の "Impossible" が、限られたコンテキストと共に指定されている場合、応答は "US" の国/地域ヒントに基づきます。 テキストがフランスに由来することがわかっている場合は、それをヒントとして指定することができます。

**入力**

```json
    {
        "documents": [
            {
                "id": "1",
                "text": "impossible"
            },
            {
                "id": "2",
                "text": "impossible",
                "countryHint": "fr"
            }
        ]
    }
```

これで、サービスでより適切な判断を行うための追加のコンテキストが追加されました。 

**出力**

```json
    {
        "documents": [
            {
                "id": "1",
                "detectedLanguages": [
                    {
                        "name": "English",
                        "iso6391Name": "en",
                        "confidenceScore": 1
                    }
                ]
            },
            {
                "id": "2",
                "detectedLanguages": [
                    {
                        "name": "French",
                        "iso6391Name": "fr",
                        "confidenceScore": 1
                    }
                ]
            }
        ],
        "errors": []
    }
```

アナライザーで入力を解析できない場合は、`(Unknown)` が返されます。 たとえば、アラビア数字だけで構成されるテキストブロックを送信した場合です。

```json
    {
        "id": "5",
        "detectedLanguages": [
            {
                "name": "(Unknown)",
                "iso6391Name": "(Unknown)",
                "confidenceScore": "NaN"
            }
        ]
    }
```

### <a name="mixed-language-content"></a>混合言語コンテンツ

同じドキュメント内に混合言語のコンテンツが含まれている場合は、コンテンツの最大の表現で言語が返されますが、評価の肯定度は低くなります。 評価には、評価の限界強度が反映されます。 次の例では、入力は英語、スペイン語、フランス語の混在です。 アナライザーは各セグメント内の文字をカウントして、主要な言語を特定します。

**入力**

```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello, I would like to take a class at your University. ¿Se ofrecen clases en español? Es mi primera lengua y más fácil para escribir. Que diriez-vous des cours en français?"
        }
      ]
    }
```

**出力**

結果の出力は主要な言語で構成され、スコアは 1.0 未満となります。この数値が小さいほど、信頼度レベルは低くなります。

```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "confidencescore": 0.94
            }
          ]
        }
      ],
      "errors": []
    }
```

## <a name="summary"></a>まとめ

この記事では、Azure Cognitive Services の Text Analytics を使用した言語検出の概念とワークフローについて説明しました。 以下の点について説明し、例を示しました。

+ [言語検出](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)は、さまざまな言語、異形、方言、および一部の地方言語や文化言語でも使用できます。
+ 要求本文内の JSON ドキュメントには、ID とテキストが含まれます。
+ POST 要求は、ユーザーのサブスクリプションで有効な、個人用に設定された[アクセス キーとエンドポイント](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)を使用して `/languages` エンドポイントに対して行われます。
+ 応答出力は、各ドキュメント ID の言語識別子で構成されます。 出力は、JSON を受け入れる任意のアプリにストリーミングできます。 アプリの例としては、Excel や Power BI などがあります。

## <a name="see-also"></a>関連項目

* [Text Analytics の概要](../overview.md)
* [Text Analytics クライアント ライブラリの使用](../quickstarts/text-analytics-sdk.md)
* [新機能](../whats-new.md)
