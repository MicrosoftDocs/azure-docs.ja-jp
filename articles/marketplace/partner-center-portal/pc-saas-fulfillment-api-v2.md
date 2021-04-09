---
title: Microsoft コマーシャル マーケットプレースの SaaS Fulfillment API v2
description: Fulfillment API バージョン 2 を使用し、Microsoft AppSource と Azure Marketplace で SaaS オファーを作成し、管理する方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 2acf5178e7d1cfdf907146d733150a48e9696a5e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101712353"
---
# <a name="saas-fulfillment-apis-version-2-in-the-commercial-marketplace"></a>コマーシャル マーケットプレースの SaaS Fulfillment API バージョン 2

この記事では、パートナーがそのサービスとしてのソフトウェア (SaaS) オファーを Microsoft AppSource と Azure Marketplace で販売できるようにする API について説明します。 発行者が取引可能な SaaS オファーをパートナー センターで公開するには、これらの API との統合を実装する必要があります。

## <a name="managing-the-saas-subscription-life-cycle"></a>SaaS サブスクリプション ライフサイクルの管理

コマーシャル マーケットプレースでは、エンド ユーザーによる購入後の SaaS サブスクリプションのライフ サイクル全体が管理されます。 また、実際の SaaS サブスクリプションのアクティブ化、使用、更新、および取り消しを駆動するメカニズムとして、ランディング ページ、Fulfillment API、Operations API、Webhook が使用されています。 エンド ユーザーの請求書は、Microsoft が管理する SaaS サブスクリプションの状態に基づきます。 

### <a name="states-of-a-saas-subscription"></a>SaaS サブスクリプションの状態

次の図は、SaaS サブスクリプションの状態と適用できるアクションを示しています。

![マーケットプレースにおけるサービスとしてのソフトウェアのサブスクリプションのライフ サイクルを示す図。](./media/saas-subscription-lifecycle-api-v2.png)

#### <a name="purchased-but-not-yet-activated-pendingfulfillmentstart"></a>購入済みであるが、まだアクティブ化されていない (*PendingFulfillmentStart*)

エンドユーザー (または CSP) がコマーシャル マーケットプレースで SaaS プランを購入した後、その購入が公開元に通知されます。 その後、公開元はそのエンド ユーザー用に、公開元側で新しい SaaS アカウントを作成して構成できます。

アカウントの作成が行われるようにするには:

1. 顧客は Microsoft AppSource または Azure portal での購入が正常に終了した後に、SaaS オファーで使用可能な **[構成]** ボタンを選択します。 代わりに、顧客は購入後すぐに受信する電子メールの **[構成]** ボタンを使用することもできます。
2. その後、新しいブラウザー タブで token パラメーター (コマーシャル マーケットプレースからの購入 ID トークン) を含むランディング ページの URL が開かれることで、Microsoft からパートナーに対して購入に関する通知が行われます。

このような呼び出しの例として `https://contoso.com/signup?token=<blob>` がありますが、パートナー センターでは、この SaaS オファーのランディング ページの URL は `https://contoso.com/signup` として構成されています。 このトークンにより、SaaS の購入と顧客を一意に識別する ID が発行者に提供されます。

>[!NOTE]
>顧客が Microsoft 側からの構成プロセスを開始するまで、SaaS の購入は公開元に通知されません。

ランディング ページの URL は毎日一日中稼働し、いつでも Microsoft からの新しい呼び出しを受け取る準備ができている必要があります。 ランディング ページが使用できなくなった場合、お客様は SaaS サービスにサインアップして使用を開始することができません。

次に、公開元は Microsoft に "*トークン*" を返す必要があります。これは、[SaaS Resolve API](#resolve-a-purchased-subscription) を呼び出し、`x-ms-marketplace-token header` ヘッダー パラメーターの値としてそのトークンを入力して行います。 Resolve API 呼び出しの結果として、そのトークンが、購入の一意の ID、購入されたオファーの ID、購入されたプランの ID などの SaaS 購入の詳細と交換されます。

ランディング ページでは、顧客は Azure Active Directory (Azure AD) シングル サインオン (SSO) を使用して、新規または既存の SaaS アカウントにサインインする必要があります。

公開元は、このフローに Microsoft が要求するユーザー エクスペリエンスを提供するために、SSO を実装する必要があります。 SSO を構成するときに、必ず、マルチテナント Azure AD アプリケーションを使用し、職場と学校のアカウントの両方または個人の Microsoft アカウントを許可してください。 この要件が適用されるのはランディング ページのみで、Microsoft 資格情報を使用して既にサインインしているときに SaaS サービスにリダイレクトされるユーザー向けです。 SSO は、SaaS サービスへのすべてのサインインに必要とされるわけではありません。

> [!NOTE]
>SSO で、管理者がアプリに対するアクセス許可を付与しなければならないことが求められる場合、パートナー センターでのオファーの説明で、管理者レベルのアクセス許可が必要であることを明記する必要があります。 この開示は、[コマーシャル マーケットプレースの認定ポリシー](/legal/marketplace/certification-policies#10003-authentication-options)に準拠するためです。

サインインした後、顧客は公開元側で SaaS 構成を完了する必要があります。 その後、公開元は、SaaS アカウントのプロビジョニングが完了したことを示す通知を Azure Marketplace に送信するために、[Activate Subscription API](#activate-a-subscription) を呼び出す必要があります。
この操作により、顧客の請求期間が開始されます。 Activate Subscription API の呼び出しが失敗した場合、顧客は購入に対して請求されません。


![プロビジョニング シナリオの API 呼び出しを示す図。](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="active-subscribed"></a>アクティブ (*Subscribed*)

"*アクティブ (Subscribed)* " は、プロビジョニング済み SaaS サブスクリプションの安定した状態です。 Microsoft 側で [Activate Subscription API](#activate-a-subscription) 呼び出しが処理された後、SaaS サブスクリプションが *Subscribed* としてマークされます。 これで、顧客は公開元側で SaaS サービスを使用できるようになり、請求されます。

SaaS サブスクリプションが既にアクティブになっている場合、顧客は Azure portal または Microsoft 365 管理センターから **[Manage SaaS experience]\(SaaS エクスペリエンスの管理\)** を選択することができます。 また、この操作により、アクティブ化フローの場合のように、Microsoft が *token* パラメーターを使用して **ランディングページの URL** を呼び出すことになります。 公開元は、新規購入と既存の SaaS アカウントの管理を区別し、このランディング ページの URL 呼び出しを適宜処理する必要があります。

#### <a name="being-updated-subscribed"></a>更新中 (*Subscribed*)

このアクションは、既存のアクティブな SaaS サブスクリプションに対する更新が、Microsoft と公開元の両方で処理中であることを意味します。 このような更新は、次のユーザーによって開始することができます。

- コマーシャル マーケットプレースから顧客によって。
- コマーシャル マーケットプレースから CSP によって。
- 公開元の SaaS サイトから顧客によって (CSP が行った購入は対象外)。

SaaS サブスクリプションに対して 2 種類の更新ができます。

- 顧客がサブスクリプションに対して別のプランを選択した場合に、プランを更新します。
- 顧客がサブスクリプションの購入済みシート数を変更したときに、数量を更新します。

アクティブなサブスクリプションのみを更新できます。 サブスクリプションが更新されている間は、Microsoft 側ではアクティブな状態のままとなります。

##### <a name="update-initiated-from-the-commercial-marketplace"></a>コマーシャル マーケットプレースから開始された更新

このフローでは、顧客は Azure portal または Microsoft 365 管理センターからサブスクリプション プランまたはシート数を変更します。

1. 更新が入力された後、Microsoft によって、パートナー センターの **[接続 Webhook]** フィールドに構成されている公開元の Webhook URL が、*action* およびその他の関連するパラメーターの適切な値を使用して呼び出されます。 
1. 公開元側は SaaS サービスに対して必要な変更を行い、[Update Status of Operation API](#update-the-status-of-an-operation) を呼び出すことで完了時に Microsoft に通知します。
1. "*失敗*" の状態でパッチが送信されると、更新プロセスは Microsoft 側で完了しません。 SaaS サブスクリプションは、既存のプランとシート数のままになります。

> [!NOTE]
> 公開元は、Webhook 通知を受信した後 "*10 秒以内に*" PATCH を呼び出して、Failure または Success の応答で [Status of Operation API を更新する](#update-the-status-of-an-operation)必要があります。 操作状態の PATCH が 10 秒以内に受信されなかった場合、変更計画は *成功として自動的にパッチが適用* されます。 

コマーシャル マーケットプレースから開始される更新シナリオ向けの一連の API 呼び出しを、次の図に示します。

![マーケットプレースで開始された更新の API 呼び出しを示す図。](./media/saas-update-status-api-v2-calls-marketplace-side.png)

##### <a name="update-initiated-from-the-publisher"></a>発行者から開始された更新

このフローでは、顧客が SaaS サービス自体から購入したサブスクリプション プランまたはシート数を変更します。 

1. 要求された変更を公開元側で行う前に、公開元のコードで [Change Plan API](#change-the-plan-on-the-subscription) または [Change Quantity API](#change-the-quantity-of-seats-on-the-saas-subscription) あるいはその両方を呼び出す必要があります。 

1. Microsoft はサブスクリプションに変更を適用し、**接続 Webhook** を使用して、同じ変更を適用するように発行者に対して通知します。

1. その段階でようやく、公開元は SaaS サブスクリプションに対して必要な変更を行い、変更が完了したら [Update Status of Operation API](#update-the-status-of-an-operation) を呼び出して Microsoft に通知します。

公開元側から開始される更新シナリオ向けの一連の API 呼び出しを、次の図に示します。

![公開元側で開始された更新の API 呼び出しを示す図。](./media/saas-update-status-api-v2-calls-publisher-side.png)

#### <a name="suspended-suspended"></a>中断 (*中断*)

この状態は、SaaS サービスに対する顧客の支払いが受け取られていないことを示します。 公開元には、SaaS サブスクリプションの状態のこの変更が Microsoft によって通知されます。 この通知は、*action* パラメーターを *Suspended* に設定した Webhook の呼び出しによって行われます。

公開元は、公開元側で SaaS サービスへの変更を行うことも、行わないこともあります。 公開元側で、中断された顧客に対してこの情報が提供されるようにし、SaaS サービスへの顧客のアクセスを制限またはブロックすることをお勧めします。 支払いが一切受け取られない可能性があります。

Microsoft は、サブスクリプションを自動的に取り消す前に、30 日間の猶予期間を顧客に提供します。 サブスクリプションが *Suspended* 状態にある場合:

* パートナーまたは ISV は SaaS アカウントを復旧可能な状態にしておく必要があります。これにより、データや設定を失わずにすべての機能を復元できます。
* パートナーまたは ISV は、猶予期間中に支払いが発生した場合は、サブスクリプションの復帰要求を想定し、そうでなければ、猶予期間の終わりにこのサブスクリプションのプロビジョニング解除要求を想定してください。 両方の要求は、Webhook メカニズムを使用して送信されます。

発行者が何らかのアクションを実行する前に、サブスクリプションの状態が Microsoft 側で "中断" に変更されます。 アクティブなサブスクリプションのみを中断できます。

#### <a name="reinstated-suspended"></a>復帰 (*中断*)

このアクションは、顧客の支払い方法が再び有効になり、SaaS サブスクリプションに対して支払いが行われており、サブスクリプションが復帰中であることを示します。 この場合、次のようになります。 

1. Microsoft によって、*action* パラメーターを *Reinstate* の値に設定した Webhook が呼び出されます。
1. 公開元で、このサブスクリプションを再び完全に運用可能にします。
1. 発行者は、成功の状態を持つ [Patch Operation API](#update-the-status-of-an-operation) を呼び出します。
1. 復帰プロセスが成功し、顧客に SaaS サブスクリプションの請求が再び行われます。 

パッチが "*失敗*" 状態で送信された場合、復帰プロセスは Microsoft 側では完了せず、サブスクリプションは *Suspended* のままになります。

中断されたサブスクリプションのみを復帰させることができます。 中断された SaaS サブスクリプションは、復帰中は *Suspended* 状態のままです。 この操作が完了すると、サブスクリプションの状態は "*アクティブ*" になります。

#### <a name="renewed-subscribed"></a>更新済み (*サブスクライブ済み*)

SaaS サブスクリプションは、1 か月または 1 年間のサブスクリプション期間の終了時に Microsoft によって自動的に更新されます。 自動更新設定の既定値は、すべての SaaS サブスクリプションで *true* です。 アクティブな SaaS サブスクリプションは、定期的に更新され続けます。 サブスクリプションが更新されるとき、Microsoft から公開元への通知は行われません。 顧客は、Microsoft 365 管理ポータル経由で、SaaS サブスクリプションの自動更新を無効にすることができます。 この場合、SaaS サブスクリプションは、現在の請求期間の終了時に自動的に取り消されます。 また、顧客はいつでも SaaS サブスクリプションを取り消すことができます。

アクティブなサブスクリプションのみが自動的に更新されます。 サブスクリプションは、更新プロセス中および自動更新が成功した場合はアクティブに維持されます。 更新後、サブスクリプション期間の開始日と終了日が新しい期間の日付に更新されます。

支払いの問題によって自動更新が失敗した場合、サブスクリプションは *Suspended* になり、公開元に通知されます。

#### <a name="canceled-unsubscribed"></a>取り消し (*登録解除済み*) 

サブスクリプションは、公開元サイト、Azure portal、または Microsoft 365 管理センターからサブスクリプションを取り消すという、顧客または CSP の明示的なアクションに呼応して、この状態になります。 サブスクリプションは、料金未払いの結果として 30 日間 *Suspended* 状態になった後、暗黙的に取り消すこともできます。

公開元は取り消しの Webhook 呼び出しを受け取ったら、要求に応じて復旧できるように顧客データを少なくとも 7 日間保持する必要があります。 その段階でようやく、顧客データを削除できます。

SaaS サブスクリプションは、ライフサイクルの任意の時点で取り消すことができます。 サブスクリプションは、取り消された後に再びアクティブにすることはできません。

## <a name="api-reference"></a>API リファレンス

このセクションでは、SaaS のサブスクリプションおよび Operations API について説明します。

**Subscription API** は、購入から取り消しまでの SaaS サブスクリプションのライフサイクルを処理するために使用する必要があります。

**Operations API** は、次のために使用する必要があります。

* 処理された Webhook 呼び出しを検証し、確認する。
* 公開元による確認を待機している保留中の操作があるアプリの一覧を取得する。

> [!NOTE]
> TLS バージョン 1.2 というバージョンが、HTTPS 通信の最小バージョンとしてまもなく適用されます。 コードでこの TLS バージョンを使用していることを確認してください。 TLS バージョン 1.0 および 1.1 はまもなく非推奨となります。

### <a name="subscription-apis"></a>Subscription API

#### <a name="resolve-a-purchased-subscription"></a>購入済みサブスクリプションを解決する

resolve エンドポイントを使用すると、公開元は、コマーシャル マーケットプレースからの購入識別トークン (「[購入済みであるが、まだアクティブ化されていない](#purchased-but-not-yet-activated-pendingfulfillmentstart)」で "*トークン*" として言及) を、永続的な購入済み SaaS サブスクリプション ID とその詳細に交換できます。

顧客がパートナーのランディング ページの URL にリダイレクトされると、この URL 呼び出しで、顧客識別トークンが *token* パラメーターとして渡されます。 パートナーは、このトークンを使用すること、およびそれを解決するための要求を行うことを期待されています。 Resolve API 応答には、購入を一意に識別するための SaaS サブスクリプション ID とその他の詳細が含まれています。 ランディング ページの URL 呼び出しで提供される "*トークン*" は、通常 24 時間有効です。 受け取った "*トークン*" が既に期限切れになっている場合は、エンド ユーザーに次のガイダンスを提供することをお勧めします。

"この購入を特定できませんでした。 Azure portal または Microsoft 365 管理センターでこの SaaS サブスクリプションを再度開いて、[アカウントの構成] または [アカウントの管理] をもう一度選択してください。"

Resolve API を呼び出すと、サポートされているすべての状態の SaaS サブスクリプションの詳細と状態が返されます。

##### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Post`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

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
|  `x-ms-marketplace-token`  | 解決する購入 ID *token* パラメーター。  このトークンは、顧客が SaaS パートナーの Web サイトにリダイレクトされるときに、ランディング ページの URL 呼び出しで渡されます (例: `https://contoso.com/signup?token=<token><authorization_token>`)。 <br> <br>  エンコードされている *token* 値は、ランディング ページの URL の一部であるため、この API 呼び出しでパラメーターとして使用する前にデコードする必要があることに注意してください。  <br> <br> URL 内のエンコードされた文字列の例は次のとおりです: `contoso.com/signup?token=ab%2Bcd%2Fef`。ここで、*token* は `ab%2Bcd%2Fef` です。  同じトークンをデコードすると、このようになります: `Ab+cd/ef` |
| | |

*応答コード:*

コード:200 - 指定された `x-ms-marketplace-token` に基づいて一意の SaaS サブスクリプション識別子を返します。

応答本文の例:

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
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
      "startDate": "2019-05-31",
      "endDate": "2019-06-29"
    },
    "isTest": true,
    "isFreeTrial": false,
    "allowedCustomerOperations": ["Delete", "Update", "Read"],
    "sandboxType": "None",
    "sessionMode": "None"
  }
}

```

コード:400 - 無効な要求です。 `x-ms-marketplace-token` が見つからないか、形式が正しくありません。または、有効期限が切れています。

コード:403 - 許可されていません。 認証トークンが無効であるか、期限切れであるか、指定されませんでした。  要求が、認証トークンの作成に使用されたものとは異なる Azure AD アプリ ID で公開されたオファーの SaaS サブスクリプションにアクセスしようとしています。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。

コード:500 - 内部サーバー エラーです。  API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://partner.microsoft.com/support/v2/?stage=1)にお問い合わせください。

#### <a name="activate-a-subscription"></a>サブスクリプションをアクティブ化する

SaaS アカウントがエンド ユーザー向けに構成されたら、公開元は Microsoft 側で Activate Subscription API を呼び出す必要があります。  この API 呼び出しが成功しない限り、顧客に対する請求は行われません。

##### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Post`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値             |
|  --------   |  ---------------  |
| `ApiVersion`  |  2018-08-31 を使用します。   |
| `subscriptionId` | 購入済み SaaS サブスクリプションの一意識別子。  この ID は、[Resolve API](#resolve-a-purchased-subscription) を使用してコマーシャル マーケットプレース認証トークンを解決した後に取得されます。
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
{  // needed for validation of the activation request
  "planId": "gold", // purchased plan, cannot be empty
  "quantity": "" // purchased number of seats, can be empty if plan is not per seat
}
```

*応答コード:*

コード:200 - サブスクリプションは、Microsoft 側でサブスクライブ済みとしてマークされました。

この呼び出しには応答本文がありません。

コード:400 - 無効な要求: 検証に失敗しました。

* `planId` が要求ペイロードに存在しません。
* 要求ペイロード内の `planId` が、購入されたものと一致しません。
* 要求ペイロード内の `quantity` が、購入されたものと一致しません。
* SaaS サブスクリプションは、*Subscribed* または *Suspended* の状態です。

コード:403 - 許可されていません。 認証トークンが無効であるか、期限切れであるか、指定されませんでした。 要求が、認証トークンの作成に使用されたものとは異なる Azure AD アプリ ID で公開されたオファーの SaaS サブスクリプションにアクセスしようとしています。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。

コード:404 見つかりません。 SaaS サブスクリプションは *Unsubscribed* 状態です。

コード:500 - 内部サーバー エラーです。  API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://partner.microsoft.com/support/v2/?stage=1)にお問い合わせください。

#### <a name="get-list-of-all-subscriptions"></a>すべてのサブスクリプションの一覧を取得する

この API は、公開元がコマーシャル マーケットプレースに公開しているすべてのオファーのすべての購入済み SaaS サブスクリプションの一覧を取得します。  可能なすべての状態の SaaS サブスクリプションが返されます。 登録解除された SaaS サブスクリプションも返されます (この情報は Microsoft 側では削除されないため)。

この API では、1 ページあたり 100 件のページ分割された結果が返されます。

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

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
| `authorization`      |  この API 呼び出しを行っている公開元を識別する一意のアクセス トークン。  「[Azure AD アプリに基づいたトークンの取得](./pc-saas-registration.md#get-the-token-with-an-http-post)」の説明に従って、発行者によってトークン値が取得される場合、形式は `"Bearer <access_token>"` です。 |

*応答コード:*

コード:200 - この公開元が行ったオファーについて、公開元の認証トークンに基づいて既存のすべてのサブスクリプションの一覧を返します。

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
      "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary. For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
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
      "allowedCustomerOperations": ["Read"],
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

この API は、公開元がコマーシャル マーケットプレースに公開している SaaS オファーの、指定された購入済み SaaS サブスクリプションを取得します。 この呼び出しを使用して (すべてのサブスクリプションの一覧を取得するために API を呼び出すのではなく)、特定の SaaS サブスクリプションに関して入手できるすべての情報をその ID で取得します。

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
| `ApiVersion`        |   2018-08-31 を使用します。 |
| `subscriptionId`     |  購入済み SaaS サブスクリプションの一意識別子。  この ID は、Resolve API を使用してコマーシャル マーケットプレース認証トークンを解決した後に取得されます。 |

*要求ヘッダー:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`  |
|  `x-ms-requestid`    |  クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|  `x-ms-correlationid` |  クライアントでの操作に対する一意の文字列値。  このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|  `authorization`     | この API 呼び出しを行っている公開元を識別する一意のアクセス トークン。 「[Azure AD アプリに基づいたトークンの取得](./pc-saas-registration.md#get-the-token-with-an-http-post)」の説明に従って、発行者によってトークン値が取得される場合、形式は `"Bearer <access_token>"` です。  |

*応答コード:*

コード:200 - 提供されている `subscriptionId` に基づいて、SaaS サブスクリプションの詳細を返します。

*応答本文の例:*

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "name": "Contoso Cloud Solution", // SaaS subscription name
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
  "saasSubscriptionStatus": " Subscribed ", // Indicates the status of the operation: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
  "term": { // the period for which the subscription was purchased
    "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
    "endDate": "2019-06-29", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
    "termUnit": "P1M" //where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values.
  }
}
```

コード:403 - 許可されていません。 認証トークンが無効であるか、期限切れであるか、指定されませんでした。 要求が、認証トークンの作成に使用されたものとは異なる Azure AD アプリ ID で公開されているオファーの SaaS サブスクリプションにアクセスしようとしています。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。 

コード:404 見つかりません。  指定された `subscriptionId` の SaaS サブスクリプションが見つかりません。

コード:500 - 内部サーバー エラーです。  API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://partner.microsoft.com/support/v2/?stage=1)にお問い合わせください。

#### <a name="list-available-plans"></a>利用可能なプランを一覧表示する

この API は、このオファーの特定の購入の `subscriptionId` によって識別される SaaS オファーのすべてのプランを取得します。  この呼び出しを使用して、SaaS サブスクリプションの利用者がサブスクリプションで更新できるすべてのプライベートおよびパブリック プランの一覧を取得します。  返されたプランは、既に購入済みのプランと同じ地域で使用可能になります。

この呼び出しからは、既に購入済みのものに加えて、その顧客が使用可能なプランの一覧が返されます。  その一覧は、公開元のサイト上でエンド ユーザーに表示できます。  エンド ユーザーはサブスクリプション プランを、返された一覧にあるいずれかのプランに変更できます。  一覧にないプランに変更すると、失敗します。

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 を使用します。  |
|  `subscriptionId`    |  購入済み SaaS サブスクリプションの一意識別子。  この ID は、Resolve API を使用してコマーシャル マーケットプレース認証トークンを解決した後に取得されます。 |

*要求ヘッダー:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|   `content-type`     |  `application/json` |
|   `x-ms-requestid`   |  クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|  `x-ms-correlationid`  |  クライアントでの操作に対する一意の文字列値。  このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|  `authorization`     |  この API 呼び出しを行っている公開元を識別する一意のアクセス トークン。  「[Azure AD アプリに基づいたトークンの取得](./pc-saas-registration.md#get-the-token-with-an-http-post)」の説明に従って、発行者によってトークン値が取得される場合、形式は `"Bearer <access_token>"` です。  |

*応答コード:*

コード:200 - 既に購入済みのものを含め、既存の SaaS サブスクリプションで使用可能なすべてのプランの一覧を返します。

応答本文の例:

```json
{
  "plans": [
    {
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

コード:403 - 許可されていません。 認証トークンが無効であるか、期限切れであるか、指定されませんでした。  要求が、認証トークンの作成に使用されたものとは異なる Azure AD アプリ ID で公開されているオファーの SaaS サブスクリプションにアクセスしようとしている可能性があります。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。 

コード:500 - 内部サーバー エラーです。  API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://partner.microsoft.com/support/v2/?stage=1)にお問い合わせください。

#### <a name="change-the-plan-on-the-subscription"></a>サブスクリプションのプランを変更する

この API を使用して、SaaS サブスクリプションで購入済みの既存のプランを新しいプラン (パブリックまたはプライベート) に更新します。  公開元は、コマーシャル マーケットプレースで購入された SaaS サブスクリプションについて公開元側でプランが変更されるときに、この API を呼び出す必要があります。

この API は、"*アクティブ*" なサブスクリプションに対してのみ呼び出すことができます。  どのプランも他の既存のプラン (パブリックまたはプライベート) に変更できますが、それ自体には変更できません。  プライベート プランの場合、顧客のテナントは、パートナー センターでプランの対象ユーザーの一部として定義されている必要があります。

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 を使用します。  |
| `subscriptionId`     | 購入済み SaaS サブスクリプションの一意識別子。  この ID は、Resolve API を使用してコマーシャル マーケットプレース認証トークンを解決した後に取得されます。 |

*要求ヘッダー:*
 
|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。 この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  `x-ms-correlationid`  | クライアントでの操作に対する一意の文字列値。  このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  `authorization`     |  この API 呼び出しを行っている公開元を識別する一意のアクセス トークン。  「[Azure AD アプリに基づいたトークンの取得](./pc-saas-registration.md#get-the-token-with-an-http-post)」の説明に従って、発行者によってトークン値が取得される場合、形式は `"Bearer <access_token>"` です。 |

*要求ペイロードの例:*

```json
{
  "planId": "gold" // the ID of the new plan to be purchased
}
```

*応答コード:*

コード:202 - プランを変更する要求が受理され、非同期に処理されました。  パートナーは、プラン変更要求の成功または失敗を判断するために **Operation-Location の URL** をポーリングする必要があります。  操作の最終状態として "*失敗*"、"*成功*"、または "*競合*" を受け取るまで数秒ごとにポーリングを行います。  最終的な操作の状態は迅速に返されますが、場合によっては数分かかることがあります。

また、コマーシャル マーケットプレース側でアクションを正常に完了させる準備が整うと、パートナーは Webhook 通知を受け取ります。  その段階でようやく、公開元側でプランの変更を行います。

*応答ヘッダー:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  操作の状態を取得するための URL。  たとえば、「 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31` 」のように入力します。 |

コード:400 - 無効な要求: 検証エラー。

* 新しいプランが存在しないか、この特定の SaaS サブスクリプションでは使用できません。
* 新しいプランは、現在のプランと同じです。
* SaaS サブスクリプションの状態が *Subscribed* ではありません。
* SaaS サブスクリプションの更新操作が `allowedCustomerOperations` に含まれていません。

コード:403 - 許可されていません。 認証トークンが無効であるか、期限切れであるか、指定されませんでした。  要求が、認証トークンの作成に使用されたものとは異なる Azure AD アプリ ID で公開されているオファーの SaaS サブスクリプションにアクセスしようとしています。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。

コード:404 見つかりません。  `subscriptionId` を持つ SaaS サブスクリプションが見つかりません。

コード:500 - 内部サーバー エラーです。  API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://partner.microsoft.com/support/v2/?stage=1)にお問い合わせください。

>[!NOTE]
>一度に変更できるのは、プランまたはシート数のいずれかであり、両方ではありません。

>[!Note]
>この API は、エンド ユーザーから変更の明示的な承認を得た後にのみ呼び出すことができます。

#### <a name="change-the-quantity-of-seats-on-the-saas-subscription"></a>SaaS サブスクリプションのシート数を変更する

この API を使用して、SaaS サブスクリプションのために購入されたシートの数を更新 (増減) します。  公開元は、コマーシャル マーケットプレースで作成された SaaS サブスクリプションのシート数が公開元側から変更されるときに、この API を呼び出す必要があります。

シートの数は、現在のプランで許可されている数よりも多くすることはできません。  この場合、公開元は、シート数を変更する前にプランを変更する必要があります。

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 を使用します。  |
|  `subscriptionId`     | 購入済み SaaS サブスクリプションの一意識別子。  この ID は、Resolve API を使用してコマーシャル マーケットプレース認証トークンを解決した後に取得されます。  |

*要求ヘッダー:*
 
|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  `x-ms-correlationid`  | クライアントでの操作に対する一意の文字列値。  このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  `authorization`     | この API 呼び出しを行っている公開元を識別する一意のアクセス トークン。  「[Azure AD アプリに基づいたトークンの取得](./pc-saas-registration.md#get-the-token-with-an-http-post)」の説明に従って、発行者によってトークン値が取得される場合、形式は `"Bearer <access_token>"` です。  |

*要求ペイロードの例:*

```json
{
  "quantity": 5 // the new amount of seats to be purchased
}
```

*応答コード:*

コード:202 - 数量を変更する要求は受理され、非同期に処理されました。 パートナーは、数量変更要求の成功または失敗を判断するために **Operation-Location の URL** をポーリングする必要があります。  操作の最終状態として "*失敗*"、"*成功*"、または "*競合*" を受け取るまで数秒ごとにポーリングを行います。  最終的な操作の状態は迅速に返されますが、場合によっては数分かかることがあります。

また、コマーシャル マーケットプレース側でアクションを正常に完了させる準備が整うと、パートナーは Webhook 通知を受け取ります。  その段階でようやく、公開元側で数量の変更を行います。

*応答ヘッダー:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  操作状態を取得するリソースへのリンク。  たとえば、「 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31` 」のように入力します。  |

コード:400 - 無効な要求: 検証エラー。

* 新しい数量が現在のプランの制限を上回っているか下回っています。
* 新しい数量がありません。
* 新しい数量は、現在の数量と同じです。
* SaaS サブスクリプションの状態がサブスクライブ済みではありません。
* SaaS サブスクリプションの更新操作が `allowedCustomerOperations` に含まれていません。

コード:403 - 許可されていません。  認証トークンが無効であるか、期限切れであるか、指定されませんでした。  要求で、現在の発行者に属していないサブスクリプションにアクセスしようとしています。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。 

コード:404 見つかりません。  `subscriptionId` を持つ SaaS サブスクリプションが見つかりません。

コード:500 - 内部サーバー エラーです。  API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://partner.microsoft.com/support/v2/?stage=1)にお問い合わせください。

>[!Note]
>一度に変更できるのはプランまたは数量だけであり、両方ではありません。

>[!Note]
>この API は、変更についてエンド ユーザーから明示的な承認を得た後にのみ呼び出すことができます。

#### <a name="cancel-a-subscription"></a>サブスクリプションを取り消す

この API を使用して、指定した SaaS サブスクリプションの登録を解除します。  公開元はこの API を使用する必要はありません。SaaS サブスクリプションを取り消すには、顧客をコマーシャル マーケットプレースに誘導することをお勧めします。

コマーシャル マーケットプレースで購入された SaaS サブスクリプションの取り消しを公開元側で実装する場合、公開元はこの API を呼び出す必要があります。  この呼び出しが完了すると、サブスクリプションの状態が Microsoft 側で *Unsubscribed* になります。

次に示す猶予期間中にサブスクリプションが取り消された場合、顧客への請求は行われません。

* 月単位のサブスクリプションでは、アクティブ化の後 24 時間。
* 年単位のサブスクリプションでは、アクティブ化の後 14 日間。

前述の猶予期間が経過した後にサブスクリプションが取り消された場合は、顧客への請求が行われます。  顧客は、取り消しの直後に Microsoft 側の SaaS サブスクリプションにアクセスできなくなります。 

##### <a name="delete-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Delete `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 を使用します。  |
|  `subscriptionId`     | 購入済み SaaS サブスクリプションの一意識別子。  この ID は、Resolve API を使用してコマーシャル マーケットプレース認証トークンを解決した後に取得されます。  |

*要求ヘッダー:*
 
|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  `x-ms-correlationid`  | クライアントでの操作に対する一意の文字列値。  このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。  |
|  `authorization`     |  この API 呼び出しを行っている発行者を識別する一意のアクセス トークン。  「[Azure AD アプリに基づいたトークンの取得](./pc-saas-registration.md#get-the-token-with-an-http-post)」の説明に従って、発行者によってトークン値が取得される場合、形式は `"Bearer <access_token>"` です。 |

*応答コード:*

コード:202 - 登録を解除する要求が受理され、非同期に処理されました。  パートナーは、この要求の成功または失敗を判断するために **Operation-Location の URL** をポーリングする必要があります。  操作の最終状態として "*失敗*"、"*成功*"、または "*競合*" を受け取るまで数秒ごとにポーリングを行います。  最終的な操作の状態は迅速に返されますが、場合によっては数分かかることがあります。

また、コマーシャル マーケットプレース側でアクションが正常に完了すると、パートナーは Webhook 通知を受け取ります。  その段階でようやく、公開元側でサブスクリプションを取り消します。

*応答ヘッダー:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  操作状態を取得するリソースへのリンク。  たとえば、「 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31` 」のように入力します。 |

コード:400 - 無効な要求です。  削除は、この SaaS サブスクリプションの `allowedCustomerOperations` 一覧に含まれていません。

コード:403 - 許可されていません。  認証トークンが無効であるか、期限切れであるか、使用できません。 要求が、認証トークンの作成に使用されたものとは異なる Azure AD アプリ ID で公開されているオファーの SaaS サブスクリプションにアクセスしようとしています。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。

コード:404 見つかりません。  `subscriptionId` を持つ SaaS サブスクリプションが見つかりません。

コード:500 - 内部サーバー エラーです。 API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://partner.microsoft.com/support/v2/?stage=1)にお問い合わせください。

### <a name="operations-apis"></a>Operations API

#### <a name="list-outstanding-operations"></a>未処理の操作を一覧表示する 

指定した SaaS サブスクリプションの保留中の操作の一覧を取得します。  公開元は、[Operation patch API](#update-the-status-of-an-operation) を呼び出すことによって、返された操作を確認する必要があります。

現時点では、この API 呼び出しの応答として返されるのは **復帰の操作** のみです。

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|    `ApiVersion`    |  2018-08-31 を使用します。         |
|    `subscriptionId` | 購入済み SaaS サブスクリプションの一意識別子。  この ID は、Resolve API を使用してコマーシャル マーケットプレース認証トークンを解決した後に取得されます。  |

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
{
  "operations": [
    {
      "id": "<guid>", //Operation ID, should be provided in the operations patch API call
      "activityId": "<guid>", //not relevant
      "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription that is being reinstated
      "offerId": "offer1", // purchased offer ID
      "publisherId": "contoso",
      "planId": "silver", // purchased plan ID
      "quantity": "20", // purchased amount of seats, will be empty is not relevant
      "action": "Reinstate",
      "timeStamp": "2018-12-01T00:00:00", // UTC
      "status": "InProgress" // the only status that can be returned in this case
    }
  ]
}
```

保留中の復帰操作がない場合は、空の json を返します。

コード:400 - 無効な要求: 検証エラー。

コード:403 - 許可されていません。 認証トークンが無効であるか、期限切れであるか、指定されませんでした。  要求が、認証トークンの作成に使用されたものとは異なる Azure AD アプリ ID で公開されているオファーの SaaS サブスクリプションにアクセスしようとしています。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。 

コード:404 見つかりません。  `subscriptionId` を持つ SaaS サブスクリプションが見つかりません。

コード:500 - 内部サーバー エラーです。 API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://partner.microsoft.com/support/v2/?stage=1)にお問い合わせください。

#### <a name="get-operation-status"></a>操作状態を取得する

この API により、公開元が、指定した非同期操作の状態を追跡できるようになります:**Unsubscribe**、**ChangePlan**、または **ChangeQuantity**。

この API 呼び出しの `operationId` は、**Operation-Location** によって返される値、保留中の Operations API の取得の呼び出し、または Webhook 呼び出しで受け取った `<id>` パラメーター値から取得できます。

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 を使用します。  |
|  `subscriptionId`    |  購入済み SaaS サブスクリプションの一意識別子。  この ID は、Resolve API を使用してコマーシャル マーケットプレース認証トークンを解決した後に取得されます。 |
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
  "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription for which this operation is relevant
  "offerId": "offer1", // purchased offer ID
  "publisherId": "contoso",
  "planId": "silver", // purchased plan ID
  "quantity": "20", // purchased amount of seats
  "action": "ChangePlan", // Can be ChangePlan, ChangeQuantity or Reinstate
  "timeStamp": "2018-12-01T00:00:00", // UTC
  "status": "InProgress", // Possible values: NotStarted, InProgress, Failed, Succeeded, Conflict (new quantity / plan is the same as existing)
  "errorStatusCode": "",
  "errorMessage": ""
}
```

コード:403 - 許可されていません。 認証トークンが無効であるか、期限切れであるか、指定されませんでした。  要求が、認証トークンの作成に使用されたものとは異なる Azure AD アプリ ID で公開されているオファーの SaaS サブスクリプションにアクセスしようとしています。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。 

コード:404 見つかりません。  

* `subscriptionId` を持つサブスクリプションが見つかりません。
* `operationId` を持つ操作が見つかりません。

コード:500 - 内部サーバー エラーです。  API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://partner.microsoft.com/support/v2/?stage=1)にお問い合わせください。

#### <a name="update-the-status-of-an-operation"></a>操作の状態を更新する

この API を使用して、保留中の操作の状態を更新し、公開元側で操作が成功したか失敗したかを示します。

この API 呼び出しの `operationId` は、**Operation-Location** によって返される値、保留中の Operations API の取得の呼び出し、または Webhook 呼び出しで受け取った `<id>` パラメーター値から取得できます。

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*クエリ パラメーター:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|   `ApiVersion`       |  2018-08-31 を使用します。  |
|   `subscriptionId`   |  購入済み SaaS サブスクリプションの一意識別子。  この ID は、Resolve API を使用してコマーシャル マーケットプレース認証トークンを解決した後に取得されます。  |
|   `operationId`      |  完了する対象である操作の一意識別子。 |

*要求ヘッダー:*

|  パラメーター         | 値             |
|  ---------------   |  ---------------  |
|   `content-type`   | `application/json`   |
|   `x-ms-requestid`   |  クライアントからの要求を追跡するための一意の文字列値 (GUID を推奨)。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|   `x-ms-correlationid` |  クライアントでの操作に対する一意の文字列値。  このパラメーターによって、クライアント操作からのすべてのイベントがサーバー側のイベントに関連付けられます。  この値を指定しないと、値が生成され、応答ヘッダーに指定されます。 |
|  `authorization`     |  この API 呼び出しを行っている公開元を識別する一意のアクセス トークン。  「[Azure AD アプリに基づいたトークンの取得](./pc-saas-registration.md#get-the-token-with-an-http-post)」の説明に従って、発行者によってトークン値が取得される場合、形式は `"Bearer <access_token>"` です。 |

*要求ペイロードの例:*

```json
{
  "status": "Success" // Allowed Values: Success/Failure. Indicates the status of the operation on ISV side.
}
```

*応答コード:*

コード:200 - パートナー側での操作の完了を通知する呼び出し。  たとえば、この応答によって発行者側でのシートまたはプランの変更を通知できます。

コード:403
- Forbidden.  認証トークンが使用できないか、無効であるか、有効期限が切れています。 要求で、現在の発行者に属していないサブスクリプションにアクセスしようとしている可能性があります。
- Forbidden.  認証トークンが無効であるか、期限切れであるか、指定されませんでした。  要求が、認証トークンの作成に使用されたものとは異なる Azure AD アプリ ID で公開されているオファーの SaaS サブスクリプションにアクセスしようとしています。

多くの場合、このエラーは、[SaaS 登録](pc-saas-registration.md)を正しく実行していないことを表す症状です。

コード:404 見つかりません。

* `subscriptionId` を持つサブスクリプションが見つかりません。
* `operationId` を持つ操作が見つかりません。

コード:409 - 競合。  たとえば、より新しい更新が既に履行されています。

コード:500 - 内部サーバー エラーです。  API 呼び出しをやり直してください。  エラーが解決しない場合は、[Microsoft サポート](https://partner.microsoft.com/support/v2/?stage=1)にお問い合わせください。

## <a name="implementing-a-webhook-on-the-saas-service"></a>SaaS サービスでの Webhook の実装

パートナー センターで取引可能な SaaS オファーを作成する場合、パートナーは、HTTP エンドポイントとして使用される **接続 Webhook** の URL を指定します。  この Webhook は、Microsoft 側で発生する次のイベントを公開元に通知するために、POST HTTP 呼び出しを使用して Microsoft によって呼び出されます。

* SaaS サブスクリプションが *Subscribed* 状態である場合:
    * ChangePlan 
    * ChangeQuantity
    * [中断]
    * サブスクライブ解除
* SaaS サブスクリプションが *Suspended* 状態である場合:
    * Reinstate
    * サブスクライブ解除

発行者は、SaaS サブスクリプションの状態を Microsoft 側と一貫性のある状態に保つために、SaaS サービスに Webhook を実装する必要があります。  SaaS サービスでは、Webhook 通知に基づいてアクションを実行する前に、Get Operation API を呼び出して、Webhook 呼び出しとペイロード データを検証および承認する必要があります。  Webhook 呼び出しが処理されるとすぐに、発行者から Microsoft に HTTP 200 を返す必要があります。  この値は、Webhook 呼び出しが発行者によって正常に受信されたことを確認します。

>[!Note]
>Webhook URL サービスは、24 時間 365 日稼働し、いつでも Microsoft からの新しい呼び出しを受け取る準備ができている必要があります。  Microsoft には、Webhook 呼び出しの再試行ポリシー (再試行は 8 時間で 500 回) があります。ただし、公開元が呼び出しを受け入れて応答を返さない場合、Webhook で通知される操作は最終的に Microsoft 側で失敗します。

*Webhook のペイロードの例:*

```json
// end user changed a quantity of purchased seats for a plan on Microsoft side
{
  "id": "<guid>", // this is the operation ID to call with get operation API
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
// end user's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": "<guid>",
  "activityId": "<guid>",
  "subscriptionId": "<guid>",
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

開発プロセスを開始するには、発行者側でダミー API 応答を作成することをお勧めします。  これらの応答は、この記事に記載されているサンプル応答に基づくことができます。

発行者がエンドツーエンド テストの準備ができたら、次のようになります。

* SaaS オファーを限られたプレビュー対象ユーザーに公開し、プレビュー段階のままにします。
* テスト中に実際の請求費用が発生しないように、プランの価格を 0 に設定します。  別の方法として、0 以外の価格を設定し、すべてのテスト購入を 24 時間以内に取り消すこともできます。
* 実際の顧客のシナリオをシミュレートするために、すべてのフローがエンド ツー エンドで呼び出されていることを確認します。
* パートナーが購入および請求の完全なフローをテストする必要がある場合は、0 ドルより高い価格のオファーを使用して、それを行ってください。  購入に対して請求が行われ、請求書が生成されます。

購入フローは、オファーが公開されている場所に応じて、Azure portal または Microsoft AppSource サイトからトリガーできます。

"*プラン変更*"、"*数量変更*"、"*登録解除*" の各アクションは、発行者側からテストされます。  Microsoft 側からは、Azure portal と管理センター (Microsoft AppSource での購入が管理されているポータル) の両方から、"*登録解除*" をトリガーできます。  "*数量とプランの変更*" は、管理センターからのみ実行できます。

## <a name="get-support"></a>サポートを受ける

発行者サポート オプションについては、「[パートナー センターでの商業マーケットプレース プログラムのサポート](../support.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

コマーシャル マーケットプレースでの SaaS オファーのその他のオプションについては、[コマーシャル マーケットプレースの測定サービス API](marketplace-metering-service-apis.md) を参照してください。

[さまざまなプログラミング言語のクライアントを確認して、サンプルを使用します](https://github.com/microsoft/commercial-marketplace-samples)。
