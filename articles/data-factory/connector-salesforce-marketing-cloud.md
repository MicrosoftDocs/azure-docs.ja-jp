---
title: Salesforce Marketing Cloud からデータをコピーする
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、Salesforce Marketing Cloud からサポートされているシンク データ ストアにデータをコピーする方法について説明します。
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/17/2020
ms.openlocfilehash: 161b81b196a1e178c7244845b25594440e6d6e1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100369749"
---
# <a name="copy-data-from-salesforce-marketing-cloud-using-azure-data-factory"></a>Azure Data Factory を使用して Salesforce Marketing Cloud からデータをコピーする

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory のコピー アクティビティを使用して、Salesforce Marketing Cloud からデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

この Salesforce Marketing Cloud コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

Salesforce Marketing Cloud から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

Salesforce Marketing Cloud コネクタでは OAuth 2 認証がサポートされており、レガシ パッケージと拡張パッケージの両方の種類がサポートされています。 このコネクタは、[Salesforce Marketing Cloud REST API](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/index-api.htm) の上に構築されています。

>[!NOTE]
>このコネクタは、カスタム オブジェクトまたはカスタム データ拡張機能の取得をサポートしていません。

## <a name="getting-started"></a>作業の開始

コピー アクティビティを含むパイプラインは、.NET SDK、Python SDK、Azure PowerShell、REST API、または Azure Resource Manager テンプレートを使用して作成できます。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](quickstart-create-data-factory-dot-net.md)をご覧ください。

次のセクションでは、Salesforce Marketing Cloud コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Salesforce Marketing Cloud のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**SalesforceMarketingCloud** | はい |
| connectionProperties | Salesforce Marketing Cloud への接続方法を定義するプロパティのグループ。 | はい |
| ***`connectionProperties` の下:*** | | |
| authenticationType | 使用する認証方法を指定します。 使用できる値は `Enhanced sts OAuth 2.0` または `OAuth_2.0` です。<br><br>Salesforce Marketing Cloud レガシ パッケージでは `OAuth_2.0` のみがサポートされていますが、拡張パッケージでは `Enhanced sts OAuth 2.0` が必要です。 <br>2019 年 8 月 1 日以降、レガシ パッケージを作成する機能が Salesforce Marketing Cloud から削除されました。 新しいパッケージはすべて、拡張パッケージです。 | はい |
| host | 拡張パッケージの場合、ホストは[サブドメイン](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/your-subdomain-tenant-specific-endpoints.htm)である必要があります。これは、"mc" という文字で始まる 28 文字の文字列で表されます (例: `mc563885gzs27c5t9-63k636ttgm`)。 <br>レガシ パッケージの場合は、`www.exacttargetapis.com` を指定します。 | はい |
| clientId | Salesforce Marketing Cloud アプリケーションに関連付けられたクライアント ID。  | はい |
| clientSecret | Salesforce Marketing Cloud アプリケーションに関連付けられたクライアント シークレット。 このフィールドを SecureString としてマークして ADF に安全に格納するか、Azure Key Vault にシークレットを格納し、データ コピーの実行時に ADF コピー アクティビティでそこからプルするかを選択できます。詳細については、「[Key Vault に資格情報を格納する](store-credentials-in-key-vault.md)」を参照してください。 | はい |
| useEncryptedEndpoints | データ ソースのエンドポイントが HTTPS を使用して暗号化されるかどうかを指定します。 既定値は、true です。  | いいえ |
| useHostVerification | TLS 経由で接続するときに、サーバーの証明書内のホスト名がサーバーのホスト名と一致する必要があるかどうか指定します。 既定値は、true です。  | いいえ |
| usePeerVerification | TLS 経由で接続するときに、サーバーの ID を検証するかどうかを指定します。 既定値は、true です。  | いいえ |

**例: 拡張パッケージに拡張 STS OAuth 2 認証を使用** 

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "connectionProperties": {
                "host": "<subdomain e.g. mc563885gzs27c5t9-63k636ttgm>",
                "authenticationType": "Enhanced sts OAuth 2.0",
                "clientId": "<clientId>",
                "clientSecret": {
                     "type": "SecureString",
                     "value": "<clientSecret>"
                },
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}

```

**例: レガシ パッケージに OAuth 2 認証を使用** 

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "connectionProperties": {
                "host": "www.exacttargetapis.com",
                "authenticationType": "OAuth_2.0",
                "clientId": "<clientId>",
                "clientSecret": {
                     "type": "SecureString",
                     "value": "<clientSecret>"
                },
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}

```

以下のペイロードで Salesforce Marketing Cloud のリンクされたサービスを使用していた場合、これは現状のまま引き続きサポートされますが、今後は拡張パッケージがサポートされる新しいものを使用することをお勧めします。

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "clientId": "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "useEncryptedEndpoints": true,
            "useHostVerification": true,
            "usePeerVerification": true
        }
    }
}

```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Salesforce Marketing Cloud データセット でサポートされるプロパティの一覧を示します。

Salesforce Marketing Cloud からデータをコピーするには、データセットの type プロパティを **SalesforceMarketingCloudObject** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります:**SalesforceMarketingCloudObject** | はい |
| tableName | テーブルの名前。 | いいえ (アクティビティ ソースの "query" が指定されている場合) |

**例**

```json
{
    "name": "SalesforceMarketingCloudDataset",
    "properties": {
        "type": "SalesforceMarketingCloudObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SalesforceMarketingCloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Salesforce Marketing Cloud ソースでサポートされるプロパティの一覧を示します。

### <a name="salesforce-marketing-cloud-as-source"></a>ソースとしての Salesforce Marketing Cloud

Salesforce Marketing Cloud からデータをコピーするには、コピー アクティビティのソースの種類を **SalesforceMarketingCloudSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは、次のように設定する必要があります:**SalesforceMarketingCloudSource** | はい |
| query | カスタム SQL クエリを使用してデータを読み取ります。 (例: `"SELECT * FROM MyTable"`)。 | いいえ (データセットの "tableName" が指定されている場合) |

**例:**

```json
"activities":[
    {
        "name": "CopyFromSalesforceMarketingCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SalesforceMarketingCloud input dataset name>",
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
                "type": "SalesforceMarketingCloudSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
