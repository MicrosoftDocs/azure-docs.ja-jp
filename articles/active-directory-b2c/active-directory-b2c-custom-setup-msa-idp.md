---
title: "Azure Active Directory B2C: カスタム ポリシーを使って Microsoft アカウント (MSA) を ID プロバイダーとして追加する"
description: "OpenID Connect (OIDC) プロトコルを使って Microsoft を ID プロバイダーとして使う例"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: yoelh
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 928e33392924a7461a4a03ac16ff0994680e68f9
ms.contentlocale: ja-jp
ms.lasthandoff: 08/28/2017

---

# <a name="azure-active-directory-b2c-add-microsoft-account-msa-as-an-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: カスタム ポリシーを使って Microsoft アカウント (MSA) を ID プロバイダーとして追加する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、[カスタム ポリシー](active-directory-b2c-overview-custom.md)を使って Microsoft アカウント (MSA) からのユーザーのサインインを有効にする方法を示します。

## <a name="prerequisites"></a>前提条件
[カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)に関する記事の手順を完了します。

その手順は次のとおりです。

1.  Microsoft アカウント アプリケーションを作成する。
2.  Azure AD B2C に Microsoft アカウント アプリケーション キーを追加する
3.  ポリシーにクレーム プロバイダーを追加する
4.  ユーザー体験に Microsoft アカウント クレーム プロバイダーを登録する
5.  Azure AD B2C テナントにポリシーをアップロードし、テストする

## <a name="create-a-microsoft-account-application"></a>Microsoft アカウント アプリケーションを作成する
Azure Active Directory (Azure AD) B2C で ID プロバイダーとして Microsoft アカウントを使用するには、Microsoft アカウント アプリケーションを作成し、適切なパラメーターを提供する必要があります。 Microsoft アカウントが必要です。 アカウントがない場合は、[https://www.live.com/](https://www.live.com/) にアクセスします。

1.  [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) に移動し、Microsoft アカウントの資格情報でサインインします。
2.  **[アプリの追加]**をクリックします。

    ![Microsoft アカウント - アプリの追加](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-new-app.png)

3.  アプリケーションの**名前**、**[連絡先の電子メール]** を指定し、**[作業を開始する]** チェック ボックスをオフにして **[作成]** をクリックします。

    ![Microsoft アカウント - アプリケーションの登録](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-name.png)

4.  **[アプリケーション ID]**の値をコピーします。この値は、テナントの ID プロバイダーとして Microsoft アカウントを構成するために必要となります。

    ![Microsoft アカウント - アプリケーション ID の値のコピー](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-id.png)

5.  **[プラットフォームの追加]** をクリックします

    ![Microsoft account - Add platform](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-platform.png)

6.  プラットフォームの一覧から **[Web]** を選択します。

    ![Microsoft アカウント - プラットフォームの一覧から Web を選択](media/active-directory-b2c-custom-setup-ms-account-idp/msa-web.png)

7.  **[リダイレクト URI]** フィールドに、「`https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`」と入力します。 **{tenant}** は、実際のテナントの名前 (例: contosob2c.onmicrosoft.com) に置き換えます。

    ![Microsoft アカウント - リダイレクト URLの設定](media/active-directory-b2c-custom-setup-ms-account-idp/msa-redirect-url.png)

8.  **[アプリケーション シークレット]** セクションの **[新しいパスワードを生成]** をクリックします。 画面に表示される新しいパスワードをコピーします。 この値は、テナントの ID プロバイダーとして Microsoft アカウントを構成するために必要となります。 このパスワードは重要なセキュリティ資格情報です。

    ![Microsoft account - Generate new password](media/active-directory-b2c-custom-setup-ms-account-idp/msa-generate-new-password.png)

    ![Microsoft アカウント - 新しいパスワードのコピー](media/active-directory-b2c-custom-setup-ms-account-idp/msa-new-password.png)

9.  **[詳細オプション]** セクションの **[Live SDK サポート]** というチェック ボックスをオンにします。 [ **Save**] をクリックします。

    ![Microsoft account - Live SDK support](media/active-directory-b2c-custom-setup-ms-account-idp/msa-live-sdk-support.png)

## <a name="add-the-microsoft-account-application-key-to-azure-ad-b2c"></a>Azure AD B2C に Microsoft アカウント アプリケーション キーを追加する
Microsoft アカウントとのフェデレーションには、Microsoft アカウントのクライアント シークレットがアプリケーションに代わって Azure AD B2C を信頼する必要があります。 Azure AD B2C テナントに Microsoft アカウント アプリケーション シークレットを格納する必要があります。   

1.  Azure AD B2C テナントに移動し、**[B2C Settings]\(B2C 設定\)**  >  **[Identity Experience Framework]** の順に選択します
2.  **[ポリシー キー]** を選択して、テナント内で利用できるキーを表示します。
3.  **[+追加]** をクリックします。
4.  **[オプション]** には **[Manual] \(手動)** を使用します。
5.  **[名前]** には `MSASecret` を使用します。  
    プレフィックス `B2C_1A_` が自動的に追加される場合があります。
6.  **[シークレット]** ボックスに、https://apps.dev.microsoft.com からの Microsoft アプリケーション シークレットを入力します
7.  **[キー使用法]** には **[署名]** を使用します。
8.  **[作成]**
9.  キー `B2C_1A_MSASecret` を作成したことを確認します。

## <a name="add-a-claims-provider-in-your-extension-policy"></a>拡張ポリシーにクレーム プロバイダーを追加する
ユーザーが Microsoft アカウントを使ってサインインできるようにするには、Microsoft アカウントをクレーム プロバイダーとして定義する必要があります。 つまり、Azure AD B2C が通信するエンドポイントを指定する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使う一連の要求を提供します。

拡張ポリシー ファイルに `<ClaimsProvider>` ノードを追加することで、Microsoft アカウントをクレーム プロバイダーとして定義します。

1.  作業ディレクトリから拡張ポリシー ファイル (TrustFrameworkExtensions.xml) を開きます。 XML エディターが必要な場合は、軽量のクロスプラットフォーム エディターである [Visual Studio Code](https://code.visualstudio.com/download) をお試しください。
2.  `<ClaimsProviders>` セクションを探します
3.  `ClaimsProviders` 要素の下に次の XML スニペットを追加します。

    ```xml
<ClaimsProvider>
    <Domain>live.com</Domain>
    <DisplayName>Microsoft Account</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="MSA-OIDC">
        <DisplayName>Microsoft Account</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <Metadata>
        <Item Key="ProviderName">https://login.live.com</Item>
        <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
        <Item Key="response_types">code</Item>
        <Item Key="response_mode">form_post</Item>
        <Item Key="scope">openid profile email</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="client_id">Your Microsoft application client id</Item>
        </Metadata>
    <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
    </CryptographicKeys>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <OutputClaim ClaimTypeReferenceId="email" />
        </OutputClaims>
        <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
        <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
        <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
    </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

4.  `client_id` 値を Microsoft アカウント アプリケーション クライアント ID で置換します

5.  ファイルを保存します。

## <a name="register-the-microsoft-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>サインアップまたはサインイン ユーザー体験に Microsoft アカウント クレーム プロバイダーを登録する

この時点では、ID プロバイダーはセットアップされていますが、サインアップ/サインイン画面で使用することはできません。 Microsoft アカウント ID プロバイダーをユーザーの `SignUpOrSignIn` ユーザー体験に追加する必要があります。 使用可能にするには、既存のテンプレート ユーザー体験の複製を作成します。  次に、Microsoft アカウント ID プロバイダーを追加します。

> [!NOTE]
>
>以前に `<UserJourneys>` 要素をポリシーの基本ファイルから拡張ファイル `TrustFrameworkExtensions.xml` にコピーした場合は、このセクションをスキップすることができます。

1.  ポリシーの基本ファイルを開きます (例: TrustFrameworkBase.xml)。
2.  `<UserJourneys>` 要素を見つけて、`<UserJourneys>` ノードのコンテンツ全体をコピーします。
3.  拡張ファイル (例: TrustFrameworkExtensions.xml) を開き、`<UserJourneys>` 要素を見つけます。 要素が存在しない場合は追加します。
4.  コピーした `<UserJournesy>` ノードのコンテンツ全体を `<UserJourneys>` 要素の子として貼り付けます。

### <a name="display-the-button"></a>ボタンを表示する
`<ClaimsProviderSelections>` 要素は、クレーム プロバイダーの選択オプションとその順序の一覧を定義します。  `<ClaimsProviderSelection>` 要素は、サインアップ/サインイン ページの ID プロバイダーのボタンに類似しています。 Microsoft アカウントのために `<ClaimsProviderSelection>` 要素を追加すると、ユーザーがページにアクセスしたときに新しいボタンが表示されます。 この要素を追加するには、次の手順を実行します。

1.  コピーしたばかりのユーザー体験内で、`Id="SignUpOrSignIn"` を含む `<UserJourney>` ノードを見つけます。
2.  `Order="1"` を含む `<OrchestrationStep>` ノードを特定します
3.  `<ClaimsProviderSelections>` ノードの下に次の XML スニペットを追加します。

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MSAExchange" />
```

### <a name="link-the-button-to-an-action"></a>ボタンのアクションへのリンク
ボタンが所定の位置に配置されたので、ボタンをアクションにリンクする必要があります。 この場合のアクションでは、Azure AD B2C が Microsoft アカウントと通信してトークンを受信します。 Microsoft アカウント クレーム プロバイダーの技術プロファイルをリンクすることで、ボタンをアクションにリンクします。

1.  `<UserJourney>` ノード内で、`Order="2"` が含まれている `<OrchestrationStep>` を見つけます。
2.  `<ClaimsExchanges>` ノードの下に次の XML スニペットを追加します。

```xml
<ClaimsExchange Id="MSAExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

> [!NOTE]
>
>   * `Id` が前のセクションの `TargetClaimsExchangeId` と同じ値であることを確認します
>   * `TechnicalProfileReferenceId` ID が前に作成した技術プロファイル (MSA-OIDC) に設定されていることを確認します。

## <a name="upload-the-policy-to-your-tenant"></a>ポリシーをテナントにアップロードする
1.  [Azure Portal](https://portal.azure.com) で、[Azure AD B2C テナントのコンテキストに切り替え](active-directory-b2c-navigate-to-b2c-context.md)、**[Azure AD B2C]** ブレードを開きます。
2.  **[Identity Experience Framework]** を選択します。
3.  **[すべてのポリシー]** ブレードを開きます。
4.  **[ポリシーのアップロード]** を選択します。
5.  **[ポリシーが存在する場合は上書きする]** チェック ボックスをオンにします。
6.  TrustFrameworkExtensions.xml を**アップロード**し、検証に失敗しないことを確認します

## <a name="test-the-custom-policy-by-using-run-now"></a>[今すぐ実行] を使用してカスタム ポリシーをテストする

1.  **[Azure AD B2C の設定]** を開き、**[Identity Experience Framework]** に移動します。
> [!NOTE]
>
>**[今すぐ実行]** を使用するには、テナントに少なくとも 1 つのアプリケーションが事前登録されている必要があります。 アプリケーションを登録する方法については、 Azure AD B2C の[概要](active-directory-b2c-get-started.md)に関する記事または[アプリケーションの登録](active-directory-b2c-app-registration.md)に関する記事を参照してください。
2.  アップロードした証明書利用者 (RP) カスタム ポリシーである **B2C_1A_signup_signin** を開きます。 **[今すぐ実行]** を選択します。
3.  Microsoft アカウントを使ってサインインすることができます。

## <a name="optional-register-the-microsoft-account-claims-provider-to-profile-edit-user-journey"></a>[省略可能] プロファイル編集ユーザー体験に Microsoft アカウント クレーム プロバイダーを登録する
Microsoft アカウント ID プロバイダーをユーザーの `ProfileEdit` ユーザー体験に追加することもできます。 利用できるようには、最後の 2 つの手順を繰り返します。

### <a name="display-the-button"></a>ボタンを表示する
1.  ポリシーの拡張ファイル (例: TrustFrameworkExtensions.xml) を開きます。
2.  コピーしたばかりのユーザー体験内で、`Id="ProfileEdit"` を含む `<UserJourney>` ノードを見つけます。
3.  `Order="1"` を含む `<OrchestrationStep>` ノードを特定します
4.  `<ClaimsProviderSelections>` ノードの下に次の XML スニペットを追加します。

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MSAExchange" />
```

### <a name="link-the-button-to-an-action"></a>ボタンのアクションへのリンク
1.  `<UserJourney>` ノード内で、`Order="2"` が含まれている `<OrchestrationStep>` を見つけます。
2.  `<ClaimsExchanges>` ノードの下に次の XML スニペットを追加します。

```xml
<ClaimsExchange Id="MSAExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>[今すぐ実行] を使ってカスタム プロファイル編集ポリシーをテストする
1.  **[Azure AD B2C の設定]** を開き、**[Identity Experience Framework]** に移動します。
2.  アップロードした証明書利用者 (RP) カスタム ポリシーである **B2C_1A_ProfileEdit** を開きます。 **[今すぐ実行]** を選択します。
3.  Microsoft アカウントを使ってサインインすることができます。

## <a name="download-the-complete-policy-files"></a>完全なポリシー ファイルをダウンロードする
省略可能: これらのサンプル ファイルを使う代わりに、カスタム ポリシーの概要チュートリアルの完了後に独自のカスタム ポリシーを使ってシナリオを構築することをお勧めします。  [参照用のサンプル ポリシー ファイル](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-msa-app)

