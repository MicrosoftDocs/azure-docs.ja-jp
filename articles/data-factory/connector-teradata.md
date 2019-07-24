---
title: Azure Data Factory を使用した Teradata からのデータ コピー | Microsoft Docs
description: Data Factory サービスの Teradata コネクタについて説明します。このコネクタを使用すると、Teradata データベースから、Data Factory によってサポートされているデータ ストアに、シンクとしてデータをコピーすることができます。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: e9fd818990c8a985a77c2e7eeea19bf63c440e4e
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54018995"
---
# <a name="copy-data-from-teradata-using-azure-data-factory"></a>Azure Data Factory を使用して Teradata からデータをコピーする
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-onprem-teradata-connector.md)
> * [現在のバージョン](connector-teradata.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、Teradata データベースからデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

Teradata データベースから、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この Teradata コネクタは以下をサポートします。

- Teradata **バージョン 12 以降**
- **基本**または **Windows** 認証を使用したデータのコピー。

## <a name="prerequisites"></a>前提条件

この Teradata コネクタを使用するには、次の手順が必要です。

- セルフホステッド統合ランタイムをセットアップする。 詳細については、[セルフホステッド統合ランタイム](create-self-hosted-integration-runtime.md)に関する記事をご覧ください。
- 統合ランタイムのコンピューターに、[.NET Data Provider for Teradata](https://go.microsoft.com/fwlink/?LinkId=278886) バージョン 14 以上をインストールします。

## <a name="getting-started"></a>使用の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、Teradata コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Teradata のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**Teradata** | はい |
| server | Teradata のサーバーの名前です。 | はい |
| authenticationType | Teradata データベースへの接続に使用される認証の種類です。<br/>使用できる値は、以下のとおりです。**Basic** および **Windows**。 | はい |
| username | Teradata データベースに接続するユーザー名を指定します。 | はい |
| password | ユーザー名に指定したユーザー アカウントのパスワードを指定します。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 「[前提条件](#prerequisites)」に記されているように、セルフホステッド統合ランタイムが必要です。 |はい |

**例:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "Basic",
            "username": "<username>",
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、データセットに関する記事をご覧ください。 このセクションでは、Teradata データセット でサポートされるプロパティの一覧を示します。

Teradata からデータをコピーするには、データセットの type プロパティを **RelationalTable** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります:**RelationalTable** | はい |
| tableName | Teradata データベースのテーブルの名前。 | いいえ (アクティビティ ソースの "query" が指定されている場合) |

**例:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Teradata ソースでサポートされるプロパティの一覧を示します。

### <a name="teradata-as-source"></a>ソースとしての Teradata

Teradata からデータをコピーするには、コピー アクティビティのソース タイプを **RelationalSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは、次のように設定する必要があります:**RelationalSource** | はい |
| query | カスタム SQL クエリを使用してデータを読み取ります。 (例: `"SELECT * FROM MyTable"`)。 | いいえ (データセットの "tableName" が指定されている場合) |

**例:**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "RelationalSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-teradata"></a>Teradata のデータ型マッピング

Teradata からデータをコピーするとき、Teradata のデータ型から Azure Data Factory の中間データ型への、以下のマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| Teradata のデータ型 | Data Factory の中間データ型 |
|:--- |:--- |
| BigInt |Int64 |
| BLOB |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |String |
| Clob |String |
| Date |Datetime |
| Decimal |Decimal |
| Double |Double |
| Graphic |String |
| Integer |Int32 |
| Interval Day |timespan |
| Interval Day To Hour |timespan |
| Interval Day To Minute |timespan |
| Interval Day To Second |timespan |
| Interval Hour |timespan |
| Interval Hour To Minute |timespan |
| Interval Hour To Second |timespan |
| Interval Minute |timespan |
| Interval Minute To Second |timespan |
| Interval Month |String |
| Interval Second |timespan |
| Interval Year |String |
| Interval Year To Month |String |
| Number |Double |
| Period(Date) |String |
| Period(Time) |String |
| Period(Time With Time Zone) |String |
| Period(Timestamp) |String |
| Period(Timestamp With Time Zone) |String |
| SmallInt |Int16 |
| Time |timespan |
| Time With Time Zone |String |
| Timestamp |Datetime |
| Timestamp With Time Zone |DateTimeOffset |
| VarByte |Byte[] |
| VarChar |String |
| VarGraphic |String |
| xml |String |


## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
