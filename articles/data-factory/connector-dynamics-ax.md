---
title: Dynamics AX からデータをコピーする
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、Dynamics AX のデータをサポートされているシンク データ ストアにコピーする方法について説明します。
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: 2a2debf9b1cbc669d2402b1797097b97e94139fc
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929475"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory"></a>Azure Data Factory を使用して Dynamics AX からデータをコピーする

この記事では、Azure Data Factory のコピー アクティビティを使用して Dynamics AX ソースからデータをコピーする方法の概要について説明します。 この記事は、コピー アクティビティの概要が説明されている「[Azure Data Factory のコピー アクティビティ](copy-activity-overview.md)」を基に作成されています。

## <a name="supported-capabilities"></a>サポートされる機能

この Dynamics AX コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

Dynamics AX から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティでソースおよびシンクとしてサポートされているデータ ストアの一覧については、「[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)」を参照してください。

具体的には、この Dynamics AX コネクタは、**OData プロトコル**と**サービス プリンシパル認証**を使用した Dynamics AX からのデータ コピーをサポートしています。

>[!TIP]
>**Dynamics 365 Finance and Operations** からデータをコピーするために、このコネクタを使用することもできます。 Dynamics 365 の [OData のサポート](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/odata)と[認証方法](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication)に関するページを参照してください。

## <a name="get-started"></a>作業開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以降のセクションでは、Dynamics AX コネクタに固有の Data Factory エンティティを定義するために使用できるプロパティについて詳細に説明します。

## <a name="prerequisites"></a>前提条件

サービス プリンシパル認証を使用するには、次の手順のようにします。

1. 「[アプリケーションを Azure AD テナントに登録する](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)」に従って、Azure Active Directory (Azure AD) にアプリケーション エンティティを登録します。 次の値を記録しておきます。リンクされたサービスを定義するときに使います。

    - アプリケーション ID
    - アプリケーション キー
    - テナント ID

2. Dynamics AX に移動し、このサービス プリンシパルに Dynamics AX にアクセスするための適切なアクセス許可を付与します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Dynamics AX のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティを **DynamicsAX** に設定する必要があります。 |はい |
| url | Dynamics AX (または Dynamics 365 Finance および Operations) インスタンスの OData エンドポイント。 |はい |
| servicePrincipalId | アプリケーションのクライアント ID を取得します。 | はい |
| servicePrincipalKey | アプリケーションのキーを取得します。 このフィールドを **SecureString** としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | はい |
| tenant | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 Azure portal の右上隅にマウスを置くことで取得します。 | はい |
| aadResourceId | 認可を要求する AAD リソースを指定します。 たとえば、Dynamics の URL が `https://sampledynamics.sandbox.operations.dynamics.com/data/` である場合、対応する AAD リソースは通常 `https://sampledynamics.sandbox.operations.dynamics.com` となります。 | はい |
| connectVia | データ ストアに接続するために使用される [Integration Runtime](concepts-integration-runtime.md)。 Azure Integration Runtime またはセルフホステッド統合ランタイムを選択できます (データ ストアがプライベート ネットワークに配置されている場合)。 指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |いいえ |

**例**

```json
{
    "name": "DynamicsAXLinkedService",
    "properties": {
        "type": "DynamicsAX",
        "typeProperties": {
            "url": "<Dynamics AX instance OData endpoint>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource, e.g. https://sampledynamics.sandbox.operations.dynamics.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}

```

## <a name="dataset-properties"></a>データセットのプロパティ

このセクションでは、Dynamics AX データセットでサポートされているプロパティの一覧を示します。

データセットの定義に使用できるセクションとプロパティの完全な一覧については、「[データセットとリンクされたサービス](concepts-datasets-linked-services.md)」を参照してください。 

Dynamics AX からデータをコピーするには、データセットの **type** プロパティを **DynamicsAXResource** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの **type** プロパティは、**DynamicsAXResource** に設定する必要があります。 | はい |
| path | Dynamics AX OData エンティティのパス。 | はい |

**例**

```json
{
    "name": "DynamicsAXResourceDataset",
    "properties": {
        "type": "DynamicsAXResource",
        "typeProperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Dynamics AX linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

このセクションでは、Dynamics AX ソースでサポートされているプロパティの一覧を示します。

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 

### <a name="dynamics-ax-as-source"></a>ソースとしての Dynamics AX

Dynamics AX からデータをコピーするは、コピー アクティビティの **source** の種類を **DynamicsAXSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの **type** プロパティは **DynamicsAXSource** に設定する必要があります | はい |
| query | データをフィルター処理するための OData クエリ オプション。 例: `"?$select=Name,Description&$top=5"`.<br/><br/>**メモ**:コネクタは、次の結合された URL からデータをコピーします。`[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]` 詳細については、[OData の URL コンポーネント](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)に関するページを参照してください。 | いいえ |

**例**

```json
"activities":[
    {
        "name": "CopyFromDynamicsAX",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics AX input dataset name>",
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
                "type": "DynamicsAXSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[ルックアップ アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、「[サポートされるデータ ストアと形式](copy-activity-overview.md##supported-data-stores-and-formats)」を参照してください。
