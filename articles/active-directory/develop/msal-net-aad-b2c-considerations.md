---
title: Azure AD B2C (.NET 用 Microsoft Authentication Library) | Azure
description: .NET 用 Microsoft 認証ライブラリ (MSAL.NET) で Azure AD B2C を使用する場合の固有の考慮事項について説明します。
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7444ecfd7a59224d0f08390385c508e4ecc40ddd
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532717"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>MSAL.NET を使用してソーシャル ID でユーザーをサインインさせる

MSAL.NET を使用して、[Azure Active Directory B2C (Azure AD B2C)](https://aka.ms/aadb2c) でソーシャル ID を持つユーザーをサインインさせることができます。 Azure AD B2C はポリシーの概念を基に構築されています。 MSAL.NET では、ポリシーを指定するということは、機関を提供することです。

- パブリック クライアント アプリケーションをインスタンス化するときは、機関でポリシーを指定する必要があります。
- ポリシーを適用するときは、`authority` パラメーターを含む `AcquireTokenInteractive` のオーバーライドを呼び出す必要があります。

このページは、MSAL 3.x に対するものです。 MSAL 2.x に関心がある場合は、「[Azure AD B2C specifics in MSAL 2.x (MSAL 2.x での Azure AD B2C の詳細)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x)」をご覧ください。

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Azure AD B2C のテナントとポリシーに対する機関

使用する機関は `https://login.microsoftonline.com/tfp/{tenant}/{policyName}` です。

- `tenant` は、Azure AD B2C テナントの名前です。 
- `policyName` は、適用するポリシーの名前です (たとえば、サインイン/サインアップの場合は "b2c_1_susi")。

Azure AD B2C からの現在のガイダンスでは、機関として `b2clogin.com` を使用します。 たとえば、「 `$"https://{your-tenant-name}.b2clogin.com/tfp/{your-tenant-ID}/{policyname}"` 」のように入力します。 詳しくは、こちらの[ドキュメント](/azure/active-directory-b2c/b2clogin)をご覧ください。

## <a name="instantiating-the-application"></a>アプリケーションをインスタンス化する

アプリケーションをビルドする場合は、機関を提供する必要があります。

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://fabrikamb2c.b2clogin.com/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>ポリシーを適用するためのトークンを取得する

パブリック クライアント アプリケーションで Azure AD B2C の保護された API に対するトークンを取得するには、機関でオーバーライドを使用する必要があります。

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireToken(scopes, parentWindow)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .ExecuteAsync();
```

を以下に置き換えます。

- `policy` は前の文字列の 1 つです (たとえば `PolicySignUpSignIn`)。
- `GetAccountByPolicy(IEnumerable<IAccount>, string)` は特定のポリシーのアカウントを検索するメソッドです。 例:

  ```csharp
  private IAccount GetAccountByPolicy(IEnumerable<IAccount> accounts, string policy)
  {
   foreach (var account in accounts)
   {
    string userIdentifier = account.HomeAccountId.ObjectId.Split('.')[0];
    if (userIdentifier.EndsWith(policy.ToLower()))
     return account;
   }
   return null;
  }
  ```

現在、ポリシーの適用は (たとえば、エンド ユーザーが自分のプロファイルを編集したり、パスワードをリセットしたりできるようにする)、`AcquireTokenInteractive` を呼び出すことによって行われます。 これら 2 つのポリシーの場合は、返されたトークン/認証結果を使用しません。

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>EditProfile ポリシーと ResetPassword ポリシーの特殊なケース

エンド ユーザーがソーシャル ID でサインインし、自分のプロファイルを編集するエクスペリエンスを提供する場合は、Azure AD B2C EditProfile ポリシーを適用します。 そのためには、`AcquireTokenInteractive` を呼び出し、そのポリシーに対する特定の期間を指定して、アカウント選択ダイアログが表示されないように Prompt を `Prompt.NoPrompt` に設定します (ユーザーは既にサインインしているため)

```csharp
private async void EditProfileButton_Click(object sender, RoutedEventArgs e)
{
 IEnumerable<IAccount> accounts = await app.GetAccountsAsync();
 try
 {
  var authResult = await app.AcquireToken(scopes:App.ApiScopes)
                               .WithAccount(GetUserByPolicy(accounts, App.PolicyEditProfile)),
                               .WithPrompt(Prompt.NoPrompt),
                               .WithB2CAuthority(App.AuthorityEditProfile)
                               .ExecuteAsync();
  DisplayBasicTokenInfo(authResult);
 }
 catch
 {
  . . .
 }
}
```
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Azure AD B2C でのリソース所有者パスワード資格情報 (ROPC)
ROPC フローについて詳しくは、こちらの[ドキュメント](v2-oauth-ropc.md)をご覧ください。

ユーザーにパスワードをたずねるアプリケーションは安全でないため、このフローは**推奨されません**。 この問題の詳細については、[この記事](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)を参照してください。 

ユーザー名/パスワードを使用すると、いくつかのことを利用できなくなります。
- 最新の ID のコア テナント: パスワードがフィッシングされて再生されます。 インターセプトできる共有シークレットのこの概念を使用しているためです。 これは、パスワードなしとは互換性がありません。
- MFA を実行する必要のあるユーザーがサインインできない (対話式操作がないため)。
- ユーザーがシングル サインオンできない。

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Azure AD B2C で ROPC フローを構成する
お使いの Azure AD B2C テナントで、新しいユーザー フローを作成し、 **[ROPC を使用してサインイン]** を選択します。 これにより、テナントの ROPC ポリシーが有効になります。 詳しくは、[リソース所有者のパスワード資格情報フローの構成](/azure/active-directory-b2c/configure-ropc)に関する記事をご覧ください。

`IPublicClientApplication` には次のメソッドが含まれています。
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

このメソッドは、パラメーターとして次のものを受け取ります。
- アクセス トークンを要求する "*スコープ*"。
- "*ユーザー名*"。
- ユーザーの SecureString の "*パスワード*"。

ROPC ポリシーが含まれる機関を使用してください。

### <a name="limitations-of-the-ropc-flow"></a>ROPC フローの制限事項
 - ROPC フローは、**ローカル アカウントでのみ動作します** (メール アドレスまたはユーザー名を使用して Azure AD B2C に登録します)。 Azure AD B2C によってサポートされる ID プロバイダーのいずれかとフェデレーションした場合、このフローは機能しません (Facebook、Google など)。
 - 現在、MSAL から ROPC フローを実装すると、**Azure AD B2C から id_token が返されません**。 つまり、Account オブジェクトを作成できないため、キャッシュには Account およびユーザーが存在しません。 このシナリオでは、AcquireTokenSilent フローは機能しません。 ただし、ROPC では UI が表示されないため、ユーザー エクスペリエンスへの影響はありません。

## <a name="google-auth-and-embedded-webview"></a>Google 認証と埋め込み WebView

ID プロバイダーとして Google を使用している Azure AD B2C 開発者の場合は、システム ブラウザーを使用することをお勧めします。Google では、[埋め込み WebView から認証を行う](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)ことはできません。 現在、Google で信頼されている機関は `login.microsoftonline.com` です。 この機関を使用すると、埋め込み WebView で動作します。 しかし、`b2clogin.com` は Google で信頼された機関ではないため、ユーザーは認証を行うことができません。

状況が変わったら、Wiki およびこの[問題](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688)に対する更新を提供します。

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>MSAL.Net の Azure AD B2C でのキャッシュ 

### <a name="known-issue-with-azure-ad-b2c"></a>Azure AD B2C での既知の問題

MSAL.Net では[トークン キャッシュ](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet)がサポートされています。 トークン キャッシュ キーは、ID プロバイダーによって返される要求に基づきます。 現在、MSAL.Net では、トークン キャッシュ キーを作成するために 2 つの要求が必要です。  
- Azure AD テナント ID である `tid` 
- `preferred_username` 

これらの要求はどちらも、Azure AD B2C のシナリオの多くにはありません。 

お客様への影響として、ユーザー名フィールドを表示するときに、値として "トークンの応答にありません" と表示されることがあります。 その場合は、ソーシャル アカウントと外部 ID プロバイダー (IDP) での制限により、Azure AD B2C で preferred_username に対する IdToken で値が返されないためです。 Azure AD は誰がユーザーかわかっているので preferred_username に対する値を返しますが、Azure AD B2C では、ユーザーはローカル アカウント、Facebook、Google、GitHub などでサインインできるので、Azure AD B2C が preferred_username に対して使用する一貫した値がありません。 MSAL による ADAL とのキャッシュ互換性のロールアウトから障害を取り除くため、IdToken が preferred_username に対して何も返さないときは、Azure AD B2C アカウントを扱う場合に、Microsoft 側では "トークンの応答にありません" を使用することにしました。 MSAL は、ライブラリ間でキャッシュの互換性を維持するため、preferred_username の値を返す必要があります。

### <a name="workarounds"></a>対処方法

#### <a name="mitigation-for-the-missing-tenant-id"></a>不足しているテナント ID の軽減策

推奨される回避策は、[ポリシーによるキャッシュ](#acquire-a-token-to-apply-a-policy)を使用することです

または、[B2C カスタム ポリシー](https://aka.ms/ief)を使用している場合は、`tid` 要求を使用できます。これにより、アプリケーションに追加の要求を返す機能が提供されます。 詳しくは、[要求の変換](/azure/active-directory-b2c/claims-transformation-technical-profile)に関する記事をご覧ください

#### <a name="mitigation-for-missing-from-the-token-response"></a>"トークンの応答にありません" の軽減策
1 つのオプションは、優先されるユーザー名として "name" 要求を使用することです。 プロセスは、この [B2C ドキュメント](../../active-directory-b2c/active-directory-b2c-reference-policies.md)で説明されています。"[要求を返す] 列で、プロファイル編集エクスペリエンスの成功後にアプリケーションに戻される承認トークンで返される要求を選択します。 たとえば、[表示名] および [郵便番号] を選択します。"

## <a name="next-steps"></a>次の手順 

Azure AD B2C アプリケーション用に MSAL.NET を使用して対話形式でトークンを取得する詳細については、次のサンプルで提供されています。

| サンプル | プラットフォーム | 説明|
|------ | -------- | -----------|
|[active-directory-b2c-xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS、Xamarin Android、UWP | MSAL.NET を使用して Azure AD B2C 経由でユーザーを認証し、結果として得られたトークンで Web API にアクセスする方法を示す簡単な Xamarin Forms アプリ。|
