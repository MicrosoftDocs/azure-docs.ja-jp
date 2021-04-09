---
title: コピー アクティビティでのスキーマとデータ型のマッピング
description: Azure Data Factory のコピー アクティビティによって、スキーマとデータ型が、ソース データからシンク データにどのようにマップされるかについて説明します。
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: jingwang
ms.openlocfilehash: 0aee6030e5608b5413864d6a32dc8442dd346f42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100392784"
---
# <a name="schema-and-data-type-mapping-in-copy-activity"></a>コピー アクティビティでのスキーマとデータ型のマッピング
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory のコピー アクティビティによって、スキーマ マッピングとデータ型のソース データからシンク データへのマッピングがどのように行われるのかについて説明します。

## <a name="schema-mapping"></a>スキーマ マッピング

### <a name="default-mapping"></a>既定のマッピング

既定では、アクティビティ マップ ソース データを **列名で** (大文字と小文字を区別して) シンクにコピーします。 ファイルへの書き込みなど、シンクが存在しない場合、ソース フィールド名はシンク名として保持されます。 このような既定のマッピングでは、柔軟なスキーマがサポートされ、実行から実行へ、ソースからシンクへのスキーマ ドリフトが行われ、ソース データ ストアから返されるすべてのデータをシンクにコピーできます。

ソースが、ヘッダー行のないテキスト ファイルの場合は、ソースに列名が含まれていないため、[明示的なマッピング](#explicit-mapping)が必要です。

### <a name="explicit-mapping"></a>明示的なマッピング

また、明示的なマッピングを指定して、必要に応じて、列とフィールドのソースからシンクへのマッピングをカスタマイズできます。 明示的なマッピングでは、ソース データの一部のみをシンクにコピーする、ソース データを名前の異なるシンクにマップする、表形式または階層構造のデータを再形成することができます。 コピー アクティビティによって、以下が行われます。

1. ソースからデータが読み取られ、ソース スキーマが特定されます。
2. 定義されたマッピングが適用されます。
3. データがシンクに書き込まれます。

各項目の詳細情報

- [表形式のソースから表形式のシンクへ](#tabular-source-to-tabular-sink)
- [階層構造のソースから表形式のシンクへ](#hierarchical-source-to-tabular-sink)
- [表形式または階層構造のソースから階層構造のシンクへ](#tabularhierarchical-source-to-hierarchical-sink)

Data Factory 作成 UI -> コピー アクティビティ -> [マッピング] タブでマッピングを構成するか、コピー アクティビティ -> `translator` プロパティのマッピングをプログラムで指定します。 `translator` -> `mappings` array -> objects -> `source` と、データをマップする特定の列とフィールドを指す `sink` では、次のプロパティがサポートされています。

| プロパティ | 説明                                                  | 必須 |
| -------- | ------------------------------------------------------------ | -------- |
| name     | ソースまたはシンクの列とフィードの名前。 表形式のソースおよびシンクに適用します。 | はい      |
| ordinal  | 列のインデックス。 1 から開始します。 <br>ヘッダー行がない区切りテキストを使用するときに適用され、必須です。 | いいえ       |
| path     | 抽出またはマップする各フィールドの JSON パス式。 階層構造のソースおよびシンク (Cosmos DB、MongoDB、REST コネクタなど) に適用します。<br>ルート オブジェクトの下のフィールドでは、JSON パスはルート `$` で始まり、`collectionReference` プロパティにより選択された配列内のフィールドでは、JSON パスは配列要素で始まり、`$` は付きません。 | いいえ       |
| type     | ソースまたはシンク列の Data Factory 中間データ型。 一般に、このプロパティを指定または変更する必要はありません。 詳細については、「[データ型のマッピング](#data-type-mapping)」を参照してください。 | いいえ       |
| culture  | ソースまたはシンク列のカルチャ。 型が `Datetime` または `Datetimeoffset` の場合に適用します。 既定では、 `en-us`です。<br>一般に、このプロパティを指定または変更する必要はありません。 詳細については、「[データ型のマッピング](#data-type-mapping)」を参照してください。 | いいえ       |
| format   | 種類が `Datetime` または `Datetimeoffset` のときに使用される書式文字列。 日時の書式を設定する方法については、「[カスタム日時書式指定文字列](/dotnet/standard/base-types/custom-date-and-time-format-strings)」を参照してください。 一般に、このプロパティを指定または変更する必要はありません。 詳細については、「[データ型のマッピング](#data-type-mapping)」を参照してください。 | いいえ       |

`mappings` に加えて、`translator` の下では、次のプロパティがサポートされています。

| プロパティ            | 説明                                                  | 必須 |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | 階層構造のソース (Cosmos DB、MongoDB、REST コネクタなど) からデータをコピーするときに適用します。<br>同じパターンを持つ **配列フィールド内** のオブジェクトからのデータの反復処理と抽出を行って、オブジェクトごとの行ごとに変換する場合は、その配列の JSON のパスを指定してクロス適用を行います。 | いいえ       |

#### <a name="tabular-source-to-tabular-sink"></a>表形式のソースから表形式のシンクへ

たとえば、Salesforce から Azure SQL Database にデータをコピーし、3 つの列を明示的にマップするには、次のようにします。

1. コピー アクティビティ -> [マッピング] タブで、 **[スキーマのインポート]** ボタンをクリックして、ソースおよびシンク スキーマの両方をインポートします。

2. 必要なフィールドをマップし、残りを除外または削除します。

![表形式を表形式にマップする](media/copy-activity-schema-and-type-mapping/map-tabular-to-tabular.png)

コピー アクティビティ ペイロードでは、同じマッピングを次のように構成できます (`translator` を参照してください)。

```json
{
    "name": "CopyActivityTabularToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "SalesforceSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "name": "Id" },
                    "sink": { "name": "CustomerID" }
                },
                {
                    "source": { "name": "Name" },
                    "sink": { "name": "LastName" }
                },
                {
                    "source": { "name": "LastModifiedDate" },
                    "sink": { "name": "ModifiedDate" }
                }
            ]
        }
    },
    ...
}
```

ヘッダー行なしの区切りテキスト ファイルからデータをコピーするには、列を名前ではなく序数で表します。 

```json
{
    "name": "CopyActivityTabularToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "ordinal": "1" },
                    "sink": { "name": "CustomerID" }
                }, 
                {
                    "source": { "ordinal": "2" },
                    "sink": { "name": "LastName" }
                }, 
                {
                    "source": { "ordinal": "3" },
                    "sink": { "name": "ModifiedDate" }
                }
            ]
        }
    },
    ...
}
```

#### <a name="hierarchical-source-to-tabular-sink"></a>階層構造のソースから表形式のシンクへ

階層構造のソースから表形式のシンクにデータをコピーする場合、コピー アクティビティにより、次の機能がサポートされます。

- オブジェクトと配列からデータを抽出します。
- 1 つの配列から同じパターンを持つ複数のオブジェクトをクロス適用します。この場合、1 つの JSON オブジェクトを複数のレコードに変換して、表形式の結果を生成します。

階層構造から表形式のより高度な変換では、[Data Flow](concepts-data-flow-overview.md) を使用できます。 

たとえば、次の内容が含まれるソース MongoDB ドキュメントがあるとします。

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

配列 *(order_pd と order_price)* 内のデータをフラット化して、ヘッダー行を含む次の形式のテキスト ファイルにコピーし、共通のルート情報 *(number、date、city)* とクロス結合します。

| orderNumber | orderDate | order_pd | order_price | city    |
| ----------- | --------- | -------- | ----------- | ------- |
| 01          | 20170122  | P1       | 23          | Seattle |
| 01          | 20170122  | P2       | 13          | Seattle |
| 01          | 20170122  | P3       | 231         | Seattle |

このようなマッピングは Data Factory 作成 UI で定義できます。

1. コピー アクティビティ -> [マッピング] タブで、 **[スキーマのインポート]** ボタンをクリックして、ソースおよびシンク スキーマの両方をインポートします。 スキーマのインポート時に、Data Factory により、上位のいくつかのオブジェクトがサンプリングされるので、フィールドが表示されない場合は、階層内の適切なレイヤーに追加できます。既存のフィールド名をポイントし、ノード、オブジェクト、または配列の追加を選択します。

2. データの反復処理と抽出を行う配列を選択します。 **コレクション参照** として自動的に設定されます。 この操作では、1 つの配列のみがサポートされることにご注意ください。

3. 必要なフィールドをシンクにマップします。 Data Factory によって、階層側の対応する JSON パスが自動的に決定されます。

> [!NOTE]
> コレクション参照としてマークされた配列が空で、チェック ボックスがオンになっているレコードの場合、レコード全体がスキップされます。

![UI を使用して階層構造から表形式にマップする](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-ui.png)

また、**詳細エディター** に切り替えることもでき、ここで、フィールドの JSON パスを直接表示および編集できます。 このビューに新しいマッピングを追加することを選択する場合は、JSON パスを指定します。

![詳細エディターを使用して階層構造から表形式にマップする](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-advanced-editor.png)

コピー アクティビティ ペイロードでは、同じマッピングを次のように構成できます (`translator` を参照してください)。


```json
{
    "name": "CopyActivityHierarchicalToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "MongoDbV2Source" },
        "sink": { "type": "DelimitedTextSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "path": "$['number']" },
                    "sink": { "name": "orderNumber" }
                },
                {
                    "source": { "path": "$['date']" },
                    "sink": { "name": "orderDate" }
                },
                {
                    "source": { "path": "['prod']" },
                    "sink": { "name": "order_pd" }
                },
                {
                    "source": { "path": "['price']" },
                    "sink": { "name": "order_price" }
                },
                {
                    "source": { "path": "$['city'][0]['name']" },
                    "sink": { "name": "city" }
                }
            ],
            "collectionReference": "$['orders']"
        }
    },
    ...
}
```

#### <a name="tabularhierarchical-source-to-hierarchical-sink"></a>表形式または階層構造のソースから階層構造のシンクへ

ユーザー エクスペリエンスのフローは、[階層構造のソースから表形式のシンク](#hierarchical-source-to-tabular-sink)の場合に似ています。 

表形式のソースから階層構造のシンクにデータをコピーする場合は、オブジェクト内の配列への書き込みはサポートされません。

階層構造のソースから階層構造のシンクにデータをコピーする場合は、オブジェクトまたは配列を選択して、内部フィールドに触れることなくシンクにマップすることによって、レイヤー全体の階層をさらに保持できます。

より高度なデータ再形成変換には、[Data Flow](concepts-data-flow-overview.md) を使用できます。 

### <a name="parameterize-mapping"></a>マッピングのパラメーター化

テンプレート化パイプラインを作成して数の多いオブジェクトを動的にコピーする場合は、[既定のマッピング](#default-mapping)を利用できるか、または各オブジェクトに対して[明示的なマッピング](#explicit-mapping)を定義する必要があるかどうかを判断します。

明示的なマッピングが必要な場合は、次のようにできます。

1. パイプライン レベルでオブジェクト型を持つパラメーターを定義します (たとえば、`mapping`)。

2. マッピングのパラメーター化: コピー アクティビティ -> [マッピング] タブで、動的なコンテンツを追加することを選択し、上記のパラメーターを選択します。 アクティビティ ペイロードは次のようになります。

    ```json
    {
        "name": "CopyActivityHierarchicalToTabular",
        "type": "Copy",
        "typeProperties": {
            "source": {...},
            "sink": {...},
            "translator": {
                "value": "@pipeline().parameters.mapping",
                "type": "Expression"
            },
            ...
        }
    }
    ```

3. マッピング パラメーターに渡す値を構築します。 これは、`translator` 定義のオブジェクト全体である必要があります。「[明示的なマッピング](#explicit-mapping)」セクションのサンプルを参照してください。 たとえば、表形式のソースから表形式のシンクへのコピーでは、値は `{"type":"TabularTranslator","mappings":[{"source":{"name":"Id"},"sink":{"name":"CustomerID"}},{"source":{"name":"Name"},"sink":{"name":"LastName"}},{"source":{"name":"LastModifiedDate"},"sink":{"name":"ModifiedDate"}}]}` である必要があります。

## <a name="data-type-mapping"></a>データ型マッピング

コピー アクティビティでは、次のフローを使用してソースの型からシンクの型へのマッピングを実行します。 

1. ソースのネイティブなデータ型から Azure Data Factory の中間データ型に変換します。
2. 必要に応じて、中間データ型を自動的に変換して、対応するシンクの型に一致させます。[既定のマッピング](#default-mapping)と[明示的なマッピング](#explicit-mapping)の両方に適用できます。
3. Azure Data Factory の中間データ型からシンクのネイティブなデータ型に変換します。

現在、コピー アクティビティによりサポートされている中間データ型は、Boolean、Byte、Byte 配列、Datetime、DatetimeOffset、Decimal、Double、GUID、Int16、Int32、Int64、SByte、Single、String、Timespan、UInt16、UInt32、UInt64 です。

次のデータ型変換は、ソースからシンクへの中間型の間でサポートされています。

| ソース \ シンク | Boolean | Byte array | Decimal | 日付/時刻 <small>(1)</small> | 浮動小数点 <small>(2)</small> | GUID | 整数 <small>(3)</small> | String | TimeSpan |
| ----------- | ------- | ---------- | ------- | ---------------------------- | ------------------------------ | ---- | -------------------------- | ------ | -------- |
| Boolean     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| Byte array  |         | ✓          |         |                              |                                |      |                            | ✓      |          |
| 日付/時刻   |         |            |         | ✓                            |                                |      |                            | ✓      |          |
| Decimal     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| 浮動小数点 | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| GUID        |         |            |         |                              |                                | ✓    |                            | ✓      |          |
| Integer     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| String      | ✓       | ✓          | ✓       | ✓                            | ✓                              | ✓    | ✓                          | ✓      | ✓        |
| TimeSpan    |         |            |         |                              |                                |      |                            | ✓      | ✓        |

(1) 日付/時刻には、DateTime と DateTimeOffset が含まれます。

(2) 浮動小数点には、Single と Double が含まれます。

(3) 整数には、SByte、Byte、Int16、UInt16、Int32、UInt32、Int64、UInt64 が含まれます。

> [!NOTE]
> - 現在、このようなデータ型の変換は、表形式のデータ間でコピーするときにサポートされています。 階層構造のソースおよびシンクはサポートされていません。これは、システム定義されたデータ型が、ソースとシンクの中間型の間では変換されないことを意味します。
> - この機能は、最新のデータセット モデルで動作します。 UI にこのオプションが表示されない場合は、新しいデータセットを作成してみてください。

データ型変換のコピー アクティビティでは、次のプロパティがサポートされています (プログラムの作成の `translator` セクションの下)。

| プロパティ                         | 説明                                                  | 必須 |
| -------------------------------- | ------------------------------------------------------------ | -------- |
| typeConversion                   | 新しいデータ型変換エクスペリエンスを有効にします。 <br>下位互換性のため、既定値は false です。<br><br>2020 年 6 月下旬以降に Data Factory 作成 UI を使用して作成された新しいコピー アクティビティについては、このデータ型変換が、最善のエクスペリエンスを実現するために既定で有効になっています。また、該当するシナリオのコピー アクティビティ -> [マッピング] タブで次の型変換設定を確認できます。 <br>プログラムを使ってパイプラインを作成するには、`typeConversion` プロパティを明示的に true に設定して有効にする必要があります。<br>この機能がリリースされる前に作成された既存のコピー アクティビティについては、下位互換性のため、型変換オプションが Data Factory 作成 UI に表示されません。 | いいえ       |
| typeConversionSettings           | 型変換設定のグループ。 `typeConversion` が `true` に設定されている場合に適用します。 次のプロパティはすべてこのグループの下にあります。 | いいえ       |
| `typeConversionSettings` の "*下*" |                                                              |          |
| allowDataTruncation              | コピー中に異なる型のシンクにソース データを変換するときのデータの切り捨てを許可します。たとえば、10 進から整数への変換では、DatetimeOffset から Datetime になります。 <br>既定値は true です。 | いいえ       |
| treatBooleanAsNumber             | ブール値を数値として扱います。たとえば、true は 1 として扱います。<br>既定値は false です。 | いいえ       |
| dateTimeFormat                   | 日付 (タイム ゾーン オフセットなし) と文字列の間で変換する場合の書式指定文字列 (`yyyy-MM-dd HH:mm:ss.fff` など)。  詳細については、[カスタム日時書式指定文字列](/dotnet/standard/base-types/custom-date-and-time-format-strings)に関する記事を参照してください。 | いいえ       |
| dateTimeOffsetFormat             | 日付 (タイム ゾーン オフセットあり) と文字列の間で変換する場合の書式指定文字列 (`yyyy-MM-dd HH:mm:ss.fff zzz` など)。  詳細については、[カスタム日時書式指定文字列](/dotnet/standard/base-types/custom-date-and-time-format-strings)に関する記事を参照してください。 | いいえ       |
| timeSpanFormat                   | 時間間隔と文字列の間で変換する場合の書式指定文字列 (`dd\.hh\:mm` など)。 詳細については、[カスタム TimeSpan 書式指定文字列](/dotnet/standard/base-types/custom-timespan-format-strings)に関する記事を参照してください。 | いいえ       |
| culture                          | `en-us` や `fr-fr`など、型を変換するときに使用されるカルチャ情報。 | いいえ       |

**例:**

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "ParquetSource"
        },
        "sink": {
            "type": "SqlSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "typeConversion": true,
            "typeConversionSettings": {
                "allowDataTruncation": true,
                "treatBooleanAsNumber": true,
                "dateTimeFormat": "yyyy-MM-dd HH:mm:ss.fff",
                "dateTimeOffsetFormat": "yyyy-MM-dd HH:mm:ss.fff zzz",
                "timeSpanFormat": "dd\.hh\:mm",
                "culture": "en-gb"
            }
        }
    },
    ...
}
```

## <a name="legacy-models"></a>レガシ モデル

> [!NOTE]
> ソース列とフィールドをシンクにマップする次のモデルは、下位互換性のために引き続きサポートされています。 「[スキーマ マッピング](#schema-mapping)」に記載されている新しいモデルを使用することをお勧めします。 Data Factory 作成 UI は、新しいモデルの生成に切り替えられました。

### <a name="alternative-column-mapping-legacy-model"></a>代替の列マッピング (レガシ モデル)

コピー アクティビティ-> `translator` -> `columnMappings` を指定して、表形式のデータ間をマップできます。 この場合、"structure" セクションは、入力と出力の両方のデータセットに必要です。 列マッピングでは、**ソース データセットの "structure" 内のすべての列または列のサブセットのシンク データセットの "structure" 内のすべての列へのマッピング** がサポートされます。 次のエラー状態では例外が発生します。

- ソース データ ストアのクエリの結果に、入力データセットの "構造" セクションで指定された列名は含まれません。
- シンク データ ストア (定義済みのスキーマを持つ場合) に、出力データセットの "structure" セクションで指定された列名は含まれません。
- シンク データセットの "structure" 内の列数が、マッピングで指定された数より少ないかまたは多い。
- 重複したマッピング。

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

### <a name="alternative-schema-mapping-legacy-model"></a>代替のスキーマ マッピング (レガシ モデル)

コピー アクティビティ -> `translator` -> `schemaMapping` を指定して、階層構造のデータと表形式のデータの間をマップできます。たとえば MongoDB/REST からテキスト ファイルにコピーしたり、Oracle から MongoDB の Azure Cosmos DB の API にコピーしたりできます。 コピー アクティビティの `translator` セクションでは、次のプロパティがサポートされます。

| プロパティ            | 説明                                                  | 必須 |
| :------------------ | :----------------------------------------------------------- | :------- |
| type                | コピー アクティビティのトランスレーターの type プロパティは **TabularTranslator** に設定する必要があります。 | はい      |
| schemaMapping       | キーと値のペアのコレクション。**ソース側からシンク側** へのマッピングの関係を表します。<br/>- **Key:** ソースを表します。 **表形式のソース** の場合、データセットの構造に定義された列名を指定します。**階層構造のソース** の場合、抽出とマッピングの対象となる各フィールドの JSON パス式を指定します。<br>- **Value:** シンクを表します。 **表形式のシンク** の場合、データセットの構造に定義された列名を指定します。**階層構造のシンク** の場合、抽出とマッピングの対象となる各フィールドの JSON パス式を指定します。 <br>階層構造のデータで、ルート オブジェクトの直下のフィールドの場合、JSON パスはルートの $ から記述します。`collectionReference` プロパティによって選択された配列内のフィールドの場合、JSON パスは配列要素から記述します。 | はい      |
| collectionReference | 同じパターンを持つ **配列フィールド内** のオブジェクトからのデータの反復処理と抽出を行って、オブジェクトごとの行ごとに変換する場合は、その配列の JSON のパスを指定してクロス適用を行います。 このプロパティは、階層形式のデータがソースであるときにのみサポートされます。 | いいえ       |

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

| orderNumber | orderDate | order_pd | order_price | city    |
| ----------- | --------- | -------- | ----------- | ------- |
| 01          | 20170122  | P1       | 23          | Seattle |
| 01          | 20170122  | P2       | 13          | Seattle |
| 01          | 20170122  | P3       | 231         | Seattle |

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
                "$.number": "orderNumber",
                "$.date": "orderDate",
                "prod": "order_pd",
                "price": "order_price",
                "$.city[0].name": "city"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="next-steps"></a>次のステップ
コピー アクティビティの他の記事を参照してください。

- [コピー アクティビティの概要](copy-activity-overview.md)
