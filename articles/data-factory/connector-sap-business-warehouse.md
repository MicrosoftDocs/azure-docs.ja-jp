---
title: "Azure Data Factory を使用して SAP BW からデータをコピーする | Microsoft Docs"
description: "Azure Data Factory パイプラインでコピー アクティビティを使用して、SAP Business Warehouse からサポートされているシンク データ ストアへデータをコピーする方法について説明します。"
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
ms.openlocfilehash: 03841e08d071eb5f846b6a3a9e5a08edaa895611
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Azure Data Factory を使用して SAP Business Warehouse からデータをコピーする
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [バージョン 1 - 一般公開](v1/data-factory-sap-business-warehouse-connector.md)
> * [バージョン 2 - プレビュー](connector-sap-business-warehouse.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、SAP Business Warehouse (BW) からデータコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[V1 の SAP BW コネクタ](v1/data-factory-sap-business-warehouse-connector.md)に関する記事を参照してください。

## <a name="supported-scenarios"></a>サポートされるシナリオ

SAP Business Warehouse のデータを、サポートされる任意のシンク データ ストアにコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この SAP Business Warehouse コネクタは以下をサポートします。

- SAP Business Warehouse **バージョン 7.x**。
- MDX クエリを使用した、**InfoCubes および QueryCubes** (BEx クエリなど) からのデータのコピー。
- 基本認証を使用したデータのコピー。

## <a name="prerequisites"></a>前提条件

この SAP Business Warehouse コネクタを使用するには、次の作業を行う必要があります。

- セルフホステッド統合ランタイムをセットアップします。 詳細については、[セルフホステッド統合ランタイム](create-self-hosted-integration-runtime.md)に関する記事をご覧ください。
- **SAP NetWeaver ライブラリ**を統合ランタイム コンピューターにインストールします。 SAP Netweaver ライブラリは SAP 管理者から入手します。また、[SAP ソフトウェアのダウンロード センター](https://support.sap.com/swdc)から直接取得することもできます。 最新のバージョンをダウンロードできる場所は、"**SAP Note #1025361**" を検索して確認してください。 統合ランタイム インストールに一致する **64 ビット** SAP NetWeaver ライブラリが選択されていることを確認します。 SAP Note に従って、SAP NetWeaver RFC SDK に含まれるすべてのファイルをインストールします。 SAP NetWeaver ライブラリは、SAP Client Tools のインストールにも含まれます。

> [!TIP]
> NetWeaver RFC SDK から抽出した dll を system32 フォルダーに格納します。

## <a name="getting-started"></a>使用の開始
コピー アクティビティを含むパイプラインは、.NET SDK、Python SDK、Azure PowerShell、REST API、または Azure Resource Manager テンプレートを使用して作成できます。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](quickstart-create-data-factory-dot-net.md)をご覧ください。

以下のセクションでは、SAP Business Warehouse コネクタに固有の Data Factory エンティティを定義するために使用されるプロパティの詳細を説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

SAP Business Warehouse (BW) のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティを **SapBw** に設定する必要があります。 | あり |
| server | SAP BW インスタンスが存在するサーバーの名前。 | あり |
| systemNumber | SAP BW システムのシステムの数。<br/>使用できる値: 文字列として表される 2 桁の 10 進数。 | あり |
| clientId | SAP BW システム内のクライアントのクライアント ID。<br/>使用できる値: 文字列として表される 3 桁の 10 進数。 | あり |
| userName | SAP サーバーにアクセスできるユーザーの名前。 | あり |
| password | ユーザーのパスワード。 このフィールドを SecureString とマークします。 | あり |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 「[前提条件](#prerequisites)」に記されているように、セルフホステッド統合ランタイムが必要です。 |あり |

**例:**

```json
{
    "name": "SapBwLinkedService",
    "properties":
    {
        "type": "SapBw",
        "typeProperties":
        {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、データセットに関する記事をご覧ください。 このセクションでは、SAP BW データセット でサポートされるプロパティの一覧を示します。

SAP BW からデータをコピーするには、データセットの type プロパティを **RelationalTable** に設定します。 しかるに、RelationalTable 型の SAP BW データセットに対して、サポートされている型固有のプロパティはありません。

**例:**

```json
{
    "name": "SAPBWDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、SAP BW ソースでサポートされるプロパティの一覧を示します。

### <a name="sap-bw-as-source"></a>ソースとしての SAP BW

SAP BW からデータをコピーするには、コピー アクティビティのソース タイプを **RelationalSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティを **RelationalSource** に設定する必要があります。 | あり |
| クエリ | SAP BW インスタンスからデータを読み取る MDX クエリを指定します。 | あり |

**例:**

```json
"activities":[
    {
        "name": "CopyFromSAPBW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW input dataset name>",
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
                "query": "<MDX query for SAP BW>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw"></a>SAP BW のデータ型マッピング

SAP BW からデータをコピーするとき、SAP BW のデータ型から Azure Data Factory の中間データ型への、以下のマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| SAP BW データ型 | Data Factory の中間データ型 |
|:--- |:--- |
| ACCP | int |
| CHAR | String |
| CLNT | String |
| CURR | Decimal |
| CUKY | String |
| DEC | Decimal |
| FLTP | Double |
| INT1 | Byte |
| INT2 | Int16 |
| INT4 | int |
| LANG | String |
| LCHR | String |
| LRAW | Byte[] |
| PREC | Int16 |
| QUAN | Decimal |
| RAW | Byte[] |
| RAWSTRING | Byte[] |
| STRING | String |
| ユニット | String |
| DATS | String |
| NUMC | String |
| TIMS | String |


## <a name="next-steps"></a>次のステップ
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md##supported-data-stores-and-formats)の表をご覧ください。