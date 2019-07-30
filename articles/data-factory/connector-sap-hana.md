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
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: cdd83c3ff9d34a5e8b7f2c164136ab82f498ffb5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60343768"
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
- SQL クエリを使用した、**HANA 情報モデル** (分析ビューや計算ビューなど) および**行/列のテーブル**からのデータのコピー。
- **基本**または **Windows** 認証を使用したデータのコピー。

> [!NOTE]
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
| サーバー | SAP HANA インスタンスが存在するサーバーの名前。 カスタマイズされたポートをサーバーが使用している場合は、`server:port` を指定します。 | はい |
| authenticationType | SAP HANA データベースへの接続に使用される認証の種類。<br/>使用できる値は、以下のとおりです。**Basic** および **Windows** | はい |
| userName | SAP サーバーにアクセスできるユーザーの名前。 | はい |
| password | ユーザーのパスワード。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 「[前提条件](#prerequisites)」に記されているように、セルフホステッド統合ランタイムが必要です。 |はい |

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

SAP HANA からデータをコピーするには、データセットの type プロパティを **RelationalTable** に設定します。 RelationalTable 型の SAP HANA データセットに対してサポートされる型固有のプロパティはありません。

**例:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、SAP HANA ソースでサポートされるプロパティの一覧を示します。

### <a name="sap-hana-as-source"></a>ソースとしての SAP HANA

SAP HANA からデータをコピーするには、コピー アクティビティのソースの種類を **RelationalSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは、次のように設定する必要があります:**RelationalSource** | はい |
| query | SAP HANA インスタンスからデータを読み取る SQL クエリを指定します。 | はい |

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
                "type": "RelationalSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-hana"></a>SAP HANA のデータ型マッピング

SAP HANA からデータをコピーするとき、次の SAP HANAのデータ型から Azure Data Factory の中間データ型へのマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| SAP HANA のデータ型 | Data Factory の中間データ型 |
|:--- |:--- |
| ALPHANUM | string |
| BIGINT | Int64 |
| BLOB | Byte[] |
| BOOLEAN | Byte |
| CLOB | Byte[] |
| DATE | DateTime |
| DECIMAL | Decimal |
| DOUBLE | Single |
| INT | Int32 |
| NVARCHAR | string |
| REAL | Single |
| SECONDDATE | DateTime |
| SMALLINT | Int16 |
| TIME | TimeSpan |
| TIMESTAMP | DateTime |
| TINYINT | Byte |
| VARCHAR | string |

## <a name="known-limitations"></a>既知の制限事項

SAP HANA からデータをコピーする場合、既知の制限事項がいくつかあります。

- NVARCHAR 文字列は、Unicode 文字の最大文字数である 4,000 文字に切り詰められます
- SMALLDECIMAL はサポートされていません
- VARBINARY はサポートされていません
- 有効な日付は 1899/12/30 ～ 9999/12/31 です


## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
