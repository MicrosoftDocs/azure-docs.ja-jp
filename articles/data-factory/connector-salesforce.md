---
title: "Azure Data Factory を使用して Salesforce からデータをコピーする | Microsoft Docs"
description: "Azure Data Factory パイプラインでコピー アクティビティを使用して、Salesforce からサポートされているシンク データ ストアにデータをコピーする方法について説明します。"
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
ms.date: 08/30/2017
ms.author: jingwang
ms.openlocfilehash: c819b3e3e715427632e793ce77d31554914d248e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-from-salesforce-using-azure-data-factory"></a>Azure Data Factory を使用して Salesforce からデータをコピーする
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [バージョン 1 - 一般公開](v1/data-factory-salesforce-connector.md)
> * [バージョン 2 - プレビュー](connector-salesforce.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、Salesforce データベースからデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[V1 の Salesforce コネクタ](v1/data-factory-salesforce-connector.md)に関する記事を参照してください。

## <a name="supported-scenarios"></a>サポートされるシナリオ

Salesforce データベースから、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この Salesforce コネクタは、Salesforce の **Developer Edition、Professional Edition、Enterprise Edition、Unlimited Edition** の各エディションをサポートします。 また、Salesforce **運用環境、サンドボックス、およびカスタム ドメイン**からのデータのコピーをサポートします。

## <a name="prerequisites"></a>前提条件

* Salesforce で API アクセス許可を有効にする必要があります。 「 [How do I enable API access in Salesforce by permission set? (権限セットで Salesforce の API アクセスを有効にする方法)](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Salesforce の要求の制限

Salesforce では、API 要求数の合計と、API の同時要求数に上限が設けられています。 以下の点に注意してください。

- 同時要求数が上限を超えると調整が発生し、ランダムにエラーが表示されます。
- 要求数の合計が上限を超えると、Salesforce アカウントが 24 時間ブロックされます。

また、どちらの場合も、"REQUEST_LIMIT_EXCEEDED" エラーが表示されることがあります。 詳細については、[Salesforce Developer の制限](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf)に関する資料の「API Request Limits (API 要求の制限)」をご覧ください。

## <a name="getting-started"></a>使用の開始
コピー アクティビティを含むパイプラインは、.NET SDK、Python SDK、Azure PowerShell、REST API、または Azure Resource Manager テンプレートを使用して作成できます。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](quickstart-create-data-factory-dot-net.md)をご覧ください。

次のセクションでは、Salesforce コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Salesforce のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type |type プロパティを **Salesforce**に設定する必要があります。 |はい |
| environmentUrl | Salesforce インスタンスの URL を指定します。 <br><br> 既定値は `"https://login.salesforce.com"` です。 <br> - サンドボックスからデータをコピーするには、`"https://test.salesforce.com"` を指定します。 <br> - カスタム ドメインからデータをコピーするには、`"https://[domain].my.salesforce.com"`のように指定します。 |いいえ |
| username |ユーザー アカウントのユーザー名を指定します。 |はい |
| パスワード |ユーザー アカウントのパスワードを指定します。 |はい |
| securityToken |ユーザー アカウントのセキュリティ トークンを指定します。 セキュリティ トークンのリセット/取得方法については、 [セキュリティ トークンの取得](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) に関する記事をご覧ください。 セキュリティ トークンの概要については、「[Security and the API (セキュリティと API)](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)」をご覧ください。 |あり |

**例:**

```json
{
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        }
    },
    "name": "SalesforceLinkedService"
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、データセットに関する記事をご覧ください。 このセクションでは、Salesforce データセット でサポートされるプロパティの一覧を示します。

Salesforce からデータをコピーするには、データセットの type プロパティを **RelationalTable** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは **RelationalTable** に設定する必要があります。 | あり |
| tableName | Salesforce データベースのテーブルの名前。 | いいえ (アクティビティ ソースの "query" が指定されている場合) |

> [!IMPORTANT]
> カスタム オブジェクトには、API 名の "__c" の部分が必要となります。

![Data Factory - Salesforce の接続 - API 名](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**例:**

```json
{
    "name": "SalesforceDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable__c"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Salesforce ソースでサポートされるプロパティの一覧を示します。

### <a name="salesforce-as-source"></a>ソースとしての Salesforce

Salesforce からデータをコピーするには、コピー アクティビティのソースの種類を **RelationalSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティを **RelationalSource** に設定する必要があります。 | あり |
| クエリ |カスタム クエリを使用してデータを読み取ります。 SQL-92 クエリまたは [Salesforce オブジェクト クエリ言語 (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) クエリを使用できます。 (例: `select * from MyTable__c`)。 | いいえ (データセットの "tableName" が指定されている場合) |

> [!IMPORTANT]
> カスタム オブジェクトには、API 名の "__c" の部分が必要となります。

![Data Factory - Salesforce の接続 - API 名](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**例:**

```json
"activities":[
    {
        "name": "CopyFromSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce input dataset name>",
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
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="query-tips"></a>クエリのヒント

### <a name="retrieving-data-from-salesforce-report"></a>Salesforce レポートからのデータの取得

`{call "<report name>"}. Example: `"query": "{call \"TestReport\"}"` というクエリを指定することで、Salesforce レポートからデータを取得できます。

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>削除済みレコードを Salesforce のごみ箱から取得する

論理的に削除されたレコードを Salesforce のごみ箱から検索するには、クエリで **"IsDeleted = 1"** と指定します。 たとえば、次のように入力します。

* 削除されたレコードのみを検索するには、"select * from MyTable__c **where IsDeleted= 1**" と指定します。
* 既存および削除済みを含むすべてのレコードを検索するには、"select * from MyTable__c **where IsDeleted = 0 or IsDeleted = 1**" と指定します。

### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>DateTime 列に対して where 句を 使ってデータを取得する

SOQL クエリまたは SQL クエリを指定する場合は、DateTime 形式の相違点に注意してください。 次に例を示します。

* **SOQL サンプル**: `$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', <datetime parameter>, <datetime parameter>)`
* **SQL の例**: `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', <datetime parameter>, <datetime parameter>)`

## <a name="data-type-mapping-for-salesforce"></a>Salesforce のデータ型マッピング

Salesforce からデータをコピーするとき、次の Salesforce のデータ型から Azure Data Factory の中間データ型へのマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| Salesforce のデータ型 | Data Factory の中間データ型 |
|:--- |:--- |
| オート ナンバー |String |
| チェックボックス |Boolean |
| 通貨 |Double |
| 日付 |DateTime |
| 日付/時刻 |DateTime |
| 電子メール |String |
| ID |String |
| 参照リレーションシップ |String |
| 複数選択の候補リスト |String |
| Number |Double |
| Percent |Double |
| 電話 |String |
| 候補リスト |String |
| テキスト |String |
| テキスト領域 |String |
| テキスト領域 (ロング) |String |
| テキスト領域 (リッチ) |String |
| テキスト (暗号化) |String |
| URL |String |


## <a name="next-steps"></a>次のステップ
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md##supported-data-stores-and-formats)の表をご覧ください。