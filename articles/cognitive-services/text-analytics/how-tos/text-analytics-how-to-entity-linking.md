---
title: Text Analytics API でエンティティ認識を利用する
titleSuffix: Azure Cognitive Services
description: Text Analytics REST API を使用して、テキスト内のエンティティの ID を識別して明確にする方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: 457be5ac014fda6b4984ed7af3dcc89780b16379
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2020
ms.locfileid: "84141619"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Text Analytics で名前付きエンティティの認識を使用する方法

Text Analytics API を使用すると、構造化されていないテキストから、あいまいさを解消したエンティティの一覧を、Web 上にある詳しい情報へのリンクと共に取得することができます。 この API は、名前付きエンティティの認識 (NER) とエンティティ リンクの両方をサポートしています。

### <a name="entity-linking"></a>Entity Linking

エンティティ リンク設定は、テキスト内で見つかったエンティティの ID を識別してあいまいさを解消する機能です (例: "Mars" という単語が出現した場合にそれが惑星を指すのか、古代ローマの戦争の神様を指すのかを判定する)。 このプロセスで、テキスト内の認識されたエンティティをリンクするためには、適切な言語のナレッジ ベースが存在している必要があります。 エンティティ リンクには、このナレッジ ベースとして [Wikipedia](https://www.wikipedia.org/) が使用されます。


### <a name="named-entity-recognition-ner"></a>名前付きエンティティの認識 (NER)

名前付きエンティティの認識 (NER) とは、人、場所、イベント、製品や組織などの事前に定義されているさまざまなテキスト形式のエンティティを、クラスまたは種類に分類する機能です。  

## <a name="named-entity-recognition-versions-and-features"></a>名前付きエンティティの認識のバージョンと機能

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| 機能                                                         | NER v3.0 | NER v3.1-preview.1 |
|-----------------------------------------------------------------|--------|----------|
| 単一要求およびバッチ要求のメソッド                          | X      | X        |
| 複数のカテゴリに対応する拡張されたエンティティの認識           | X      | X        |
| エンティティ リンク設定と NER 要求の送信のためのエンドポイントが分けられました。 | X      | X        |
| 個人 (`PII`) および医療 (`PHI`) 情報エンティティの認識        |        | X        |

詳細については、[言語サポート](../language-support.md)に関するページを参照してください。

### <a name="entity-types"></a>エンティティの種類

名前付きエンティティの認識 v3 では、複数の種類に対応する拡張された検出が提供されます。 現在、NER v3.0 は、[一般エンティティ カテゴリ](../named-entity-types.md)に含まれるエンティティを認識できます。

固有表現認識 v3.1-preview.1 には、v3.0 の検出機能に加え、`v3.1-preview.1/entities/recognition/pii` エンドポイントを使用して個人情報 (`PII`) を検出する機能が含まれています。 オプションの `domain=phi` パラメーターを使用すると、機密性の高い医療情報 (`PHI`) を検出できます。 詳細については、[エンティティのカテゴリ](../named-entity-types.md)に関する記事と以下の「[要求エンドポイント](#request-endpoints)」セクションを参照してください。


## <a name="sending-a-rest-api-request"></a>REST API 要求の送信

### <a name="preparation"></a>準備

JSON ドキュメントは、次の形式である必要があります: ID、テキスト、言語。

各ドキュメントは 5,120 文字未満である必要があり、コレクションあたり最大 1,000 の項目 (ID) を含めることができます。 コレクションは、要求の本文で送信されます。

### <a name="structure-the-request"></a>要求を構造化する

POST 要求を作成します。 次のリンクにある [Postman](text-analytics-how-to-call-api.md) または **API テスト コンソール**を使用して、簡単に要求を構造化し、送信することができます。 

> [!NOTE]
> Azure portal で Text Analytics リソースのキーとエンドポイントを確認できます。 それらは、リソースの**クイック スタート** ページの**リソース管理**の下にあります。 


### <a name="request-endpoints"></a>要求エンドポイント

#### <a name="version-30"></a>[バージョン 3.0](#tab/version-3)

名前付きエンティティの認識 v3 では、NER とエンティティ リンク設定の要求に別個のエンドポイントを使用します。 要求に応じて、次の URL 形式を使用します。

エンティティ リンク設定
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

#### <a name="version-31-preview1"></a>[バージョン 3.1-preview.1](#tab/version-3-preview)

固有表現認識 `v3.1-preview.1` では、NER とエンティティ リンク設定の要求に個別のエンドポイントを使用します。 要求に応じて、次の URL 形式を使用します。

エンティティ リンク設定
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/entities/linking`

NER
* 一般的なエンティティ- `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/entities/recognition/general`

* 個人 (`PII`) 情報 - `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/entities/recognition/pii`

また、オプションの `domain=phi` パラメーターを使用すると、テキスト内の医療 (`PHI`) 情報を検出することもできます。 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/entities/recognition/pii?domain=phi`

---

要求ヘッダーに Text Analytics API キーが含まれるように設定します。 要求本文では、用意した JSON ドキュメントを指定します。

### <a name="example-ner-request"></a>NER 要求の例 

次に示したのは、API に送信するコンテンツの例です。 要求の形式は、どちらのバージョンの API でも同じです。

```json
{
  "documents": [
    {
        "id": "1",
        "language": "en",
        "text": "Our tour guide took us up the Space Needle during our trip to Seattle last week."
    }
  ]
}

```

## <a name="post-the-request"></a>要求を投稿する

要求が受信されると分析が実行されます。 分単位および秒単位で送信できる要求のサイズと数については、概要の「[データ制限](../overview.md#data-limits)」セクションを参照してください。

Text Analytics API はステートレスです。 データはアカウントに保存されず、結果がすぐに応答で返されます。

## <a name="view-results"></a>結果の表示

すべての POST 要求で、ID と検出されたエンティティのプロパティを含む JSON 形式の応答が返されます。

出力はすぐに返されます。 結果は、JSON を受け付けるアプリケーションにストリームするか、ローカル システム上のファイルに出力を保存してから、そのファイルを、データの並べ替え、検索、および操作が可能なアプリケーションにインポートすることができます。 多言語と絵文字のサポートにより、応答にはテキスト オフセットが含まれる場合があります。 詳細については[オフセットの処理方法](../concepts/text-offsets.md)に関するページを参照してください。

### <a name="example-v3-responses"></a>v3 の応答の例

バージョン 3 では、NER とエンティティ リンク設定に別々のエンドポイントが用意されています。 両方の操作の応答を次に示します。 

#### <a name="example-ner-response"></a>NER の応答の例

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "text": "tour guide",
          "category": "PersonType",
          "offset": 4,
          "length": 10,
          "confidenceScore": 0.45
        },
        {
          "text": "Space Needle",
          "category": "Location",
          "offset": 30,
          "length": 12,
          "confidenceScore": 0.38
        },
        {
          "text": "trip",
          "category": "Event",
          "offset": 54,
          "length": 4,
          "confidenceScore": 0.78
        },
        {
          "text": "Seattle",
          "category": "Location",
          "subcategory": "GPE",
          "offset": 62,
          "length": 7,
          "confidenceScore": 0.78
        },
        {
          "text": "last week",
          "category": "DateTime",
          "subcategory": "DateRange",
          "offset": 70,
          "length": 9,
          "confidenceScore": 0.8
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-04-01"
}
```


#### <a name="example-entity-linking-response"></a>エンティティ リンク設定の応答の例

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "name": "Space Needle",
          "matches": [
            {
              "text": "Space Needle",
              "offset": 30,
              "length": 12,
              "confidenceScore": 0.4
            }
          ],
          "language": "en",
          "id": "Space Needle",
          "url": "https://en.wikipedia.org/wiki/Space_Needle",
          "dataSource": "Wikipedia"
        },
        {
          "name": "Seattle",
          "matches": [
            {
              "text": "Seattle",
              "offset": 62,
              "length": 7,
              "confidenceScore": 0.25
            }
          ],
          "language": "en",
          "id": "Seattle",
          "url": "https://en.wikipedia.org/wiki/Seattle",
          "dataSource": "Wikipedia"
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-02-01"
}
```


## <a name="summary"></a>まとめ

この記事では、Cognitive Services の Text Analytics を使用するエンティティ リンク設定の概念とワークフローについて説明しました。 要約すると:

* 要求本文内の JSON ドキュメントには、ID、テキスト、および言語のコードが含まれます。
* POST 要求は、ユーザーのサブスクリプションで有効な、個人用に設定された[アクセス キーとエンドポイント](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)を使用して 1 つまたは複数のエンドポイントに送信されます。
* リンクされているエンティティ (ドキュメント ID ごとに信頼度スコア、オフセット、Web リンクが含まれている) で構成される応答の出力は、どのアプリケーションでも使用できます

## <a name="next-steps"></a>次のステップ

* [Text Analytics の概要](../overview.md)
* [Text Analytics クライアント ライブラリの使用](../quickstarts/text-analytics-sdk.md)
* [新機能](../whats-new.md)
