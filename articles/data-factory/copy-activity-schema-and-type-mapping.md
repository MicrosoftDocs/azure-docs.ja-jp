---
title: コピー アクティビティでのスキーマ マッピング
description: Azure Data Factory のコピー アクティビティが、データのコピー時にスキーマとデータ型をソース データからシンク データにどのようにマッピングするかについて説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 2c637346aae72a238963607f6f5d23910684265c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921992"
---
# <a name="schema-mapping-in-copy-activity"></a>コピー アクティビティでのスキーマ マッピング

この記事では、Azure Data Factory のコピー アクティビティが、データ コピーの実行時にソース データからシンク データへのスキーマ マッピングとデータ型のマッピングをどのように行うかについて説明します。

## <a name="schema-mapping"></a>スキーマ マッピング

列マッピングは、ソースからシンクにデータをコピーするときに適用されます。 既定では、アクティビティ **マップ ソース データを列名でシンクにコピー**します。 [明示的なマッピング](#explicit-mapping)を指定して、必要に応じて列マッピングをカスタマイズできます。 具体的には、コピー アクティビティは次のことを行います。

1. ソースからデータを読み取り、ソース スキーマを特定する
2. 名前で列をマップするには列マッピングの既定値を使用します。または明示的な列マッピングが指定されている場合はそれを適用します。
3. データをシンクに書き込む

### <a name="explicit-mapping"></a>明示的なマッピング

[コピー アクティビティ] -> [`translator` -> `mappings` プロパティ] でマップする列を指定できます。 次の例では、区切られたテキストから Azure SQL Database にデータをコピーするパイプラインで、コピー アクティビティを定義します。

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [{
        "referenceName": "DelimitedTextInput",
        "type": "DatasetReference"
    }],
    "outputs": [{
        "referenceName": "AzureSqlOutput",
        "type": "DatasetReference"
    }],
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": {
                        "name": "UserId",
                        "type": "Guid"
                    },
                    "sink": {
                        "name": "MyUserId"
                    }
                }, 
                {
                    "source": {
                        "name": "Name",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyName"
                    }
                }, 
                {
                    "source": {
                        "name": "Group",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyGroup"
                    }
                }
            ]
        }
    }
}
```

次のプロパティは、[`translator` -> `mappings`] -> [オブジェクト] で `source` と `sink` によりサポートされます。

| プロパティ | 説明                                                  | 必須 |
| -------- | ------------------------------------------------------------ | -------- |
| 名前     | ソースまたはシンク列の名前。                           | はい      |
| ordinal  | 列のインデックス。 1 から始まります。 <br>ヘッダー行がない区切りテキストを使用するときに適用され、必須です。 | いいえ       |
| path     | 抽出またはマップする各フィールドの JSON パス式。 たとえば MongoDB/REST などの階層データに適用します。<br>ルート オブジェクトの下のフィールドでは、JSON パスはルート $ で始まり、`collectionReference` プロパティにより選択された配列内のフィールドでは、JSON パスは配列要素で始まります。 | いいえ       |
| type     | ソースまたはシンク列の Data Factory 中間データ型。 | いいえ       |
| culture  | ソースまたはシンク列のカルチャ。 <br>型が `Datetime` または `Datetimeoffset` の場合に適用します。 既定では、 `en-us`です。 | いいえ       |
| format   | 種類が `Datetime` または `Datetimeoffset` のときに使用される書式文字列。 日時の書式を設定する方法については、「[カスタム日時書式指定文字列](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)」を参照してください。 | いいえ       |

次のプロパティは、`translator` -> `mappings` の下で、`source` および `sink` があるオブジェクトに加えてサポートされます。

| プロパティ            | 説明                                                  | 必須 |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | MongoDB/REST などの階層データがソースである場合にのみサポートされます。<br>同じパターンを持つ**配列フィールド内**のオブジェクトからのデータの反復処理と抽出を行って、オブジェクトごとの行ごとに変換する場合は、その配列の JSON のパスを指定してクロス適用を行います。 | いいえ       |

### <a name="alternative-column-mapping"></a>代替列マッピング

[コピー アクティビティ]-> [`translator` -> `columnMappings`] を指定して、表形式データ間でマップできます。 この場合、"structure" セクションは、入力と出力の両方のデータセットに必要です。 列マッピングでは、**ソース データセットの "structure" 内のすべての列または列のサブセットのシンク データセットの "structure" 内のすべての列へのマッピング**がサポートされます。 次のエラー状態では例外が発生します。

* ソース データ ストアのクエリの結果に、入力データセットの "構造" セクションで指定された列名は含まれません。
* シンク データ ストア (定義済みのスキーマを持つ場合) に、出力データセットの "structure" セクションで指定された列名は含まれません。
* シンク データセットの "structure" 内の列数が、マッピングで指定された数より少ないかまたは多い。
* 重複したマッピング。

次の例では、入力データセットには構造があり、オンプレミス Oracle データベース内のテーブルをポイントします。

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

この例では、出力データセットには構造があり、Salesfoce 内のテーブルをポイントします。

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

次の JSON は、パイプラインのコピー アクティビティを定義します。 **translator** -> **columnMappings** プロパティを使用してシンク内の列にマップされるソースからの列。

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings":
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

列マッピングの指定に `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` の構文を使用している場合は、引き続きそのままサポートされます。

### <a name="alternative-schema-mapping"></a>代替スキーマ マッピング

[コピー アクティビティ] -> [`translator` -> `schemaMapping`] を指定して、階層形式データと表形式データの間をマップできます。たとえば MongoDB/REST からテキスト ファイルにコピーしたり、Oracle から MongoDB の Azure Cosmos DB の API にコピーしたりできます。 コピー アクティビティの `translator` セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのトランスレーターの type プロパティは**TabularTranslator** に設定する必要があります。 | はい |
| schemaMapping | キーと値のペアのコレクション。**ソース側からシンク側**へのマッピングの関係を表します。<br/>- **Key:** ソースを表します。 **表形式のソース**の場合、データセットの構造に定義された列名を指定します。**階層構造のソース**の場合、抽出とマッピングの対象となる各フィールドの JSON パス式を指定します。<br>- **Value:** シンクを表します。 **表形式のシンク**の場合、データセットの構造に定義された列名を指定します。**階層構造のシンク**の場合、抽出とマッピングの対象となる各フィールドの JSON パス式を指定します。 <br>階層構造のデータで、ルート オブジェクトの直下のフィールドの場合、JSON パスはルートの $ から記述します。`collectionReference` プロパティによって選択された配列内のフィールドの場合、JSON パスは配列要素から記述します。  | はい |
| collectionReference | 同じパターンを持つ**配列フィールド内**のオブジェクトからのデータの反復処理と抽出を行って、オブジェクトごとの行ごとに変換する場合は、その配列の JSON のパスを指定してクロス適用を行います。 このプロパティは、階層形式のデータがソースであるときにのみサポートされます。 | いいえ |

**例: MongoDB から Oracle へのコピー:**

たとえば、次の内容が含まれる MongoDB ドキュメントがあるとします。

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

配列 *(order_pd and order_price)* 内のデータをフラット化し、共通のルート情報 *(number, date, and city)* とクロス結合させることで Azure SQL テーブルに次の形式でコピーする場合は、

| orderNumber | orderDate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | シアトル |
| 01 | 20170122 | P2 | 13 | シアトル |
| 01 | 20170122 | P3 | 231 | シアトル |

次のコピー アクティビティの JSON サンプルとしてスキーマ マッピング ルールを構成します。

```json
{
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "orderNumber": "$.number",
                "orderDate": "$.date",
                "order_pd": "prod",
                "order_price": "price",
                "city": " $.city[0].name"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="data-type-mapping"></a>データ型のマッピング

コピー アクティビティは、次の 2 段階のアプローチを使用してソースの型からシンクの型へのマッピングを実行します。

1. ネイティブなソースの型から Azure Data Factory の中間データ型に変換する
2. Azure Data Factory の中間データ型からネイティブなシンクの型に変換する

ネイティブ型から中間型へのマッピングは、各コネクタのトピックにある「データ型のマッピング」のセクションで見つけることができます。

### <a name="supported-data-types"></a>サポートされているデータ型

Data Factory は次の中間データ型をサポートしています。[データセット構造](concepts-datasets-linked-services.md#dataset-structure-or-schema)の構成で型情報を構成するときは、次の値を指定できます。

* Byte[]
* Boolean
* Datetime
* Datetimeoffset
* Decimal
* Double
* Guid
* Int16
* Int32
* Int64
* Single
* string
* Timespan

## <a name="next-steps"></a>次の手順
コピー アクティビティの他の記事を参照してください。

- [コピー アクティビティの概要](copy-activity-overview.md)
