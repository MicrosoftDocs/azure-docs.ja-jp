---
title: Text Analytics REST API を使用した言語の検出 | Microsoft Docs
description: Azure Cognitive Services の Text Analytics REST API を使用して言語を検出する方法。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 8197d091763709282c42379a7ca0ea802e5c6fdf
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2019
ms.locfileid: "56886790"
---
# <a name="example-how-to-detect-language-with-text-analytics"></a>例:Text Analytics で言語を検出する方法

[言語検出 API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) では、テキスト入力が評価され、各ドキュメントについて言語識別子と、分析の強度を示すスコアが返されます。 Text Analytics では、最大 120 の言語が認識されます。

この機能は、言語が不明な任意のテキストを取集するコンテンツ ストアに役立ちます。 この分析の結果を解析して、入力ドキュメントでいずれの言語が使用されるかを特定できます。 応答では、モデルの信頼度 (0 から 1 の値) が反映されたスコアも返されます。

> [!TIP]
> Text Analytics には言語検出用の Linux ベースの Docker コンテナー イメージも用意されているため、データの近くに [Text Analytics コンテナーをインストールして実行](text-analytics-how-to-install-containers.md)することができます。

## <a name="preparation"></a>準備

JSON ドキュメントは、id、text の形式である必要があります。

ドキュメントのサイズは、ドキュメントあたり 5,120 文字未満である必要があり、コレクションあたり最大 1,000 の項目 (ID) を含めることができます。 コレクションは、要求の本文で送信されます。 言語検出のために送信するコンテンツの例を次に示します。

   ```
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

要求定義の詳細については、[Text Analytics API を呼び出す方法](text-analytics-how-to-call-api.md)に関するページを参照してください。 確認に便利なように、以下に再度、要点を示します。

+ **POST** 要求を作成します。 この要求については次の API ドキュメントを確認してください。[Language Detection API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)

+ Azure 上の Text Analytics リソースまたはインスタンス化された [Text Analytics コンテナー](text-analytics-how-to-install-containers.md)を使用して、言語検出用の HTTP エンドポイントを設定します。 そこには、`/languages` リソースが含まれている必要があります: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages`

+ Text Analytics 操作用のアクセス キーが含まれるように要求ヘッダーを設定します。 詳細については、[エンドポイントを見つけてアクセス キーにアクセスする方法](text-analytics-how-to-access-key.md)についてのページを参照してください。

+ 要求本文で、この分析のために準備した JSON ドキュメントのコレクションを提供します。

> [!Tip]
> [Postman](text-analytics-how-to-call-api.md) を使用するか、[ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)に記載されている **API テスト コンソール**を開き、要求を構造化して POST でサービスに投稿します。

## <a name="step-2-post-the-request"></a>手順 2:要求を投稿する

要求が受信されると分析が実行されます。 サービスは、1 分あたり最大 100 個の要求を受け付けます。 各要求の最大サイズは 1 MB です。

サービスはステートレスであることを思い出してください。 ユーザーのアカウントに保存されるデータはありません。 結果はすぐに、応答で返されます。


## <a name="step-3-view-results"></a>手順 3:結果の表示

すべての POST 要求で、ID と検出されたプロパティを含む JSON 形式の応答が返されます。

出力はすぐに返されます。 結果は、JSON を受け付けるアプリケーションにストリームするか、ローカル システム上のファイルに出力を保存してから、そのファイルを、データの並べ替え、検索、および操作が可能なアプリケーションにインポートすることができます。

要求の例の結果は、次の JSON のようになります。 複数の項目を含む 1 つのドキュメントであることに注意してください。 出力は英語です。 言語識別子には、[ISO 639-1](https://www.iso.org/standard/22109.html) 形式のフレンドリ名と言語コードが含まれます。

正のスコア 1.0 は、分析の可能な最も高い信頼レベルを表します。



```
{
    "documents": [
        {
            "id": "1",
            "detectedLanguages": [
                {
                    "name": "English",
                    "iso6391Name": "en",
                    "score": 1
                }
            ]
        },
        {
            "id": "2",
            "detectedLanguages": [
                {
                    "name": "Spanish",
                    "iso6391Name": "es",
                    "score": 1
                }
            ]
        },
        {
            "id": "3",
            "detectedLanguages": [
                {
                    "name": "French",
                    "iso6391Name": "fr",
                    "score": 1
                }
            ]
        },
        {
            "id": "4",
            "detectedLanguages": [
                {
                    "name": "Chinese_Simplified",
                    "iso6391Name": "zh_chs",
                    "score": 1
                }
            ]
        },
        {
            "id": "5",
            "detectedLanguages": [
                {
                    "name": "Russian",
                    "iso6391Name": "ru",
                    "score": 1
                }
            ]
        }
    ],
```

### <a name="ambiguous-content"></a>あいまいなコンテンツ

アナライザーで入力を解析できない場合 (たとえば、アラビア数字のみで構成されるテキスト ブロックを送信した場合など)、`(Unknown)` が返されます。

```
    {
      "id": "5",
      "detectedLanguages": [
        {
          "name": "(Unknown)",
          "iso6391Name": "(Unknown)",
          "score": "NaN"
        }
      ]
```
### <a name="mixed-language-content"></a>混合言語のコンテンツ

同じドキュメント内の言語が混在したコンテンツでは、コンテンツの最大の表現で言語が返されますが、評価の肯定度は低くなります。これは、その評価の限界強度が反映されるためです。 次の例では、入力は英語、スペイン語、フランス語の混在です。 アナライザーは各セグメント内の文字をカウントして、主要な言語を特定します。

**Input (入力)**

```
{
  "documents": [
    {
      "id": "1",
      "text": "Hello, I would like to take a class at your University. ¿Se ofrecen clases en español? Es mi primera lengua y más fácil para escribir. Que diriez-vous des cours en français?"
    }
  ]
}
```

**Output**

結果の出力は、主要な言語で構成され、スコアは 1.0 未満で低い信頼度レベルが示されます。

```
{
  "documents": [
    {
      "id": "1",
      "detectedLanguages": [
        {
          "name": "Spanish",
          "iso6391Name": "es",
          "score": 0.9375
        }
      ]
    }
  ],
  "errors": []
}
```

## <a name="summary"></a>まとめ

この記事では、Cognitive Services の Text Analytics を使用する言語検出の概念とワークフローについて説明しました。 これまでに説明してデモを示した主要なポイントの参照先は以下のとおりです。

+ [言語検出 API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) は 120 の言語に対応しています。
+ 要求本文内の JSON ドキュメントには、id、text が含まれます。
+ POST 要求は、ユーザーのサブスクリプションで有効な、個人用に設定された[アクセス キーとエンドポイント](text-analytics-how-to-access-key.md)を使用して `/languages` エンドポイントに対して行われます。
+ ドキュメント ID ごとの言語識別子で構成される応答出力は、Excel や Power BI を含む JSON を受け取るすべてのアプリにストリーミングすることができます。

## <a name="see-also"></a>関連項目 

 [Text Analytics の概要](../overview.md)  
 [よく寄せられる質問 (FAQ)](../text-analytics-resource-faq.md)</br>
 [Text Analytics 製品ページ](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [感情を分析する](text-analytics-how-to-sentiment-analysis.md)
