---
title: SaaS Fulfillment API V2 - Azure Marketplace | Microsoft Docs
description: 関連付けられているフルフィルメント V2 API を使用して Azure Marketplace で SaaS オファーを作成する方法について説明します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: pbutlerm
ms.openlocfilehash: 437009079c1bebe3694aaa26f945bd726b3c9fb9
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2019
ms.locfileid: "59010574"
---
# <a name="saas-fulfillment-apis-version-2"></a>SaaS Fulfillment API バージョン 2 

この記事では、独立系ソフトウェア ベンダー (ISV) が Azure Marketplace と SaaS アプリケーションを統合できるようにする API について説明します。 この API により、ISV アプリケーションは、すべての商取引が有効になっているチャネル (直接、パートナー主導 (再販業者)、フィールド主導) に参加できるようになります。  この API は、Azure Marketplace で取引可能な SaaS オファーを一覧表示するための要件になります。


## <a name="managing-the-saas-subscription-lifecycle"></a>SaaS サブスクリプション ライフサイクルの管理

Microsoft SaaS サービスでは、SaaS サブスクリプション購入のライフサイクル全体を管理し、実際のフルフィルメント、プランへの変更、および ISV とのサブスクリプションの削除を促進するメカニズムとして、フルフィルメント API を使用します。 顧客は、Microsoft が保持している SaaS サブスクリプションの状態に基づいて課金されます。 次の図には、状態と、状態間の変更を扱う操作を示します。

![SaaS サブスクリプション ライフサイクルの状態](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>SaaS サブスクリプションの状態

次の表には、SaaS サブスクリプションのプロビジョニング状態が、それぞれの説明やシーケンス図 (該当する場合) を含めて一覧表示されています。 

#### <a name="provisioning"></a>プロビジョニング

顧客は購入を開始すると、ISV は、URL パラメーターを使用して顧客のインタラクティブな Web ページ上でこの情報を認証コードで受け取ります。 プロビジョニングする必要のあるものの詳細について、認証コードを検証および交換できます。  SaaS サービスはプロビジョニングを終了すると、フルフィルメントが完了し、顧客に課金できることを伝えるために、アクティブ化呼び出しを送信します。  次の図は、プロビジョニング シナリオのための API 呼び出しのシーケンスを示します。  

![SaaS サービスのプロビジョニングの API 呼び出し。](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>プロビジョニング済み

この状態は、プロビジョニング済みサービスの安定した状態です。

#### <a name="provisioning-for-update"></a>更新プログラムのプロビジョニング
(Marketplace から) 

この状態は、既存のサービスに対する更新プログラムが保留されていることを表します。 このような更新プログラムは、マーケットプレースまたは SaaS サービスでのみ顧客が開始できます (直接顧客トランザクションにのみ)。次の図には、マーケットプレースから更新プログラムが開始されたときのアクションが示されます。

![更新プログラムがマーケットプレースから開始されたときの API 呼び出し。](./media/saas-update-api-v2-calls-from-marketplace-a.png)

#### <a name="provisioning-for-update"></a>更新プログラムのプロビジョニング  
(SaaS サービスから) 

次の図には、SaaS サービスによって更新プログラムが開始されたときのアクションが示されます。 (Webhook の呼び出しは、SaaS サービスによって開始されたサブスクリプションへの更新プログラムによって置き換えられます。 

![更新プログラムが SaaS サービスによって開始されたときの API 呼び出し。](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Suspended

この状態は、顧客の支払いが受け取られていないことを示します。 ポリシーにより、サブスクリプションの履行解除までの猶予期間を顧客に与えます。 サブスクリプションがこの状態の場合: 

- ISV として、サービスへのユーザーのアクセスを低下させるか、防止するかを選択できます。 
- サブスクリプションは、データや設定を失わずにすべての機能を復元できる回復可能な状態に保持する必要があります。 
- フルフィルメント API を介してこのサブスクリプションの再開要求を取得したり、猶予期間の最後にプロビジョニング解除要求を取得することが予期できます。 

#### <a name="unsubscribed"></a>未購読 

明示的な顧客の要求に応じて、または料金の未払いに対応して、サブスクリプションがこの状態に到達します。 ISV からの期待は、顧客のデータが、最低 X 日間、要求時の回復のために保持された後、削除されるというものです。 


## <a name="api-reference"></a>API リファレンス

このセクションでは、SaaS *サブスクリプション API* および *Operations API* について説明します。  バージョン 2 API の `api-version` パラメーターの値は `2018-08-31` です。  


### <a name="parameter-and-entity-definitions"></a>パラメーターとエンティティの定義

次の表は、Fulfillment API で使用される一般的なパラメーターとエンティティの定義の一覧です。

|     エンティティ/パラメーター     |     定義                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | SaaS リソースの GUID 識別子  |
| `name`                   | お客様がこのリソースに付けたフレンドリ名 |
| `publisherId`            | 発行元ごとに自動的に生成される一意の文字列識別子 (たとえば "conotosocorporation") |
| `offerId`                | オファーごとに自動的に生成される一意の文字列識別子 (たとえば "contosooffer1")  |
| `planId`                 | プラン/sku ごとに自動的に生成される一意の文字列識別子 (たとえば "contosobasicplan") |
| `operationId`            | 特定の操作の GUID 識別子  |
|  `action`                | ソースに対して実行されているアクション。`subscribe`、`unsubscribe`、`suspend`、`reinstate`、または `changePlan`  |
|   |   |

グローバルに一意な識別子 ([GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)) は、通常、自動的に生成される 128 ビット (32 桁の 16 進数) の数値です。 


### <a name="subscription-api"></a>サブスクリプション API

サブスクリプション API は、次の HTTPS 操作をサポートしています。**Get**、**Post**、**Patch**、および **Delete** です。


#### <a name="list-subscriptions"></a>サブスクリプションの一覧

パブリッシャーのすべての SaaS サブスクリプションを一覧表示します。

**Get: <br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`**

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
| authorization      |  JSON Web トークン (JWT) ベアラー トークン。  |

*応答コード:*

コード:200<br>
authN トークンに基づいて、すべてのパブリッシャーのオファーに対するパブリッシャーおよび対応するサブスクリプションを取得します。<br> 応答ペイロード:<br>

```json
{
  [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "cont-cld-tier2",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. [Provisioning, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

継続トークンは、取得するプランの追加の "ページ" がある場合にのみ存在します。 


コード:403 <br>
権限がありません。 認証トークンが提供されていないか、無効であるか、または現在のユーザーに属していない購入に要求がアクセスしようとしています。 

コード:500 内部サーバー エラー

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

**Get: <br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`**

*クエリ パラメーター:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Resolve API を使用してトークンを解決した後に取得される SaaS サブスクリプションの一意の ID   |
|  ApiVersion        |   この要求で使用する操作のバージョン   |

*要求ヘッダー:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|  x-ms-correlationid |  クライアントでの操作に対する一意の文字列値。 このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  authorization     |  JSON Web トークン (JWT) ベアラー トークン  |

*応答コード:*

コード:200<br>
識別子から SaaS サブスクリプションを取得します<br> 応答ペイロード:<br>

```json
Response Body:
{ 
        "id":"",
        "name":"Contoso Cloud Solution",
        "publisherId": "contoso",
        "offerId": "cont-cld-tier2",
        "planId": "silver",
        "quantity": "10"",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "status": "Subscribed", // Indicates the status of the operation.
}
```

コード:404<br>
見つかりません<br> 

コード:403<br>
権限がありません。 認証トークンが提供されていないか、無効であるか、または現在のユーザーに属していない購入に要求がアクセスしようとしています。

コード:500<br>
内部サーバー エラー<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>利用可能なプランを一覧表示する

この呼び出しを使用して、現在のユーザーのすると、現在のユーザーのプライベート/パブリック オファーがあるかどうかを調べます。

**Get: <br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`**

*クエリ パラメーター:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   この要求で使用する操作のバージョン  |

*要求ヘッダー:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|  x-ms-correlationid  | クライアントでの操作に対する一意の文字列値。 このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|  authorization     |  JSON Web トークン (JWT) ベアラー トークン |

*応答コード:*

コード:200<br>
顧客の利用可能なプランの一覧を取得します。<br>

応答本文:

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
見つかりません<br> 

コード:403<br>
権限がありません。 認証トークンが提供されていないか、無効であるか、または現在のユーザーに属していない購入に要求がアクセスしようとしています。 <br> 

コード:500<br>
内部サーバー エラー<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="resolve-a-subscription"></a>サブスクリプションを解除する 

解決エンドポイントにより、ユーザーはマーケットプレース トークンを永続的なリソース ID に解決できます。 リソース ID は、SAAS サブスクリプションの一意の識別子です。  ユーザーが ISV の Web サイトにリダイレクトされると、URL に、クエリ パラメーターのトークンが含まれます。 ISV は、このトークンを使用し、それを解決するための要求を行うことが期待されます。 応答には、リソースの一意の SAAS サブスクリプション ID、名前、オファーID、およびプランが含まれます。 このトークンは、1 時間だけ有効です。 

**Post: <br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`**

*クエリ パラメーター:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  この要求で使用する操作のバージョン  |

*要求ヘッダー:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|  x-ms-correlationid |  クライアントでの操作に対する一意の文字列値。 このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  authorization     |  JSON Web トークン (JWT) ベアラー トークン  |
|  x-ms-marketplace-token  |  ユーザーが Azure から SaaS ISV の Web サイトにリダイレクトされるときの、URL のトークン クエリ パラメーター。 *注:* ブラウザーからのトークン値は、使用前に URL によってデコードされます。 |

*応答コード:*

コード:200<br>
不透明なトークンを SaaS サブスクリプションに解決します。<br>

```json
Response body:
{
    "subscriptionId": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "cont-cld-tier2",
    "planId": "silver",
    "quantity": "20",
    "operationId": "<guid>"  
}
```

コード:404<br>
見つかりません

コード:400<br>
無効な要求 - 検証エラー

コード:403<br>
権限がありません。 認証トークンが提供されていないか、無効であるか、または現在のユーザーに属していない購入に要求がアクセスしようとしています。

コード:500<br>
内部サーバー エラー

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="activate-a-subscription"></a>サブスクリプションをアクティブ化する

**Post: <br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`**

*クエリ パラメーター:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  この要求で使用する操作のバージョン  |
| subscriptionId     | Resolve API を使用してトークンを解決した後に取得される SaaS サブスクリプションの一意の ID  |

*要求ヘッダー:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  x-ms-correlationid  | クライアントでの操作に対する一意の文字列値。 この文字列によって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  authorization     |  JSON Web トークン (JWT) ベアラー トークン |

*要求:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*応答コード:*

コード:202<br>
サブスクリプションをアクティブ化します。<br>

コード:404<br>
見つかりません

コード:400<br>
無効な要求 - 検証エラー

コード:403<br>
権限がありません。 認証トークンが提供されていないか、無効であるか、または現在のユーザーに属していない購入に要求がアクセスしようとしています。

コード:500<br>
内部サーバー エラー

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="update-a-subscription"></a>サブスクリプションを更新する

指定された値でサブスクリプション プランを更新または変更します。

**Patch:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`**

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
| authorization      |  JSON Web トークン (JWT) ベアラー トークン。  |

*要求ペイロード:*

```json
Request Body:
{
    "planId": "gold",
    "quantity": ""
}
```

*要求ヘッダー:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | 操作状態を取得するリソースへのリンク。   |

*応答コード:*

コード:202<br>
ISV は、プランの変更、または数量の変更を開始します。 <br>

コード:404<br>
見つかりません

コード:400<br>
無効な要求 - 検証エラー。

>[!Note]
>一度にパッチを適用できるのはプランまたは数量だけであり、両方には適用できません。 **[更新]** によるサブスクリプションの編集は、`allowedCustomerOperations` にありません。

コード:403<br>
権限がありません。 認証トークンが提供されていないか、無効であるか、または現在のユーザーに属していない購入に要求がアクセスしようとしています。

コード:500<br>
内部サーバー エラー

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="delete-a-subscription"></a>サブスクリプションを削除する

指定したサブスクリプションを登録解除し削除します。

**削除:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`**

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
|  authorization     |  JSON Web トークン (JWT) ベアラー トークン。   |

*応答コード:*

コード:200<br>
ISV は、SaaS サブスクリプションでの登録解除を示す呼び出しを開始しました。<br>

コード:404<br>
見つかりません

コード:400<br>
`allowedCustomerOperations` ではなく **[削除]** でサブスクリプションを削除します。

コード:403<br>
権限がありません。 認証トークンが提供されていないか、無効であるか、または現在のユーザーに属していない購入に要求がアクセスしようとしています。

コード:500<br>
内部サーバー エラー

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


#### <a name="update-a-subscription"></a>サブスクリプションを更新する

指定された値でサブスクリプションを更新します。

**Patch:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

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
|  authorization     |  JSON Web トークン (JWT) ベアラー トークン。  |

*要求ペイロード:*

```json
{
    "planId": "cont-cld-tier2",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}
```

*応答コード:*

コード:200<br> ISV 側での操作の完了を通知する呼び出し。 たとえば、この応答で、シート/プランの変更を通知することもできます。

コード:404<br>
見つかりません

コード:400<br>
無効な要求 - 検証エラー

コード:403<br>
権限がありません。 認証トークンが提供されていないか、無効であるか、または現在のユーザーに属していない購入に要求がアクセスしようとしています。

コード:409<br>
競合しています。 たとえば、より新しいトランザクションが既に履行されています

コード:500<br> 内部サーバー エラー

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="list-outstanding-operations"></a>未処理の操作を一覧表示する 

現在のユーザーの未処理の操作を一覧表示します。 

**Get: <br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`**

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
|  authorization     |  JSON Web トークン (JWT) ベアラー トークン。  |

*応答コード:*

コード:200<br> サブスクリプションでの保留中の操作の一覧を取得します。<br>
応答ペイロード:

```json
[{
    "id": "<guid>",  
    "activityId": "<guid>",
    "subscriptionId": "<guid>",
    "offerId": "cont-cld-tier2",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",  
    "status": "NotStarted"  
}]
```

コード:404<br>
見つかりません

コード:400<br>
無効な要求 - 検証エラー

コード:403<br>
権限がありません。 認証トークンが提供されていないか、無効であるか、または現在のユーザーに属していない購入に要求がアクセスしようとしています。

コード:500<br>
内部サーバー エラー

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>操作状態を取得する

ユーザーが、指定したトリガーによる非同期操作 (プランのサブスクライブ、サブスクライブ解除、変更) の状態を追跡できるようにします。

**Get: <br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

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
|  authorization     | JSON Web トークン (JWT) ベアラー トークン。  |

*応答コード:* コード:200<br> 指定した保留中のすべての SaaS 操作を取得します<br>
応答ペイロード:

```json
Response body:
{
    "id  ": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "cont-cld-tier2",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",
    "status": "NotStarted"
}

```

コード:404<br>
見つかりません

コード:400<br>
無効な要求 - 検証エラー

コード:403<br>
権限がありません。 認証トークンが提供されていないか、無効であるか、または現在のユーザーに属していない購入に要求がアクセスしようとしています。
 
コード:500<br> 内部サーバー エラー

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="webhook-on-the-saas-service"></a>SaaS サービスでの Webhook

パブリッシャーは、この SaaS サービスでの変更内容についてユーザーに事前に通知するように、Webhook を実装する必要があります。 API は未認証であることが期待され、Microsoft SaaS サービスによって呼び出されます。 SaaS サービスは、Webhook 通知のアクションを実行する前に、検証および承認するために API を呼び出すことが期待されます。

```json
{
    "operationId": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "cont-cld-tier2",
    "publisherId": "contoso",
    "planId": "silver",
    "quantity": "20"  ,
    "action": "Activate",   // Activate/Delete/Suspend/Reinstate/Change[new]  
    "timeStamp": "2018-12-01T00:00:00"
}

```


## <a name="mock-api"></a>モック API

開発、特にプロジェクトのプロトタイプ作成およびテストの開始に役立てるためにモック API を使用できます。 

ホスト エンドポイント:`https://marketplaceapi.microsoft.com/api` API バージョン:`2018-09-15` 認証を必要としないサンプル URI: `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15`

この記事の API 呼び出しはどれも、モック ホスト エンドポイントに対して行えます。 応答としてモック データを取り戻すことができます。


## <a name="next-steps"></a>次の手順

開発者は、[Cloud パートナー ポータル REST API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) を使用して、ワークロード、オファー、およびパブリッシャー プロファイルの操作をプログラムで取得することもできます。
