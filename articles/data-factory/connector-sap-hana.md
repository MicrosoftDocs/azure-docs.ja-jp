---
title: Azure Data Factory を使用して SAP HANA からデータをコピーする | Microsoft Docs
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、SAP HANA からサポートされているシンク データ ストアへデータをコピーする方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: jingwang
ms.openlocfilehash: e9b024fc3c07670201cf72cf80c0b69bf68f1cc8
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68725996"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Azure Data Factory を使用して SAP HANA からデータをコピーする
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-sap-hana-connector.md)
> * [現在のバージョン](connector-sap-hana.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、SAP HANA データベースからデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

SAP HANA データベースから、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。

具体的には、この SAP HANA コネクタは以下をサポートします。

- SAP HANA データベースの任意のバージョンからのデータのコピー。
- **HANA 情報モデル** (分析ビューや計算ビューなど) および**行/列のテーブル**からのデータのコピー。
- **基本**または **Windows** 認証を使用したデータのコピー。

> [!TIP]
> データを SAP HANA データ ストア**に**コピーするには、汎用 ODBC コネクタを使用します。 詳細については、「[SAP HANA シンク](connector-odbc.md#sap-hana-sink)」を参照してください。 SAP HANA コネクタと ODBC コネクタ用のリンクされたサービスは種類が異なるため、再利用することはできないことに注意してください。

## <a name="prerequisites"></a>前提条件

この SAP HANA コネクタを使用するには、次の手順が必要です。

- セルフホステッド統合ランタイムをセットアップする。 詳細については、[セルフホステッド統合ランタイム](create-self-hosted-integration-runtime.md)に関する記事をご覧ください。
- SAP HANA ODBC ドライバーを統合ランタイム コンピューターにインストールします。 SAP HANA ODBC ドライバーは [SAP ソフトウェアのダウンロード センター](https://support.sap.com/swdc)からダウンロードできます。 "**SAP HANA CLIENT for Windows**" というキーワードで検索してください。

## <a name="getting-started"></a>使用の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、SAP HANA コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

SAP HANA のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**SapHana** | はい |
| connectionString | **基本認証**または **Windows 認証**のいずれかを使用して SAP HANA に接続するために必要な情報を指定します。 以下のサンプルを参照してください。<br>接続文字列には、サーバー/ポートが必要です (既定のポートは 30015)。基本認証を使用する場合は、ユーザー名とパスワードが必要です。 詳細設定については、[SAP HANA ODBC の接続プロパティ](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>)に関する記事を参照してください。<br/>パスワードを Azure Key Vault に格納し、接続文字列からパスワード構成を引き出すこともできます。 詳細については、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」の記事を参照してください。 | はい |
| userName | Windows 認証を使用する場合は、ユーザー名を指定します。 例: `user@domain.com` | いいえ |
| password | ユーザー アカウントのパスワードを指定します。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | いいえ |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 「[前提条件](#prerequisites)」に記されているように、セルフホステッド統合ランタイムが必要です。 |はい |

**例: 基本認証を使用する**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;UID=<userName>;PWD=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例: Windows 認証を使用する**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;",
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

以下のペイロードで SAP HANA のリンクされたサービスを使用していた場合、現状のまま引き続きサポートされますが、今後は新しいものを使用することをお勧めします。

**例:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
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

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、データセットに関する記事をご覧ください。 このセクションでは、SAP HANA データセット でサポートされるプロパティの一覧を示します。

SAP HANA からのデータ コピーについては、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります:**SapHanaTable** | はい |
| schema | SAP HANA データベース内のスキーマの名前。 | いいえ (アクティビティ ソースの "query" が指定されている場合) |
| table | SAP HANA データベース内のテーブルの名前。 | いいえ (アクティビティ ソースの "query" が指定されている場合) |

**例:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "SapHanaTable",
        "typeProperties": {
            "schema": "<schema name>",
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

`RelationalTable` 型のデータセットを使用していた場合、現状のまま引き続きサポートされますが、今後は新しいものを使用することをお勧めします。

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、SAP HANA ソースでサポートされるプロパティの一覧を示します。

### <a name="sap-hana-as-source"></a>ソースとしての SAP HANA

SAP HANA からデータをコピーするために、コピー アクティビティの **source** セクションでは次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは、次のように設定する必要があります:**SapHanaSource** | はい |
| query | SAP HANA インスタンスからデータを読み取る SQL クエリを指定します。 | はい |
| packetSize | データを複数のブロックに分割するためのネットワーク パケット サイズ (KB 単位) を指定します。 大量のデータをコピーする場合、パケット サイズを大きくすると、ほとんどの場合、SAP HANA からの読み取り速度が向上する可能性があります。 パケット サイズを調整する場合は、パフォーマンス テストを行うことをお勧めします。 | いいえ。<br>既定値は 2,048 (2 MB)です。 |

**例:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "type": "SapHanaSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

`RelationalSource` 型のコピー ソースを使用していた場合、現状のまま引き続きサポートされますが、今後は新しいものを使用することをお勧めします。

## <a name="data-type-mapping-for-sap-hana"></a>SAP HANA のデータ型マッピング

SAP HANA からデータをコピーするとき、次の SAP HANAのデータ型から Azure Data Factory の中間データ型へのマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| SAP HANA のデータ型 | Data Factory の中間データ型 |
| ------------------ | ------------------------------ |
| ALPHANUM           | string                         |
| BIGINT             | Int64                          |
| BINARY             | Byte[]                         |
| BINTEXT            | string                         |
| BLOB               | Byte[]                         |
| BOOL               | Byte                           |
| CLOB               | string                         |
| DATE               | Datetime                       |
| DECIMAL            | Decimal                        |
| DOUBLE             | DOUBLE                         |
| FLOAT              | Double                         |
| INTEGER            | Int32                          |
| NCLOB              | string                         |
| NVARCHAR           | string                         |
| REAL               | Single                         |
| SECONDDATE         | Datetime                       |
| SHORTTEXT          | string                         |
| SMALLDECIMAL       | Decimal                        |
| SMALLINT           | Int16                          |
| STGEOMETRYTYPE     | Byte[]                         |
| STPOINTTYPE        | Byte[]                         |
| TEXT               | string                         |
| TIME               | TimeSpan                       |
| TINYINT            | Byte                           |
| VARCHAR            | string                         |
| TIMESTAMP          | Datetime                       |
| VARBINARY          | Byte[]                         |

## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
