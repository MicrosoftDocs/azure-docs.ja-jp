---
title: 測定サービス API - Microsoft 商業マーケットプレース
description: 使用状況イベント API を使用すると、Microsoft AppSource および Azure Marketplace で SaaS オファーの使用状況イベントを出力できます。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/26/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: b75964f8cfc41efc35858284dbffded3aa406eb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96436064"
---
# <a name="marketplace-metered-billing-apis"></a>マーケットプレースの従量制課金 API

従量制課金 API は、発行元がパートナー センターで発行されるオファーのカスタム測定ディメンションを作成するときに使用する必要があります。 従量制課金 API との統合は、使用状況イベントを生成するカスタム ディメンションを含む 1 つ以上のプランを持つ購入済みオファーに必要です。

SaaS のカスタム測定ディメンションを作成する方法の詳細については、「[マーケットプレース測定サービスを使用した従量制課金](saas-metered-billing.md)」を参照してください。

マネージド アプリ プランを使用して Azure アプリケーション オファーのカスタム測定ディメンションを作成する方法の詳細については、「[Azure アプリケーション オファーのセットアップの詳細を構成する](../create-new-azure-apps-offer.md#configure-your-azure-application-offer-setup-details)」を参照してください。

## <a name="enforcing-tls-12-note"></a>TLS 1.2 Note を適用する

TLS バージョン 1.2 バージョンは、HTTPS 通信の最小バージョンとして適用されます。 コードでこの TLS バージョンを使用していることを確認してください。 TLS バージョン 1.0 および 1.1 は非推奨となり、接続の試行は拒否されます。

## <a name="metered-billing-single-usage-event"></a>従量制課金の単一使用状況イベント

使用状況イベント API は、特定の顧客によって購入されたプランのアクティブなリソース (サブスクライブ済み) に対して使用状況イベントを生成するために、発行元から呼び出される必要があります。 使用状況イベントは、オファーの発行時に、発行元によって定義されたプランのカスタム ディメンションごとに個別に生成されます。

カレンダー日の 1 時間ごとに生成できる使用状況イベントは 1 つだけです。 たとえば、今日の午前 8:15 に、1 つの使用状況イベントを生成できます。 このイベントが受け入れられた場合、次の使用状況イベントは、今日の午前 9:00 から受け入れられます。 追加のイベントを今日の 8:15 と 8:59:59 の間に送信すると、重複として拒否されます。 1 時間以内に消費されたすべてのユニットを累積し、1 つのイベントで生成する必要があります。

カレンダー日の 1 時間ごとに生成できる使用状況イベントはリソースあたり 1 つだけです。 1 時間に複数のユニットが消費される場合は、その時間内に消費されるすべてのユニットを累積し、1 つのイベントで生成します。 使用状況イベントは過去 24 時間のみ生成できます。 使用状況イベントを 8:00 と 8:59:59 の間の任意の時点で生成した (かつ受け入れられた) 場合、同じ日の 8:00 と 8:59:59 の間に追加のイベントを送信すると、重複として拒否されます。

**POST**: `https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*クエリ パラメーター:*

| パラメーター | 推奨          |
| ---------- | ---------------------- |
| `ApiVersion` | 2018-08-31 を使用します。 |
| | |

*要求ヘッダー:*

| Content-type       | `application/json` を使用します  |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
| `x-ms-correlationid` | クライアントでの操作に対する一意の文字列値。 このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
| `authorization`   | この API 呼び出しを行う ISV を識別する一意のアクセス トークン。 以下の説明に従って、発行元によってトークン値が取得される場合、形式は `"Bearer <access_token>"` です <br> <ul> <li> [HTTP POST でトークンを取得する](./pc-saas-registration.md#get-the-token-with-an-http-post) SaaS。 </li> <li> [認証方法](./marketplace-metering-service-authentication.md)のマネージド アプリケーション。 </li> </ul> |
| | |

*要求本文の例:*

```json
{
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. 
  "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, from now and until 24 hours back
  "planId": "plan1", // id of the plan purchased for the offer
}
```

>[!NOTE]
>`resourceId` は、SaaS アプリとカスタム メーターを生成するマネージド アプリでは、意味が異なります。 

Azure Application Managed Apps プランの場合、`resourceId` は、マネージド アプリの `resource group Id`です。 これを取得するためのサンプル スクリプトについては、「[Azure マネージド ID トークンの使用](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token)」をご覧ください。 

SaaS オファーの場合、`resourceId` は SaaS サブスクリプション ID です。 SaaS サブスクリプションの詳細については、「[サブスクリプションの一覧](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions)」をご覧ください。

### <a name="responses"></a>Responses

コード:200<br>
OK です。 使用状況の生成は、さらに処理と課金を行うために、Microsoft 側で受け入れられ、記録されました。

応答ペイロードの例: 

```json
{
  "usageEventId": <guid>, // unique identifier associated with the usage event in Microsoft records
  "status": "Accepted" // this is the only value in case of single usage event
  "messageTime": "2020-01-12T13:19:35.3458658Z", // time in UTC this event was accepted
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. For SaaS it's the subscriptionId.
  "quantity": 5.0, // amount of emitted units as recorded by Microsoft
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, as sent by the ISV
  "planId": "plan1", // id of the plan purchased for the offer
}
```

コード:400 <br>
無効な要求です。

* 指定された要求データが見つからないか、無効です。
* `effectiveStartTime` が過去 24 時間を過ぎています。 イベントの有効期限が切れています。
* SaaS サブスクリプションはサブスクライブ済み状態ではありません。

応答ペイロードの例: 

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "The resourceId is required.",
      "target": "ResourceId",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```

コード:403<br>

Forbidden. 認証トークンが指定されていない、無効である、または有効期限が切れています。  または、要求が、認証トークンの作成に使用されたものとは異なる Azure AD アプリ ID で発行されたオファーのサブスクリプションにアクセスしようとしています。

コード:409<br>
競合しています。 指定されたリソース ID、有効な使用日時について、使用状況イベントが既に正常に報告されています。

応答ペイロードの例: 

```json
{
  "additionalInfo": {
    "acceptedMessage": {
      "usageEventId": "<guid>", //unique identifier associated with the usage event in Microsoft records
      "status": "Duplicate",
      "messageTime": "2020-01-12T13:19:35.3458658Z",
      "resourceId": "<guid>", //unique identifier of the resource against which usage is emitted.
      "quantity": 1.0,
      "dimension": "dim1",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "plan1"
    }
  },
  "message": "This usage event already exist.",
  "code": "Conflict"
}
```

## <a name="metered-billing-batch-usage-event"></a>従量制課金のバッチ使用状況イベント

バッチ使用状況イベント API を使用すると、複数の購入済みリソースの使用状況イベントを一度に生成できます。 また、カレンダー時間が異なる限り、同じリソースに対して複数の使用状況イベントを生成することもできます。 1 つのバッチに含まれるイベントの最大数は 25 です。

**POST:** `https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*クエリ パラメーター:*

| パラメーター  | 推奨     |
| ---------- | -------------------- |
| `ApiVersion` | 2018-08-31 を使用します。 |

*要求ヘッダー:*

| Content-type       | `application/json` を使用します       |
| ------------------ | ------ |
| `x-ms-requestid`     | クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。 この値が指定されない場合は、値が生成され、応答ヘッダーに指定されます。 |
| `x-ms-correlationid` | クライアントでの操作に対する一意の文字列値。 このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。 この値が指定されない場合は、値が生成され、応答ヘッダーに指定されます。 |
| `authorization`      | この API 呼び出しを行う ISV を識別する一意のアクセス トークン。 以下の説明に従って、発行元によってトークン値が取得される場合、形式は `Bearer <access_token>` です <br> <ul> <li> [HTTP POST でトークンを取得する](./pc-saas-registration.md#get-the-token-with-an-http-post) SaaS。 </li> <li> [認証方法](./marketplace-metering-service-authentication.md)のマネージド アプリケーション。 </li> </ul> |
| | |


*要求本文の例:*

```json
{
  "request": [ // list of usage events for the same or different resources of the publisher
    { // first event
      "resourceId": "<guid1>", // Unique identifier of the resource against which usage is emitted. 
      "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
      "dimension": "dim1", //Custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",//Time in UTC when the usage event occurred, from now and until 24 hours back
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // next event
      "resourceId": "<guid2>", 
      "quantity": 39.0, 
      "dimension": "email", 
      "effectiveStartTime": "2018-11-01T23:33:10
      "planId": "gold", // id of the plan purchased for the offer
    }
  ]
}
```

>[!NOTE]
>`resourceId` は、SaaS アプリとカスタム メーターを生成するマネージド アプリでは、意味が異なります。 

Azure Application Managed Apps プランの場合、`resourceId` は、マネージド アプリの `resource group Id`です。 これを取得するためのサンプル スクリプトについては、「[Azure マネージド ID トークンの使用](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token)」をご覧ください。 

SaaS オファーの場合、`resourceId` は SaaS サブスクリプション ID です。 SaaS サブスクリプションの詳細については、「[サブスクリプションの一覧](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions)」をご覧ください。

### <a name="responses"></a>Responses

コード:200<br>
OK です。 バッチ使用状況の生成は、さらに処理と課金を行うために、Microsoft 側で受け入れられ、記録されました。 応答リストは、バッチ内の個々のイベントごとに状態と共に返されます。 バッチ イベントの一部として送信された個々の使用状況イベントの応答を理解するには、応答ペイロードを反復処理する必要があります。

応答ペイロードの例: 

```json
{
  "count": 2, // number of records in the response
  "result": [
    { // first response
      "usageEventId": "<guid>", // unique identifier associated with the usage event in Microsoft records
      "status": "Accepted" // see list of possible statuses below,
      "messageTime": "2020-01-12T13:19:35.3458658Z", // Time in UTC this event was accepted by Microsoft,
      "resourceId": "<guid1>", // unique identifier of the resource against which usage is emitted.
      "quantity": 5.0, // amount of emitted units as recorded by Microsoft 
      "dimension": "dim1", // custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",// time in UTC when the usage event occurred, as sent by the ISV
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // second response
      "status": "Duplicate",
      "messageTime": "0001-01-01T00:00:00",
      "error": {
        "additionalInfo": {
          "acceptedMessage": {
            "usageEventId": "<guid>",
            "status": "Duplicate",
            "messageTime": "2020-01-12T13:19:35.3458658Z",
            "resourceId": "<guid2>",
            "quantity": 1.0,
            "dimension": "email",
            "effectiveStartTime": "2020-01-12T11:03:28.14Z",
            "planId": "gold"
          }
        },
        "message": "This usage event already exist.",
        "code": "Conflict"
      },
      "resourceId": "<guid2>",
      "quantity": 1.0,
      "dimension": "email",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "gold"
    }
  ]
}
```

`BatchUsageEvent` API 応答で参照される状態コードの説明:

| status code  | 説明 |
| ---------- | -------------------- |
| `Accepted` | 受理されました。 |
| `Expired` | 使用状況の有効期限が切れています。 |
| `Duplicate` | 重複した使用状況が指定されました。 |
| `Error` | エラー コード。 |
| `ResourceNotFound` | 指定された使用状況リソースは無効です。 |
| `ResourceNotAuthorized` | このリソースの使用状況を指定することは許可されていません。 |
| `InvalidDimension` | 使用状況が渡されたディメンションはこのオファー/プランでは無効です。 |
| `InvalidQuantity` | 渡された数量が 0 以下です。 |
| `BadArgument` | 入力がないか、または不正な形式です。 |

コード:400<br>
無効な要求です。 バッチに 25 個を超える使用状況イベントが含まれていました。

コード:403<br>
Forbidden. 認証トークンが指定されていない、無効である、または有効期限が切れています。  または、要求が、認証トークンの作成に使用されたものとは異なる Azure AD アプリ ID で発行されたオファーのサブスクリプションにアクセスしようとしています。

## <a name="development-and-testing-best-practices"></a>開発とテストのベスト プラクティス

カスタム メーターの生成をテストするには、使用状況測定 API との統合を実装し、ユニットあたりの価格がゼロでカスタム ディメンションが定義された発行済みの SaaS オファーのプランを作成します。 また、このオファーをプレビューとして発行すると、制限されたユーザーのみが統合にアクセスしてテストできるようになります。

また、既存のライブ オファーにプライベート プランを使用して、テスト中に限定された対象ユーザーに対してのみ、このプランへのアクセスを制限することもできます。

## <a name="get-support"></a>サポートを受ける

[パートナー センターでの商業マーケットプレース プログラムのサポート](../support.md)の手順に従って、発行元のサポート オプションを理解し、Microsoft のサポート チケットを開きます。

## <a name="next-steps"></a>次のステップ

詳細については、「[Marketplace の測定サービス API - FAQ](./marketplace-metering-service-apis-faq.md)」を参照してください。
