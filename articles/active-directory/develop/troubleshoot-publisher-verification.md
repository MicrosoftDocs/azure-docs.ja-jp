---
title: 発行者の確認に関するトラブルシューティング | Azure
titleSuffix: Microsoft identity platform
description: Microsoft Graph API を呼び出すことによって、Microsoft ID プラットフォームの発行者確認のトラブルシューティングを行う方法について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 01/28/2021
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: acce282dcaac971716a97d7722543d78e28b7a34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102517669"
---
# <a name="troubleshoot-publisher-verification"></a>発行者の確認に関するトラブルシューティング
[発行者の確認](publisher-verification-overview.md)で、エラーが発生してプロセスを完了できない場合、または予期しない動作が発生する場合は、次の手順を行う必要があります。 

1. [要件](publisher-verification-overview.md#requirements)を調べて、すべてが満たされていることを確認します。

1. 指示に従って、[アプリを発行者確認済み](mark-app-as-publisher-verified.md)としてマークし、すべての手順が正常に実行されていることを確認します。

1. [一般的な問題](#common-issues)の一覧を確認します。

1. [Graph エクスプローラー](#making-microsoft-graph-api-calls)を使用して要求を再現し、追加情報を収集して、UI の問題を除外します。

## <a name="common-issues"></a>一般的な問題
以下に示すのは、プロセスの間に発生する可能性がある一般的な問題です。 

- **自分の Microsoft Partner Network ID (MPN ID) がわかりません、またはアカウントの主要な連絡先がわかりません** 
    1. [MPN の登録ページ](https://partner.microsoft.com/dashboard/account/v3/enrollment/joinnow/basicpartnernetwork/new)に移動します
    1. 組織のプライマリ Azure AD テナントにユーザー アカウントでサインインします 
    1. MPN アカウントが既に存在する場合は、これが認識され、アカウントに追加されます 
    1. MPN ID とプライマリ アカウントの連絡先が表示される[パートナー プロファイル ページ](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile)に移動します

- **自分の Azure AD 全体管理者 (会社の管理者またはテナント管理者とも呼ばれます) がわからない場合は、どのようにして見つけることができますか。アプリケーション管理者またはクラウド アプリケーション管理者はどうですか。**
    1. 組織のプライマリ テナントのユーザー アカウントを使用して、[Azure AD ポータル](https://aad.portal.azure.com)にサインインします
    1. [[ロールと管理者]](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) に移動します
    1. 目的の管理者ロールをクリックします。
    1. そのロールを割り当てられているユーザーの一覧が表示されます

- **自分の MPN アカウントの管理者がわかりません** [MPN のユーザー管理ページ](https://partner.microsoft.com/pcv/users)にアクセスし、ユーザー一覧をフィルター処理して、さまざまな管理者ロールのユーザーを確認します。

- **MPN ID が無効である、またはアクセス権を持っていない、というエラーが表示されます。**
    1. 自分の[パートナー プロファイル](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile)に移動し、次のことを確認します。 
        - MPN ID が正しい。 
        - エラーまたは "保留中のアクション" が表示されず、会社プロファイルとパートナー情報の両方の検証状態が "承認済み" または "成功" と表示される。
    1. [MPN テナント管理ページ](https://partner.microsoft.com/dashboard/account/v3/tenantmanagement)に移動し、アプリが登録されているテナント、およびサインインに使用しているユーザー アカウントのテナントが、関連付けられているテナントの一覧に含まれることを確認します。 さらにテナントを追加するには、[こちら](/partner-center/multi-tenant-account)の手順に従います。 追加したテナントのすべてのグローバル管理者には、パートナー センター アカウントの全体管理者特権が付与されることに注意してください。
    1. [MPN ユーザー管理ページ](https://partner.microsoft.com/pcv/users)に移動し、サインインに使用しているユーザーが、グローバル管理者、MPN 管理者、またはアカウント管理者のいずれかであることを確認します。パートナー センターでロールにユーザーを追加するには、[こちら](/partner-center/create-user-accounts-and-set-permissions)の手順に従います。

- **Azure AD ポータルにサインインすると、登録されているアプリが表示されません。なぜですか?** 
    アプリの登録は、このテナントの別のユーザー アカウント、個人またはコンシューマー アカウント、または別のテナントを使用して作成された可能性があります。 アプリの登録が作成されたテナントで、正しいアカウントを使用してサインインしていることを確認します。

- **多要素認証に関連するエラーが発生します。どうすればよいですか。** 
    [多要素認証](../fundamentals/concept-fundamentals-mfa-get-started.md)が有効になっており、かつサインインに使用しているユーザーとこのシナリオに **必要** であることを確認します。 たとえば、MFA には次のような条件があります。
    - サインインに使用しているユーザーには常に必要です
    - [Azure の管理に必要です](../conditional-access/howto-conditional-access-policy-azure-management.md)。
    - サインインに使用している[この種類の管理者には必要です](../conditional-access/howto-conditional-access-policy-admin-mfa.md)。

## <a name="making-microsoft-graph-api-calls"></a>Microsoft Graph API の呼び出しを行う 

問題が発生しても、UI に表示されている内容に基づいてその理由を把握できない場合は、Microsoft Graph の呼び出しを使用して、アプリ登録ポータルで実行できる操作と同じ操作を実行することで、さらにトラブルシューティングを行うと役に立つことがあります。

これらの要求を行う最も簡単な方法は、[Graph エクスプローラー](https://developer.microsoft.com/graph/graph-explorer)を使用することです。 また、[Postman](https://www.postman.com/) の使用や、PowerShell を使用した [Web 要求の呼び出し](/powershell/module/microsoft.powershell.utility/invoke-webrequest)など、他のオプションを検討することもできます。  

Microsoft Graph を使用して、アプリの確認済み発行者を設定および設定解除し、これらの操作のいずれかを実行した後で結果を確認できます。 結果は、アプリの登録に対応する[アプリケーション](/graph/api/resources/application) オブジェクトと、そのアプリからインスタンス化されている任意の[サービス プリンシパル](/graph/api/resources/serviceprincipal)のどちらでも確認できます。 それらのオブジェクト間の関係の詳細については、次を参照してください: 「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](app-objects-and-service-principals.md)」。  

いくつかの便利な要求の例を次に示します。  

### <a name="set-verified-publisher"></a>確認済み発行者を設定する 

Request

```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/setVerifiedPublisher 

{ 

    "verifiedPublisherId": "12345678" 

} 
```
 
Response 
```
204 No Content 
```
> [!NOTE]
> *verifiedPublisherID* は自分の MPN ID です。 

### <a name="unset-verified-publisher"></a>確認済み発行者を設定解除する 

要求:  
```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/unsetVerifiedPublisher 
```
 
Response 
```
204 No Content 
```
### <a name="get-verified-publisher-info-from-application"></a>アプリケーションから確認済み発行者の情報を取得する 
 
```
GET https://graph.microsoft.com/v1.0/applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec 

HTTP/1.1 200 OK 

{ 
    "id": "0cd04273-0d11-4e62-9eb3-5c3971a7cbec", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

### <a name="get-verified-publisher-info-from-service-principal"></a>サービス プリンシパルから確認済み発行者の情報を取得する 
```
GET https://graph.microsoft.com/v1.0/servicePrincipals/010422a7-4d77-4f40-9335-b81ef5c23dd4 

HTTP/1.1 200 OK 

{ 
    "id": "010422a7-4d77-4f40-9335-b81ef5c22dd4", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

## <a name="error-reference"></a>エラー リファレンス 

Microsoft Graph でのトラブルシューティングのとき、またはアプリ登録ポータルでのプロセスの間に発生する可能性のあるエラー コードの一覧を以下に示します。

### <a name="mpnaccountnotfoundornoaccess"></a>MPNAccountNotFoundOrNoAccess

指定した MPN ID (`MPNID`) が存在しないか、それへのアクセス権がありません。 有効な MPN ID を指定して、やり直してください。
    
サインインしているユーザーがパートナー センターの MPN アカウントの適切なロールのメンバーになっていないことが原因で最もよく発生します。対象となるロールの一覧については、「[必要条件](publisher-verification-overview.md#requirements)」をご覧ください。詳しくは、「[一般的な問題](#common-issues)」 をご覧ください。 また、アプリが登録されているテナントが MPN アカウントに追加されていないか、MPN ID が無効であるために発生する可能性もあります。

### <a name="mpnglobalaccountnotfound"></a>MPNGlobalAccountNotFound

指定した MPN ID (`MPNID`) が無効です。 有効な MPN ID を指定して、やり直してください。
    
パートナーの場所アカウント (PLA) に対応する MPN ID が指定されている場合に最もよく発生します。 パートナー グローバル アカウントのみがサポートされています。 詳しくは、[パートナー センターのアカウントの構造](/partner-center/account-structure)に関する記事をご覧ください。

### <a name="mpnaccountinvalid"></a>MPNAccountInvalid

指定した MPN ID (`MPNID`) が無効です。 有効な MPN ID を指定して、やり直してください。
    
間違った MPN ID が指定されていることが原因で最もよく発生します。

### <a name="mpnaccountnotvetted"></a>MPNAccountNotVetted

指定した MPN ID (`MPNID`) は、審査プロセスを完了していません。 パートナー センターでこのプロセスを完了してから、操作をやり直してください。 
    
MPN アカウントが[検証](/partner-center/verification-responses)プロセスを完了していない場合に最もよく発生します。

### <a name="nopublisheridonassociatedmpnaccount"></a>NoPublisherIdOnAssociatedMPNAccount

指定した MPN ID (`MPNID`) が無効です。 有効な MPN ID を指定して、やり直してください。 
   
間違った MPN ID が指定されていることが原因で最もよく発生します。

### <a name="mpniddoesnotmatchassociatedmpnaccount"></a>MPNIdDoesNotMatchAssociatedMPNAccount

指定した MPN ID (`MPNID`) が無効です。 有効な MPN ID を指定して、やり直してください。
    
間違った MPN ID が指定されていることが原因で最もよく発生します。

### <a name="applicationnotfound"></a>ApplicationNotFound

ターゲット アプリケーション (`AppId`) が見つかりません。 有効なアプリケーション ID を指定して、もう一度やり直してください。
    
Graph API 経由で検証が実行され、指定されたアプリケーションの ID が正しくない場合に最もよく発生します。 注 - アプリ ID やクライアント ID ではなく、アプリケーションの ID を指定する必要があります。

### <a name="b2ctenantnotallowed"></a>B2CTenantNotAllowed

この機能は、Azure AD B2C テナントではサポートされていません。

### <a name="emailverifiedtenantnotallowed"></a>EmailVerifiedTenantNotAllowed

この機能は、メールで確認されたテナントではサポートされていません。

### <a name="nopublisherdomainonapplication"></a>NoPublisherDomainOnApplication

ターゲット アプリケーション (`AppId`) には、パブリッシャー ドメインが設定されている必要があります。 パブリッシャー ドメインを設定してから、やり直してください。

アプリで[パブリッシャー ドメイン](howto-configure-publisher-domain.md)が構成されていない場合に発生します。

### <a name="publisherdomainmismatch"></a>PublisherDomainMismatch

ターゲット アプリケーションのパブリッシャー ドメイン (`publisherDomain`) が、パートナー センターでメール確認を実行するために使用されたドメイン (`pcDomain`) と一致しません。 これらのドメインが一致していることを確認してから、やり直してください。 
    
アプリの[パブリッシャー ドメイン](howto-configure-publisher-domain.md)と、Azure AD テナントに追加された[カスタム ドメイン](../fundamentals/add-custom-domain.md)のいずれもが、パートナー センターで電子メールの検証を実行するために使用されたドメインと一致しない場合に発生します。

### <a name="notauthorizedtoverifypublisher"></a>NotAuthorizedToVerifyPublisher

アプリケーション (<`AppId`) の確認済み発行者プロパティを設定することを承認されていません 
  
サインインしているユーザーが Azure AD の MPN アカウントの適切なロールのメンバーになっていないことが原因で最もよく発生します。対象となるロールの一覧については、「[必要条件](publisher-verification-overview.md#requirements)」をご覧ください。詳しくは、「[一般的な問題](#common-issues)」 をご覧ください。

### <a name="mpnidwasnotprovided"></a>MPNIdWasNotProvided

要求本文で MPN ID が指定されていないか、または要求の内容の種類が "application/json" ではありませんでした。

### <a name="msanotsupported"></a>MSANotSupported 

この機能は、Microsoft コンシューマー アカウントではサポートされていません。 Azure AD ユーザーによって Azure AD に登録されているアプリケーションのみがサポートされます。

### <a name="interactionrequired"></a>InteractionRequired

検証済みの発行者をアプリに追加しようとする前に多要素認証が実行されていない場合に発生します。 詳しくは、「[一般的な問題](#common-issues)」をご覧ください。 注:確認済み発行者を追加するときは、同じセッションで MFA を行う必要があります。 MFA が有効になっていても、そのセッションで行う必要がない場合、要求は失敗します。 

次のようなエラー メッセージが表示されます。"管理者によって構成が変更されたか、自分が新しい場所に移動したため、続行するには多要素認証を使用する必要があります。"

### <a name="unabletoaddpublisher"></a>UnableToAddPublisher

次のようなエラー メッセージが表示されます。"A verified publisher cannot be added to this application. (確認済み発行者をこのアプリケーションに追加できません。) Please contact your administrator for assistance. (管理者にお問い合わせください。)"

まず、[発行者の確認の要件](publisher-verification-overview.md#requirements)を満たしていることを確認します。

確認済み発行者を追加する要求が行われると、多数のシグナルを使用してセキュリティ リスク評価が実行されます。 要求が危険であると判断された場合は、エラーが返されます。 セキュリティ上の理由から、Microsoft は、要求が危険かどうかを判断するために使用される具体的な基準を公開していません。

## <a name="next-steps"></a>次のステップ

以上のすべての情報を確認しても、まだ Microsoft Graph からエラーを受け取る場合は、失敗した要求に関連する次の情報をできるだけ多く収集し、[Microsoft サポート](developer-support-help-options.md#create-an-azure-support-request)にお問い合わせください。

- Timestamp 
- CorrelationId 
- サインインしているユーザーの ObjectID または UserPrincipalName 
- ターゲット アプリケーションの ObjectId
- ターゲット アプリケーションの AppId
- アプリが登録されている TenantId
- MPN ID
- 行われた REST 要求 
- 返されるエラー コードとメッセージ