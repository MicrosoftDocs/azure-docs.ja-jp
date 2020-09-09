---
title: 発行者確認のトラブルシューティング - Microsoft ID プラットフォーム | Microsoft
description: Microsoft Graph API を呼び出すことによって、Microsoft ID プラットフォームの発行者確認 (プレビュー) のトラブルシューティングを行う方法について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 3069e3caf81d9bb2f809b21c88383c419e3b90b3
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282978"
---
# <a name="troubleshoot-publisher-verification-preview"></a>発行者確認のトラブルシューティング (プレビュー)
[発行者の確認 (プレビュー)](publisher-verification-overview.md) で、エラーが発生してプロセスを完了できない場合、または予期しない動作が発生する場合は、次の手順を行う必要があります。 

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

- **自分の Azure AD グローバル管理者 (会社の管理者またはテナント管理者とも呼ばれます) がわからない場合は、どのようにして見つけることができますか。アプリ管理者または別の管理者ロールの場合はどうですか。**
    1. 組織のプライマリ テナントのユーザー アカウントを使用して、[Azure AD ポータル](https://aad.portal.azure.com)にサインインします
    1. [[ロールと管理者]](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) に移動します
    1. [グローバル管理者] または目的の管理者ロールをクリックします
    1. そのロールを割り当てられているユーザーの一覧が表示されます

- **自分の MPN アカウントの管理者がわかりません** [MPN のユーザー管理ページ](https://partner.microsoft.com/pcv/users)にアクセスし、ユーザー一覧をフィルター処理して、さまざまな管理者ロールのユーザーを確認します。

- **MPN ID が無効である、またはアクセス権を持っていない、というエラーが表示されます。**
    1. 自分の[パートナー プロファイル](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile)に移動し、次のことを確認します。 
        - MPN ID が正しい。 
        - エラーまたは "保留中のアクション" が表示されず、会社プロファイルとパートナー情報の両方の検証状態が "承認済み" または "成功" と表示される。
    1. [MPN テナント管理ページ](https://partner.microsoft.com/dashboard/account/v3/tenantmanagement)に移動し、アプリが登録されているテナント、およびサインインに使用しているユーザー アカウントのテナントが、関連付けられているテナントの一覧に含まれることを確認します。
    1. [MPN ユーザー管理ページ](https://partner.microsoft.com/pcv/users)に移動し、サインインに使用しているユーザーが、グローバル管理者、MPN 管理者、またはアカウント管理者のいずれかであることを確認します。

- **Azure AD ポータルにサインインすると、登録されているアプリが表示されません。なぜですか?** 
    アプリの登録が別のユーザー アカウントまたは別のテナントを使用して作成されている可能性があります。 アプリの登録が作成されたテナントで、正しいアカウントを使用してサインインしていることを確認します。

- **Azure AD でのアプリ登録の所有者はどうすればわかりますか。** 
    アプリが登録されているテナントにサインインしたら、[アプリの登録] ブレードに移動し、アプリをクリックして、[所有者] をクリックします。

## <a name="making-microsoft-graph-api-calls"></a>Microsoft Graph API の呼び出しを行う 

問題が発生しても、UI に表示されている内容に基づいてその理由を把握できない場合は、Microsoft Graph の呼び出しを使用して、アプリ登録ポータルで実行できる操作と同じ操作を実行することで、さらにトラブルシューティングを行うと役に立つことがあります。 プレビュー フェーズの間は、これらの API は Microsoft Graph の /beta エンドポイントでのみ使用できます。  

これらの要求を行う最も簡単な方法は、[Graph エクスプローラー](https://developer.microsoft.com/graph/graph-explorer)を使用することです。 また、[Postman](https://www.postman.com/) の使用や、PowerShell を使用した [Web 要求の呼び出し](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7)など、他のオプションを検討することもできます。  

Microsoft Graph を使用して、アプリの確認済み発行者を設定および設定解除し、これらの操作のいずれかを実行した後で結果を確認できます。 結果は、アプリの登録に対応する[アプリケーション](/graph/api/resources/application?view=graph-rest-beta) オブジェクトと、そのアプリからインスタンス化されている任意の[サービス プリンシパル](/graph/api/resources/serviceprincipal?view=graph-rest-beta)のどちらでも確認できます。 それらのオブジェクト間の関係の詳細については、次を参照してください: 「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](app-objects-and-service-principals.md)」。  

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
GET https://graph.microsoft.com/beta/applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec 

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
GET https://graph.microsoft.com/beta/servicePrincipals/010422a7-4d77-4f40-9335-b81ef5c23dd4 

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

指定した MPN ID (<MPNID>) が存在しないか、それへのアクセス権がありません。 有効な MPN ID を指定して、やり直してください。 

### <a name="mpnglobalaccountnotfound"></a>MPNGlobalAccountNotFound     

指定した MPN ID (<MPNID>) が無効です。 有効な MPN ID を指定して、やり直してください。 

### <a name="mpnaccountinvalid"></a>MPNAccountInvalid    

指定した MPN ID (<MPNID>) が無効です。 有効な MPN ID を指定して、やり直してください。 

### <a name="mpnaccountnotvetted"></a>MPNAccountNotVetted  

指定した MPN ID (<MPNID>) は、審査プロセスを完了していません。 パートナー センターでこのプロセスを完了してから、操作をやり直してください。 

### <a name="nopublisheridonassociatedmpnaccount"></a>NoPublisherIdOnAssociatedMPNAccount  

指定した MPN ID (<MPNID>) が無効です。 有効な MPN ID を指定して、やり直してください。 

### <a name="mpniddoesnotmatchassociatedmpnaccount"></a>MPNIdDoesNotMatchAssociatedMPNAccount    

指定した MPN ID (<MPNID>) が無効です。 有効な MPN ID を指定して、やり直してください。 

### <a name="applicationnotfound"></a>ApplicationNotFound  

ターゲット アプリケーション (<AppId>) が見つかりません。 有効なアプリケーション ID を指定して、もう一度やり直してください。 

### <a name="b2ctenantnotallowed"></a>B2CTenantNotAllowed  

この機能は、Azure AD B2C テナントではサポートされていません。 

### <a name="emailverifiedtenantnotallowed"></a>EmailVerifiedTenantNotAllowed    

この機能は、メールで確認されたテナントではサポートされていません。 

### <a name="nopublisherdomainonapplication"></a>NoPublisherDomainOnApplication   

ターゲット アプリケーション (<AppId>) には、パブリッシャー ドメインが設定されている必要があります。 パブリッシャー ドメインを設定してから、やり直してください。 

### <a name="publisherdomainisnotdnsverified"></a>PublisherDomainIsNotDNSVerified  

ターゲット アプリケーションのパブリッシャー ドメイン (<publisherDomain>) は、このテナントの検証済みドメインではありません。 DNS の検証を使用してテナント ドメインを確認してから、やり直してください。 

### <a name="publisherdomainmismatch"></a>PublisherDomainMismatch  

ターゲット アプリケーションのパブリッシャー ドメイン (<publisherDomain>) が、パートナー センターでメール確認を実行するために使用されたドメイン (<pcDomain>) と一致しません。 これらのドメインが一致していることを確認してから、やり直してください。 

### <a name="notauthorizedtoverifypublisher"></a>NotAuthorizedToVerifyPublisher   

アプリケーション (<AppId>) の確認済み発行者プロパティを設定することを承認されていません 

### <a name="mpnidwasnotprovided"></a>MPNIdWasNotProvided  

要求本文で MPN ID が指定されていないか、または要求の内容の種類が "application/json" ではありませんでした。 

### <a name="msanotsupported"></a>MSANotSupported  

この機能は、Microsoft コンシューマー アカウントではサポートされていません。 Azure AD ユーザーによって Azure AD に登録されているアプリケーションのみがサポートされます。 

## <a name="next-steps"></a>次のステップ

以上のすべての情報を確認しても、まだ Microsoft Graph からエラーを受け取る場合は、失敗した要求に関連する次の情報をできるだけ多く収集し、[Microsoft サポート](developer-support-help-options.md#open-a-support-request)にお問い合わせください。

- Timestamp 
- CorrelationId 
- サインインしているユーザーの ObjectID または UserPrincipalName 
- ターゲット アプリケーションの ObjectId
- ターゲット アプリケーションの AppId
- アプリが登録されている TenantId
- MPN ID
- 行われた REST 要求 
- 返されるエラー コードとメッセージ 
