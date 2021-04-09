---
title: 最初の API 呼び出しを行って、コマーシャル マーケットプレース分析データにアクセスする
description: コマーシャル マーケットプレース分析データにアクセスするための API の使用方法を説明するための例。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 2d0c0e7322ecb92fd371f5bf7924a370dd29fe85
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583567"
---
# <a name="make-your-first-api-call-to-access-commercial-marketplace-analytics-data"></a>最初の API 呼び出しを行って、コマーシャル マーケットプレース分析データにアクセスする

コマーシャル マーケットプレース分析データにアクセスするための API の一覧については、[コマーシャル マーケットプレース分析データにアクセスするための API](analytics-available-apis.md) に関するページを参照してください。 最初の API 呼び出しを行う前に、プログラムを使用してコマーシャル マーケットプレース分析データにアクセスするための[前提条件](analytics-prerequisites.md)を満たしていることを確認してください。

## <a name="token-generation"></a>トークンの生成

いずれかのメソッドを呼び出す前に、まず Azure Active Directory (Azure AD) アクセス トークンを取得する必要があります。 Azure AD アクセス トークンは、API の各メソッドの Authorization ヘッダーに渡す必要があります。 アクセス トークンを取得したら、期限が切れる 60 分が経過する前に使用します。 トークンの有効期限が切れた後は、トークンを更新してそれ以降の API 呼び出しで引き続き使用できます。

トークンの生成については、以下の要求のサンプルを参照してください。 トークンを生成するために必要な 3 つの値は、`clientId`、`clientSecret`、および `tenantId` です。 `resource` パラメーターは `https://graph.windows.net` に設定する必要があります。

***要求の例***:

```json
curl --location --request POST 'https://login.microsoftonline.com/{TenantId}/oauth2/token' \
--header 'return-client-request-id: true' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'resource=https://graph.windows.net' \
--data-urlencode 'client_id={client_id}' \
--data-urlencode 'client_secret={client_secret}' \
--data-urlencode 'grant_type=client_credentials'
```

***応答の例***:

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1612794445",
    "not_before": "1612790545",
    "resource": "https://graph.windows.net",
    "access_token": {Token}
}
```

アプリケーションの Azure AD トークンを取得する方法の詳細については、[Store サービスを使用した分析データへのアクセス](https://docs.microsoft.com/windows/uwp/monetize/access-analytics-data-using-windows-store-services#step-2-obtain-an-azure-ad-access-token)に関するページを参照してください。

## <a name="programmatic-api-call"></a>プログラムによる API 呼び出し

前のセクションの説明に従って Azure AD トークンを取得した後、次の手順に従って、プログラムによる最初のアクセス レポートを作成します。

データは、次のデータセット (datasetName) からダウンロードできます。

- ISVCustomer
- ISVMarketplaceInsights
- ISVUsage
- ISVOrder

次のセクションでは、ISVOrder データセットから `OrderId` にプログラムでアクセスする方法の例を示します。

### <a name="step-1-make-a-rest-call-using-the-get-datasets-api"></a>手順 1: データセットの取得 API を使用して REST 呼び出しを行う

この API 応答では、レポートをダウンロードできるデータセット名が提供されます。 特定のデータセットについては、この API 応答でカスタム レポート テンプレートに使用できる選択可能な列の一覧も提供されます。 また、次の表を参照して、列の名前を取得することもできます。

- [[注文の詳細] テーブル](orders-dashboard.md#orders-details-table)
- [使用状況の詳細テーブル](usage-dashboard.md#usage-details-table)
- [顧客詳細テーブル](customer-dashboard.md#customer-details-table)
- [Marketplace の分析情報の詳細テーブル](insights-dashboard.md#marketplace-insights-details-table)

***要求の例***:

```json
curl 
--location 
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledDataset ' \ 
--header 'Authorization: Bearer <AzureADToken>'
```

***応答の例***:

```json
{
    "value": [
        {
            "datasetName": "ISVOrder",
            "selectableColumns": [
                "MarketplaceSubscriptionId",
                "MonthStartDate",
                "OfferType",
                "AzureLicenseType",
                "Sku",
                "CustomerCountry",
                "IsPreviewSKU",
                "OrderId",
                "OrderQuantity",
                "CloudInstanceName",
                "IsNewCustomer",
                "OrderStatus",
                "OrderCancelDate",
                "CustomerCompanyName",
                "CustomerName",
                "OrderPurchaseDate",
                "OfferName",
                "TrialEndDate",
                "CustomerId",
                "BillingAccountId"
            ],
            "availableMetrics": [],
            "availableDateRanges": [
                "LAST_MONTH",
                "LAST_3_MONTHS",
                "LAST_6_MONTHS",
                "LIFETIME"
            ]
        },
    ],
    "totalCount": 1,
    "message": "Dataset fetched successfully",
    "statusCode": 200
}
```

### <a name="step-2-create-the-custom-query"></a>手順 2: カスタム クエリを作成する

この手順では、注文レポートの注文 ID を使用して、目的のレポートのカスタム クエリを作成します。 クエリで指定されていない場合の既定の `timespan` は、6 か月です。

***要求の例***:

```json
curl 
--location 
--request POST ' https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries' \ 
--header ' Authorization: Bearer <AzureAD_Token>' \ 
--header 'Content-Type: application/json' \ 
--data-raw 
            '{ 
                "Query": "SELECT OrderId from ISVOrder", 
                "Name": "ISVOrderQuery1", 
                "Description": "Get a list of all Order IDs" 
             }'
```

***応答の例***:

```json
{
    "value": [
        {
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "name": "ISVOrderQuery1",
            "description": "Get a list of all Order IDs”,
            "query": "SELECT OrderId from ISVOrder",
            "type": "userDefined",
            "user": "142344300",
            "createdTime": "2021-01-06T05:38:34",
            "modifiedTime": null
        }
    ],
    "totalCount": 1,
    "message": "Query created successfully",
    "statusCode": 200
}
```

クエリが正常に実行されると、レポートを生成するために使用する必要がある `queryId` が生成されます。

### <a name="step-3-execute-test-query-api"></a>手順 3: テスト クエリ API を実行する

この手順では、テスト クエリ API を使用して、作成されたクエリの上位 100 行を取得します。

***要求の例***:

```json
curl 
--location 
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/testQueryResult?exportQuery=SELECT%20OrderId%20from%20ISVOrder' \ 
--header ' Authorization: Bearer <AzureADToken>'
```

***応答の例***:

```json
{
    "value": [
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2ba8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bb8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bc8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bd8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2be8"
        },
               .
               .
               .

        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf0"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf1"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf2"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf3"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf4"
        }
    ],
    "totalCount": 100,
    "message": null,
    "statusCode": 200
}
```

### <a name="step-4-create-the-report"></a>手順 4: レポートを作成する

この手順では、以前に生成した `QueryId` を使用してレポートを作成します。

***要求の例***:

```json
curl 
--location 
--request POST 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport' \ 
--header ' Authorization: Bearer <AzureADToken>' \ 
--header 'Content-Type: application/json' \ 
--data-raw 
                 '{
                   “ReportName": "ISVReport1",
                   "Description": "Report for getting list of Order Ids",
                   "QueryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
                   "StartTime": "2021-01-06T19:00:00Z”,
                   "RecurrenceInterval": 48,
                   "RecurrenceCount": 20,
                    "Format": "csv"
                  }'
```

<br>

_**表 1: この要求の例で使用されるパラメーターの説明**_

| パラメーター | 説明 |
| ------------ | ------------- |
| `Description` | 生成されるレポートの簡単な説明を入力します。 |
| `QueryId` | これは、「手順 2: カスタム クエリを作成する」でクエリを作成したときに生成された `queryId` です。 |
| `StartTime` | レポートの最初の実行が開始された時刻。 |
| `RecurrenceInterval` | レポートの作成中に指定された繰り返し間隔。 |
| `RecurrenceCount` | レポートの作成中に指定された繰り返し回数。 |
| `Format` | CSV および TSV ファイル形式がサポートされています。 |
|||

***応答の例***:

```json
{
    "value": [
        {
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "reportName": "ISVReport1",
            "description": "Report for getting list of Order Ids",
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "query": "SELECT OrderId from ISVOrder",
            "user": "142344300",
            "createdTime": "2021-01-06T05:46:00Z",
            "modifiedTime": null,
            "startTime": "2021-01-06T19:00:00Z",
            "reportStatus": "Active",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": null,
            "format": "csv"
        }
    ],
    "totalCount": 1,
    "message": "Report created successfully",
    "statusCode": 200
}
```

正常に実行されると、レポートのダウンロードをスケジュールするために使用する必要がある `reportId` が生成されます。

### <a name="step-5-execute-report-executions-api"></a>手順 5: レポート実行 API を実行する

レポートのセキュリティで保護された場所 (URL) を取得するため、ここでレポート実行 API を実行します。

***要求の例***:

```json
Curl
--location
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/execution/72fa95ab-35f5-4d44-a1ee-503abbc88003' \
--header ' Authorization: Bearer <AzureADToken>' \
```

***応答の例***:

```json
{
    "value": [
        {
            "executionId": "1f18b53b-df30-4d98-85ee-e6c7e687aeed",
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": null,
            "format": "csv",
            "executionStatus": "Pending",
            "reportAccessSecureLink": null,
            "reportExpiryTime": null,
            "reportGeneratedTime": null
        }
    ],
    "totalCount": 1,
    "message": null,
    "statusCode": 200
}
```

## <a name="next-steps"></a>次のステップ

- [Swagger API URL](https://partneranalytics-api.azure-api.net/analytics/cmp/swagger/index.html) を使用して API を試すことができます
- [プログラムによるアクセスのパラダイム](analytics-programmatic-access.md)
