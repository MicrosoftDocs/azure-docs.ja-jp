---
title: MongoDB からデータをコピーする
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、MongoDB のデータをサポートされているシンク データ ストアにコピーする方法について説明します。
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2021
ms.openlocfilehash: eae2d2adfe2cfdd7e47f2ace9ede9253003aa5bf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100368780"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Azure Data Factory を使用して MongoDB のデータをコピーする

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory のコピー アクティビティを使用して、MongoDB データベースからデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

>[!IMPORTANT]
>ADF は、MongoDB のネイティブ サポートを向上させる、この新しいバージョンの MongoDB コネクタをリリースします。 旧バージョンとの互換性のため引き続きサポートされている以前の MongoDB コネクタをソリューションで使用している場合は、「[MongoDB コネクタ (レガシ)](connector-mongodb-legacy.md)」の記事を参照してください。


## <a name="supported-capabilities"></a>サポートされる機能

MongoDB データベースのデータを、サポートされているシンク データ ストアにコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この MongoDB コネクタでは **4.2 までのバージョン** がサポートされます。


## <a name="prerequisites"></a>前提条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]


## <a name="getting-started"></a>作業の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、MongoDB コネクタに固有の Data Factory エンティティを定義するために使用されるプロパティについて詳しく説明します。


## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

MongoDB のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type |type プロパティは、次のように設定する必要があります:**MongoDbV2** |はい |
| connectionString |MongoDB 接続文字列 (例: `mongodb://[username:password@]host[:port][/[database][?options]]`) を指定します。 詳細については、[MongoDB のマニュアルの接続文字列に関するページ](https://docs.mongodb.com/manual/reference/connection-string/)を参照してください。 <br/><br /> 接続文字列を Azure Key Vault に格納することもできます。 詳細については、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」を参照してください。 |はい |
| database | アクセスするデータベースの名前。 | はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 詳細については、「[前提条件](#prerequisites)」セクションを参照してください。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ |

**例:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": "mongodb://[username:password@]host[:port][/[database][?options]]",
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットの定義に使用できるセクションとプロパティの完全な一覧については、「[データセットとリンクされたサービス](concepts-datasets-linked-services.md)」を参照してください。 MongoDB データセットでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります:**MongoDbV2Collection** | はい |
| collectionName |MongoDB データベースのコレクション名前。 |はい |

**例:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbV2Collection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```


## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、MongoDB ソースでサポートされるプロパティの一覧を示します。

### <a name="mongodb-as-source"></a>ソースとしての MongoDB

コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは、次のように設定する必要があります:**MongoDbV2Source** | はい |
| filter | クエリ演算子を使用して選択フィルターを指定します。 コレクション内のすべてのドキュメントを返すには、このパラメーターを省略するか、空のドキュメント ({}) を渡します。 | いいえ |
| cursorMethods.project | プロジェクションのドキュメントで返されるフィールドを指定します。 一致するドキュメント内のすべてのフィールドを返すには、このパラメーターを省略します。 | いいえ |
| cursorMethods.sort | 一致するドキュメントがクエリによって返される順序を指定します。 「[cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort)」を参照してください。 | いいえ |
| cursorMethods.limit | サーバーが返すドキュメントの最大数を指定します。 「[cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit)」を参照してください。  | いいえ |
| cursorMethods.skip | スキップするドキュメントの数と、MongoDB が結果を返すときの開始位置を指定します。 「[cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip)」を参照してください。 | いいえ |
| batchSize | MongoDB インスタンスからの応答の各バッチで返されるドキュメントの数を指定します。 ほとんどの場合、バッチ サイズを変更しても、ユーザーまたはアプリケーションへの影響はありません。 Cosmos DB では各バッチのサイズが 40 MB を超過しないように制限されていますが、これはドキュメントが batchSize の数だけ存在するときの合計サイズなので、ドキュメントのサイズが大きくなる場合はこの値を減らしてください。 | いいえ<br/>(既定値は **100**) |

>[!TIP]
>ADF は、**厳格モード** での BSON ドキュメントの利用をサポートしています。 フィルター クエリがシェル モードではなく厳格モードであることを確認してください。 詳細については、[MongoDB のマニュアル](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html)を参照してください。

**例:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "MongoDbV2Source",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="export-json-documents-as-is"></a>JSON ドキュメントをそのままエクスポートする

この MongoDB コネクタを使用すると、JSON ドキュメントを MongoDB コレクションからさまざまなファイル ベースのストアまたは Azure Cosmos DB にそのままエクスポートできます。 このようなスキーマに依存しないコピーを実現するには、データセットの "構造" ("*スキーマ*" とも呼ばれる) のセクションと、コピー アクティビティでのスキーマ マッピングをスキップします。


## <a name="schema-mapping"></a>スキーマ マッピング

MongoDB から表形式のシンクにデータをコピーするには、「[スキーマ マッピング](copy-activity-schema-and-type-mapping.md#schema-mapping)」を参照してください。


## <a name="next-steps"></a>次のステップ
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
