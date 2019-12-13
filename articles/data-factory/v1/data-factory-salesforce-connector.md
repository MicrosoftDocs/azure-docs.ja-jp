---
title: Data Factory を使用して Salesforce からデータを移動する
description: Azure Data Factory を使用して Salesforce からデータを移動する方法を説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: dbe3bfd6-fa6a-491a-9638-3a9a10d396d1
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8b94f6388d77cca2ef74c802aec7648091172775
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929267"
---
# <a name="move-data-from-salesforce-by-using-azure-data-factory"></a>Azure Data Factory を使用して Salesforce からデータを移動する
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](data-factory-salesforce-connector.md)
> * [バージョン 2 (最新バージョン)](../connector-salesforce.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[V2 の Salesforce コネクタ](../connector-salesforce.md)に関するページを参照してください。

この記事では、Azure Data Factory のコピー アクティビティを使用して、Salesforce から、 [サポートされているソースとシンク](data-factory-data-movement-activities.md#supported-data-stores-and-formats) に関する表のシンクの欄に一覧表示されているデータ ストアにデータをコピーする方法について説明します。 この記事は、「 [データ移動アクティビティ](data-factory-data-movement-activities.md) 」という記事に基づき、コピー アクティビティによるデータ移動の一般概要とサポートされるデータ ストアの組み合わせについて紹介しています。

現在のところ、Azure Data Factory は、Salesforce から、[サポートされているシンク データ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats) へのデータの移動のみに対応しており、他のデータ ストアから Salesforce にデータを移動することはできません。

## <a name="supported-versions"></a>サポートされているバージョン
このコネクタでサポートされている Salesforce のエディションは、Developer Edition、Professional Edition、Enterprise Edition、または Unlimited Edition です。 また、Salesforce 運用、サンドボックス、およびカスタム ドメインからのコピーをサポートしています。

## <a name="prerequisites"></a>前提条件
* API アクセス許可を有効にする必要があります。 「 [How do I enable API access in Salesforce by permission set? (権限セットで Salesforce の API アクセスを有効にする方法)](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)
* Salesforce からオンプレミスのデータ ストアにデータをコピーするには、バージョン 2.0 以降の Data Management Gateway がオンプレミス環境にインストールされている必要があります。

## <a name="salesforce-request-limits"></a>Salesforce の要求の制限
Salesforce では、API 要求数の合計と、API の同時要求数に上限が設けられています。 以下の点に注意してください。

- 同時要求数が上限を超えると調整が発生し、ランダムにエラーが表示されます。
- 要求数の合計が上限を超えると、Salesforce アカウントが 24 時間ブロックされます。

また、どちらの場合も、"REQUEST_LIMIT_EXCEEDED" エラーが表示されることがあります。 詳細については、[Salesforce Developer の制限](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf)に関する資料の「API Request Limits (API 要求の制限)」をご覧ください。

## <a name="getting-started"></a>使用の開始
さまざまなツール/API を使用して、Salesforce からデータを移動するコピー アクティビティを含むパイプラインを作成できます。

パイプラインを作成する最も簡単な方法は、**コピー ウィザード**を使うことです。 手順については、「[チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md)」を参照してください。データのコピー ウィザードを使用してパイプラインを作成する簡単なチュートリアルです。

また、次のツールを使用してパイプラインを作成することもできます。**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager テンプレート**、 **.NET API**、**REST API**。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)をご覧ください。

ツールと API のいずれを使用する場合も、次の手順を実行して、ソース データ ストアからシンク データ ストアにデータを移動するパイプラインを作成します。

1. **リンクされたサービス**を作成し、入力データ ストアと出力データ ストアをデータ ファクトリにリンクします。
2. コピー操作用の入力データと出力データを表す**データセット**を作成します。
3. 入力としてのデータセットと出力としてのデータセットを受け取るコピー アクティビティを含む**パイプライン**を作成します。

ウィザードを使用すると、Data Factory エンティティ (リンクされたサービス、データセット、パイプライン) に関する JSON の定義が自動的に作成されます。 (.NET API を除く) ツールまたは API を使う場合は、JSON 形式でこれらの Data Factory エンティティを定義します。 Salesforce からデータをコピーするために使用される Data Factory エンティティに関する JSON 定義のサンプルについては、この記事の「[JSON サンプル: Salesforce から Azure BLOB にデータをコピーする](#json-example-copy-data-from-salesforce-to-azure-blob)」のセクションを参照してください。

次のセクションでは、Salesforce に固有の Data Factory エンティティの定義に使用される JSON プロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ
次の表は、Salesforce のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| type |type プロパティは、次のように設定する必要があります:**Salesforce**。 |はい |
| environmentUrl | Salesforce インスタンスの URL を指定します。 <br><br> - 既定では "https:\//login.salesforce.com" です。 <br> - サンドボックスからデータをコピーするには、"https://test.salesforce.com" を指定します。 <br> - カスタム ドメインからデータをコピーするには、たとえば "https://[ドメイン].my.salesforce.com" を指定します。 |いいえ |
| username |ユーザー アカウントのユーザー名を指定します。 |はい |
| password |ユーザー アカウントのパスワードを指定します。 |はい |
| securityToken |ユーザー アカウントのセキュリティ トークンを指定します。 セキュリティ トークンのリセット/取得方法については、 [セキュリティ トークンの取得](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) に関する記事をご覧ください。 セキュリティ トークンの概要については、「[Security and the API (セキュリティと API)](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)」をご覧ください。 |はい |

## <a name="dataset-properties"></a>データセットのプロパティ
データセットの定義に使用できるセクションとプロパティの完全な一覧については、 [データセットの作成](data-factory-create-datasets.md) に関する記事をご覧ください。 データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型 (Azure SQL、Azure BLOB、Azure テーブルなど) でほぼ同じです。

**typeProperties** セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。 **RelationalTable** 型のデータセットの typeProperties セクションには次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| tableName |Salesforce のテーブル名。 |いいえ (**RelationalSource** の**クエリ**が指定されている場合) |

> [!IMPORTANT]
> カスタム オブジェクトには、API 名の "__c" の部分が必要となります。

![Data Factory - Salesforce の接続 - API 名](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ
アクティビティの定義に使用できるセクションとプロパティの完全な一覧については、 [パイプラインの作成](data-factory-create-pipelines.md) に関する記事をご覧ください。 名前、説明、入力テーブル、出力テーブル、さまざまなポリシーなどのプロパティがあらゆる種類のアクティビティで利用できます。

一方、アクティビティの typeProperties セクションで使用できるプロパティは、各アクティビティの種類によって異なります。 コピー アクティビティの場合、ソースとシンクの種類によって異なります。

コピー アクティビティで、source が **RelationalSource** 型 (Salesforce を含む) の場合は、typeProperties セクションで次のプロパティを使用できます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| query |カスタム クエリを使用してデータを読み取ります。 |SQL-92 クエリまたは [Salesforce オブジェクト クエリ言語 (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) クエリ。 (例: `select * from MyTable__c`)。 |いいえ (**dataset** の **tableName** が指定されている場合) |

> [!IMPORTANT]
> カスタム オブジェクトには、API 名の "__c" の部分が必要となります。

![Data Factory - Salesforce の接続 - API 名](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="query-tips"></a>クエリのヒント
### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>DateTime 列に対して where 句を 使ってデータを取得する
SOQL クエリまたは SQL クエリを指定する場合は、DateTime 形式の相違点に注意してください。 例:

* **SOQL サンプル**: `$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd)`
* **SQL サンプル**:
    * **コピー ウィザードを使用してクエリを指定:** `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)`
    * **JSON 編集を使用してクエリを指定 (char を適切にエスケープ):** `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', WindowStart, WindowEnd)`

### <a name="retrieving-data-from-salesforce-report"></a>Salesforce レポートからのデータの取得
`{call "<report name>"}` というクエリを指定することで、Salesforce レポートからデータを取得できます。例: `"query": "{call \"TestReport\"}"`

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>削除済みレコードを Salesforce のごみ箱から取得する
論理的に削除されたレコードを Salesforce のごみ箱から検索するには、クエリで **"IsDeleted = 1"** と指定します。 たとえば、次のように入力します。

* 削除されたレコードのみを検索するには、"select * from MyTable__c **where IsDeleted= 1**" と指定します。
* 既存および削除済みを含むすべてのレコードを検索するには、"select * from MyTable__c **where IsDeleted = 0 or IsDeleted = 1**" と指定します。

## <a name="json-example-copy-data-from-salesforce-to-azure-blob"></a>JSON の使用例:Salesforce から Azure BLOB にデータをコピーする
次の例は、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。 これらの例は、Salesforce から Azure BLOB ストレージにデータをコピーする方法を示しています。 ただし、Azure Data Factory のコピー アクティビティを使用して、 [こちら](data-factory-data-movement-activities.md#supported-data-stores-and-formats) に記載されているシンクのいずれかにデータをコピーすることができます。

このシナリオの実現にあたって作成する必要のある Data Factory のアーティファクトは次のとおりです。 これらの手順については、箇条書きの後に続く各セクションで詳しく説明します。

* [Salesforce](#linked-service-properties)
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* [RelationalTable](#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)
* [RelationalSource](#copy-activity-properties) と [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)

**Salesforce のリンクされたサービス**

この例では、 **Salesforce** のリンクされたサービスを使用します。 このリンクされたサービスでサポートされているプロパティについては、 [Salesforce のリンクされたサービス](#linked-service-properties) に関するセクションをご覧ください。 セキュリティ トークンのリセット/取得方法については、 [セキュリティ トークンの取得](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) に関する記事をご覧ください。

```json
{
    "name": "SalesforceLinkedService",
    "properties":
    {
        "type": "Salesforce",
        "typeProperties":
        {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```
**Azure Storage のリンクされたサービス**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```
**Salesforce の入力データセット**

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**external** を **true** に設定すると、データセットが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。

> [!IMPORTANT]
> カスタム オブジェクトには、API 名の "__c" の部分が必要となります。

![Data Factory - Salesforce の接続 - API 名](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Azure BLOB の出力データセット**

データは新しい BLOB に 1 時間おきに書き込まれます (頻度: 時間、間隔: 1)。

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/alltypes_c"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**コピー アクティビティのあるパイプライン**

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプライン JSON 定義で、**source** の型が **RelationalSource** に設定され、**sink** の型が **BlobSink** に設定されています。

RelationalSource でサポートされるプロパティの一覧については、「 [RelationalSource type プロパティ](#copy-activity-properties) 」をご覧ください。

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[
        {
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "SalesforceInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }
        ]
    }
}
```
> [!IMPORTANT]
> カスタム オブジェクトには、API 名の "__c" の部分が必要となります。

![Data Factory - Salesforce の接続 - API 名](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)


### <a name="type-mapping-for-salesforce"></a>Salesforce の型マッピング

| Salesforce の型 | .NET ベースの型 |
| --- | --- |
| オート ナンバー |String |
| Checkbox |Boolean |
| Currency |Decimal |
| Date |DateTime |
| 日付/時刻 |DateTime |
| Email |String |
| Id |String |
| Lookup Relationship |String |
| Multi-Select Picklist |String |
| Number |Decimal |
| Percent |Decimal |
| Phone |String |
| Picklist |String |
| Text |String |
| Text Area |String |
| Text Area (Long) |String |
| Text Area (Rich) |String |
| Text (Encrypted) |String |
| URL |string |

> [!NOTE]
> ソース データセット列からシンク データセット列へのマッピングについては、[Azure Data Factory のデータセット列のマッピング](data-factory-map-columns.md)に関するページをご覧ください。

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「 [コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md) 」をご覧ください。
