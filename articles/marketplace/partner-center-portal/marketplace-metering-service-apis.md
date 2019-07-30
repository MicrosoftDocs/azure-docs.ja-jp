---
title: Marketplace の測定サービス API | Azure Marketplace
description: Azure Marketplace での SaaS オファーの使用状況イベント。
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 35e6c61a8e8537035d70323c85dfc7a76f87cbcd
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869338"
---
# <a name="marketplace-metering-service-apis"></a>Marketplace の測定サービス API

使用状況イベント API を使用すると、特定の購入済みエンティティの使用状況イベントを出力できます。 使用状況イベントの要求は、オファーを発行するときに発行元によって定義された測定サービスのディメンションを参照します。

## <a name="usage-event"></a>使用状況イベント

**POST**: `https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*クエリ パラメーター:*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | この要求で使用する操作のバージョン。 最新の API バージョンは 2018-08-31 です。 |

*要求ヘッダー:*

| Content-type       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
| `x-ms-correlationid` | クライアントでの操作に対する一意の文字列値。 このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
| `authorization`   | [JSON Web トークン (JWT) ベアラー トークンを取得します。](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) 注:HTTP 要求を作成するとき、参照されるリンクから取得されたトークンにプレフィックス `Bearer` を付けます。 |

*要求:*

```json
{
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

### <a name="responses"></a>Responses

コード:200<br>
OK 

```json
{
  "usageEventId": "Unique identifier associated with the usage event",
  "status": "Accepted",
  "messageTime": "Time this message was created in UTC",
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

コード:400 <br>
正しくない要求。データがないか、無効なデータが指定されたか、または有効期限が切れている

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
正しくない要求。データがないか、無効なデータが指定されたか、または有効期限が切れている

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

コード:409<br>
競合。使用状況リソース ID と有効な使用状況の使用状況呼び出しを受信したとき、それは既に存在しています。 応答には、受け付けられたメッセージに関する情報を含む `additionalInfo` フィールドが含まれます。

```json
{
  "code": "Conflict",
  "additionalInfo": {
    "usageEventId": "Unique identifier associated with the usage event",
    "status": "Accepted|NotProcessed|Expired",
    "messageTime": "Time this message was created in UTC",
    "resourceId": "Identifier of the resource against which usage is emitted",
    "quantity": 5.0,
    "dimension": "Dimension identifier",
    "effectiveStartTime": "Time in UTC when the usage event occurred",
    "planId": "Plan associated with the purchased offer"
  }
}
```

## <a name="batch-usage-event"></a>バッチ使用状況イベント

バッチ使用状況イベント API を使用すると、複数の購入済みエンティティの使用状況イベントを直ちに出力できます。 バッチ使用状況イベントの要求は、オファーを発行するときに発行元によって定義された測定サービスのディメンションを参照します。

>[!Note]
>Microsoft の商用マーケットプレースでは、複数の SaaS オファーを登録できます。 登録されている各 SaaS オファーには、認証と承認のために登録されている固有の Azure AD アプリケーションがあります。 バッチに出力されるイベントは、オファーの登録の時点で、同じ Azure AD アプリケーションを含むオファーに属している必要があります。

**POST:** `https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*クエリ パラメーター:*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | この要求で使用する操作のバージョン。 最新の API バージョンは 2018-08-31 です。 |

*要求ヘッダー:*

| Content-type       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。 この値が指定されない場合は、値が生成され、応答ヘッダーに指定されます。 |
| `x-ms-correlationid` | クライアントでの操作に対する一意の文字列値。 このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。 この値が指定されない場合は、値が生成され、応答ヘッダーに指定されます。 |
| `authorization`      | [JSON Web トークン (JWT) ベアラー トークンを取得します。](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) 注:HTTP 要求を作成するとき、参照されるリンクから取得されたトークンにプレフィックス `Bearer` を付けます。  |

*要求:*
```json
{
  "request": [
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    },
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    }
  ]
}
```
### <a name="responses"></a>Responses

コード:200<br>
OK

```json
{
  "count": 2,
  "result": [
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    },
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    }
  ]
}
```

`BatchUsageEvent` API 応答で参照される状態コードの説明:

| status code  | 説明 |
| ---------- | -------------------- |
| `Accepted` | コードが受け付けられました。 |
| `Expired` | 使用状況の有効期限が切れています。 |
| `Duplicate` | 重複した使用状況が指定されました。 |
| `Error` | エラー コード。 |
| `ResourceNotFound` | 指定された使用状況リソースは無効です。 |
| `ResourceNotAuthorized` | このリソースの使用状況を指定することは許可されていません。 |
| `InvalidDimension` | 使用状況が渡されたディメンションはこのオファー/プランでは無効です。 |
| `InvalidQuantity` | 渡された数量が 0 未満です。 |
| `BadArgument` | 入力がないか、または不正な形式です。 |

コード:400<br>
正しくない要求。データがないか、無効なデータが指定されたか、または有効期限が切れている

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "Invalid data format.",
      "target": "usageEventRequest",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```
コード:403<br>
ユーザーはこの呼び出しを行うことを承認されていない

```json
{
  "code": "Forbidden",
  "message": "User is not allowed to call this"
}
```

## <a name="next-steps"></a>次の手順

詳細については、[SaaS の従量制課金](./saas-metered-billing.md)に関するページを参照してください。
