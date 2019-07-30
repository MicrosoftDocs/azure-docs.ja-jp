---
title: SaaS Fulfillment API v2 | Azure Marketplace
description: この記事では、関連付けられている Fulfillment v2 API を使用して、AppSource と Azure Marketplace 上に SaaS オファーを作成して管理する方法について説明します。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: 476aaacbe6f1bf6d1920df0f12599976bfcc27b7
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67701137"
---
# <a name="saas-fulfillment-apis-version-2"></a>SaaS Fulfillment API バージョン 2 

この記事では、パートナーが自社の SaaS アプリケーションを AppSource マーケットプレースと Azure Marketplace で販売できるようにする API について説明します。 これらの API は、AppSource と Azure Marketplace で取引可能な SaaS オファーの要件になります。

## <a name="managing-the-saas-subscription-life-cycle"></a>SaaS サブスクリプション ライフサイクルの管理

Azure SaaS では、SaaS サブスクリプション購入のライフ サイクル全体が管理されます。 Fulfillment API をメカニズムとして使用して、パートナーとの実際のフルフィルメント、プランの変更、およびサブスクリプションの削除が推進されます。 顧客には、Microsoft によって保持されている SaaS サブスクリプションの状態に基づいて課金されます。 次の図には、状態と、状態間の変更を扱う操作を示します。

![SaaS サブスクリプション ライフサイクルの状態](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>SaaS サブスクリプションの状態

次の表には、SaaS サブスクリプションのプロビジョニング状態が、それぞれの説明やシーケンス図 (該当する場合) を含めて一覧表示されています。 

#### <a name="provisioning"></a>プロビジョニング

顧客が購入を開始すると、パートナーは、URL パラメーターが使用される顧客との対話型 Web ページの承認コードでこの情報を受信します。 たとえば、`https://contoso.com/signup?token=..` です。ここで、パートナー センターのランディング ページの URL は `https://contoso.com/signup` です。 Resolve API を呼び出すことによって、この承認コードを検証し、プロビジョニング サービスの詳細情報と交換することができます。  SaaS サービスでプロビジョニングが終了すると、フルフィルメントが完了し、顧客に課金できることを伝えるために、アクティブ化呼び出しが送信されます。 

次の図は、プロビジョニング シナリオのための API 呼び出しのシーケンスを示します。  

![SaaS サービスをプロビジョニングするための API 呼び出し](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>プロビジョニング済み

この状態は、プロビジョニング済みサービスの安定した状態です。

##### <a name="provisioning-for-update"></a>更新プログラムのプロビジョニング 

この状態は、既存のサービスに対する更新が保留されていることを表します。 このような更新は、マーケットプレースまたは SaaS サービスでのみ顧客が開始できます (直販トランザクションのみ)。

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>更新のプロビジョニング (Marketplace から開始された場合)

次の図には、マーケットプレースから更新が開始されたときの一連のアクションが示されています。

![更新がマーケットプレースから開始されたときの API 呼び出し](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>更新のプロビジョニング (SaaS サービスから開始された場合)

次の図には、SaaS サービスから更新が開始されたときのアクションが示されています (SaaS サービスによって開始されたサブスクリプションへの更新によって、Webhook 呼び出しが置き換えられます)。 

![更新が SaaS サービスから開始されたときの API 呼び出し](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Suspended

この状態は、顧客の支払いが受け取られていないことを示します。 ポリシーにより、サブスクリプションをキャンセルするまでの猶予期間が顧客に与えられています。 サブスクリプションがこの状態の場合: 

- パートナーとして、サービスへのユーザーのアクセスを低下させるか、ブロックすることを選択できます。
- サブスクリプションは、データや設定を失わずにすべての機能を復元できる回復可能な状態に保持する必要があります。 
- Fulfillment API を介したこのサブスクリプションの再開要求、または猶予期間の最後のプロビジョニング解除要求の取得を想定してください。 

#### <a name="unsubscribed"></a>未購読 

サブスクリプションは、明示的な顧客の要求または料金の未払いに応じて、この状態に到達します。 パートナーが期待しているのは、回復要求に対応するために顧客のデータが一定の日数にわたって保持された後、削除されるというものです。 


## <a name="api-reference"></a>API リファレンス

このセクションでは、SaaS *サブスクリプション API* および *Operations API* について説明します。  バージョン 2 API の `api-version` パラメーターの値は `2018-08-31` です。  


### <a name="parameter-and-entity-definitions"></a>パラメーターとエンティティの定義

次の表は、Fulfillment API で使用される一般的なパラメーターとエンティティの定義の一覧です。

|     エンティティ/パラメーター     |     定義                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | SaaS リソースの GUID 識別子。  |
| `name`                   | 顧客がこのリソースに付けたフレンドリ名。 |
| `publisherId`            | 各発行元を表す一意の文字列識別子 (例: "contoso")。 |
| `offerId`                | 各オファーを表す一意の文字列識別子 (例: "offer1")。  |
| `planId`                 | 各プラン/SKU を表す一意の文字列識別子 (例: "silver")。 |
| `operationId`            | 特定の操作の GUID 識別子。  |
|  `action`                | リソースに対して実行されるアクション (`unsubscribe`、`suspend`、`reinstate`、または `changePlan`、`changeQuantity`、`transfer`)。  |
|   |   |

グローバルに一意な識別子 ([GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)) は、通常、自動的に生成される 128 ビット (32 桁の 16 進数) の数値です。 

#### <a name="resolve-a-subscription"></a>サブスクリプションを解除する 

解決エンドポイントにより、発行元はマーケットプレース トークンを永続的なリソース ID に解決できます。 リソース ID は、SaaS サブスクリプションの一意の識別子です。 ユーザーがパートナーの Web サイトにリダイレクトされると、URL のクエリ パラメーターにトークンが含まれます。 パートナーは、このトークンを使用すること、およびそれを解決するための要求を行うことを期待されています。 応答には、リソースの一意の SaaS サブスクリプション ID、名前、オファーID、およびプランが含まれます。 このトークンは、1 時間だけ有効です。 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Post<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*クエリ パラメーター:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  この要求で使用する操作のバージョン。  |

*要求ヘッダー:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|  x-ms-correlationid |  クライアントでの操作に対する一意の文字列値。 このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  authorization     |  [JSON Web トークン (JWT) ベアラー トークンを取得します](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。 |
|  x-ms-marketplace-token  |  ユーザーが Azure から SaaS パートナーの Web サイトにリダイレクトされるときの URL 内のトークン クエリ パラメーター (例: `https://contoso.com/signup?token=..`)。 *注:* ブラウザーからのトークン値は、使用前に URL によってデコードされます。  |

*応答コード:*

コード:200<br>
不透明なトークンを SaaS サブスクリプションに解決します。 応答本文:
 

```json
{
    "id": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

コード:400<br>
無効な要求です。 x-ms-marketplace-token が見つからないか、形式が正しくないか、または期限切れです。

コード:403<br>
権限がありません。 認証トークンが提供されていないか無効である、または要求で現在の発行元に属していない購入にアクセスしようとしています。

コード:404<br>
見つかりません。

コード:500<br>
内部サーバー エラー。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

### <a name="subscription-api"></a>サブスクリプション API

サブスクリプション API は、次の HTTPS 操作をサポートしています。**Get**、**Post**、**Patch**、および **Delete** です。


#### <a name="list-subscriptions"></a>サブスクリプションの一覧

パブリッシャーのすべての SaaS サブスクリプションを一覧表示します。

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>取得<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*クエリ パラメーター:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  この要求で使用する操作のバージョン。  |

*要求ヘッダー:*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-ms-requestid     |  クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
| x-ms-correlationid |  クライアントでの操作に対する一意の文字列値。 このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
| authorization      |  [JSON Web トークン (JWT) ベアラー トークンを取得します](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  |

*応答コード:*

コード:200 <br/>
認証トークンに基づいて、すべての発行元のオファーの発行元と対応するサブスクリプションを取得します。
応答ペイロード:<br>

```json
{
  [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": "true", // true – the customer subscription is currently in free trial, false – the customer subscription is not currently in free trial.
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

継続トークンは、取得するプランに追加の "ページ" がある場合にのみ存在します。 

コード:403 <br>
権限がありません。 認証トークンが提供されていないか無効である、または要求で現在の発行元に属していない購入にアクセスしようとしています。 

コード:500<br>
内部サーバー エラー。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="get-subscription"></a>サブスクリプションを取得する

指定された SaaS サブスクリプションを取得します。 この呼び出しを使用して、ライセンス情報およびプラン情報を取得します。

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>取得<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`

*クエリ パラメーター:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Resolve API を使用してトークンを解決した後に取得される SaaS サブスクリプションの一意の ID。   |
|  ApiVersion        |   この要求で使用する操作のバージョン。   |

*要求ヘッダー:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|  x-ms-correlationid |  クライアントでの操作に対する一意の文字列値。 このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  authorization     |  [JSON Web トークン (JWT) ベアラー トークンを取得します](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。 |

*応答コード:*

コード:200<br>
識別子から SaaS サブスクリプションを取得します。 応答ペイロード:<br>

```json
Response Body:
{ 
        "id":"",
        "name":"Contoso Cloud Solution",
        "publisherId": "contoso",
        "offerId": "offer1",
        "planId": "silver",
        "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "isFreeTrial": "true", // true – customer subscription is currently in free trial, false – customer subscription is not currently in free trial.
        "status": "Subscribed", // Indicates the status of the operation.
          "term": { //This gives the free trial term start and end date
            "startDate": "2019-05-31",
            "endDate": "2019-06-29",
            "termUnit": "P1M"
        },
}
```

コード:403<br>
権限がありません。 認証トークンが提供されていないか無効である、または要求で現在の発行元に属していない購入にアクセスしようとしています。

コード:404<br>
見つかりません。<br> 

コード:500<br>
内部サーバー エラー。<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>利用可能なプランを一覧表示する

この呼び出しを使用して、現在の発行元のプライベートまたはパブリック オファーがあるかどうかを調べます。

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>取得<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*クエリ パラメーター:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   この要求で使用する操作のバージョン。  |

*要求ヘッダー:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|  x-ms-correlationid  | クライアントでの操作に対する一意の文字列値。 このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|  authorization     |  [JSON Web トークン (JWT) ベアラー トークンを取得します](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。 |

*応答コード:*

コード:200<br>
顧客が利用できるプランの一覧を取得します。 応答本文:

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso",
        "isPrivate": true
    }]
}
```

コード:404<br>
見つかりません。<br> 

コード:403<br>
権限がありません。 認証トークンが提供されていないか無効である、または要求で現在の発行元に属していない購入にアクセスしようとしています。 <br> 

コード:500<br>
内部サーバー エラー。<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>サブスクリプションをアクティブ化する

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Post<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*クエリ パラメーター:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  この要求で使用する操作のバージョン。  |
| subscriptionId     | Resolve API を使用してトークンを解決した後に取得される SaaS サブスクリプションの一意の ID。  |

*要求ヘッダー:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  x-ms-correlationid  | クライアントでの操作に対する一意の文字列値。 この文字列によって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  authorization     |  [JSON Web トークン (JWT) ベアラー トークンを取得します](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。 |

*要求ペイロード:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*応答コード:*

コード:200<br>
サブスクリプションをアクティブ化します。<br>

コード:400<br>
無効な要求: 検証エラー。

コード:403<br>
権限がありません。 認証トークンが提供されていないか無効である、または要求で現在の発行元に属していない購入にアクセスしようとしています。

コード:404<br>
見つかりません。

コード:500<br>
内部サーバー エラー。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-plan-on-the-subscription"></a>サブスクリプションのプランを変更する

サブスクリプションのプランを更新します。

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>修正プログラム<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*クエリ パラメーター:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  この要求で使用する操作のバージョン。  |
| subscriptionId     | Resolve API を使用してトークンを解決した後に取得される SaaS サブスクリプションの一意の ID。  |

*要求ヘッダー:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  x-ms-correlationid  |  クライアントでの操作に対する一意の文字列値。 このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。    |
| authorization      |  [JSON Web トークン (JWT) ベアラー トークンを取得します](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  |

*要求ペイロード:*

```json
Request Body:
{
    "planId": "gold"
}
```

*要求ヘッダー:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | 操作状態を取得するためのリソースへのリンク。   |

*応答コード:*

コード:202<br>
プランを変更する要求は受理されました。 パートナーは、成功または失敗を判断するために Operation-Location をポーリングする必要があります。 <br>

コード:400<br>
無効な要求: 検証エラー。

コード:403<br>
権限がありません。 認証トークンが提供されていないか無効である、または要求で現在の発行元に属していない購入にアクセスしようとしています。

コード:404<br>
見つかりません。

コード:500<br>
内部サーバー エラー。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>一度にパッチを適用できるのはプランまたは数量だけであり、両方には適用できません。 **[更新]** によるサブスクリプションの編集は、`allowedCustomerOperations` にありません。

#### <a name="change-the-quantity-on-the-subscription"></a>サブスクリプションの数量を変更する

サブスクリプションの数量を更新します。

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*クエリ パラメーター:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  この要求で使用する操作のバージョン。  |
| subscriptionId     | Resolve API を使用してトークンを解決した後に取得される SaaS サブスクリプションの一意の ID。  |

*要求ヘッダー:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  x-ms-correlationid  |  クライアントでの操作に対する一意の文字列値。 このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。    |
| authorization      |  [JSON Web トークン (JWT) ベアラー トークンを取得します](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  |

*要求ペイロード:*

```json
Request Body:
{
    "quantity": 5
}
```

*要求ヘッダー:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | 操作状態を取得するリソースへのリンク。   |

*応答コード:*

コード:202<br>
数量を変更する要求は受理されました。 パートナーは、成功または失敗を判断するために Operation-Location をポーリングする必要があります。 <br>

コード:400<br>
無効な要求: 検証エラー。


コード:403<br>
権限がありません。 認証トークンが提供されていないか無効である、または要求で現在の発行元に属していない購入にアクセスしようとしています。

コード:404<br>
見つかりません。

コード:500<br>
内部サーバー エラー。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>一度にパッチを適用できるのはプランまたは数量だけであり、両方には適用できません。 **[更新]** によるサブスクリプションの編集は、`allowedCustomerOperations` にありません。

#### <a name="delete-a-subscription"></a>サブスクリプションを削除する

指定したサブスクリプションを登録解除し削除します。

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionid-api-versionapiversion"></a>削除<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`

*クエリ パラメーター:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  この要求で使用する操作のバージョン。  |
| subscriptionId     | Resolve API を使用してトークンを解決した後に取得される SaaS サブスクリプションの一意の ID。  |

*要求ヘッダー:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |   クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。   |
|  x-ms-correlationid  |  クライアントでの操作に対する一意の文字列値。 このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。   |
|  authorization     |  [JSON Web トークン (JWT) ベアラー トークンを取得します](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  |

*応答コード:*

コード:202<br>
SaaS サブスクリプションを登録解除するための呼び出しを開始したパートナー。<br>

コード:400<br>
`allowedCustomerOperations` ではなく **[削除]** でサブスクリプションを削除します。

コード:403<br>
権限がありません。 認証トークンが提供されていないか無効である、または要求で現在の発行元に属していない購入にアクセスしようとしています。

コード:404<br>
見つかりません。

コード:500<br>
内部サーバー エラー。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```


### <a name="operations-api"></a>操作 API

操作 API は、次のパッチおよび取得操作をサポートします。

#### <a name="list-outstanding-operations"></a>未処理の操作を一覧表示する 

現在の発行元の未処理の操作を一覧表示します。 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>取得<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*クエリ パラメーター:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   この要求で使用する操作のバージョン。                |
| subscriptionId     | Resolve API を使用してトークンを解決した後に取得される SaaS サブスクリプションの一意の ID。  |

*要求ヘッダー:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  x-ms-correlationid |  クライアントでの操作に対する一意の文字列値。 このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  authorization     |  [JSON Web トークン (JWT) ベアラー トークンを取得します](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  |

*応答コード:*

コード:200<br> サブスクリプションでの保留中の操作の一覧を取得します。 応答ペイロード:

```json
[{
    "id": "<guid>",  
    "activityId": "<guid>",
    "subscriptionId": "<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",  
    "status": "NotStarted"  
}]
```


コード:400<br>
無効な要求: 検証エラー。

コード:403<br>
権限がありません。 認証トークンが提供されていないか無効である、または要求で現在の発行元に属していない購入にアクセスしようとしています。

コード:404<br>
見つかりません。

コード:500<br>
内部サーバー エラー。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>操作状態を取得する

発行元が、指定したトリガーによる非同期操作 (`subscribe`、`unsubscribe`、`changePlan`、`changeQuantity` など) の状態を追跡できるようにします。

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>取得<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*クエリ パラメーター:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  この要求で使用する操作のバージョン。  |

*要求ヘッダー:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`   |
|  x-ms-requestid    |   クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  x-ms-correlationid |  クライアントでの操作に対する一意の文字列値。 このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  authorization     |  [JSON Web トークン (JWT) ベアラー トークンを取得します](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  |

*応答コード:*<br>

コード:200<br> 指定した保留中のすべての SaaS 操作を取得します。 応答ペイロード:

```json
Response body:
{
    "id  ": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",
    "status": "NotStarted"
}

```

コード:400<br>
無効な要求: 検証エラー。

コード:403<br>
権限がありません。 認証トークンが提供されていないか無効である、または要求で現在の発行元に属していない購入にアクセスしようとしています。
 
コード:404<br>
見つかりません。

コード:500<br> 内部サーバー エラー。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>操作の状態を更新する

操作の状態を更新して、提供された値の使用による成功または失敗を示します。

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>修正プログラム<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*クエリ パラメーター:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  この要求で使用する操作のバージョン。  |
| subscriptionId     | Resolve API を使用してトークンを解決した後に取得される SaaS サブスクリプションの一意の ID。  |
|  operationId       | 完了していない操作。 |

*要求ヘッダー:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     | `application/json`   |
|   x-ms-requestid   |   クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|  x-ms-correlationid |  クライアントでの操作に対する一意の文字列値。 このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|  authorization     |  [JSON Web トークン (JWT) ベアラー トークンを取得します](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  |

*要求ペイロード:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*応答コード:*

コード:200<br> パートナー側での操作の完了を通知する呼び出し。 たとえば、この応答によってシートまたはプランの変更を通知できます。

コード:400<br>
無効な要求: 検証エラー。

コード:403<br>
権限がありません。 認証トークンが提供されていないか無効である、または要求で現在の発行元に属していない購入にアクセスしようとしています。

コード:404<br>
見つかりません。

コード:409<br>
競合しています。 たとえば、より新しいトランザクションが既に履行されています。

コード:500<br> 内部サーバー エラー。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="implementing-a-webhook-on-the-saas-service"></a>SaaS サービスでの Webhook の実装

パブリッシャーは、この SaaS サービスでの変更内容についてユーザーに事前に通知するように、Webhook を実装する必要があります。 SaaS サービスは、Webhook 通知のアクションを実行する前に、検証および承認するために API を呼び出すことが期待されます。

```json
{
  "id": "<this is a GUID operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher’s name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
次のいずれかのアクションが可能です。 
- `unsubscribe` (リソースが削除されているとき)
- `changePlan` (プランの変更操作が完了しているとき)
- `changeQuantity` (数量の変更操作が完了しているとき)
- `suspend` (リソースが中断されているとき)
- `reinstate` (リソースが中断後に再開されているとき)

状態は、次のいずれかになります。 
- **NotStarted** <br>
 - **InProgress** <br>
- **Succeeded** <br>
- **Failed** <br>
- **Conflict** <br>

Webhook 通知では、アクション可能な状態は、**Succeeded** と **Failed** です。 操作のライフ サイクルは、**NotStarted** から、**Succeeded**、**Failed**、**Conflict** などの最終状態までです。 **NotStarted** または **InProgress** を受信した場合は、操作が最終状態に達するまで GET API を使用して状態の要求を続行してから、アクションを実行してください。 

## <a name="mock-apis"></a>モック API

開発 (特にプロトタイプ作成) とプロジェクトのテストを開始するために役立つモック API を使用できます。 

ホスト エンドポイント: `https://marketplaceapi.microsoft.com/api` (認証は不要です)<br/>
API バージョン: `2018-09-15`<br/>
サンプル URI: `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

API エンドポイント パスはモック API と実際の API のどちらでも同じですが、API のバージョンは異なります。 モック バージョン用のバージョンは `2018-09-15` であり、製品バージョン用は `2018-08-31` です。 

この記事の API 呼び出しはどれも、モック ホスト エンドポイントに対して行えます。 通常は、応答としてモック データを戻すことが期待されています。 モック API に対するサブスクリプションの更新メソッドの呼び出しには、常に 500 が返されます。 

## <a name="next-steps"></a>次の手順

開発者は、[Cloud パートナー ポータル REST API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) を使用して、ワークロード、オファー、およびパブリッシャー プロファイルの操作をプログラムで取得することもできます。
