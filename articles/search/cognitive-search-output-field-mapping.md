---
title: コグニティブ検索の強化された入力フィールドを出力フィールドにマップする - Azure Search
description: ソース データ フィールドを抽出して強化し、Azure Search インデックスの出力フィールドにマップします。
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 1d1ef7be83601a1ccf09ac52f87d0fcc953c605c
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639181"
---
# <a name="how-to-map-enriched-fields-to-a-searchable-index"></a>強化されたフィールドを検索可能なインデックスにマップする方法

この記事では、強化された入力フィールドを検索可能なインデックスの出力フィールドにマップする方法を学習します。 [定義済みのスキルセット](cognitive-search-defining-skillset.md)が用意できたら、値に直接影響するすべてのスキルの出力フィールドを検索インデックス内の特定のフィールドにマップする必要があります。 フィールド マッピングは、強化されたドキュメントからインデックスにコンテンツを移動するために必要です。


## <a name="use-outputfieldmappings"></a>outputFieldMappings の使用
フィールドをマップするには、次に示すように `outputFieldMappings` をインデクサー定義に追加します。

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
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
            "targetFieldName": "descriptions"
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
出力フィールド マッピングごとに、強化されたフィールド (sourceFieldName) の名前を設定してから、インデックスで参照されるフィールド (targetFieldName) の名前を設定します。

sourceFieldName のパスは、1 つ以上の要素を表すことができます。 上記の例では、```/document/content/sentiment``` が 1 つの数値を表しているのに対し、```/document/content/organizations/*/description``` は複数の組織の説明を表しています。 複数の要素がある場合は、それらは配列に "フラット化" され、その中に各要素が格納されます。 より具体的には、```/document/content/organizations/*/description``` の例の場合、*descriptions* フィールド内のデータは、インデックスが作成されるまで、descriptions のフラットな配列のようになります。

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>次の手順
強化されたフィールドを検索可能なフィールドにマップすると、[インデックス定義の一部として](search-what-is-an-index.md)検索可能な各フィールドにフィールド属性を設定できます。

フィールド マッピングの詳細については、「[Field mappings in Azure Search indexers](search-indexer-field-mappings.md)」 (Azure Search インデクサーでのフィールド マッピング) を参照してください。
