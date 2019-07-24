---
title: Azure Data Factory を使用して SAP Cloud for Customer との間でデータをコピーする | Microsoft Docs
description: Data Factory を使用して、SAP Cloud for Customer からサポートされるシンク データ ストアにコピーしたり、サポートされるソース データ ストアから SAP Cloud for Customer にデータをコピーしたりする方法を説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: jingwang
ms.openlocfilehash: e4625b934f9e1cf98254f3dee59f9c26e8e16fb5
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2019
ms.locfileid: "54353381"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Azure Data Factory を使用して SAP Cloud for Customer (C4C) からデータをコピーする

この記事では、Azure Data Factory のコピー アクティビティを使用して、SAP Cloud for Customer (C4C) との間でデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

SAP Cloud for Customer のデータを、サポートされる任意のシンク データ ストアにコピーしたり、サポートされる任意のソース データ ストアのデータを SAP Cloud for Customer にコピーしたりできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、Azure Data Factory はこのコネクタを使用して、SAP Cloud for Sales、SAP Cloud for Service、SAP Cloud for Social Engagement ソリューションを含む SAP Cloud for Customer との間でデータをコピーできます。

## <a name="getting-started"></a>使用の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、SAP Cloud for Customer コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

SAP Cloud for Customer のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**SapCloudForCustomer**。 | はい |
| url | SAP C4C OData サービスの URL。 | はい |
| username | SAP C4C に接続するためのユーザー名を指定します。 | はい |
| password | username に指定したユーザー アカウントのパスワードを指定します。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 | ソースの場合はいいえ、シンクの場合ははい |

>[!IMPORTANT]
>SAP Cloud for Customer にデータをコピーするには、次の例に示すように、SAP Cloud for Customer の近くの場所を使用して明示的に [Azure IR を作成](create-azure-integration-runtime.md#create-azure-ir)し、リンクされたサービスで関連付けます。

**例:**

```json
{
    "name": "SAPC4CLinkedService",
    "properties": {
        "type": "SapCloudForCustomer",
        "typeProperties": {
            "url": "https://<tenantname>.crm.ondemand.com/sap/c4c/odata/v1/c4codata/" ,
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、SAP Cloud for Customer データセットでサポートされるプロパティの一覧を示します。

SAP Cloud for Customer からデータをコピーするには、データセットの type プロパティを **SapCloudForCustomerResource** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります:**SapCloudForCustomerResource** |はい |
| path | SAP C4C OData エンティティのパスを指定します。 |はい |

**例:**

```json
{
    "name": "SAPC4CDataset",
    "properties": {
        "type": "SapCloudForCustomerResource",
        "typeProperties": {
            "path": "<path e.g. LeadCollection>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP C4C linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、SAP Cloud for Customer ソースでサポートされるプロパティの一覧を示します。

### <a name="sap-c4c-as-source"></a>ソースとしての SAP C4C

SAP Cloud for Customer からデータをコピーするには、コピー アクティビティのソースの種類を **SapCloudForCustomerSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**SapCloudForCustomerSource**  | はい |
| query | データを読み取るカスタム OData クエリを指定します。 | いいえ  |

特定の日のデータを取得するサンプル クエリ: `"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

**例:**

```json
"activities":[
    {
        "name": "CopyFromSAPC4C",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP C4C input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapCloudForCustomerSource",
                "query": "<custom query e.g. $top=10>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sap-c4c-as-sink"></a>シンクとしての SAP C4C

SAP Cloud for Customer にデータをコピーするには、コピー アクティビティのシンクの種類を **SapCloudForCustomerSink** に設定します。 コピー アクティビティの **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**SapCloudForCustomerSink**  | はい |
| writeBehavior | 操作の書き込み動作。 “Insert” または “Update” を指定できます。 | いいえ。 既定値: "Insert"。 |
| writeBatchSize | 書き込み操作のバッチ サイズ。 最適なパフォーマンスを得るバッチ サイズは、テーブルまたはサーバーによって異なることがあります。 | いいえ。 既定値: 10。 |

**例:**

```json
"activities":[
    {
        "name": "CopyToSapC4c",
        "type": "Copy",
        "inputs": [{
            "type": "DatasetReference",
            "referenceName": "<dataset type>"
        }],
        "outputs": [{
            "type": "DatasetReference",
            "referenceName": "SapC4cDataset"
        }],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SapCloudForCustomerSink",
                "writeBehavior": "Insert",
                "writeBatchSize": 30
            },
            "parallelCopies": 10,
            "dataIntegrationUnits": 4,
            "enableSkipIncompatibleRow": true,
            "redirectIncompatibleRowSettings": {
                "linkedServiceName": {
                    "referenceName": "ErrorLogBlobLinkedService",
                    "type": "LinkedServiceReference"
                },
                "path": "incompatiblerows"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>SAP Cloud for Customer のデータ型マッピング

SAP Cloud for Customer からデータをコピーするとき、次のSAP Cloud for Customer のデータ型から Azure Data Factory の中間データ型へのマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| SAP C4C OData のデータ型 | Data Factory の中間データ型 |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | Datetime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| Edm.Time | timespan |
| Edm.DateTimeOffset | DateTimeOffset |


## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
