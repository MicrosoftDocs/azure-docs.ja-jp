---
title: Azure AD B2C と MSAL.NET
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library for .NET (MSAL.NET) で Azure AD B2C を使用する場合の考慮事項。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2020
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: bdb9e12fdf721204ce98d23e5d5aeea535ddf23d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574796"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>MSAL.NET を使用してソーシャル ID でユーザーをサインインさせる

MSAL.NET を使用して、[Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/overview.md) でソーシャル ID を持つユーザーをサインインさせることができます。 Azure AD B2C はポリシーの概念を基に構築されています。 MSAL.NET では、ポリシーを指定するということは、機関を提供することです。

- パブリック クライアント アプリケーションをインスタンス化するときは、機関の一部としてポリシーを指定します。
- ポリシーを適用するときは、`authority` パラメーターを受け取る `AcquireTokenInteractive` のオーバーライドを呼び出します。

この記事は、MSAL.NET 3.x に適用されます。 MSAL.NET 2.x については、GitHub の MSAL.NET Wiki にある「[MSAL 2.x での Azure AD B2C の詳細](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x)」を参照してください。

## <a name="authority-for-an-azure-ad-b2c-tenant-and-policy"></a>Azure AD B2C のテナントとポリシーに対する機関

Azure AD B2C 機関の形式は次のとおりです。`https://{azureADB2CHostname}/tfp/{tenant}/{policyName}`

- `azureADB2CHostname` - Azure AD B2C テナントの名前にホストを組み合わせたものです。 たとえば、*contosob2c.b2clogin.com* です。
- `tenant` - Azure AD B2C テナントのドメイン名またはディレクトリ (テナント) ID。 たとえば、それぞれ *contosob2c.onmicrosoft.com* または GUID などです。
- `policyName` - 適用するユーザー フローまたはカスタム ポリシーの名前。 たとえば、*b2c_1_susi* のようなサインアップ/サインイン ポリシーです。

Azure AD B2C 機関の詳細については、[b2clogin.com へのリダイレクト URL の設定](../../active-directory-b2c/b2clogin.md)に関する記事を参照してください。

## <a name="instantiating-the-application"></a>アプリケーションをインスタンス化する

アプリケーション オブジェクトを作成する場合は、`WithB2CAuthority()` を呼び出して機関を指定する必要があります。

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string AzureADB2CHostname = "fabrikamb2c.b2clogin.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://{AzureADB2CHostname}/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>ポリシーを適用するためのトークンを取得する

パブリック クライアント アプリケーションで Azure AD B2C で保護された API のトークンを取得するには、次のように、そのオーバーライドを機関で使用する必要があります。

```csharp
AuthenticationResult authResult = null;
IEnumerable<IAccount> accounts = await application.GetAccountsAsync(policy);
IAccount account = accounts.FirstOrDefault();
try
{
    authResult = await application.AcquireTokenSilent(scopes, account)
                      .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    authResult = await application.AcquireTokenInteractive(scopes)
                        .WithAccount(account)
                        .WithParentActivityOrWindow(ParentActivityOrWindow)
                        .ExecuteAsync();
}  
```

上記のコード スニペットは、次のようになっています。

- `policy` は、Azure AD B2C ユーザー フローまたはカスタム ポリシーの名前を含む文字列です (例: `PolicySignUpSignIn`)。
- `ParentActivityOrWindow` は、Android (Activity) では必須となります。親 UI (Microsoft Windows のウィンドウや iOS の UIViewController など) をサポートするその他のプラットフォームでは省略可能です。 UI ダイアログの詳細については、MSAL Wiki の「[WithParentActivityOrWindow](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow)」を参照してください。

現在、ユーザー フローまたはカスタム ポリシーの適用は (たとえば、ユーザーが自分のプロファイルを編集したり、パスワードをリセットしたりできるようにする)、`AcquireTokenInteractive` を呼び出すことによって行われます。 これら 2 つのポリシーでは、返されたトークンや認証結果を使用しません。

## <a name="profile-edit-policies"></a>プロファイルの編集ポリシー

ユーザーがソーシャル ID でサインインし、自分のプロファイルを編集できるようにするには、Azure AD B2C EditProfile ポリシーを適用します。

そのためには、そのポリシーの機関を使用して `AcquireTokenInteractive` を呼び出します。 ユーザーは既にサインインしていて、アクティブな Cookie セッションがあるため、`Prompt.NoPrompt` を使用して、[アカウントの選択] ダイアログが表示されないようにします。

```csharp
private async void EditProfileButton_Click(object sender, RoutedEventArgs e)
{
    IEnumerable<IAccount> accounts = await application.GetAccountsAsync(PolicyEditProfile);
    IAccount account = accounts.FirstOrDefault();
    try
    {
        var authResult = await application.AcquireTokenInteractive(scopes)
                            .WithPrompt(Prompt.NoPrompt),
                            .WithAccount(account)
                            .WithB2CAuthority(AuthorityEditProfile)
                            .ExecuteAsync();
     }
    catch
    {
    }
}
```

## <a name="resource-owner-password-credentials-ropc"></a>リソース所有者のパスワード資格情報 (ROPC)

ROPC フローの詳細については、[リソース所有者のパスワード資格情報の付与を使用したサインイン](v2-oauth-ropc.md)に関する記事を参照してください。

アプリケーションでユーザーにパスワードを求めることは安全でないため、ROPC フローは **推奨されません**。 この問題の詳細については、[深刻化するパスワードの問題の解決策](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)に関する記事を参照してください。

ROPC フローでユーザー名とパスワードを使用すると、次のようなことが犠牲になります。

- 最新の ID の核となる概念:共有シークレットが傍受される可能性があるため、パスワードはフィッシングまたは再生される可能性があります。 定義上、ROPC はパスワードなしのフローと互換性がありません。
- MFA を実行する必要のあるユーザーがサインインできない (対話式操作がないため)。
- ユーザーがシングル サインオン (SSO) を使用できない。

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Azure AD B2C で ROPC フローを構成する

お使いの Azure AD B2C テナントで、新しいユーザー フローを作成し、 **[ROPC を使用してサインイン]** を選択して、ユーザー フローで ROPC を有効にします。 詳しくは、[リソース所有者のパスワード資格情報フローの構成](../../active-directory-b2c/add-ropc-policy.md)に関する記事をご覧ください。

`IPublicClientApplication` には `AcquireTokenByUsernamePassword` メソッドが含まれています。

```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

この `AcquireTokenByUsernamePassword` メソッドは、次のパラメーターを受け取ります。

- アクセス トークンを取得する "*スコープ*"。
- "*ユーザー名*"。
- ユーザーの SecureString の "*パスワード*"。

### <a name="limitations-of-the-ropc-flow"></a>ROPC フローの制限事項

ROPC フローは、ユーザーがメール アドレスまたはユーザー名を使用して Azure AD B2C に登録した **ローカル アカウントでのみ動作します**。 Azure AD B2C によってサポートされる外部 ID プロバイダー (Facebook、Google など) とフェデレーションした場合、このフローは機能しません。

## <a name="google-auth-and-embedded-webview"></a>Google 認証と埋め込み Web ビュー

ID プロバイダーとして Google を使用している 場合は、システム ブラウザーを使用することをお勧めします。Google では、[埋め込み Web ビューから認証を行う](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)ことはできないためです。 現在、`login.microsoftonline.com` は Google で信頼されている機関であり、埋め込み Web ビューで動作します。 しかし、`b2clogin.com` は Google で信頼されている機関ではないため、ユーザーは認証を行うことができません。

状況に変化があった場合、この[問題](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688)に対する更新を提供します。

## <a name="token-caching-in-msalnet"></a>MSAL.NET でのトークンのキャッシュ

### <a name="known-issue-with-azure-ad-b2c"></a>Azure AD B2C での既知の問題

MSAL.NET では[トークン キャッシュ](/dotnet/api/microsoft.identity.client.tokencache)がサポートされています。 トークン キャッシュ キーは、ID プロバイダー (IdP) によって返される要求に基づきます。

現在、MSAL.NET では、トークン キャッシュ キーを作成するために 2 つの要求が必要です。

- `tid` (Azure AD テナント ID)
- `preferred_username`

Azure AD B2C シナリオでは、これらの両方の要求が欠落する可能性があります。これは、すべてのソーシャル ID プロバイダー (Facebook、Google など) が、Azure AD B2C に返すトークンにそれらを返すわけではないからです。

このようなシナリオの症状として、Azure AD B2C によって発行されたトークンの `preferred_username` 要求の値にアクセスしたときに、MSAL.NET から `Missing from the token response` が返されることがあります。 MSAL は、`preferred_username` の `Missing from the token response` 値を使用して、ライブラリ間のキャッシュの相互互換性を維持します。

### <a name="workarounds"></a>対処方法

#### <a name="mitigation-for-missing-tenant-id"></a>テナント ID が見つからない場合の軽減策

推奨される回避策は、前に説明した[ポリシーによるキャッシュ](#acquire-a-token-to-apply-a-policy)を使用することです。

また、Azure AD B2C で[カスタム ポリシー](../../active-directory-b2c/custom-policy-get-started.md)を使用している場合は、`tid` 要求を使用することもできます。 カスタム ポリシーは、[要求の変換](../../active-directory-b2c/claims-transformation-technical-profile.md)を使用して、アプリケーションに追加の要求を返すことができます。

#### <a name="mitigation-for-missing-from-the-token-response"></a>"トークンの応答にありません" の軽減策

1 つの方法は、`preferred_username` ではなく `name` 要求を使用することです。 Azure AD B2C によって発行された ID トークンに `name` 要求を含めるには、ユーザー フローを構成するときに **表示名** を選択します。

ユーザー フローによって返される要求を指定する方法の詳細については、「[チュートリアル:Azure AD B2C 内にユーザーフローを作成する](../../active-directory-b2c/tutorial-create-user-flows.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

Azure AD B2C アプリケーション用に MSAL.NET を使用して対話形式でトークンを取得する詳細については、次のサンプルで提供されています。

| サンプル | プラットフォーム | 説明|
|------ | -------- | -----------|
|[active-directory-b2c-xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS、Xamarin Android、UWP | MSAL.NET を使用して Azure AD B2C 経由でユーザーを認証し、返されたトークンを使用して Web API にアクセスする Xamarin Forms アプリ。|
