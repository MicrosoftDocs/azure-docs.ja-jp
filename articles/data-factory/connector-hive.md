---
title: Azure Data Factory を使用して Hive からデータをコピーする
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、Hive のデータをサポートされているシンク データ ストアにコピーする方法について説明します。
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: jingwang
ms.openlocfilehash: 8f6e85d82c01663e404f7046f84706feb209ba5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100367029"
---
# <a name="copy-and-transform-data-from-hive-using-azure-data-factory"></a>Azure Data Factory を使用して Hive からデータをコピーおよび変換する 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory のコピー アクティビティを使用して、Hive からデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

この Hive コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

Hive から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

Azure Data Factory では接続を有効にする組み込みのドライバーが提供されるので、このコネクタを使用してドライバーを手動でインストールする必要はありません。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>作業の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、Hive コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Hive のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**Hive** | はい |
| host | Hive サーバーの IP アドレスまたはホスト名。複数のホストは ';' で区切ります (serviceDiscoveryMode が有効な場合のみ)。  | はい |
| port | Hive サーバーがクライアント接続のリッスンに使用する TCP ポート。 Azure HDInsights に接続する場合は、port を 443 と指定します。 | はい |
| serverType | Hive サーバーの種類。 <br/>使用できる値は、以下のとおりです。**HiveServer1**、**HiveServer2**、**HiveThriftServer**。 | いいえ |
| thriftTransportProtocol | Thrift レイヤーで使用するトランスポート プロトコル。 <br/>使用できる値は、以下のとおりです。**Binary**、**SASL**、**HTTP**。 | いいえ |
| authenticationType | Hive サーバーへのアクセスに使用する認証方法。 <br/>使用できる値は、以下のとおりです。**Anonymous**、**Username**、**UsernameAndPassword**、**WindowsAzureHDInsightService**。 Kerberos 認証は現在サポートされていません。 | はい |
| serviceDiscoveryMode | ZooKeeper サービスの使用を指定する場合は true、そうでない場合は false。  | いいえ |
| zooKeeperNameSpace | Hive サーバーの 2 ノードが追加される ZooKeeper 上の名前空間。  | いいえ |
| useNativeQuery | ドライバーがネイティブの HiveQL クエリを使用するか、または HiveQL の同等の形式に変換するかを指定します。  | いいえ |
| username | Hive サーバーへのアクセスに使用するユーザー名。  | いいえ |
| password | ユーザーに対応するパスワード。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | いいえ |
| httpPath | Hive サーバーに対応する部分的な URL。  | いいえ |
| enableSsl | サーバーへの接続が TLS を使用して暗号化されるかどうかを指定します。 既定値は false です。  | いいえ |
| trustedCertPath | TLS 経由で接続するときにサーバーを検証するための信頼された CA 証明書を含む .pem ファイルの完全なパス。 このプロパティは、セルフホステッド IR 上で TLS を使用している場合にのみ設定できます。 既定値は、IR でインストールされる cacerts.pem ファイルです。  | いいえ |
| useSystemTrustStore | システムの信頼ストアと指定した PEM ファイルのどちらの CA 証明書を使用するかを指定します。 既定値は false です。  | いいえ |
| allowHostNameCNMismatch | TLS 経由で接続するときに、CA が発行した TLS/SSL 証明書名がサーバーのホスト名と一致する必要があるかどうかを指定します。 既定値は false です。  | いいえ |
| allowSelfSignedServerCert | サーバーからの自己署名証明書を許可するかどうかを指定します。 既定値は false です。  | いいえ |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 詳細については、「[前提条件](#prerequisites)」セクションを参照してください。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ |
| storageReference | マッピング データ フローでデータをステージングするために使用されるストレージ アカウントのリンクされたサービスへの参照。 これは、マッピング データ フローで Hive のリンクされたサービスを使用する場合にのみ必要です | いいえ |

**例:**

```json
{
    "name": "HiveLinkedService",
    "properties": {
        "type": "Hive",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Hive データセットでサポートされるプロパティの一覧を示します。

Hive からデータをコピーするには、データセットの type プロパティを **HiveObject** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります:**HiveObject** | はい |
| schema | スキーマの名前。 |いいえ (アクティビティ ソースの "query" が指定されている場合)  |
| table | テーブルの名前。 |いいえ (アクティビティ ソースの "query" が指定されている場合)  |
| tableName | スキーマ部分を含むテーブルの名前。 このプロパティは下位互換性のためにサポートされています。 新しいワークロードでは、`schema` と `table` を使用します。 | いいえ (アクティビティ ソースの "query" が指定されている場合) |

**例**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Hive ソースでサポートされるプロパティの一覧を示します。

### <a name="hivesource-as-source"></a>ソースとしての HiveSource

Hive からデータをコピーするは、コピー アクティビティのソースの種類を **HiveSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは、次のように設定する必要があります:**HiveSource** | はい |
| query | カスタム SQL クエリを使用してデータを読み取ります。 (例: `"SELECT * FROM MyTable"`)。 | いいえ (データセットの "tableName" が指定されている場合) |

**例:**

```json
"activities":[
    {
        "name": "CopyFromHive",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hive input dataset name>",
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
                "type": "HiveSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapping Data Flow のプロパティ

Hive コネクタは、マッピング データ フローで[インライン データセット](data-flow-source.md#inline-datasets)のソースとしてサポートされています。 クエリを使用するか、HDInsight の Hive テーブルから直接読み取ります。 Hive データは、データ フローの一部として変換される前に、ストレージ アカウントで Parquet ファイルとしてステージングされます。 

### <a name="source-properties"></a>ソース プロパティ

次の表に、Hive ソースでサポートされるプロパティの一覧を示します。 これらのプロパティは、 **[ソース オプション]** タブで編集できます。

| 名前 | 説明 | 必須 | 使用できる値 | データ フロー スクリプトのプロパティ |
| ---- | ----------- | -------- | -------------- | ---------------- |
| ストア | ストアは `hive` である必要があります | はい |  `hive` | store | 
| フォーマット | テーブルまたはクエリから読み取るかを指定します | はい | `table` または `query` | format |
| スキーマ名 | テーブルから読み取る場合、ソース テーブルのスキーマ |  はい (フォーマットが `table` の場合) | String | schemaName |
| テーブル名 | テーブルから読み取る場合、テーブル名 |   はい (フォーマットが `table` の場合) | String | tableName |
| クエリ | フォーマットが `query` の場合、Hive のリンクされたサービスに対するソース クエリ | はい (フォーマットが `query` の場合) | String | query |
| ステージング済み | Hive テーブルは常にステージングされます。 | はい | `true` | staged |
| ストレージ コンテナー | Hive から読み取る前、または Hive に書き込む前にデータをステージングするために使用されるストレージ コンテナー。 Hive クラスターは、このコンテナーへのアクセス権を持っている必要があります。 | はい | String | storageContainer |
| ステージング データベース | リンクされたサービスで指定されたユーザー アカウントがアクセスできるスキーマ/データベース。 ステージング中に外部テーブルを作成するために使用され、その後削除します | no | `true` または `false` | stagingDatabaseName |
| 事前 SQL スクリプト | データを読み取る前に Hive テーブルで実行する SQL コード | no | String | preSQLs |

#### <a name="source-example"></a>ソースの例

以下に Hive ソース構成の例を示します。

![Hive ソースの例](media/data-flow/hive-source.png "[Hive ソースの例")

これらの設定は、次のデータ フロー スクリプトに変換されます。

```
source(
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false,
    format: 'table',
    store: 'hive',
    schemaName: 'default',
    tableName: 'hivesampletable',
    staged: true,
    storageContainer: 'khive',
    storageFolderPath: '',
    stagingDatabaseName: 'default') ~> hivesource
```
### <a name="known-limitations"></a>既知の制限事項

* 配列、マップ、構造体、和集合などの複合型の読み取りはサポートされていません。 
* Hive コネクタがサポートしているのは、バージョン 4.0 以上 (Apache Hive 3.1.0) の Azure HDInsight の Hive テーブルのみです

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
