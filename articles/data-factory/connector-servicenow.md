---
title: ServiceNow からデータをコピーする
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、ServiceNow からサポートされているシンク データ ストアへデータをコピーする方法について説明します。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: dabcc5afe4a092e4919c854071a698c6e6ebf0b3
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926167"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Azure Data Factory を使用して ServiceNow からデータをコピーする

この記事では、Azure Data Factory のコピー アクティビティを使用して、ServiceNow からデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

この ServiceNow コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

ServiceNow から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

Azure Data Factory では接続を有効にする組み込みのドライバーが提供されるので、このコネクタを使用してドライバーを手動でインストールする必要はありません。

## <a name="getting-started"></a>使用の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、ServiceNow コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

ServiceNow のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります。**ServiceNow** | はい |
| endpoint | ServiceNow サーバーのエンドポイント (`http://<instance>.service-now.com`)。  | はい |
| authenticationType | 使用する認証の種類。 <br/>使用できる値は、以下のとおりです。**Basic**、**OAuth2** | はい |
| username | Basic および OAuth2 認証で ServiceNow サーバーへの接続に使用されるユーザー名。  | はい |
| password | Basic および OAuth2 認証のユーザー名に対応するパスワード。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | はい |
| clientId | OAuth2 認証のクライアント ID。  | いいえ |
| clientSecret | OAuth2 認証のクライアント シークレット。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | いいえ |
| useEncryptedEndpoints | データ ソースのエンドポイントが HTTPS を使用して暗号化されるかどうかを指定します。 既定値は true です。  | いいえ |
| useHostVerification | SSL 経由で接続するときに、サーバーの証明書内のホスト名がサーバーのホスト名と一致する必要があるかどうかを指定します。 既定値は true です。  | いいえ |
| usePeerVerification | SSL 経由で接続するときに、サーバーの ID を検証するかどうかを指定します。 既定値は true です。  | いいえ |

**例:**

```json
{
    "name": "ServiceNowLinkedService",
    "properties": {
        "type": "ServiceNow",
        "typeProperties": {
            "endpoint" : "http://<instance>.service-now.com",
            "authenticationType" : "Basic",
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、ServiceNow データセットでサポートされるプロパティの一覧を示します。

ServiceNow からデータをコピーするには、データセットの type プロパティを **ServiceNowObject** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります。**ServiceNowObject** | はい |
| tableName | テーブルの名前。 | いいえ (アクティビティ ソースの "query" が指定されている場合) |

**例**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、ServiceNow ソース でサポートされるプロパティの一覧を示します。

### <a name="servicenow-as-source"></a>ソースとしての ServiceNow

ServiceNow からデータをコピーするには、コピー アクティビティのソースの種類を **ServiceNowSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは、次のように設定する必要があります。**ServiceNowSource** | はい |
| query | カスタム SQL クエリを使用してデータを読み取ります。 (例: `"SELECT * FROM Actual.alm_asset"`)。 | いいえ (データセットの "tableName" が指定されている場合) |

クエリで ServiceNow のスキーマと列を指定するときは、次のことに注意してください。また、**コピーのパフォーマンスの影響について、「[パフォーマンスに関するヒント](#performance-tips)」を参照してください**。

- **スキーマ:** ServiceNow のクエリでは、スキーマを `Actual` または `Display` として指定します。これは、[ServiceNow restful API](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET) を呼び出す際に、`sysparm_display_value` パラメーター (true または false) として確認できます。 
- **列:** `Actual` スキーマの下の実際の値の列名は `[column name]_value` で、`Display` スキーマの下の表示値は `[column name]_display_value` です。 列名は、クエリで使用されているスキーマにマップされる必要があります。

**サンプル クエリ:** 
`SELECT col_value FROM Actual.alm_asset`または  
`SELECT col_display_value FROM Display.alm_asset`

**例:**

```json
"activities":[
    {
        "name": "CopyFromServiceNow",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ServiceNow input dataset name>",
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
                "type": "ServiceNowSource",
                "query": "SELECT * FROM Actual.alm_asset"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="performance-tips"></a>パフォーマンスに関するヒント

### <a name="schema-to-use"></a>使用するスキーマ

ServiceNow には 2 つの異なるスキーマがあります。1 つは **"Actual"** で、実際のデータが返されます。もう 1 つは **"Display"** で、データの表示値が返されます。 

クエリ内にフィルターがある場合は、コピーのパフォーマンスがよい "Actual" スキーマを使用してください。 "Actual" スキーマに対してクエリを実行すると、ServiceNow ではデータをフェッチするときにネイティブにフィルターがサポートされて、フィルター処理された結果セットのみが返されます。一方、"Display" スキーマに対してクエリを実行すると、ADF ではすべてのデータが取得されて、内部的にフィルターが適用されます。

### <a name="index"></a>Index

ServiceNow のテーブル インデックスは、クエリのパフォーマンスの向上に役立ちます。「[Create a table index](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/table_administration/task/t_CreateCustomIndex.html)」(テーブル インデックスを作成する) をご覧ください。

## <a name="lookup-activity-properties"></a>ルックアップ アクティビティのプロパティ

プロパティの詳細については、[ルックアップ アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。


## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
