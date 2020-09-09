---
title: Azure Data Factory を使用して ODBC データ ストアをコピー元またはコピー先としてデータをコピーする
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、ODBC データ ストアをコピー元またはコピー先としてデータをコピーする方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: jingwang
ms.openlocfilehash: c92428666f0766f78475be16416027cdc6e71f20
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85506533"
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Azure Data Factory を使用して ODBC データ ストアをコピー元またはコピー先としてデータをコピーする
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-odbc-connector.md)
> * [現在のバージョン](connector-odbc.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory のコピー アクティビティを使用して、ODBC データ ストアをコピー元またはコピー先としてデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

この ODBC コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

ODBC ソースのデータをサポートされる任意のシンク データ ストアにコピーしたり、サポートされる任意のソース データ ストアのデータを ODBC シンクにコピーしたりできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この ODBC コネクタは、**基本**または**匿名**認証を使用して、**任意の ODBC 対応データ ストア**をコピー元またはコピー先とするデータのコピーをサポートします。 **64 ビットの ODBC ドライバー**が必要です。 ODBC シンクの場合、ADF は ODBC バージョン 2.0 標準をサポートします。

## <a name="prerequisites"></a>前提条件

この ODBC コネクタを使用するには、次の手順が必要です。

- セルフホステッド統合ランタイムをセットアップする。 詳細については、[セルフホステッド統合ランタイム](create-self-hosted-integration-runtime.md)に関する記事をご覧ください。
- データ ストア用の 64 ビット ODBC ドライバーを Integration Runtime マシンにインストールする。

## <a name="getting-started"></a>作業の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、ODBC コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

ODBC のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**Odbc** | はい |
| connectionString | 資格情報部分を除外した接続文字列。 `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"` のようなパターンで接続文字列を指定するか、Integration Runtime マシンに設定したシステム DSN (データ ソース名) を `"DSN=<name of the DSN on IR machine>;"` で使用することができます (その場合も、リンクされたサービスの資格情報部分をそれに応じて指定する必要があります)。<br>パスワードを Azure Key Vault に格納して、接続文字列から  `password`  構成をプルすることもできます。 詳細については、「 [Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md) 」を参照してください。| はい |
| authenticationType | ODBC データ ストアへの接続に使用される認証の種類です。<br/>使用できる値は、以下のとおりです。**Basic** と **Anonymous**。 | はい |
| userName | 基本認証を使用している場合は、ユーザー名を指定します。 | いいえ |
| password | userName に指定したユーザー アカウントのパスワードを指定します。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | いいえ |
| 資格情報 (credential) | ドライバー固有のプロパティ値の形式で指定された接続文字列のアクセス資格情報の部分。 例: `"RefreshToken=<secret refresh token>;"`. このフィールドを SecureString とマークします。 | いいえ |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 「[前提条件](#prerequisites)」に記されているように、セルフホステッド統合ランタイムが必要です。 |はい |

**例 1: 基本認証の使用**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "<connection string>",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例 2: 匿名認証の使用**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "<connection string>",
            "authenticationType": "Anonymous",
            "credential": {
                "type": "SecureString",
                "value": "RefreshToken=<secret refresh token>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、ODBC データセット でサポートされるプロパティの一覧を示します。

ODBC 互換データ ストアとの間でデータをコピーする場合、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります:**OdbcTable** | はい |
| tableName | ODBC データ ストア内のテーブルの名前。 | ソースの場合はいいえ (アクティビティ ソースの "query" が指定されている場合)、<br/>シンクの場合ははい |

**例**

```json
{
    "name": "ODBCDataset",
    "properties": {
        "type": "OdbcTable",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ODBC linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

`RelationalTable` 型のデータセットを使用していた場合、現状のまま引き続きサポートされますが、今後は新しいものを使用することをお勧めします。

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、ODBC ソースでサポートされるプロパティの一覧を示します。

### <a name="odbc-as-source"></a>ソースとしての ODBC

ODBC 互換データ ストアからデータをコピーする場合、コピー アクティビティの **source** セクションで次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは、次のように設定する必要があります:**OdbcSource** | はい |
| query | カスタム SQL クエリを使用してデータを読み取ります。 (例: `"SELECT * FROM MyTable"`)。 | いいえ (データセットの "tableName" が指定されている場合) |

**例:**

```json
"activities":[
    {
        "name": "CopyFromODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ODBC input dataset name>",
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
                "type": "OdbcSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

`RelationalSource` 型のソースを使用していた場合は現状のまま引き続きサポートされますが、今後は新しいものを使用することをお勧めします。

### <a name="odbc-as-sink"></a>シンクとしての ODBC

ODBC 対応データ ストアにデータをコピーするには、コピー アクティビティのシンクの種類を **OdbcSink** に設定します。 コピー アクティビティの **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの type プロパティは、次のように設定する必要があります: **OdbcSink** | はい |
| writeBatchTimeout |タイムアウトする前に一括挿入操作の完了を待つ時間です。<br/>使用可能な値: 期間。 例:"00:30:00" (30 分)。 |いいえ |
| writeBatchSize |バッファー サイズが writeBatchSize に達したときに SQL テーブルにデータを挿入します。<br/>使用可能な値: 整数 (行数)。 |いいえ (既定値は 0 - 自動検出) |
| preCopyScript |コピー アクティビティの毎回の実行で、データをデータ ストアに書き込む前に実行する SQL クエリを指定します。 このプロパティを使用して、事前に読み込まれたデータをクリーンアップできます。 |いいえ |

> [!NOTE]
> "writeBatchSize" が設定されていない場合 (自動検出)、コピー アクティビティは、ドライバーでバッチ操作がサポートされるかどうかを検出した後、サポートされる場合は 10000 を、サポートされない場合は 1 を設定します。 0 以外の値を明示的に設定した場合、コピー アクティビティはその値を尊重します。実行時にドライバーがバッチ操作をサポートしていない場合、操作は失敗します。

**例:**

```json
"activities":[
    {
        "name": "CopyToODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ODBC output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OdbcSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="troubleshoot-connectivity-issues"></a>接続の問題のトラブルシューティング

接続の問題をトラブルシューティングするには、**Integration Runtime Configuration Manager**の **[診断]** タブを使用します。

1. **Integration Runtime Configuration Manager** を起動します。
2. **[診断]** タブに切り替えます。
3. [テスト接続] セクションで、データ ストアの **[種類]** (リンクされたサービス) を選択します。
4. データ ストアへの接続に使用する **[接続文字列]** を入力し、 **[認証]** を選択し、 **[ユーザー名]** 、 **[パスワード]** 、 **[資格情報]** を入力します。
5. **[接続テスト]** をクリックして、データ ストアへの接続をテストします。

## <a name="next-steps"></a>次のステップ
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
