---
title: Azure Data Factory を使用して SAP ECC からデータをコピーする | Microsoft Docs
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、SAP ECC からサポートされているシンク データ ストアへデータをコピーする方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: 1a56006e5209a8ff6c15a2c626a752c00fcf131e
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509177"
---
# <a name="copy-data-from-sap-ecc-using-azure-data-factory"></a>Azure Data Factory を使用して SAP ECC からデータをコピーする

この記事では、Azure Data Factory のコピー アクティビティを使用して、SAP ECC (SAP Enterprise Central Component) からデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

SAP ECC から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この SAP ECC コネクタは以下をサポートします。

- SAP NetWeaver バージョン 7.0 以降上の SAP ECC からのデータのコピー。 
- SAP ECC OData サービス (例: SAP テーブル/ビュー、BAPI、データ エクストラクターなど) によって公開されたオブジェクトからのデータのコピー、または相対アダプターによって OData として取得できる SAP PI に送信されたデータ/IDOC のコピー。
- 基本認証を使用したデータのコピー。

>[!TIP]
>SAP ECC のデータを SAP テーブル/ビュー経由でコピーするには、よりパフォーマンスが高く、かつスケーラブルな [SAP テーブル](connector-sap-table.md) コネクタを使用できます。

## <a name="prerequisites"></a>前提条件

一般に、SAP ECC は、SAP ゲートウェイを通して OData サービスでエンティティを公開します。 この SAP ECC コネクタを使用するには、次の手順が必要です。

- **SAP ゲートウェイを設定します**。 バージョン 7.4 より後の SAP NetWeaver があるサーバーでは、SAP ゲートウェイは既にインストールされています。 それ以外の場合は、OData サービスによって SAP ECC データを公開する前に、埋め込みゲートウェイまたはゲートウェイ ハブをインストールする必要があります。 SAP ゲートウェイのセットアップ方法については、[インストール ガイド](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm)をご覧ください。

- **SAP OData サービスをアクティブ化して構成します**。 OData サービスは TCODE SICF ですぐにアクティブ化できます。 また、公開する必要があるオブジェクトを構成することもできます。 サンプルの[手順ガイド](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/)はこちらをご覧ください。

## <a name="getting-started"></a>使用の開始

コピー アクティビティを含むパイプラインは、.NET SDK、Python SDK、Azure PowerShell、REST API、または Azure Resource Manager テンプレートを使用して作成できます。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](quickstart-create-data-factory-dot-net.md)をご覧ください。

次のセクションでは、SAP ECC コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

SAP ECC のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**SapEcc** | はい |
| url | SAP ECC OData サービスの URL。 | はい |
| username | SAP ECC への接続に使うユーザー名。 | いいえ |
| password | SAP ECC への接続に使うプレーンテキスト パスワード。 | いいえ |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 セルフホステッド統合ランタイムまたは Azure 統合ランタイム (データ ストアがパブリックにアクセスできる場合) を使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ |

**例:**

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData url e.g. http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、SAP ECC データセット でサポートされるプロパティの一覧を示します。

SAP ECC からデータをコピーするには、データセットの type プロパティを **SapEccResource** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| path | SAP ECC OData エンティティのパス。 | はい |

**例**

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、SAP ECC ソースでサポートされるプロパティの一覧を示します。

### <a name="sap-ecc-as-source"></a>ソースとしての SAP ECC

SAP ECC からデータをコピーするには、コピー アクティビティのソース タイプを **SapEccSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは、次のように設定する必要があります:**SapEccSource** | はい |
| query | データをフィルター処理するための OData クエリ オプション。 例: "$select=Name,Description&$top=10"<br/><br/>SAP ECC コネクタは、次の結合された URL からデータをコピーします。`(url specified in linked service)/(path specified in dataset)?(query specified in copy activity source)` [OData の URL コンポーネント](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)に関する記事を参照してください。 | いいえ |

**例:**

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-ecc"></a>SAP ECC のデータ型マッピング

SAP ECC からデータをコピーするとき、次の SAP ECC データの OData データ型から Azure Data Factory の中間データ型へのマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| OData のデータ型 | Data Factory の中間データ型 |
|:--- |:--- |
| Edm.Binary | string |
| Edm.Boolean | Bool |
| Edm.Byte | string |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | string |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | string |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> 現在、複雑なデータ型はサポートされていません。

## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
