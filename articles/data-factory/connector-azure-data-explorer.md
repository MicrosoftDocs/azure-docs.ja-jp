---
title: Azure Data Explorer との間でデータをコピーする
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、Azure Data Explorer との間で双方向にデータをコピーする方法について説明します。
ms.author: orspodek
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2020
ms.openlocfilehash: 16126e8b9e5c34529016018273edcf65a31e2280
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100379983"
---
# <a name="copy-data-to-or-from-azure-data-explorer-by-using-azure-data-factory"></a>Azure Data Factory を使用して Azure Data Explorer をコピー先またはコピー元としてデータをコピーする

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory のコピー アクティビティを使用して、[Azure Data Explorer](/azure/data-explorer/data-explorer-overview) との間で双方向にデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

>[!TIP]
>Azure Data Factory と Azure Data Explorer の統合全般の詳細については、「[Azure Data Explorer と Azure Data Factory の統合](/azure/data-explorer/data-factory-integration)」を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

この Azure Data Explorer コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

Azure Data Explorer には、サポートされているソース データ ストアからデータをコピーすることができます。 また、Azure Data Explorer のデータを、サポートされているシンク データ ストアにコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するページの表をご覧ください。

>[!NOTE]
>セルフホステッド統合ランタイムを使用して、Azure Data Explorer との間でオンプレミス データ ストアから双方向にデータをコピーすることは、バージョン 3.14 以降からサポートされています。

Azure Data Explorer コネクタを使用すると、次のことができます。

* Azure Active Directory (Azure AD) アプリケーション トークン認証と **サービス プリンシパル** を使用して、データをコピーする。
* ソースとして、KQL (Kusto) クエリを使用してデータを取得する。
* シンクとして、コピー先テーブルにデータを追加する。

## <a name="getting-started"></a>作業の開始

>[!TIP]
>Azure Data Explorer コネクタのチュートリアルについては、「[Azure Data Factory を使用して Azure Data Explorer をコピー先またはコピー元としてデータをコピーする](/azure/data-explorer/data-factory-load-data)」と、[データベースから Azure Data Explorer への一括コピー](/azure/data-explorer/data-factory-template)に関するページを参照してください。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下のセクションでは、Azure Data Explorer コネクタに固有の Data Factory エンティティを定義するために使用されるプロパティの詳細を説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Azure Data Explorer のコネクタでは、サービス プリンシパル認証を使用しています。 次の手順に従い、サービス プリンシパルを取得し、アクセス許可を付与します。

1. 「[アプリケーションを Azure AD テナントに登録する](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)」の手順に従って、Azure Active Directory にアプリケーション エンティティを登録します。 次の値を記録しておきます。リンクされたサービスを定義するときに使います。

    - アプリケーション ID
    - アプリケーション キー
    - テナント ID

2. Azure Data Explorer でサービス プリンシパルに適切なアクセス許可を付与します。 ロールおよびアクセス許可の詳細について、またアクセス許可の管理方法の詳細については、「[Azure Data Explorer のデータベース アクセス許可を管理する](/azure/data-explorer/manage-database-permissions)」を参照してください。 一般的に、次のことを行う必要があります。

    - **ソースとして**、少なくとも **データベース ビューアー** ロールをデータベースに付与します。
    - **シンクとして**、少なくとも **データベースのデータ取り込み** ロールをデータベースに付与します。

>[!NOTE]
>Data Factory UI を使用して作成する場合、Azure Data Explorer クラスター、データベース、およびテーブルを一覧表示するために、ログイン ユーザー アカウントが使用されます。 これらの操作のためのアクセス許可がない場合は、名前を手動で入力します。

Azure Data Explorer のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティは、**AzureDataExplorer** に設定する必要があります。 | はい |
| endpoint | Azure Data Explorer クラスターのエンドポイント URL。形式は `https://<clusterName>.<regionName>.kusto.windows.net` です。 | はい |
| database | データベースの名前。 | はい |
| tenant | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 これは、[Kusto 接続文字列](/azure/kusto/api/connection-strings/kusto#application-authentication-properties)の "機関 ID" として知られています。 これは、Azure portal の右上隅にマウス ポインターを合わせることで取得できます。 | はい |
| servicePrincipalId | アプリケーションのクライアント ID を取得します。 これは、[Kusto 接続文字列](/azure/kusto/api/connection-strings/kusto#application-authentication-properties)の "AAD アプリケーション クライアント ID" として知られています。 | はい |
| servicePrincipalKey | アプリケーションのキーを取得します。 これは、[Kusto 接続文字列](/azure/kusto/api/connection-strings/kusto#application-authentication-properties)の "AAD アプリケーション キー" として知られています。 このフィールドを **SecureString** としてマークして Data Factory に安全に保管するか、[Azure Key Vault 内のセキュリティで保護された格納データを参照](store-credentials-in-key-vault.md)します。 | はい |

**リンクされたサービスのプロパティの例**:

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、「[Azure Data Factory のデータセット](concepts-datasets-linked-services.md)」を参照してください。 このセクションでは、Azure Data Explorer データセットでサポートされるプロパティの一覧を示します。

Azure Data Explorer にデータをコピーするには、データセットの type プロパティを **AzureDataExplorerTable** に設定します。

次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティは、**AzureDataExplorerTable** に設定する必要があります。 | はい |
| table | リンクされたサービスが参照するテーブルの名前。 | シンクの場合は Yes、ソースの場合は No |

**データセットのプロパティの例:**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "typeProperties": {
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、「[Azure Data Factory のパイプラインとアクティビティ](concepts-pipelines-activities.md)」を参照してください。 このセクションでは、Azure Data Explorer のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="azure-data-explorer-as-source"></a>ソースとしての Azure Data Explorer

Azure Data Explorer からデータをコピーするには、コピー アクティビティ ソースの **type** プロパティを **AzureDataExplorerSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの **type** プロパティを、次の値に設定する必要があります:**AzureDataExplorerSource** | はい |
| query | [KQL 形式](/azure/kusto/query/)で指定された読み取り専用要求。 参照としてカスタム KQL クエリを使用します。 | はい |
| queryTimeout | クエリ要求がタイムアウトするまでの待機時間。既定値は 10 分 (00:10:00)、許容される最大値は 1 時間 (01:00:00) です。 | いいえ |
| noTruncation | 返される結果セットを切り詰めるかどうかを示します。 既定では、結果は 500,000 件のレコードまたは 64 メガバイト (MB) の後に切り詰められます。 アクティビティの正常な動作のためには、切り詰めを強くお勧めします。 |いいえ |

>[!NOTE]
>既定では、Azure Data Explorer ソースには、500,000 レコードまたは 64 MB のサイズ制限があります。 切り捨てることなくすべてのレコードを取得するには、クエリの先頭に `set notruncation;` を指定します。 詳細については、「[クエリの制限](/azure/kusto/concepts/querylimits)」を参照してください。

**例:**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>シンクとしての Azure Data Explorer

Azure Data Explorer にデータをコピーするには、コピー アクティビティ シンクの type プロパティを **AzureDataExplorerSink** に設定します。 コピー アクティビティの **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの **type** プロパティは、次のように設定する必要があります:**AzureDataExplorerSink** | はい |
| ingestionMappingName | Kusto テーブルで事前作成済みの[マッピング](/azure/kusto/management/mappings#csv-mapping)の名前。 ソースから Azure Data Explorer に列をマッピングするには (CSV/JSON/Avro 形式など、[サポートされているすべてのソース ストアや形式](copy-activity-overview.md#supported-data-stores-and-formats)に適用されます)、コピー アクティビティの[列マッピング](copy-activity-schema-and-type-mapping.md) (名前で暗黙的に、または構成で明示的に) や Azure Data Explorer のマッピングを使用できます。 | いいえ |
| additionalProperties | まだ Azure Data Explorer シンクによって設定されていないインジェストのプロパティを指定するために使用できるプロパティ バッグ。 具体的には、インジェスト タグの指定に便利です。 詳細については [Azure Data Explore データ インジェスト ドキュメント](/azure/data-explorer/ingestion-properties)でご覧ください。 | いいえ |

**例:**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>",
                "additionalProperties": {<additional settings for data ingestion>}
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[ルックアップ アクティビティ](control-flow-lookup-activity.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

* Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)をご覧ください。

* Azure Data Factory から Azure Data Explorer にデータをコピーする方法の詳細については、[こちら](/azure/data-explorer/data-factory-load-data)を参照してください。