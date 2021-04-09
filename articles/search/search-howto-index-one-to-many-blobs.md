---
title: 複数のドキュメントを含むインデックス BLOB
titleSuffix: Azure Cognitive Search
description: Azure BLOB をクロールしてテキスト コンテンツを探すために、Azure Cognitive Search Blob インデクサーを使用します。この場合、各 BLOB から 1 つ以上の検索インデックス ドキュメントが生成される場合があります。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: ea22b3cff8a0303c4e6698db4090df0f5ed2153a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99430982"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>BLOB のインデックス作成して複数の検索ドキュメントを生成する

既定では、BLOB インデクサーで BLOB のコンテンツが単一の検索ドキュメントとして扱われます。 検索インデックスで BLOB をより細かく表現する場合は、1 つの BLOB から複数の検索ドキュメントを作成するように **parsingMode** 値を設定できます。 多くの検索ドキュメントを生成する **parsingMode** 値には、`delimitedText` ([CSV](search-howto-index-csv-blobs.md) の場合)、`jsonArray` または `jsonLines` ([JSON](search-howto-index-json-blobs.md) の場合) が含まれます。

これらの解析モードのいずれかを使用する場合、出現する新しい検索ドキュメントには一意のドキュメント キーが必要であり、その値の取得元を決定する際に問題が発生します。 親 BLOB には `metadata_storage_path property` の形式で一意の値が少なくとも 1 つ含まれていますが、その値が複数の検索ドキュメントに提供される場合、そのキーはインデックス内で一意ではなくなります。

この問題に対処するために、BLOB インデクサーでは、1 つの BLOB 親から作成された各子検索ドキュメントを一意に識別する `AzureSearch_DocumentKey` を生成します。 この記事では、この動作のしくみについて説明します。

## <a name="one-to-many-document-key"></a>一対多のドキュメント キー

Azure Cognitive Search インデックスに表示される各ドキュメントは、ドキュメント キーによって一意に識別されます。 

解析モードが指定されず、検索ドキュメント キーのインデクサー定義に[明示的なフィールド マッピング](search-indexer-field-mappings.md)がない場合、BLOB インデクサーでは `metadata_storage_path property` をドキュメント キーとして自動的にマップします。 このマッピングにより、各 BLOB が個別の検索ドキュメントとして表示されるようになり、このフィールド マッピングを自分で作成する手間が省けます (通常は、同じ名前と型を持つフィールドのみが自動的にマップされます)。

上記にリストされた解析モードのいずれかを使用する場合、1 つの BLOB が "多くの" 検索ドキュメントにマップされ、BLOB メタデータのみに基づくドキュメント キーが不適切なものとなります。 この制約を克服するために、Azure Cognitive Search では、BLOB から抽出された個々のエンティティごとに "一対多" のドキュメント キーを生成できます。 このプロパティは AzureSearch_DocumentKey という名前で、BLOB から抽出された個々のエンティティに追加されます。 このプロパティの値は必ず、BLOB 全体の 個々のエンティティごとに一意となり、エンティティは別の検索ドキュメントとして表示されます。

既定では、キー インデックス フィールドに対して明示的なフィールド マッピングが指定されていない場合、`base64Encode` フィールド マッピング関数を使用して、`AzureSearch_DocumentKey` がそれにマップされます。

## <a name="example"></a>例

次のフィールドのインデックス定義があるとします。

+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

BLOB コンテナーには次の構造の BLOB があります。

_Blob1.json_

```json
{ "temperature": 100, "pressure": 100, "timestamp": "2020-02-13T00:00:00Z" }
{ "temperature" : 33, "pressure" : 30, "timestamp": "2020-02-14T00:00:00Z" }
```

_Blob2.json_

```json
{ "temperature": 1, "pressure": 1, "timestamp": "2019-01-12T00:00:00Z" }
{ "temperature" : 120, "pressure" : 3, "timestamp": "2017-05-11T00:00:00Z" }
```

インデクサーを作成し、キー フィールドに対して明示的なフィールド マッピングを指定せずに、**parsingMode** を `jsonLines` に設定すると、以下のマッピングが暗黙的に適用されます。

```http
{
    "sourceFieldName" : "AzureSearch_DocumentKey",
    "targetFieldName": "id",
    "mappingFunction": { "name" : "base64Encode" }
}
```

このセットアップでは、次のように明確なドキュメント キーが生成されます (簡潔にするために、base64 でエンコードされた ID が短縮されています)。

| id | 温度 | pressure | timestamp |
|----|-------------|----------|-----------|
| aHR0 ...YjEuanNvbjsx | 100 | 100 | 2020-02-13T00:00:00Z |
| aHR0 ...YjEuanNvbjsy | 33 | 30 | 2020-02-14T00:00:00Z |
| aHR0 ...YjIuanNvbjsx | 1 | 1 | 2019-01-12T00:00:00Z |
| aHR0 ...YjIuanNvbjsy | 120 | 3 | 2017-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>インデックス キー フィールドのカスタム フィールド マッピング

前述の例と同じインデックス定義があり、BLOB コンテナーに次の構造の BLOB があるとします。

_Blob1.json_

```json
recordid, temperature, pressure, timestamp
1, 100, 100,"2019-02-13T00:00:00Z" 
2, 33, 30,"2019-02-14T00:00:00Z" 
```

_Blob2.json_

```json
recordid, temperature, pressure, timestamp
1, 1, 1,"2018-01-12T00:00:00Z" 
2, 120, 3,"2013-05-11T00:00:00Z" 
```

`delimitedText` **parsingMode** でインデクサーを作成するときに、以下のようにキー フィールドにフィールド マッピング関数を設定するのは自然だと思われるかもしれません。

```http
{
    "sourceFieldName" : "recordid",
    "targetFieldName": "id"
}
```

しかし、このマッピングでは、インデックスに 4 つのドキュメントは表示 _されません_。`recordid` フィールドは _BLOB 全体で_ 一意ではないためです。 そのため、"1 対多" の解析モードでは、`AzureSearch_DocumentKey` プロパティからキー インデックス フィールドに適用される暗黙的なフィールド マッピングを利用することをお勧めします。

明示的なフィールド マッピングを設定する場合は、**BLOB 全体の** 個々のエンティティの _sourceField_ がそれぞれ固有であることを確認してください。

> [!NOTE]
> 抽出されたエンティティごとの一意性を確保するために `AzureSearch_DocumentKey` で使用される手法は変更される可能性があるため、アプリケーションのニーズに応じて、その値に依存しないようにしてください。

## <a name="next-steps"></a>次のステップ

BLOB のインデックス作成の基本構造とワークフローについてまだよく理解していない場合は、まず [Azure Cognitive Search で Azure Blob Storage のインデックスを作成する](search-howto-index-json-blobs.md)方法に関するページを参照してください。 さまざまな種類の BLOB コンテンツ タイプ の解析モードに関する詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [CSV BLOB のインデックス作成](search-howto-index-csv-blobs.md)
> [JSON BLOB のインデックス作成](search-howto-index-json-blobs.md)
