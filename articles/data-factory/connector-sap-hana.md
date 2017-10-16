---
title: "Azure Data Factory を使用して SAP HANA からデータをコピーする | Microsoft Docs"
description: "Azure Data Factory パイプラインでコピー アクティビティを使用して、SAP HANA からサポートされているシンク データ ストアへデータをコピーする方法について説明します。"
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
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: 2e66edf63b4bdac1aacfedb64ca1edf207e46a84
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Azure Data Factory を使用して SAP HANA からデータをコピーする
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [バージョン 1 - 一般公開](v1/data-factory-sap-hana-connector.md)
> * [バージョン 2 - プレビュー](connector-sap-hana.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、SAP HANA データベースからデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[V1 の SAP HANA コネクタ](v1/data-factory-sap-hana-connector.md)に関する記事を参照してください。

## <a name="supported-scenarios"></a>サポートされるシナリオ

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
コピー アクティビティを含むパイプラインは、.NET SDK、Python SDK、Azure PowerShell、REST API、または Azure Resource Manager テンプレートを使用して作成できます。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](quickstart-create-data-factory-dot-net.md)をご覧ください。

次のセクションでは、SAP HANA コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

SAP HANA のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティを **SapHana** に設定する必要があります。 | あり |
| server | SAP HANA インスタンスが存在するサーバーの名前。 カスタマイズされたポートをサーバーが使用している場合は、`server:port` を指定します。 | あり |
| authenticationType | SAP HANA データベースへの接続に使用される認証の種類。<br/>使用可能な値: **Basic** および **Windows** | あり |
| userName | SAP サーバーにアクセスできるユーザーの名前。 | あり |
| password | ユーザーのパスワード。 このフィールドを SecureString とマークします。 | あり |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 「[前提条件](#prerequisites)」に記されているように、セルフホステッド統合ランタイムが必要です。 |あり |

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
    "properties":
    {
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
| type | コピー アクティビティのソースの type プロパティを **RelationalSource** に設定する必要があります。 | あり |
| クエリ | SAP HANA インスタンスからデータを読み取る SQL クエリを指定します。 | あり |

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
| ALPHANUM | String |
| BIGINT | Int64 |
| BLOB | Byte[] |
| BOOLEAN | Byte |
| CLOB | Byte[] |
| DATE | DateTime |
| DECIMAL | DECIMAL |
| DOUBLE | Single |
| INT | Int32 |
| NVARCHAR | String |
| REAL | Single |
| SECONDDATE | DateTime |
| SMALLINT | Int16 |
| TIME | timespan |
| TIMESTAMP | DateTime |
| TINYINT | Byte |
| VARCHAR | String |

## <a name="known-limitations"></a>既知の制限事項

SAP HANA からデータをコピーする場合、既知の制限事項がいくつかあります。

- NVARCHAR 文字列は、Unicode 文字の最大文字数である 4,000 文字に切り詰められます
- SMALLDECIMAL はサポートされていません
- VARBINARY はサポートされていません
- 有効な日付は 1899/12/30 ～ 9999/12/31 です


## <a name="next-steps"></a>次のステップ
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md##supported-data-stores-and-formats)の表をご覧ください。