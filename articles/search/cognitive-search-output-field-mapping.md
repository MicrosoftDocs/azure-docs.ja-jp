---
title: 入力を出力フィールドにマップする
titleSuffix: Azure Cognitive Search
description: ソース データ フィールドを抽出して強化し、Azure Cognitive Search インデックスの出力フィールドにマップします。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 58bb87d5af785d3cffd96f3bd02477f97ed967a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96001305"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>AI によって強化されたフィールドを検索可能なインデックスにマップする方法

![インデクサーのステージ](./media/cognitive-search-output-field-mapping/indexer-stages-output-field-mapping.png "インデクサーのステージ")

この記事では、強化された入力フィールドを検索可能なインデックスの出力フィールドにマップする方法を学習します。 [定義済みのスキルセット](cognitive-search-defining-skillset.md)を持ったら、値に直接影響するすべてのスキルの出力フィールドを検索インデックス内の特定のフィールドにマップする必要があります。

出力フィールド マッピングは、強化されたドキュメントからインデックスにコンテンツを移動するために必要です。  強化されたドキュメントは情報のツリーであり、インデックスで複合型がサポートされている場合でも、強化されたツリーの情報をより単純な型 (文字列配列など) に変換することが必要になる場合があります。 出力フィールド マッピングを使用すると、情報をフラット化することによってデータ シェイプを変換できます。 出力フィールド マッピングは、必ずスキルセットの実行後に発生します。ただし、スキルセットが定義されていない場合でも実行されることがあります。

出力フィールド マッピングの例を次に示します。

* スキルセットの一環として、ドキュメントの各ページに記載されている組織の名前を抽出しました。 次に、これらの各組織名を、Edm.Collection(Edm.String) 型のインデックスのフィールドにマップしたいと考えています。

* スキルセットの一環として、“document/translated_text” という新しいノードを作成しました。 このノードの情報をインデックス内の特定のフィールドにマップしたいと考えています。

* スキルセットはありませんが、Cosmos DB データベースから複合型にインデックスを作成しています。 その複合型のノードにアクセスして、インデックスのフィールドにマップしたいと考えています。

> [!NOTE]
> 最近、出力フィールド マッピングで関数をマッピングする機能を有効にしました。 マッピング関数の詳細については、「[フィールド マッピング関数](./search-indexer-field-mappings.md#field-mapping-functions)」を参照してください。

## <a name="use-outputfieldmappings"></a>outputFieldMappings の使用

フィールドをマップするには、次に示すように `outputFieldMappings` をインデクサー定義に追加します。

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
```

要求の本文の構造は次のとおりです。

```json
{
    "name": "myIndexer",
    "dataSourceName": "myDataSource",
    "targetIndexName": "myIndex",
    "skillsetName": "myFirstSkillSet",
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction": {
                "name": "base64Encode"
            }
        }
    ],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/content/organizations/*/description",
            "targetFieldName": "descriptions",
            "mappingFunction": {
                "name": "base64Decode"
            }
        },
        {
            "sourceFieldName": "/document/content/organizations",
            "targetFieldName": "orgNames"
        },
        {
            "sourceFieldName": "/document/content/sentiment",
            "targetFieldName": "sentiment"
        }
    ]
}
```

出力フィールド マッピングごとに、強化されたドキュメント ツリー内のデータの場所 (sourceFieldName) の名前を設定してから、インデックスで参照されるフィールド (targetFieldName) の名前を設定します。

## <a name="flattening-information-from-complex-types"></a>複合型から情報をフラット化する 

sourceFieldName のパスは、1 つ以上の要素を表すことができます。 上記の例では、```/document/content/sentiment``` が 1 つの数値を表しているのに対し、```/document/content/organizations/*/description``` は複数の組織の説明を表しています。 

複数の要素がある場合は、それらは配列に "フラット化" され、その中に各要素が格納されます。 

より具体的には、```/document/content/organizations/*/description``` の例の場合、*descriptions* フィールド内のデータは、インデックスが作成されるまで、descriptions のフラットな配列のようになります。

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

これは重要な原則であるため、もう 1 つ例をご紹介します。 強化ツリーの一部として複合型の配列を使用していると仮定します。 ここでは、次に示すような複合型の配列を持つ customEntities というメンバーがあるとします。

```json
"document/customEntities": 
[
    {
        "name": "heart failure",
        "matches": [
            {
                "text": "heart failure",
                "offset": 10,
                "length": 12,
                "matchDistance": 0.0
            }
        ]
    },
    {
        "name": "morquio",
        "matches": [
            {
                "text": "morquio",
                "offset": 25,
                "length": 7,
                "matchDistance": 0.0
            }
        ]
    }
    //...
]
```

ここでは、インデックスに型 Collection (Edm.String) 'diseases' (病気) という名前のフィールド があり、エンティティの各名前を格納すると仮定します。 

これは、次のように "\*" 記号を使用して簡単に行うことができます。

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

この操作では、次のように、customEntities の要素の各名前を 1 つの文字列配列に "フラット化" します。

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>次のステップ
強化されたフィールドを検索可能なフィールドにマップすると、[インデックス定義の一部として](search-what-is-an-index.md)検索可能な各フィールドにフィールド属性を設定できます。

フィールド マッピングの詳細については、[Azure Cognitive Search インデクサーでのフィールド マッピング](search-indexer-field-mappings.md)に関する記事を参照してください。