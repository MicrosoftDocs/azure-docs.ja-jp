---
title: リダイレクト URL を b2clogin.com に設定する - Azure Active Directory B2C
description: Azure Active Directory B2C のリダイレクト URL での b2clogin.com の使用について説明します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: dbc366daac89f44d4b084081590124f81ff9cc9c
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533747"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Azure Active Directory B2C の b2clogin.com にリダイレクト URL を設定する

Azure Active Directory B2C (Azure AD B2C) アプリケーションへのサインアップおよびサインイン用に ID プロバイダーを設定する際に、リダイレクト URL を指定する必要があります。 アプリケーションと API での *login.microsoftonline.com* の参照は、これ以上実行しないでください。 代わりに、すべての新しいアプリケーションで *b2clogin.com* を使用し、既存のアプリケーションを *login.microsoftonline.com* から *b2clogin.com* に移行してください。

## <a name="benefits-of-b2clogincom"></a>b2clogin.com の利点

リダイレクト URL として *b2clogin.com* を使用すると:

* Microsoft サービスによって Cookie ヘッダーで使用される領域が減ります。
* リダイレクト URL に、Microsoft への参照を含める必要がなくなります。
* カスタマイズされたページで、JavaScript クライアント側コードがサポートされます (現在、[プレビュー段階](user-flow-javascript-overview.md)です)。 *login.microsoftonline.com* を使用する場合は、セキュリティ上の制限により、JavaScript コードと HTML フォームの要素はカスタム ページから削除されます。

## <a name="overview-of-required-changes"></a>必要な変更の概要

アプリケーションを *b2clogin.com* に移行するには、いくつかの変更が必要になることがあります。

* ID プロバイダーのアプリケーション内のリダイレクト URL を、*b2clogin.com* を参照するように変更します。
* お使いの Azure AD B2C アプリケーションのユーザー フロー参照とトークン エンドポイントの参照で、*b2clogin.com* を使用するように更新します。
* [ユーザー インターフェイスのカスタマイズ](active-directory-b2c-ui-customization-custom-dynamic.md)用に CORS の設定に定義されている**許可されたオリジン**を変更します。

## <a name="change-identity-provider-redirect-urls"></a>ID プロバイダーのリダイレクト URL を変更する

アプリケーションを作成した各 ID プロバイダーの Web サイトで、すべての信頼された URL を *login.microsoftonline.com* ではなく `your-tenant-name.b2clogin.com` にリダイレクトするように変更します。

b2clogin.com リダイレクト URL には、2 つの形式を使用できます。 1 つ目には、テナント ドメイン名の代わりにテナント ID (GUID) を使用することで、URL のどこにも "Microsoft" が表示されなくなるという利点があります。

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

2 つ目のオプションでは、テナント ドメイン名を `your-tenant-name.onmicrosoft.com` の形式で使用します。 例:

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

## <a name="microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL)

### <a name="validateauthority-property"></a>ValidateAuthority プロパティ

[MSAL.NET][msal-dotnet] v2 以前を使用している場合、*b2clogin.com* へのリダイレクトを可能にするには、クライアント インスタンス化の **ValidateAuthority** プロパティを `false` に設定します。 この設定は、MSAL.NET v3 以降では必要ありません。

```CSharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

[MSAL for JavaScript][msal-js] を使用している場合:

```JavaScript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md