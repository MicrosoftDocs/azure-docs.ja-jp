---
title: Microsoft コマーシャル マーケットプレースの SaaS Fulfillment API v2
description: Fulfillment API バージョン 2 を使用し、Microsoft AppSource と Azure Marketplace で SaaS オファーを作成し、管理する方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: f40da30ff0d702078861367dea810cc8ca1ab91b
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87305144"
---
# <a name="saas-fulfillment-apis-version-2-in-microsoft-commercial-marketplace"></a>Microsoft コマーシャル マーケットプレースの SaaS Fulfillment API バージョン 2

この記事では、パートナーがその SaaS オファーを Microsoft AppSource と Azure Marketplace で販売できるようにする API について説明します。 発行者が取引可能な SaaS オファーをパートナー センターで公開するには、これらの API との統合を実装する必要があります。

## <a name="managing-the-saas-subscription-life-cycle"></a>SaaS サブスクリプション ライフサイクルの管理

Azure Marketplace では、エンド カスタマーによる購入後の SaaS サブスクリプションのライフサイクル全体が管理されます。  また、実際の SaaS サブスクリプションのアクティブ化と使用、更新、サブスクリプションの取り消しを駆動するメカニズムとして、ランディング ページ、Fulfillment API、Operations API、Webhook が使用されています。  エンド カスタマーは、Microsoft が保持している SaaS サブスクリプションの状態に基づいて請求されます。 

### <a name="states-of-a-saas-subscription"></a>SaaS サブスクリプションの状態

SaaS サブスクリプションの状態と適用可能なアクションを示します。

![Marketplace での SaaS サブスクリプションのライフサイクル](./media/saas-subscription-lifecycle-api-v2.png)

#### <a name="purchased-but-not-yet-activated-pendingfulfillmentstart"></a>購入済みであるが、まだアクティブ化されていない (*PendingFulfillmentStart*)

エンド カスタマー (または CSP) がマーケットプレースで SaaS オファーを購入した後、発行者に購入が通知されます。これにより、発行者側でエンド カスタマー用に新しい SaaS アカウントが作成され、構成されます。

アカウントの作成が行われるようにするには:

1. 顧客は Microsoft AppSource または Azure portal での購入が正常に終了した後に、SaaS オファーで使用可能な **[構成]** ボタンをクリックする必要があります。 または、顧客が購入後すぐに受け取る電子メールでそうします。
2. その後、購入について Microsoft からパートナーへの通知が行われます。このために、token パラメーター (マーケットプレースでの購入 ID トークン) を含むランディング ページの URL が新しいブラウザー タブで開かれます。

このような呼び出しの例として `https://contoso.com/signup?token=<blob>` がありますが、パートナー センターでは、この SaaS オファーのランディング ページの URL は `https://contoso.com/signup` として構成されています。 このトークンにより、SaaS の購入と顧客を一意に識別する ID が発行者に提供されます。

>[!NOTE]
>発行者は、顧客が Microsoft 側からの構成プロセスを開始するまで、SaaS の購入に関する通知を受け取りません。

ランディング ページの URL は、24 時間 365 日稼働し、いつでも Microsoft からの新しい呼び出しを受け取る準備ができている必要があります。 ランディング ページが使用できなくなった場合、お客様は SaaS サービスにサインアップして使用を開始することができません。

次に、`x-ms-marketplace-token header` ヘッダー パラメーターの値として [SaaS Resolve API](#resolve-a-purchased-subscription) を呼び出すことによって、発行者から Microsoft に "*トークン*" を返す必要があります。  Resolve API 呼び出しの結果として、購入の一意の ID、購入されたオファーの ID、購入されたプランの ID などの SaaS 購入の詳細を表すトークンが交換されます。

ランディング ページでは、顧客は Azure Active Directory (AAD) シングル サインオン (SSO) を使用して新規または既存の SaaS アカウントにログオンする必要があります。

発行者は、Microsoft によってこのフローに要求されるユーザー エクスペリエンスを提供するために、SSO ログインを実装する必要があります。  SSO を構成するときに、マルチテナント Azure AD アプリケーションを使用し、職場と学校のアカウントの両方または個人の Microsoft アカウントを許可するようにしてください。  この要件は、ランディング ページと、Microsoft 資格情報を使用して既にログインしているときに SaaS サービスにリダイレクトされるユーザーにのみ適用されます。 SaaS サービスへのすべてのログインに適用されるわけではありません。

> [!NOTE]
>SSO ログインで、管理者がアプリに対するアクセス許可を付与する必要がある場合、パートナー センターでのオファーの説明で、管理者レベルのアクセス許可が必要であることを明記する必要があります。 これは、[マーケットプレースの認定ポリシー](https://docs.microsoft.com/legal/marketplace/certification-policies#10003-authentication-options)に準拠するためです。

ログインした後、顧客は発行者側で SaaS 構成を完了する必要があります。 その後、発行者は、SaaS アカウントのプロビジョニングが完了したことを示す通知を Marketplace に送信するために、[Activate Subscription API](#activate-a-subscription) を呼び出す必要があります。
これにより、顧客の請求サイクルが開始されます。 Activate Subscription API 呼び出しが失敗した場合、顧客は購入に対して請求されません。


![プロビジョニングのための API 呼び出しのシナリオ](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="active-subscribed"></a>アクティブ (サブスクライブ済み)

この状態は、プロビジョニング済み SaaS サブスクリプションを表す安定した状態です。 [Activate Subscription API](#activate-a-subscription) 呼び出しが Microsoft 側で処理されると、SaaS サブスクリプションがサブスクライブ済みとしてマークされます。 これで、発行者側で顧客が SaaS サービスを使用できるようになり、顧客に請求が行われます。

SaaS サブスクリプションが既にアクティブになっていて、顧客が Azure portal または M365 管理センターから SaaS の**管理**エクスペリエンスを開始することを選択した場合、アクティブ化フローの場合と同じように、**ランディング ページの URL** が *token* パラメーターを使用して Microsoft によって呼び出されます。  発行者は、新規購入と既存の SaaS アカウントの管理を区別し、このランディング ページの URL 呼び出しを適宜処理する必要があります。

#### <a name="being-updated-subscribed"></a>更新中 (サブスクライブ済み)

このアクションは、既存のアクティブな SaaS サブスクリプションに対する更新が、Microsoft と発行者の両方によって処理されている最中であることを意味します。 このような更新は以下のように開始できます。

* 顧客によってマーケットプレースから
* CSP によってマーケットプレースから
* 顧客によって発行者の SaaS サイトから (CSP が行った購入には適用されません)

SaaS サブスクリプションに対して 2 種類の更新ができます。

1. 顧客がサブスクリプションに対して別のプランを選択した場合に、プランを更新します。
1. 顧客がサブスクリプションの購入済みシート数を変更したときに、数量を更新します。

アクティブなサブスクリプションのみを更新できます。 サブスクリプションが更新されている間、その状態は Microsoft 側でアクティブなままになります。

##### <a name="update-initiated-from-the-marketplace"></a>マーケットプレースから開始された更新

このフローでは、顧客は M365 管理センターからサブスクリプション プランまたはシート数を変更します。  

1. 更新が入力されると、Microsoft によって、パートナー センターの **[接続 Webhook]** フィールドに構成されている発行者の Webhook URL が、*action* およびその他の関連するパラメーターの適切な値を使用して呼び出されます。  
1. 発行者側は SaaS サービスに対して必要な変更を行い、[Update Status of Operation API](#update-the-status-of-an-operation) を呼び出すことによって、変更が完了したことを Microsoft に通知します。
1. 失敗の状態を持つ Patch が送信された場合、更新プロセスは Microsoft 側で完了しません。  SaaS サブスクリプションは、既存のプランとシート数のままになります。

Marketplace で開始された更新シナリオの API 呼び出しのシーケンスを次に示します。

![マーケットプレースで開始された更新の API 呼び出し](./media/saas-update-status-api-v2-calls-marketplace-side.png)

##### <a name="update-initiated-from-the-publisher"></a>発行者から開始された更新

このフローでは、顧客が SaaS サービス自体から購入したサブスクリプション プランまたはシート数を変更します。 

1. 要求された変更を発行者側で行う前に、発行者のコードで [Change Plan API](#change-the-plan-on-the-subscription) または [Change Quantity API](#change-the-quantity-of-seats-on-the-saas-subscription) あるいはその両方を呼び出す必要があります。 

1. Microsoft はサブスクリプションに変更を適用し、**接続 Webhook** を使用して、同じ変更を適用するように発行者に対して通知します。  

1. 発行者はその後に限り SaaS サブスクリプションに対して必要な変更を行い、変更が完了したら [Update Status of Operation API](#update-the-status-of-an-operation) を呼び出して Microsoft に通知します。

発行者側で開始された更新シナリオの API 呼び出しのシーケンスです。

![発行者側で開始された更新の API 呼び出し](./media/saas-update-status-api-v2-calls-publisher-side.png)

#### <a name="suspended-suspended"></a>中断 (*中断*)

この状態は、SaaS サービスに対する顧客の支払いが受け取られていないことを示します。 発行者には、SaaS サブスクリプションの状態の変更が Microsoft によって通知されます。 この通知は、*action* パラメーターを *Suspended* に設定した Webhook の呼び出しによって行われます。

発行者は、発行者側での SaaS サービスへの変更を行うことも、行わないこともできます。 発行者が中断された顧客に対してこの情報を使用可能にすること、また、顧客の SaaS サービスへのアクセスを制限またはブロックすることをお勧めします。  支払いが一切受け取られない可能性があります。

Microsoft は、サブスクリプションを自動的に取り消す前に、30 日間の猶予期間を顧客に提供します。 サブスクリプションが中断状態にあるとき:

* SaaS アカウントは ISV によって、回復可能な状態で保持されている必要があります。 データや設定を失わずに、すべての機能を復元できます。
* 猶予期間中に支払いが発生した場合は、このサブスクリプションの復帰要求が届き、そうでない場合は、猶予期間の終わりに、このサブスクリプションのプロビジョニング解除要求が届くことを想定しておいてください。両方とも Webhook メカニズムを介して届きます。

発行者が何らかのアクションを実行する前に、サブスクリプションの状態が Microsoft 側で "中断" に変更されます。 アクティブなサブスクリプションのみを中断できます。

#### <a name="reinstated-suspended"></a>復帰 (*中断*)

サブスクリプションを復帰させています。

このアクションは、顧客の支払い方法が再度有効になったこと、SaaS サブスクリプションに対して支払いが行われることを示します。  サブスクリプションを復帰させています。 この場合、次のようになります。 

1. Microsoft によって、*action* パラメーターを *Reinstate* の値に設定した Webhook が呼び出されます。  
1. 発行者は、このサブスクリプションを発行者側で再び完全に運用可能にします。
1. 発行者は、成功の状態を持つ [Patch Operation API](#update-the-status-of-an-operation) を呼び出します。  
1. その後、復帰が成功し、顧客に SaaS サブスクリプションの請求が再び行われます。 
1. 失敗の状態を持つ Patch が送信された場合、復帰プロセスは Microsoft 側で完了しません。 サブスクリプションは中断されたままになります。

失敗の状態を持つ Patch が送信された場合、復帰プロセスは Microsoft 側で完了しません。  サブスクリプションは中断されたままになります。

中断されたサブスクリプションのみを復帰させることができます。  SaaS サブスクリプションを復帰させている間、その状態は中断のままになります。  この操作が完了すると、サブスクリプションの状態はアクティブになります。

#### <a name="renewed-subscribed"></a>更新済み (*サブスクライブ済み*)

サブスクリプション期間が (1 か月後または 1 年後に) 終了すると、SaaS サブスクリプションが Microsoft によって自動的に更新されます。  自動更新設定の既定値は、すべての SaaS サブスクリプションで *true* です。 アクティブな SaaS サブスクリプションは、継続して定期的に更新されます。 サブスクリプションが更新されるとき、Microsoft から発行者への通知は行われません。 顧客は、M365 管理ポータルまたは Azure portal 経由で、SaaS サブスクリプションの自動更新を無効にすることができます。  この場合、SaaS サブスクリプションは、現在の請求期間の終了時に自動的に取り消されます。  また、顧客はいつでも SaaS サブスクリプションを取り消すことができます。

アクティブなサブスクリプションのみが自動的に更新されます。  サブスクリプションは、更新プロセス中および自動更新が成功した場合はアクティブに維持されます。  更新後、サブスクリプション期間の開始日と終了日が新しい期間の日付に更新されます。

支払いの問題によって自動更新が失敗した場合、サブスクリプションは中断されます。  発行者に通知が行われます。

#### <a name="canceled-unsubscribed"></a>取り消し (*登録解除済み*) 

サブスクリプションは、発行者サイト、Azure portal、または M365 管理センターからサブスクリプションを取り消すことによる、明示的な顧客または CSP のアクションに応じて、この状態になります。  サブスクリプションは、料金未払いを原因に 30 日間中断状態になった後、暗黙的に取り消すこともできます。

取り消しの Webhook 呼び出しを受け取った場合、発行者は、要求に応じて復旧できるように顧客データを少なくとも 7 日間保持する必要があります。 その後に限り、顧客データを削除できます。

SaaS サブスクリプションは、ライフサイクルの任意の時点で取り消すことができます。 サブスクリプションを取り消すと、再アクティブ化することはできません。

## <a name="api-reference"></a>API リファレンス

このセクションでは、SaaS のサブスクリプションおよび Operations API について説明します。

**Subscription API** は、購入から取り消しまでの SaaS サブスクリプションのライフサイクルを処理するために使用する必要があります。

**Operations API** は、次のために使用する必要があります。

* 処理された Webhook 呼び出しを検証し、確認する
* 発行者による確認を待機している保留中の操作があるアプリの一覧を取得する

### <a name="enforcing-tls-12-note"></a>TLS 1.2 Note を適用する

TLS バージョン 1.2 というバージョンが、HTTPS 通信の最小バージョンとしてまもなく適用されます。 コードでこの TLS バージョンを使用していることを確認してください。  TLS バージョン 1.0 および 1.1 はまもなく非推奨となります。

### <a name="subscription-apis"></a>Subscription API

#### <a name="resolve-a-purchased-subscription"></a>購入済みサブスクリプションを解決する

解決エンドポイントを使用すると、発行者は、マーケットプレースの購入識別トークン (「[購入済みであるが、まだアクティブ化されていない](#purchased-but-not-yet-activated-pendingfulfillmentstart)」で "*トークン*" として参照されている) を、永続的な購入済み SaaS サブスクリプション ID とその詳細に交換できます。

顧客がパートナーのランディング ページの URL にリダイレクトされると、この URL 呼び出しで、顧客識別トークンが *token* パラメーターとして渡されます。 パートナーは、このトークンを使用すること、およびそれを解決するための要求を行うことを期待されています。 Resolve API 応答には、購入を一意に識別するための SaaS サブスクリプション ID とその他の詳細が含まれています。 ランディング ページの URL 呼び出しで提供される "*トークン*" は、通常 24 時間有効です。 受け取った "*トークン*" が既に期限切れになっている場合は、エンド カスタマーに次のガイダンスを提供することをお勧めします。

"この購入を特定できませんでした。Azure portal または M365 管理センターでこの SaaS サブスクリプションを再度開いて、[アカウントの構成] または [アカウントの管理] ボタンをもう一度クリックしてください。"

Resolve API を呼び出すと、サポートされているすべての状態の SaaS サブスクリプションの詳細と状態が返されます。

##### <a name="posthttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Post `https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値            |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 を使用します。   |

*要求ヘッダー:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json` |
|  `x-ms-requestid`    |  クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|  `x-ms-correlationid` |  クライアントでの操作に対する一意の文字列値。 このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  `authorization`     |  この API 呼び出しを行っている発行者を識別する一意のアクセス トークン。 「[Azure AD アプリに基づいたトークンの取得](./pc-saas-registration.md#get-the-token-with-an-http-post)」の説明に従って、発行者によってトークン値が取得される場合、形式は `"Bearer <accessaccess_token>"` です。 |
|  `x-ms-marketplace-token`  | 解決する対象である、マーケットプレースでの購入を識別する *token* パラメーター。  このトークンは、顧客が SaaS パートナーの Web サイトにリダイレクトされるときに、ランディング ページの URL 呼び出しで渡されます (例: `https://contoso.com/signup?token=<token><authorization_token>`)。 <br> <br>  *注:* エンコードされている *token* 値は、ランディング ページの URL の一部であるため、この API 呼び出しでパラメーターとして使用する前にデコードする必要があります。  <br> <br> URL 内のエンコードされた文字列の例は、`contoso.com/signup?token=ab%2Bcd%2Fef` のようになり、トークンは `ab%2Bcd%2Fef` です。  同じトークンをデコードすると、このようになります: `Ab+cd/ef` |
| | |

*応答コード:*

コード:200 - 指定された `x-ms-marketplace-token` に基づいて一意の SaaS サブスクリプション識別子を返します。

応答本文の例:

```json
{
    "id": "<guid>",  // purchased SaaS subscription ID 
    "subscriptionName": "Contoso Cloud Solution", // SaaS subscription name 
    "offerId": "offer1", // purchased offer ID
    "planId": "silver", // purchased offer's plan ID
    "quantity": "20", // number of purchased seats, might be empty if the plan is not per seat
    "subscription": { // full SaaS subscription details, see Get Subscription APIs response body for full description
    "id": "<guid>",
    "publisherId": "contoso",
    "offerId": "offer1",
    "name": "Contoso Cloud Solution",
    "saasSubscriptionStatus": " PendingFulfillmentStart ",
    "beneficiary": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
"pid": "<ID of the user>"
    },
    "purchaser": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
"pid": "<ID of the user>"
    },
    "planId": "silver",
    "term": {
      "termUnit": "P1M",
                   startDate": "2019-05-31", 
   "endDate": "2019-06-29",
    },
    "isTest": true,
    "isFreeTrial": false,
    "allowedCustomerOperations": [
      "Delete",
      "Update",
      "Read"
    ],
    "sandboxType": "None",
    "sessionMode": "None"
  }
}

```

コード:400 - 無効な要求です。 `x-ms-marketplace-token` が見つからないか、形式が正しくありません。または、有効期限が切れています。

コード:403 - 許可されていません。 認証トークンが無効であるか、期限切れか、指定されていません。  要求が、認証トークンの作成に使用されたものとは異なる Azure AD アプリ ID で発行されたオファーの SaaS サブスクリプションにアクセスしようとしています。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。

コード:500 - 内部サーバー エラーです。  API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://partner.microsoft.com/support/v2/?stage=1)にお問い合わせください。

#### <a name="activate-a-subscription"></a>サブスクリプションをアクティブ化する

SaaS アカウントがエンド カスタマー向けに構成されたら、発行者は Microsoft 側での Activate Subscription API を呼び出す必要があります。  この API 呼び出しが成功しない限り、顧客に対する請求は行われません。

##### <a name="posthttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Post`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値             |
|  --------   |  ---------------  |
| `ApiVersion`  |  2018-08-31 を使用します。   |
| `subscriptionId` | 購入済み SaaS サブスクリプションの一意識別子。  この ID は、[Resolve API](#resolve-a-purchased-subscription) を使用してマーケットプレース承認トークンを解決した後に取得されます。
 |

*要求ヘッダー:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
| `x-ms-correlationid` |  クライアントでの操作に対する一意の文字列値。  この文字列によって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
| `authorization`      |  この API 呼び出しを行っている発行者を識別する一意のアクセス トークン。 「[Azure AD アプリに基づいたトークンの取得](./pc-saas-registration.md#get-the-token-with-an-http-post)」の説明に従って、発行者によってトークン値が取得される場合、形式は `"Bearer <access_token>"` です。 |

*要求ペイロードの例:*

```json
{ // needed for validation of the activation request
    "planId": "gold", // purchased plan, cannot be empty
    "quantity": "" // purchased number of seats, can be empty if plan is not per seat
}
```

*応答コード:*

コード:200 - サブスクリプションは、Microsoft 側でサブスクライブ済みとしてマークされました。

この呼び出しには応答本文がありません。

コード:400 - 無効な要求: 検証に失敗しました。

* `planId` が要求ペイロードに存在しません。
* 要求ペイロード内の `planId` が購入されたものと一致しません。
* 要求ペイロード内の `quantity` が、購入されたものと一致しません
* SaaS サブスクリプションは、サブスクライブ済みまたは中断状態です。

コード:403 - 許可されていません。 認証トークンが無効であるか、期限切れか、指定されていません。 要求が、認証トークンの作成に使用されたものとは異なる Azure AD アプリ ID で発行されたオファーの SaaS サブスクリプションにアクセスしようとしています。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。

コード:404 見つかりません。 SaaS サブスクリプションは登録解除済みの状態です。

コード:500 - 内部サーバー エラーです。  API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://partner.microsoft.com/support/v2/?stage=1)にお問い合わせください。

#### <a name="get-list-of-all-subscriptions"></a>すべてのサブスクリプションの一覧を取得する

マーケットプレースで発行者によって発行されたすべてのオファーについて、すべての購入済み SaaS サブスクリプションの一覧を取得します。  可能なすべての状態の SaaS サブスクリプションが返されます。 登録解除された SaaS サブスクリプションも返されます (この情報は Microsoft 側では削除されないため)。

この API では、ページ分割された結果が返されます。 ページ サイズは 100 です。

##### <a name="gethttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Get`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値             |
|  --------   |  ---------------  |
| `ApiVersion`  |  2018-08-31 を使用します。  |
| `continuationToken`  | 省略可能なパラメーター。 結果の最初のページを取得するには、空のままにします。  次のページを取得するには、`@nextLink` パラメーターで返された値を使用します。 |

*要求ヘッダー:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
| `x-ms-correlationid` |  クライアントでの操作に対する一意の文字列値。  このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
| `authorization`      |  この API 呼び出しを行っている発行者を識別する一意のアクセス トークン。  「[Azure AD アプリに基づいたトークンの取得](./pc-saas-registration.md#get-the-token-with-an-http-post)」の説明に従って、発行者によってトークン値が取得される場合、形式は `"Bearer <access_token>"` です。 |

*応答コード:*

コード:200 - この発行者のすべてのオファーについて、発行者の承認トークンに基づいて既存のすべてのサブスクリプションの一覧を返します。

*応答本文の例:*

```json
{
  "subscriptions": [
      {
          "id": "<guid>", // purchased SaaS subscription ID
          "name": "Contoso Cloud Solution", // SaaS subscription name
          "publisherId": "contoso", // publisher ID
          "offerId": "offer1", // purchased offer ID
          "planId": "silver", // purchased plan ID
          "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
          "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription was purchased.
              "emailId": " test@contoso.com",
              "objectId": "<guid>",
              "tenantId": "<guid>",
              "pid": "<ID of the user>"
          },
          "purchaser": { // email address, user ID and tenant ID that purchased the SaaS subscription. These could be different from beneficiary information for reseller (CSP) purchase
              "emailId": " test@contoso.com",
              "objectId": "<guid>",
              "tenantId": "<guid>",
              "pid": "<ID of the user>"
          },
            "term": { // The period for which the subscription was purchased. 
                "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
                "endDate": "2019-06-30", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
                "termUnit": "P1M" // where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values
          },
          "allowedCustomerOperations": [
              "Read", "Update", "Delete" 
          ], // Indicates operations allowed on the SaaS subscription for beneficiary. For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
          "sessionMode": "None", // not relevant
          "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. (Optional field -– if not returned, the value is false.)
          "isTest": false, // not relevant
          "sandboxType": "None", // not relevant
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. Can be one of the following: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
      },
// next SaaS subscription details, might be a different offer
{
          "id": "<guid1>", 
          "name": "Contoso Cloud Solution1", 
          "publisherId": "contoso", 
          "offerId": "offer2", 
          "planId": "gold", 
          "quantity": "", 
          "beneficiary": {
              "emailId": " test@contoso.com",
              "objectId": "<guid>",
              "tenantId": "<guid>",
              "pid": "<ID of the user>"
          },
          "purchaser": { 
              "emailId": "purchase@csp.com ",
              "objectId": "<guid>",
              "tenantId": "<guid>",
               "pid": "<ID of the user>"
          },
            "term": { 
                "startDate": "2019-05-31",
                "endDate": "2020-04-30",
                "termUnit": "P1Y"
          },
          "allowedCustomerOperations": [
              "Read" 
          ], 
          "sessionMode": "None",
          "isFreeTrial": false,
          "isTest": false,
          "sandboxType": "None",
          "saasSubscriptionStatus": "Suspended"
      }
  ],
  "@nextLink": "https:// https://marketplaceapi.microsoft.com/api/saas/subscriptions/?continuationToken=%5b%7b%22token%22%3a%22%2bRID%3a%7eYeUDAIahsn22AAAAAAAAAA%3d%3d%23RT%3a1%23TRC%3a2%23ISV%3a1%23FPC%3aAgEAAAAQALEAwP8zQP9%2fFwD%2b%2f2FC%2fwc%3d%22%2c%22range%22%3a%7b%22min%22%3a%22%22%2c%22max%22%3a%2205C1C9CD673398%22%7d%7d%5d&api-version=2018-08-31" // url that contains continuation token to retrieve next page of the SaaS subscriptions list, if empty or absent, this is the last page. ISV can use this url as is to retrieve the next page or extract the value of continuation token from this url.
}
```

この発行者について購入済み SaaS サブスクリプションが見つからない場合は、空の応答本文が返されます。

コード:403 - 許可されていません。 認証トークンが使用できないか、無効であるか、有効期限が切れています。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。 

コード:500 - 内部サーバー エラーです。 API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://partner.microsoft.com/support/v2/?stage=1)にお問い合わせください。

#### <a name="get-subscription"></a>サブスクリプションを取得する

発行者によってマーケットプレースで発行された SaaS オファーの、指定された購入済み SaaS サブスクリプションを取得します。 この呼び出しを使用して (すべてのサブスクリプションの一覧を取得するために API を呼び出すのではなく)、特定の SaaS サブスクリプションに関して使用可能なすべての情報を ID で取得します。

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
| `ApiVersion`        |   2018-08-31 を使用します。 |
| `subscriptionId`     |  購入済み SaaS サブスクリプションの一意識別子。  この ID は、Resolve API を使用してマーケットプレース承認トークンを解決した後に取得されます。 |

*要求ヘッダー:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`  |
|  `x-ms-requestid`    |  クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|  `x-ms-correlationid` |  クライアントでの操作に対する一意の文字列値。  このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|  `authorization`     | この API 呼び出しを行っている発行者を識別する一意のアクセス トークン。 「[Azure AD アプリに基づいたトークンの取得](./pc-saas-registration.md#get-the-token-with-an-http-post)」の説明に従って、発行者によってトークン値が取得される場合、形式は `"Bearer <access_token>"` です。  |

*応答コード:*

コード:200 - 提供されている `subscriptionId` に基づいて、SaaS サブスクリプションの詳細を返します。

*応答本文の例:*

```json
{
        "id":<guid>, // purchased SaaS subscription ID
        "name":"Contoso Cloud Solution", // SaaS subscription name
         "publisherId": "contoso", // publisher ID
          "offerId": "offer1", // purchased offer ID
          "planId": "silver", // purchased plan ID
          "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
         "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription is purchased.
              "emailId": "test@contoso.com",
              "objectId": "<guid>",
              "tenantId": "<guid>",
              "pid": "<ID of the user>"
          },
          "purchaser": { // email address ,user ID and tenant ID that purchased the SaaS subscription.  These could be different from beneficiary information for reseller (CSP) scenario
              "emailId": "test@test.com",
              "objectId": "<guid>",
              "tenantId": "<guid>",
              "pid": "<ID of the user>"
          },
        "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary.  For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
        "sessionMode": "None", // not relevant
        "isFreeTrial": false, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. Optional field – if not returned the value is false.
          "isTest": false, // not relevant
          "sandboxType": "None", // not relevant
          "saasSubscriptionStatus": " Subscribed " // Indicates the status of the operation: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
          "term": { // the period for which the subscription was purchased 
            "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
            "endDate": "2019-06-29", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
            "termUnit": "P1M" //where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values.
        }
}
```

コード:403 - 許可されていません。 認証トークンが無効であるか、有効期限が切れているか、指定されていません。 要求が、認証トークンの作成に使用されたものとは異なる Azure AD アプリ ID で発行されたオファーの SaaS サブスクリプションにアクセスしようとしています。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。 

コード:404 見つかりません。  指定された `subscriptionId` の SaaS サブスクリプションが見つかりません。

コード:500 - 内部サーバー エラーです。  API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://partner.microsoft.com/support/v2/?stage=1)にお問い合わせください。

#### <a name="list-available-plans"></a>利用可能なプランを一覧表示する

このオファーの特定の購入を表す `subscriptionId` によって識別される SaaS オファーのすべてのプランを取得します。  この呼び出しを使用して、SaaS サブスクリプションの利用者がサブスクリプションで更新できるすべてのプライベートおよびパブリック プランの一覧を取得します。  返されたプランは、既に購入済みのプランと同じ地域で使用可能になります。

この呼び出しからは、既に購入済みのものに加えて、その顧客が使用可能なプランの一覧が返されます。  その一覧は、発行者のサイト上でエンド ユーザーに表示できます。  エンド カスタマーはサブスクリプション プランを、返された一覧にあるいずれかのプランに変更できます。  一覧に表示されていないプランに変更すると、失敗します。

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 を使用します。  |
|  `subscriptionId`    |  購入済み SaaS サブスクリプションの一意識別子。  この ID は、Resolve API を使用してマーケットプレース承認トークンを解決した後に取得されます。 |

*要求ヘッダー:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|   `content-type`     |  `application/json` |
|   `x-ms-requestid`   |  クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|  `x-ms-correlationid`  |  クライアントでの操作に対する一意の文字列値。  このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|  `authorization`     |  この API 呼び出しを行っている発行者を識別する一意のアクセス トークン。  「[Azure AD アプリに基づいたトークンの取得](./pc-saas-registration.md#get-the-token-with-an-http-post)」の説明に従って、発行者によってトークン値が取得される場合、形式は `"Bearer <access_token>"` です。  |

*応答コード:*

コード:200 - 既に購入済みのものを含め、既存の SaaS サブスクリプションで使用可能なすべてのプランの一覧を返します。

応答本文の例:

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso", // display name of the plan as it appears in the marketplace
        "isPrivate": true //true or false
    },
{ 
        "planId": "gold",
        "displayName": "Gold plan for Contoso", 
        "isPrivate": false //true or false
    }
]
}
```

`subscriptionId` が見つからない場合は、空の応答本文が返されます。

コード:403 - 許可されていません。 認証トークンが無効であるか、期限切れか、指定されていません。  要求が、認証トークンの作成に使用されたものとは異なる Azure AD アプリ ID で発行されたオファーの SaaS サブスクリプションにアクセスしようとしている可能性があります。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。 

コード:500 - 内部サーバー エラーです。  API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://partner.microsoft.com/support/v2/?stage=1)にお問い合わせください。

#### <a name="change-the-plan-on-the-subscription"></a>サブスクリプションのプランを変更する

SaaS サブスクリプションで購入済みの既存のプランを新しいプラン (パブリックまたはプライベート) に更新します。  発行者は、マーケットプレースで購入された SaaS サブスクリプションについて発行者側でプランを変更するときに、この API を呼び出す必要があります。

この API は、アクティブなサブスクリプションに対してのみ呼び出すことができます。  どのプランも他の既存のプラン (パブリックまたはプライベート) に変更できますが、それ自体には変更できません。  プライベート プランの場合、顧客のテナントは、パートナー センターでプランの対象ユーザーの一部として定義されている必要があります。

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 を使用します。  |
| `subscriptionId`     | 購入済み SaaS サブスクリプションの一意識別子。  この ID は、Resolve API を使用してマーケットプレース承認トークンを解決した後に取得されます。 |

*要求ヘッダー:*
 
|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  `x-ms-correlationid`  | クライアントでの操作に対する一意の文字列値。  このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  `authorization`     |  この API 呼び出しを行っている発行者を識別する一意のアクセス トークン。  「[Azure AD アプリに基づいたトークンの取得](./pc-saas-registration.md#get-the-token-with-an-http-post)」の説明に従って、発行者によってトークン値が取得される場合、形式は `"Bearer <access_token>"` です。 |

*要求ペイロードの例:*

```json
{
    "planId": "gold" // the ID of the new plan to be purchased
}
```

*応答コード:*

コード:202 - プランを変更する要求が受理され、非同期に処理されました。  パートナーは、プラン変更要求の成功または失敗を判断するために **Operation-Location の URL** をポーリングする必要があります。  ポーリングは、操作の最終状態として "失敗"、"成功"、または "競合" を受け取るまで数秒ごとに実行する必要があります。  最終的な操作の状態は迅速に返されますが、場合によっては数分かかることがあります。

また、Marketplace 側でアクションを正常に完了させる準備が整うと、パートナーは Webhook 通知を受け取ります。  発行者側での発行者によるプランの変更は、その後に限って行う必要があります。

*応答ヘッダー:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  操作の状態を取得するための URL。  たとえば、「 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31` 」のように入力します。 |

コード:400 - 無効な要求: 検証エラー。

* 新しいプランが存在しないか、この特定の SaaS サブスクリプションでは使用できません。
* 同じプランへの変更が試みられます。
* SaaS サブスクリプションの状態がサブスクライブ済みではありません。
* SaaS サブスクリプションの更新操作が `allowedCustomerOperations` に含まれていません。

コード:403 - 許可されていません。 認証トークンが無効であるか、期限切れか、指定されていません。  要求が、認証トークンの作成に使用されたものとは異なる Azure AD アプリ ID で発行されたオファーの SaaS サブスクリプションにアクセスしようとしています。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。

コード:404 見つかりません。  `subscriptionId` を持つ SaaS サブスクリプションが見つかりません。

コード:500 - 内部サーバー エラーです。  API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://partner.microsoft.com/support/v2/?stage=1)にお問い合わせください。

>[!NOTE]
>一度に変更できるのは、プランまたはシート数のいずれかであり、両方ではありません。

>[!Note]
>この API は、エンド カスタマーから変更の明示的な承認を得た後にのみ呼び出すことができます。

#### <a name="change-the-quantity-of-seats-on-the-saas-subscription"></a>SaaS サブスクリプションのシート数を変更する

SaaS サブスクリプションで購入されたシートの数を更新 (増減) します。  マーケットプレースで作成された SaaS サブスクリプションのシート数を発行者側から変更するときに、発行者はこの API を呼び出す必要があります。

シートの数を現在のプランで許可されているよりも多くすることはできません。  この場合、数量を変更する前にプランを変更する必要があります。

##### <a name="patchhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 を使用します。  |
|  `subscriptionId`     | 購入済み SaaS サブスクリプションの一意識別子。  この ID は、Resolve API を使用してマーケットプレース承認トークンを解決した後に取得されます。  |

*要求ヘッダー:*
 
|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  `x-ms-correlationid`  | クライアントでの操作に対する一意の文字列値。  このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  `authorization`     | この API 呼び出しを行っている発行者を識別する一意のアクセス トークン。  「[Azure AD アプリに基づいたトークンの取得](./pc-saas-registration.md#get-the-token-with-an-http-post)」の説明に従って、発行者によってトークン値が取得される場合、形式は `"Bearer <access_token>"` です。  |

*要求ペイロードの例:*

```json
{
    "quantity": 5 // the new amount of seats to be purchased
}
```

*応答コード:*

コード:202 - 数量を変更する要求は受理され、非同期に処理されました。 パートナーは、数量変更要求の成功または失敗を判断するために **Operation-Location の URL** をポーリングする必要があります。  ポーリングは、操作の最終状態として "失敗"、"成功"、または "競合" を受け取るまで数秒ごとに実行する必要があります。  最終的な操作の状態は迅速に返されますが、場合によっては数分かかることがあります。

また、Marketplace 側でアクションを正常に完了させる準備が整うと、パートナーは Webhook 通知を受け取ります。  発行者側での発行者による数量の変更は、その後に限って行う必要があります。

*応答ヘッダー:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  操作状態を取得するリソースへのリンク。  たとえば、「 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31` 」のように入力します。  |

コード:400 - 無効な要求: 検証エラー。

* 新しい数量が現在のプランの制限を上回っているか下回っています。
* 新しい数量がありません。
* 同じ数量に変更しようとしています。
* SaaS サブスクリプションの状態がサブスクライブ済みではありません。
* SaaS サブスクリプションの更新操作が `allowedCustomerOperations` に含まれていません。

コード:403 - 許可されていません。  認証トークンが無効であるか、期限切れか、指定されていません。  要求で、現在の発行者に属していないサブスクリプションにアクセスしようとしています。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。 

コード:404 見つかりません。  `subscriptionId` を持つ SaaS サブスクリプションが見つかりません。

コード:500 - 内部サーバー エラーです。  API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://partner.microsoft.com/support/v2/?stage=1)にお問い合わせください。

>[!Note]
>一度に変更できるのはプランまたは数量だけであり、両方ではありません。

>[!Note]
>この API は、エンド カスタマーから変更の明示的な承認を得た後にのみ呼び出すことができます。

#### <a name="cancel-a-subscription"></a>サブスクリプションを取り消す

指定した SaaS サブスクリプションの登録を解除します。  発行者はこの API を使用する必要はありません。SaaS サブスクリプションを取り消すには、顧客をマーケットプレースに転送することをお勧めします。

マーケットプレースで購入された SaaS サブスクリプションの取り消しを発行者側で実装する場合、発行者はこの API を呼び出す必要があります。  この呼び出しが完了すると、サブスクリプションの状態が Microsoft 側で "*登録解除済み*" になります。

次に示す猶予期間中にサブスクリプションが取り消された場合、顧客への請求は行われません。

* 月単位のサブスクリプションでは、アクティブ化の後 24 時間。
* 年単位のサブスクリプションでは、アクティブ化の後 14 日。

上記の猶予期間が経過した後にサブスクリプションが取り消された場合、顧客への請求が発生します。  取り消しが成功すると、顧客は直ちに Microsoft 側の SaaS サブスクリプションにアクセスできなくなります。

##### <a name="deletehttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Delete`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 を使用します。  |
|  `subscriptionId`     | 購入済み SaaS サブスクリプションの一意識別子。  この ID は、Resolve API を使用してマーケットプレース承認トークンを解決した後に取得されます。  |

*要求ヘッダー:*
 
|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  `x-ms-correlationid`  | クライアントでの操作に対する一意の文字列値。  このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  `authorization`     |  この API 呼び出しを行っている発行者を識別する一意のアクセス トークン。  「[Azure AD アプリに基づいたトークンの取得](./pc-saas-registration.md#get-the-token-with-an-http-post)」の説明に従って、発行者によってトークン値が取得される場合、形式は `"Bearer <access_token>"` です。 |

*応答コード:*

コード:202 - 登録を解除する要求が受理され、非同期に処理されました。  パートナーは、この要求の成功または失敗を判断するために **Operation-Location の URL** をポーリングする必要があります。  ポーリングは、操作の最終状態として "失敗"、"成功"、または "競合" を受け取るまで数秒ごとに実行する必要があります。  最終的な操作の状態は迅速に返されますが、場合によっては数分かかることがあります。

また、Marketplace 側でアクションが正常に完了すると、パートナーは Webhook 通知を受け取ります。  発行者側での発行者によるサブスクリプションの取り消しは、その後に限って行う必要があります。

*応答ヘッダー:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  操作状態を取得するリソースへのリンク。  たとえば、「 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31` 」のように入力します。 |

コード:400 - 無効な要求です。  削除は、この SaaS サブスクリプションの `allowedCustomerOperations` 一覧に含まれていません。

コード:403 - 許可されていません。  認証トークンが無効であるか、有効期限が切れているか、使用できません。 要求が、認証トークンの作成に使用されたものとは異なる Azure AD アプリ ID で発行されたオファーの SaaS サブスクリプションにアクセスしようとしています。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。

コード:404 見つかりません。  `subscriptionId` を持つ SaaS サブスクリプションが見つかりません。

コード:500 - 内部サーバー エラーです。 API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://partner.microsoft.com/support/v2/?stage=1)にお問い合わせください。

### <a name="operations-apis"></a>Operations API

#### <a name="list-outstanding-operations"></a>未処理の操作を一覧表示する 

指定した SaaS サブスクリプションの保留中の操作の一覧を取得します。  発行者は、[Operation patch API](#update-the-status-of-an-operation) を呼び出すことによって、返された操作を確認する必要があります。

現時点では、この API 呼び出しの応答として返されるのは**復帰の操作**のみです。

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|    `ApiVersion`    |  2018-08-31 を使用します。         |
|    `subscriptionId` | 購入済み SaaS サブスクリプションの一意識別子。  この ID は、Resolve API を使用してマーケットプレース承認トークンを解決した後に取得されます。  |

*要求ヘッダー:*
 
|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `content-type`     |  `application/json` |
|  `x-ms-requestid`    |  クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  `x-ms-correlationid` |  クライアントでの操作に対する一意の文字列値。  このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  `authorization`     |  「[Azure AD アプリに基づいたトークンの取得](./pc-saas-registration.md#get-the-token-with-an-http-post)」の説明に従って、発行者によってトークン値が取得される場合、形式は `"Bearer <access_token>"` です。  |

*応答コード:*

コード:200 - 指定された SaaS サブスクリプションで保留中の復帰操作を返します。

*応答ペイロードの例:*

```json
{"operations": [{
    "id": "<guid>",  //Operation ID, should be provided in the operations patch API call
    "activityId": "<guid>", //not relevant
    "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription that is being reinstated
    "offerId": "offer1",  // purchased offer ID
    "publisherId": "contoso",  
    "planId": "silver",  // purchased plan ID
    "quantity": "20", // purchased amount of seats, will be empty is not relevant
    "action": "Reinstate", 
    "timeStamp": "2018-12-01T00:00:00",  // UTC
    "status": "InProgress" // the only status that can be returned in this case
}]
}
```

保留中の復帰操作がない場合は、空の json を返します。

コード:400 - 無効な要求: 検証エラー。

コード:403 - 許可されていません。 認証トークンが無効であるか、期限切れか、指定されていません。  要求が、認証トークンの作成に使用されたものとは異なる Azure AD アプリ ID で発行されたオファーの SaaS サブスクリプションにアクセスしようとしています。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。 

コード:404 見つかりません。  `subscriptionId` を持つ SaaS サブスクリプションが見つかりません。

コード:500 - 内部サーバー エラーです。 API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://partner.microsoft.com/support/v2/?stage=1)にお問い合わせください。

#### <a name="get-operation-status"></a>操作状態を取得する

発行者が、指定した非同期操作の状態を追跡できるようにします: **Unsubscribe**、**ChangePlan**、または **ChangeQuantity**。

この API 呼び出しの `operationId` は、**Operation-Location** によって返される値、保留中の操作を取得する API 呼び出し、または Webhook 呼び出しで受け取った `<id>` パラメーター値から取得できます。

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 を使用します。  |
|  `subscriptionId`    |  購入済み SaaS サブスクリプションの一意識別子。  この ID は、Resolve API を使用してマーケットプレース承認トークンを解決した後に取得されます。 |
|  `operationId`       |  取得する対象である操作を表す一意識別子。 |

*要求ヘッダー:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`   |
|  `x-ms-requestid`    |  クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|  `x-ms-correlationid` |  クライアントでの操作に対する一意の文字列値。  このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  `authorization`     |  この API 呼び出しを行っている発行者を識別する一意のアクセス トークン。  「[Azure AD アプリに基づいたトークンの取得](./pc-saas-registration.md#get-the-token-with-an-http-post)」の説明に従って、発行者によってトークン値が取得される場合、形式は `"Bearer <access_token>"` です。  |

*応答コード:*

コード:200 - 指定した SaaS 操作の詳細を取得します。 

*応答ペイロードの例:*

```json
Response body:
{
    "id  ": "<guid>", //Operation ID, should be provided in the patch operation API call
    "activityId": "<guid>", //not relevant
    "subscriptionId":"<guid>", // subscriptionId of the SaaS subscription for which this operation is relevant
    "offerId": "offer1", // purchased offer ID
    "publisherId": "contoso",  
    "planId": "silver", // purchased plan ID
    "quantity": "20", // purchased amount of seats 
    "action": "ChangePlan", // Can be ChangePlan, ChangeQuantity or Reinstate
    "timeStamp": "2018-12-01T00:00:00", // UTC
    "status": "InProgress", // Possible values: NotStarted, InProgress, Failed, Succeed, Conflict (new quantity / plan is the same as existing)

"errorStatusCode": "",
"errorMessage": ""
}
```

コード:403 - 許可されていません。 認証トークンが無効であるか、期限切れか、指定されていません。  要求が、認証トークンの作成に使用されたものとは異なる Azure AD アプリ ID で発行されたオファーの SaaS サブスクリプションにアクセスしようとしています。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。 

コード:404 見つかりません。  

* `subscriptionId` を持つサブスクリプションが見つかりません。
* `operationId` を持つ操作が見つかりません。

コード:500 - 内部サーバー エラーです。  API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://partner.microsoft.com/support/v2/?stage=1)にお問い合わせください。

#### <a name="update-the-status-of-an-operation"></a>操作の状態を更新する

保留中の操作の状態を更新して、発行者側で操作が成功したか失敗したかを示します。

この API 呼び出しの `operationId` は、**Operation-Location** によって返される値、保留中の操作を取得する API 呼び出し、または Webhook 呼び出しで受け取った `<id>` パラメーター値から取得できます。

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|   `ApiVersion`       |  2018-08-31 を使用します。  |
|   `subscriptionId`   |  購入済み SaaS サブスクリプションの一意識別子。  この ID は、Resolve API を使用してマーケットプレース承認トークンを解決した後に取得されます。  |
|   `operationId`      |  完了する対象である操作の一意識別子。 |

*要求ヘッダー:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|   `content-type`   | `application/json`   |
|   `x-ms-requestid`   |  クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|   `x-ms-correlationid` |  クライアントでの操作に対する一意の文字列値。  このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|  `authorization`     |  この API 呼び出しを行っている発行者を識別する一意のアクセス トークン。  「[Azure AD アプリに基づいたトークンの取得](./pc-saas-registration.md#get-the-token-with-an-http-post)」の説明に従って、発行者によってトークン値が取得される場合、形式は `"Bearer <access_token>"` です。 |

*要求ペイロードの例:*

```json
{ 
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation on ISV side.
}
```

*応答コード:*

コード:200 - パートナー側での操作の完了を通知する呼び出し。  たとえば、この応答によって発行者側でのシートまたはプランの変更を通知できます。

コード:403 - 許可されていません。  認証トークンが使用できないか、無効であるか、有効期限が切れています。 要求で、現在の発行者に属していないサブスクリプションにアクセスしようとしている可能性があります。
Forbidden.  認証トークンが無効であるか、期限切れか、指定されていません。  要求が、認証トークンの作成に使用されたものとは異なる Azure AD アプリ ID で発行されたオファーの SaaS サブスクリプションにアクセスしようとしています。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。

コード:404 見つかりません。

* `subscriptionId` を持つサブスクリプションが見つかりません。
* `operationId` を持つ操作が見つかりません。

コード:409 - 競合。  たとえば、より新しい更新が既に履行されています。

コード:500 - 内部サーバー エラーです。  API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://partner.microsoft.com/support/v2/?stage=1)にお問い合わせください。

## <a name="implementing-a-webhook-on-the-saas-service"></a>SaaS サービスでの Webhook の実装

パートナー センターで取引可能な SaaS オファーを作成する場合、パートナーは、HTTP エンドポイントとして使用される**接続 Webhook** の URL を指定します。  この Webhook は、Microsoft 側で発生する次のイベントを発行者に通知するために、POST HTTP 呼び出しを使用して Microsoft によって呼び出されます。

* SaaS サブスクリプションがサブスクライブ済み状態にあるとき:
    * ChangePlan 
    * ChangeQuantity
    * [中断]
    * サブスクライブ解除
* SaaS サブスクリプションが中断状態にあるとき:
    * Reinstate
    * サブスクライブ解除

発行者は、SaaS サブスクリプションの状態を Microsoft 側と一貫性のある状態に保つために、SaaS サービスに Webhook を実装する必要があります。  SaaS サービスでは、Webhook 通知に基づいてアクションを実行する前に、操作を取得する API を呼び出して、Webhook 呼び出しとペイロード データを検証および承認する必要があります。  Webhook 呼び出しが処理されるとすぐに、発行者から Microsoft に HTTP 200 を返す必要があります。  この値は、Webhook 呼び出しが発行者によって正常に受信されたことを確認します。

>[!Note]
>Webhook URL サービスは、24 時間 365 日稼働し、いつでも Microsoft からの新しい呼び出しを受け取る準備ができている必要があります。  Microsoft には、Webhook 呼び出しの再試行ポリシー (再試行は 8 時間で 500 回) があります。ただし、発行者が呼び出しを受け入れて応答を返さない場合、Webhook で通知される操作は最終的に Microsoft 側で失敗します。

*Webhook のペイロードの例:*

```json
// end customer changed a quantity of purchased seats for a plan on Microsoft side
{
  "id": <guid>, // this is the operation ID to call with get operation API
  "activityId": "<guid>", // do not use
  "subscriptionId": "guid", // The GUID identifier for the SaaS resource which status changes
  "publisherId": "contoso", // A unique string identifier for each publisher
  "offerId": "offer1", // A unique string identifier for each offer
  "planId": "silver", // the most up-to-date plan ID
  "quantity": " 25", // the most up-to-date number of seats, can be empty if not relevant
  "timeStamp": "2019-04-15T20:17:31.7350641Z", // UTC time when the webhook was called
  "action": "ChangeQuantity", // the operation the webhook notifies about
  "status": "Success" // Can be either InProgress or Success  
}
```

```json
// end customer's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": <guid>, 
  "activityId": <guid>, 
  "subscriptionId": "guid", 
  "publisherId": "contoso",
  "offerId": "offer2 ",
  "planId": "gold", 
  "quantity": " 20", 
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Reinstate",
  "status": "In Progress" 
} 
```

## <a name="development-and-testing"></a>開発とテスト

開発プロセスを開始するには、発行者側でダミー API 応答を作成することをお勧めします。  これらの応答は、このドキュメントに示されているサンプル応答に基づくことができます。

発行者がエンドツーエンド テストの準備ができたら、次のようになります。 

* SaaS オファーを限られたプレビュー対象ユーザーに公開し、プレビュー段階のままにします。
* テスト中に実際の請求エクスペリエンスがトリガーされないように、このオファーには価格が 0 のプランが存在する必要があります。  別の方法として、0 以外の価格を設定し、すべてのテスト購入を 24 時間以内に取り消すこともできます。 
* 顧客がオファーを購入した場合と同じように、すべてのフローがエンドツーエンドで呼び出されるようにします。 
* パートナーが完全な購入と請求のフローをテストする必要がある場合は、$0 より大きい価格のオファーを使用して、それを行ってください。  購入に対して請求が行われ、請求書が生成されます。

購入フローは、オファーが公開されている場所に応じて、Azure portal または Microsoft AppSource サイトからトリガーできます。

"*プラン変更*"、"*数量変更*"、"*登録解除*" の各アクションは、発行者側からテストされます。  Microsoft 側からは、Azure portal と管理センター (Microsoft AppSource での購入が管理されているポータル) の両方から、"*登録解除*" をトリガーできます。  "*数量とプランの変更*" は、管理センターからのみトリガーできます。

## <a name="get-support"></a>サポートを受ける

発行者サポート オプションについては、「[パートナー センターでの商業マーケットプレース プログラムのサポート](support.md)」を参照してください。


## <a name="next-steps"></a>次のステップ

マーケットプレースにおける SaaS オファーのその他のオプションについては、Marketplace の[測定サービス API](marketplace-metering-service-apis.md) を参照してください。

このドキュメントで説明されている API の上に構築された [SaaS SDK](https://github.com/Azure/Microsoft-commercial-marketplace-transactable-SaaS-offer-SDK) を確認して使用します。
