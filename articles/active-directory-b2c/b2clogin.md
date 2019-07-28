---
title: b2clogin.com にリダイレクト URL を設定する - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C のリダイレクト URL での b2clogin.com の使用について説明します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: marsma
ms.subservice: b2c
ms.openlocfilehash: 4d70fe3f3f19723cd37080ae09dce97bfd8f3d34
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66511686"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Azure Active Directory B2C の b2clogin.com にリダイレクト URL を設定する

Azure Active Directory (Azure AD) B2C アプリケーションへのサインアップおよびサインイン用に ID プロバイダーを設定する場合は、リダイレクト URL を指定する必要があります。 以前は login.microsoftonline.com が使用されていましたが、現在は b2clogin.com を使用する必要があります。

b2clogin.com を使用すると、次のような利点が加わります。

- Microsoft サービスによって Cookie ヘッダーで使用される領域が減ります。
- お使いの URL に、Microsoft への参照が含まれなくなりました。 たとえば、「 `https://your-tenant-name.b2clogin.com/tenant-id/oauth2/authresp` 」のように入力します。

>[!NOTE]
> 次のように、テナント名とテナントの GUID の両方を使用できます。
> * `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com` (引き続き `onmicrosoft.com` を参照します)
> * `https://your-tenant-name.b2clogin.com/your-tenant-guid` (この場合、Microsoft への参照は存在しません)
>
> ただし、ご自身の Azure Active Directory B2C テナントに対して_カスタム ドメイン_を使用することはできません。たとえば、`https://your-tenant-name.b2clogin.com/your-custom-domain-name` は動作 "_しません_"。

b2clogin.com を使用する際は、変更が必要になる可能性があるこれらの設定に配慮してください。

- ID プロバイダー アプリケーションのリダイレクト URL を、b2clogin.com を使用するように設定します。 
- Azure AD B2C アプリケーションを、ユーザー フロー参照とトークン エンドポイントに b2clogin.com を使用するように設定します。 
- MSAL を使用している場合は、**ValidateAuthority** プロパティを `false` に設定する必要があります。
- [ユーザー インターフェイスのカスタマイズ](active-directory-b2c-ui-customization-custom-dynamic.md)のため、CORS の設定で定義した任意の**許可される元のドメイン**を必ず変更します。  

## <a name="change-redirect-urls"></a>リダイレクト URL の変更

b2clogin.com を使用するには、お使いの ID プロバイダー アプリケーションの設定で信頼できる URL の一覧を探して、Azure AD B2C にリダイレクトするよう変更します。  現時点では、おそらくはいくつかの login.microsoftonline.com サイトにリダイレクトするよう設定されています。 

`your-tenant-name.b2clogin.com` が承認されるように、リダイレクト URL を変更する必要があります。 `your-tenant-name` をお使いの Azure AD B2C テナントの名前と置き換え、`/te` が URL 内に存在する場合は削除してください。 この URL には ID プロバイダーごとに若干のバリエーションがあるため、対応するページを確認して正確な URL を取得してください。

ID プロバイダーの設定情報については、次の記事を参照してください。

- [Microsoft アカウント](active-directory-b2c-setup-msa-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Google](active-directory-b2c-setup-goog-app.md)
- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [LinkedIn](active-directory-b2c-setup-li-app.md)
- [Twitter](active-directory-b2c-setup-twitter-app.md)
- [GitHub](active-directory-b2c-setup-github-app.md)
- [Weibo](active-directory-b2c-setup-weibo-app.md)
- [QQ](active-directory-b2c-setup-qq-app.md)
- [WeChat](active-directory-b2c-setup-wechat-app.md)
- [Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md)
- [カスタム OIDC](active-directory-b2c-setup-oidc-idp.md)

## <a name="update-your-application"></a>アプリケーションの更新

お使いの Azure AD B2C アプリケーションでは、おそらく、ユーザー フロー参照やトークン エンドポイントなどのいくつかの場所で `login.microsoftonline.com` が参照されています。  承認エンドポイント、トークン エンドポイント、および発行者が、`your-tenant-name.b2clogin.com` を使用するように更新されていることを確認してください。  

## <a name="set-the-validateauthority-property"></a>ValidateAuthority プロパティの設定

MSAL を使用している場合は、**ValidateAuthority** プロパティを `false` に設定します。 **ValidateAuthority** が `false` に設定されると、b2clogin.com へのリダイレクトが許可されます。 

次の例は、このプロパティを設定する方法を示しています。

[MSAL for .Net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) の場合:

```CSharp
 ConfidentialClientApplication client = new ConfidentialClientApplication(...); // can also be PublicClientApplication
 client.ValidateAuthority = false;
```

[MSAL for JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js) の場合:

```Javascript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```
