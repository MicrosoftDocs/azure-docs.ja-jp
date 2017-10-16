---
title: "コピー アクティビティでのスキーマ マッピング | Microsoft Docs"
description: "Azure Data Factory のコピー アクティビティが、データのコピー時にスキーマとデータ型をソース データからシンク データにどのようにマッピングするかについて説明します。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jingwang
ms.openlocfilehash: 459c792028d3eede059814324597811b24e65ac2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="schema-mapping-in-copy-activity"></a>コピー アクティビティでのスキーマ マッピング
この記事では、Azure Data Factory のコピー アクティビティが、データ コピーの実行時にソース データからシンク データへのスキーマ マッピングとデータ型のマッピングをどのように行うかについて説明します。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory バージョン 1 のドキュメント](v1/data-factory-introduction.md)を参照してください。


## <a name="column-mapping"></a>列マッピング

既定では、[明示的な列マッピング](#explicit-column-mapping)が構成されていない限り、コピー アクティビティは**ソース データをシンクに列名でマッピングします**。 具体的には、コピー アクティビティは次のことを行います。

1. ソースからデータを読み取り、ソース スキーマを特定する

    * メタデータを含むデータベース/ファイル (Avro/ORC/Parquet/ヘッダー付きのテキスト) など、データ ストア内の定義済みのスキーマを持つデータ ソースや、ファイル形式のデータ ソースの場合、ソース スキーマはクエリの結果またはファイル メタデータから抽出されます。
    * Azure テーブル/Cosmos DB など、柔軟なスキーマを持つデータ ソースの場合、ソース スキーマはクエリの結果から推測されます。 データセット内に "structure" を指定することによって、それを上書きできます。
    * ヘッダーのないテキスト ファイルの場合は、パターン "Prop_0"、"Prop_1"、... で既定の列名が生成されます。データセット内に "structure" を指定することによって、それを上書きできます。
    * Dynamics ソースの場合は、データセットの "structure" セクション内にスキーマ情報を指定する必要があります。

2. 指定されている場合は、明示的な列マッピングを適用します。

3. データをシンクに書き込む

    * 定義済みのスキーマを持つデータ ストアの場合、データは同じ名前を持つ列に書き込まれます。
    * 固定のスキーマのないデータ ストアや、ファイル形式のデータ ストアの場合、列名/メタデータはソース スキーマに基づいて生成されます。

### <a name="explicit-column-mapping"></a>明示的な列マッピング

明示的な列マッピングを行うには、コピー アクティビティの **typeProperties** セクションで **columnMappings** を指定できます。 このシナリオでは、入力データセットと出力データセットの両方に "structure" セクションが必要です。 列マッピングでは、**ソース データセットの "structure" 内のすべての列または列のサブセットのシンク データセットの "structure" 内のすべての列へのマッピング**がサポートされます。 次のエラー状態では例外が発生します。

* ソース データ ストアのクエリの結果に、入力データセットの "構造" セクションで指定された列名は含まれません。
* シンク データ ストア (定義済みのスキーマを持つ場合) に、出力データセットの "structure" セクションで指定された列名は含まれません。
* シンク データセットの "structure" 内の列数が、マッピングで指定された数より少ないかまたは多い。
* 重複したマッピング。

#### <a name="explicit-column-mapping-example"></a>明示的な列マッピングの例

このサンプルでは、入力テーブルは構造を持ち、オンプレミスの SQL データベース内のテーブルを指しています。

```json
{
    "name": "SqlServerInput",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "SqlServerLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

このサンプルでは、出力テーブルは構造を持ち、Azure SQL Database 内のテーブルを指しています。

```json
{
    "name": "AzureSqlOutput",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

次の JSON は、パイプラインのコピー アクティビティを定義します。 **translator** プロパティを使用して、シンク (**columnMappings**) 内の列にマッピングされたソースの列。

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "SqlServerInput",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "AzureSqlOutput",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "SqlSource" },
        "sink": { "type": "SqlSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    }
}
```

**列マッピングのフロー:**

![列マッピングのフロー](./media/copy-activity-schema-and-type-mapping/column-mapping-sample.png)

## <a name="data-type-mapping"></a>データ型のマッピング

コピー アクティビティは、次の 2 段階のアプローチを使用してソースの型からシンクの型へのマッピングを実行します。

1. ネイティブなソースの型から Azure Data Factory の中間データ型に変換する
2. Azure Data Factory の中間データ型からネイティブなシンクの型に変換する

ネイティブ型から中間型へのマッピングは、各コネクタのトピックにある「データ型のマッピング」のセクションで見つけることができます。

### <a name="supported-data-types"></a>サポートされているデータ型

Data Factory は、次の中間データ型をサポートしています。[データセット構造](concepts-datasets-linked-services.md#dataset-structure)の構成で型情報を指定するときは、次の値を指定できます。

* Byte[]
* Boolean
* DateTime
* Datetimeoffset
* Decimal
* Double
* Guid
* Int16
* Int32
* Int64
* Single
* String
* Timespan

### <a name="explicit-data-type-conversion"></a>明示的なデータ型変換

固定のスキーマを持つデータ ストア (SQL Server/Oracle など) にデータをコピーするとき、ソースとシンクが同じ列に異なる型を持っている場合は、ソース側で明示的な型変換を宣言する必要があります。

* ファイル ソース (CSV/Avro など) の場合、型変換は、完全な列リスト (ソース側の列名とシンク側の型) を持つソース構造によって宣言されます。
* リレーショナル ソース (SQL/Oracle など) の場合、型変換は、クエリ ステートメント内の明示的な型キャストによって実現されます。

## <a name="when-to-specify-dataset-structure"></a>データセットの "structure" を指定する場合

次のシナリオでは、データセット内に "structure" が必要です。

* コピー中のファイル ソースへの[明示的なデータ型変換](#explicit-data-type-conversion)の適用 (入力データセット)
* コピー中の[明示的な列マッピング](#explicit-column-mapping)の適用 (入力データセットと出力データセットの両方)
* Dynamics 365/CRM ソースからのコピー (入力データセット)
* ソースが JSON ファイルでない場合、入れ子になったオブジェクトとしての Cosmos DB へのコピー (出力データセット)

次のシナリオでは、データセット内に "structure" が推奨されます。

* ヘッダーのないテキスト ファイルからのコピー (入力データセット)。 明示的な列マッピングを指定する代わりに、対応するシンク列に合わせたテキスト ファイルの列名を指定できます。
* 柔軟なスキーマを持つデータ ストア (Azure テーブル/Cosmos DB (入力データセット) など) からのコピー。これは、アクティビティの実行ごとに上部の行に基づいてコピー アクティビティにスキーマを推測させることなく、予測されるデータ (列) がコピーされることを保証するためです。


## <a name="next-steps"></a>次のステップ
コピー アクティビティの他の記事を参照してください。

- [コピー アクティビティの概要](copy-activity-overview.md)
- [コピー アクティビティのフォールト トレランス](copy-activity-fault-tolerance.md)
- [コピー アクティビティのパフォーマンス](copy-activity-performance.md)
