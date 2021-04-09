---
title: ホーム領域検出ポリシーを使用して Azure AD でのサインインの自動高速化を防ぐ
description: フェデレーション IDP への domain_hint の自動高速化を防ぐ方法について説明します。
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/12/2021
ms.author: hirsin
ms.openlocfilehash: 53dfdfaf37695059d6d52428c2ba109970d9f7f7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589380"
---
# <a name="disable-auto-acceleration-to-a-federated-idp-during-user-sign-in-with-home-realm-discovery-policy"></a>ホーム領域検出ポリシーを使用してユーザー サインイン時のフェデレーション IDP への自動高速化を無効にする

[ホーム領域検出ポリシー](/graph/api/resources/homeRealmDiscoveryPolicy) (HRD) により、管理者がユーザー認証の方法と場所を制御するための複数の方法が提供されます。 HRD ポリシーの `domainHintPolicy` セクションを使用すると、フェデレーション ユーザーを常に Azure AD のサインイン ページにアクセスさせ、ドメイン ヒントによってフェデレーション IDP に自動高速化されないようにすることで、[FIDO](../authentication/howto-authentication-passwordless-security-key.md) などのクラウドで管理されている資格情報に移行させるのに役立ちます。

このポリシーは、管理者が制御または更新できないアプリケーションによってサインイン中にドメイン ヒントが追加される場合に必要です。  たとえば、`outlook.com/contoso.com` の場合、ユーザーは `&domain_hint=contoso.com` パラメーターが追加されたログイン ページに移動します。これは、ユーザーを `contoso.com` ドメインのフェデレーション IDP に直接高速化することが目的です。 管理されている資格情報を持つユーザーをフェデレーション IDP に移動させた場合、管理されている資格情報を使用したサインインはできません。その結果、サインイン エクスペリエンスのランダム化によるセキュリティの低下とユーザーの不満が発生します。 管理されている資格情報をロールアウトする管理者は、管理されている資格情報をユーザーが常に使用できるように、[このポリシーも設定する必要があります](#suggested-use-within-a-tenant)。

## <a name="domainhintpolicy-details"></a>DomainHintPolicy の詳細

HRD ポリシーの DomainHintPolicy セクションは JSON オブジェクトであり、管理者はこれを使用して、ドメイン ヒントの使用対象から特定のドメインとアプリケーションをオプトアウトできます。  機能的には、これにより Azure AD のサインイン ページが、ログイン要求に `domain_hint` パラメーターが存在しない場合と同様に動作するようになります。

### <a name="the-respect-and-ignore-policy-sections"></a>ポリシーの Respect (優先) と Ignore (無視) のセクション

|Section | 説明 | 値 |
|--------|---------|--------|
|`IgnoreDomainHintForDomains` |このドメイン ヒントが要求で送信された場合は、無視します。 |ドメイン アドレスの配列 (`contoso.com` など)。 `all_domains` もサポートされています|
|`RespectDomainHintForDomains`| 要求でアプリを自動高速化しないことが `IgnoreDomainHintForApps` によって指示されていても、このドメイン ヒントが要求で送信された場合は、それを優先します。 これは、ネットワーク内で非推奨になるドメイン ヒントのロールアウトを遅くするために使用されます。一部のドメインがまだ高速化の対象であることを指示できます。 | ドメイン アドレスの配列 (`contoso.com` など)。 `all_domains` もサポートされています|
|`IgnoreDomainHintForApps`| このアプリケーションからの要求にドメイン ヒントが含まれている場合は、無視します。 | アプリケーション ID (GUID) の配列。 `all_apps` もサポートされています|
|`RespectDomainHintForApps` |このアプリケーションからの要求にドメイン ヒントが含まれている場合は、`IgnoreDomainHintForDomains` にそのドメインが含まれていても、それを優先します。 ドメイン ヒントなしで中断されていることを検出した場合に、一部のアプリの動作を維持するために使用します。 | アプリケーション ID (GUID) の配列。 `all_apps` もサポートされています|

### <a name="policy-evaluation"></a>ポリシーの評価

DomainHintPolicy ロジックは、ドメイン ヒントを含む受信要求ごとに実行され、要求に含まれる 2 つのデータ、つまりドメイン ヒント内のドメインと、クライアント ID (アプリ) に基づいて高速化されます。 要するに、ドメインまたはアプリを "優先" する方が、特定のドメインまたはアプリケーションに関するドメイン ヒントを "無視" する命令よりも優先されます。

1. ドメイン ヒント ポリシーがない場合、あるいは 4 つのセクションのいずれにおいてもアプリまたはドメイン ヒントが参照されていない場合は、[HRD ポリシーの残りの部分が評価されます](configure-authentication-for-federated-users-portal.md#priority-and-evaluation-of-hrd-policies)。
1. 要求で `RespectDomainHintForApps` または `RespectDomainHintForDomains` セクションのどちらか一方 (または両方) にアプリまたはドメイン ヒントが含まれている場合、ユーザーは要求どおりにフェデレーション IDP に自動高速化されます。
1. 要求の `IgnoreDomainHintsForApps` または `IgnoreDomainHintsForDomains` のどちらか一方 (または両方) でアプリまたはドメイン ヒントが参照されていて、"Respect" セクションでは参照されていない場合、要求は自動高速化されず、ユーザーは Azure AD のログイン ページにとどまってユーザー名を指定します。

ユーザーは、ログイン ページでユーザー名を入力すれば、管理されている資格情報 (登録されている場合) を使用できます。  管理されている資格情報を使用しないことを選択した場合、または登録していない場合は、通常どおり、資格情報を入力するためのフェデレーション IDP に移動します。

## <a name="suggested-use-within-a-tenant"></a>テナント内での推奨される使用方法

フェデレーション ドメインの管理者は、HRD ポリシーのこのセクションを 4 フェーズ計画で設定する必要があります。 この計画の目的は、最終的にテナント内のすべてのユーザーが、ドメインやアプリケーションに関係なく管理されている資格情報を使用できるようにすることです。ただし、`domain_hint` の使用に対して固定された依存関係を持つアプリは除きます。  この計画は、管理者がこのようなアプリを検出し、新しいポリシーの適用対象から除外し、テナントの残りの部分への変更のロールアウトを続行するのに役立ちます。

1. この変更を最初にロールアウトするドメインを選択します。  これがテスト ドメインになります。そのため、UX の変更 (つまり、別のログイン ページが表示される) を反映しやすいものを選択してください。  これにより、このドメイン名を使用するすべてのアプリケーションからのすべてのドメイン ヒントが無視されます。 テナントの既定の HRD ポリシーで、このポリシーを[設定](#configuring-policy-through-graph-explorer)します。

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": [] 
} 
```

2. テスト ドメインのユーザーからフィードバックを収集します。 この変更の結果として中断されたアプリケーションの詳細を収集します。それらはドメイン ヒントの使用に対して依存関係があるため、更新する必要があります。 ここでは、`RespectDomainHintForApps` セクションに追加します。

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

3. 新しいドメインへのポリシーのロールアウトを引き続き拡大し、より多くのフィードバックを収集します。

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com", "otherDomain.com", "anotherDomain.com"], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

4. ロールアウトを完了します。ターゲットは、引き続き高速化する必要があるドメインを除外した、すべてのドメインです。

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "*" ], 
    "RespectDomainHintForDomains": ["guestHandlingDomain.com"], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

手順 4 を完了すると、`guestHandlingDomain.com` 以外のすべてのユーザーが、フェデレーション IDP への自動高速化を発生させるドメイン ヒントがあったとしても、Azure AD サインイン ページでサインインできるようになります。  これの例外は、サインインを要求しているアプリが除外対象の 1 つである場合です。そのようなアプリについては、すべてのドメイン ヒントが引き続き受け入れられます。

## <a name="configuring-policy-through-graph-explorer"></a>Graph エクスプローラーを使用したポリシーの構成

Microsoft Graph を使用して、[HRD ポリシー](/graph/api/resources/homeRealmDiscoveryPolicy)を通常どおりに設定します。  

1. [Graph エクスプローラー](https://developer.microsoft.com/graph/graph-explorer)で、Policy.ReadWrite.ApplicationConfiguration 権限を付与します。  
1. URL は `https://graph.microsoft.com/v1.0/policies/homeRealmDiscoveryPolicies` を使用します。
1. 新しいポリシーをこの URL に POST します。または、既存のものを上書きする場合は `/policies/homerealmdiscoveryPolicies/{policyID}` に PATCH します。

POST または PATCH の内容:

```json
{
    "displayName":"Home Realm Discovery Domain Hint Exclusion Policy",
    "definition":[
        "{\"HomeRealmDiscoveryPolicy\" : {\"DomainHintPolicy\": { \"IgnoreDomainHintForDomains\": [ \"Contoso.com\" ], \"RespectDomainHintForDomains\": [], \"IgnoreDomainHintForApps\": [\"sample-guid-483c-9dea-7de4b5d0a54a\"], \"RespectDomainHintForApps\": [] } } }"
    ],
    "isOrganizationDefault":true
}
```

Graph を使用する場合は、必ずスラッシュを使用して JSON セクション `Definition` をエスケープしてください。  

`isOrganizationDefault` は true である必要がありますが、displayName と定義は変更できます。

## <a name="next-steps"></a>次のステップ

* [パスワードなしのセキュリティ キー サインインを有効にする](../authentication/howto-authentication-passwordless-security-key.md)
* [Microsoft Authenticator アプリでパスワードなしのサインインを有効にする](../authentication/howto-authentication-passwordless-phone.md)