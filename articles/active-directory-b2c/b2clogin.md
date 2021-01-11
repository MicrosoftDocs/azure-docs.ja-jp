---
title: アプリケーションと API を b2clogin.com に移行する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C のリダイレクト URL での b2clogin.com の使用について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 79807e8e0f798a73063576a00b8d0c32cdfe5a4b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87005346"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Azure Active Directory B2C の b2clogin.com にリダイレクト URL を設定する

Azure Active Directory B2C (Azure AD B2C) アプリケーションへのサインアップおよびサインイン用に ID プロバイダーを設定する際に、リダイレクト URL を指定する必要があります。 アプリケーションと API での *login.microsoftonline.com* の参照は、これ以上実行しないでください。 代わりに、すべての新しいアプリケーションで *b2clogin.com* を使用し、既存のアプリケーションを *login.microsoftonline.com* から *b2clogin.com* に移行してください。

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>login.microsoftonline.com の廃止

2019 年 12 月 4 日、Azure AD B2C での login.microsoftonline.com のサポートが、**2020 年 12 月 4 日**で終了する予定であることが発表されました。

[Azure Active Directory B2C での login.microsoftonline.com が廃止されます](https://azure.microsoft.com/updates/b2c-deprecate-msol/)

login.microsoftonline.com の廃止は、2020年 12 月 4 日にすべての Azure AD B2C テナントで実施されます。この間、既存のテナントには b2clogin.com に移行するために 1 年の猶予期間が与えられます。 2019 年 12 月 4 日より後に作成された新しいテナントは、login.microsoftonline.com からの要求を受け付けません。 b2clogin.com エンドポイントでは、すべての機能が同じままです。

login.microsoftonline.com の廃止は、Azure Active Directory のテナントには影響しません。 この変更によって影響を受けるのは、Azure Active Directory B2C のテナントだけです。

## <a name="benefits-of-b2clogincom"></a>b2clogin.com の利点

リダイレクト URL として *b2clogin.com* を使用すると:

* Microsoft サービスによって Cookie ヘッダーで使用される領域が減ります。
* リダイレクト URL に、Microsoft への参照を含める必要がなくなります。
* カスタマイズされたページで、JavaScript クライアント側コードがサポートされます (現在、[プレビュー段階](user-flow-javascript-overview.md)です)。 *login.microsoftonline.com* を使用する場合は、セキュリティ上の制限により、JavaScript コードと HTML フォームの要素はカスタム ページから削除されます。

## <a name="overview-of-required-changes"></a>必要な変更の概要

アプリケーションを *b2clogin.com* に移行するには、いくつかの変更が必要になることがあります。

* ID プロバイダーのアプリケーション内のリダイレクト URL を、*b2clogin.com* を参照するように変更します。
* お使いの Azure AD B2C アプリケーションのユーザー フロー参照とトークン エンドポイントの参照で、*b2clogin.com* を使用するように更新します。
* [ユーザー インターフェイスのカスタマイズ](custom-policy-ui-customization.md)用に CORS の設定に定義されている**許可されたオリジン**を変更します。

## <a name="change-identity-provider-redirect-urls"></a>ID プロバイダーのリダイレクト URL を変更する

アプリケーションを作成した各 ID プロバイダーの Web サイトで、すべての信頼された URL を *login.microsoftonline.com* ではなく `your-tenant-name.b2clogin.com` にリダイレクトするように変更します。

b2clogin.com リダイレクト URL には、2 つの形式を使用できます。 1 つ目には、テナント ドメイン名の代わりにテナント ID (GUID) を使用することで、URL のどこにも "Microsoft" が表示されなくなるという利点があります。

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

2 つ目のオプションでは、テナント ドメイン名を `your-tenant-name.onmicrosoft.com` の形式で使用します。 次に例を示します。

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

どちらの形式でも:

* `{your-tenant-name}`を Azure AD B2C テナントの名前に置き換えます。
* URL 内に `/te` が存在する場合はそれを削除します。

## <a name="update-your-applications-and-apis"></a>アプリケーションと API を更新する

Azure AD B2C 対応のアプリケーションと API のコードでは、さまざまな場所で `login.microsoftonline.com` が参照されている可能性があります。 たとえば、コードにユーザー フローとトークン エンドポイントへの参照が含まれていることがあります。 代わりに `your-tenant-name.b2clogin.com` が参照されるように以下を更新します。

* Authorization endpoint (承認エンドポイント)
* Token endpoint (トークン エンドポイント)
* トークン発行者

たとえば、Contoso のサインアップ/サインイン ポリシーの機関エンドポイントは次のようになります。

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

OWIN ベースの Web アプリケーションを b2clogin.com に移行する方法の詳細については、「[OWIN ベースの Web API を b2clogin.com に移行する](multiple-token-endpoints.md)」を参照してください。

Azure AD B2C によって保護されている Azure API Management API を移行する方法については、「[Azure AD B2C を使用して Azure API をセキュリティで保護する](secure-api-management.md)」の「[b2clogin.com への移行](secure-api-management.md#migrate-to-b2clogincom)」セクションを参照してください。

## <a name="microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL)

### <a name="msalnet-validateauthority-property"></a>MSAL.NET の validateAuthority プロパティ

[MSAL.NET][msal-dotnet] v2 以前を使用している場合、*b2clogin.com* へのリダイレクトを可能にするには、クライアント インスタンス化の **ValidateAuthority** プロパティを `false` に設定します。 MSAL.NET v3 以降では、この値を `false` に設定する必要はありません。

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

### <a name="msal-for-javascript-validateauthority-property"></a>MSAL for JavaScript の validateAuthority プロパティ

[MSAL for JavaScript][msal-js] v1.2.2 以降を使用している場合は、**validateAuthority** プロパティを `false` に設定します。

```JavaScript
// MSAL.js v1.2.2 and earlier
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false // Required in MSAL.js v1.2.2 and earlier **ONLY**
  }
);
```

MSAL.js 1.3.0 以降で `validateAuthority: true` (既定値) を設定した場合は、`knownAuthorities` を使用して有効なトークン発行者も指定する必要があります。

```JavaScript
// MSAL.js v1.3.0+
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: true, // Supported in MSAL.js v1.3.0+
    knownAuthorities: ['tenant-name.b2clogin.com'] // Required if validateAuthority: true
  }
);
```

## <a name="next-steps"></a>次のステップ

OWIN ベースの Web アプリケーションを b2clogin.com に移行する方法の詳細については、「[OWIN ベースの Web API を b2clogin.com に移行する](multiple-token-endpoints.md)」を参照してください。

Azure AD B2C によって保護されている Azure API Management API を移行する方法については、「[Azure AD B2C を使用して Azure API をセキュリティで保護する](secure-api-management.md)」の「[b2clogin.com への移行](secure-api-management.md#migrate-to-b2clogincom)」セクションを参照してください。

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md
