---
title: フルテキスト検索のために Azure Blob インデクサーから複数の検索インデックス ドキュメントを含む BLOB にインデックスを付ける - Azure Search
description: Azure Search Blob インデクサーを使用してテキスト コンテンツのために Azure BLOB をクロールします。 各 BLOB には、1 つまたは複数の Azure Search インデックス ドキュメントが含まれる場合があります。
ms.date: 05/02/2019
author: arv100kri
manager: nitinme
ms.author: arjagann
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seofeb2018
ms.openlocfilehash: 2c2a17d006f65854a89b9fac1818fcec420c07dc
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182309"
---
# <a name="indexing-blobs-producing-multiple-search-documents"></a>複数の検索ドキュメントを生成する BLOB のインデックス作成
既定では、BLOB インデクサーで BLOB のコンテンツが単一の検索ドキュメントとして扱われます。 特定の **parsingMode** 値では、個々の BLOB で複数の検索ドキュメントが生成される可能性のあるシナリオがサポートされます。 インデクサーで BLOB から複数の検索ドキュメントを抽出できるようにする、さまざまな種類の **parsingMode** を以下に示します。
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>一対多のドキュメント キー
Azure Search インデックスに表示される各ドキュメントは、ドキュメント キーによって一意に識別されます。 

解析モードが指定されていない状態で、インデックス内のキー フィールドに対して明示的なマッピングが存在しない場合、Azure Search で自動的にキーとして `metadata_storage_path` プロパティが[マップ](search-indexer-field-mappings.md)されます。 このマッピングによって、各 BLOB が別個の検索ドキュメントとして確実に表示されます。

上記にリストされた解析モードのいずれかを使用する場合、1 つの BLOB が "多くの" 検索ドキュメントにマップされ、BLOB メタデータのみに基づくドキュメント キーが不適切なものとなります。 この制約を克服するために、Azure Search では、BLOB から抽出された個々のエンティティごとに "一対多" のドキュメント キーを生成できます。 このプロパティは `AzureSearch_DocumentKey` という名前で、BLOB から抽出された個々のエンティティに追加されます。 このプロパティの値は必ず、_BLOB 全体の_ 個々のエンティティごとに一意となり、エンティティは別の検索ドキュメントとして表示されます。

既定では、キー インデックス フィールドに対して明示的なフィールド マッピングが指定されていない場合、`base64Encode` フィールド マッピング関数を使用して、`AzureSearch_DocumentKey` がそれにマップされます。

## <a name="example"></a>例
次のフィールドのインデックス定義があるとします。
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

BLOB コンテナーには次の構造の BLOB があります。

_Blob1.json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2.json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

インデクサーを作成し、キー フィールドに対して明示的なフィールド マッピングを指定せずに、**parsingMode** を `jsonLines` に設定すると、以下のマッピングが暗黙的に適用されます
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

この設定では、以下の情報を含む Azure Search インデックスが生成されます (簡潔にするため、base64 エンコード ID は短縮されています)

| id | 温度 | pressure | timestamp |
|----|-------------|----------|-----------|
| aHR0 ...YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ...YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ...YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ...YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>インデックス キー フィールドのカスタム フィールド マッピング

前述の例と同じインデックス定義があり、BLOB コンテナーに次の構造の BLOB があるとします。

_Blob1.json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2.json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

`delimitedText` **parsingMode** でインデクサーを作成するときに、以下のようにキー フィールドにフィールド マッピング関数を設定するのは自然だと思われるかもしれません。

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

しかし、このマッピングでは、インデックスに 4 つのドキュメントは表示_されません_。`recordid` フィールドは _BLOB 全体で_ 一意ではないためです。 そのため、"1 対多" の解析モードでは、`AzureSearch_DocumentKey` プロパティからキー インデックス フィールドに適用される暗黙的なフィールド マッピングを利用することをお勧めします。

明示的なフィールド マッピングを設定する場合は、**BLOB 全体の**個々のエンティティの _sourceField_ がそれぞれ固有であることを確認してください。

> [!NOTE]
> 抽出されたエンティティごとの一意性を確保するために `AzureSearch_DocumentKey` で使用される手法は変更される可能性があるため、アプリケーションのニーズに応じて、その値に依存しないようにしてください。

## <a name="see-also"></a>関連項目

+ [Azure Search のインデクサー](search-indexer-overview.md)
+ [Azure Blob Storage のインデックスを Azure Search で作成する](search-howto-index-json-blobs.md)
+ [Azure Search BLOB インデクサーを使用した CSV BLOB のインデックス作成](search-howto-index-csv-blobs.md)
+ [Azure Search の BLOB インデクサーを使用して JSON BLOB のインデックスを作成する](search-howto-index-json-blobs.md)

## <a name="NextSteps"></a>次のステップ
* Azure Search について詳しくは、[Search サービス ページ](https://azure.microsoft.com/services/search/)をご覧ください。