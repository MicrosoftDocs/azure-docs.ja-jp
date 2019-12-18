---
title: カスタム ポリシーを使用して Twitter アカウントでのサインインを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C でカスタム ポリシーを使用して Twitter アカウントでのサインインを設定します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 460f16775bf8b02bbb345b609e1419311f81bd22
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948421"
---
# <a name="set-up-sign-in-with-a-twitter-account-by-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でカスタム ポリシーを使用して Twitter アカウントでのサインインを設定する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、Azure Active Directory B2C (Azure AD B2C) で[カスタム ポリシー](active-directory-b2c-overview-custom.md)を使用して Twitter アカウントのユーザーのサインインを有効にする方法について説明します。

## <a name="prerequisites"></a>前提条件

- 「[Azure Active Directory B2C でのカスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)」にある手順を完了する。
- まだ Twitter アカウントを持っていない場合は、[Twitter のサインアップ ページ](https://twitter.com/signup)でこのアカウントを作成します。

## <a name="create-an-application"></a>アプリケーションの作成

Azure AD B2C で ID プロバイダーとして Twitter を使用するには、Twitter アプリケーションを作成する必要があります。

1. Twitter アカウント資格情報を使用して [Twitter Developers](https://developer.twitter.com/en/apps) Web サイトにサインインします。
2. **[アプリの作成]** を選択します。
3. **アプリ名**と**アプリケーションの説明**を入力します。
4. **Web サイトの URL** で、`https://your-tenant.b2clogin.com`を入力します。 `your-tenant` をテナントの名前に置き換えます。 たとえば、「 https://contosob2c.b2clogin.com 」のように入力します。
5. **[Callback URL]** に「`https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-policy-Id/oauth1/authresp`」と入力します。 ご自身のテナント名の名前を`your-tenant`と置換し、ポリシーの識別子を`your-policy-Id`と置換してください。 たとえば、「 `b2c_1A_signup_signin_twitter` 」のように入力します。 テナントが Azure AD B2C に大文字で定義されている場合でも、テナント名を入力するときに、すべての小文字を使用する必要があります。
6. ページ下部の使用条件を確認して同意し、 **[作成]** を選択します。
7. **[アプリの詳細]** ページで、 **[編集 > 詳細の編集]** を選択して、 **[Twitter でサインインを有効にする]** のチェック ボックスにチェックを入れ、 **[保存]** を選択します。
8. **[キーとトークン]** を選択し、後で使用される **[コンシューマー API キー]** と **[コンシューマー API のシークレット キー]** の値を記録します。

## <a name="create-a-policy-key"></a>ポリシー キーを作成する

Azure AD B2C テナントで前に記録した秘密鍵を格納する必要があります。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部メニューで **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用のテナントが含まれるディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
4. [概要] ページで、 **[Identity Experience Framework]** を選択します。
5. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
6. **オプション**については、`Manual`を選択します。
7. ポリシー キーの**名前**を入力します。 たとえば、「 `TwitterSecret` 」のように入力します。 プレフィックス `B2C_1A_` がキーの名前に自動的に追加されます。
8. **[シークレット]** に、前に記録したクライアント シークレットを入力します。
9. **[キー使用法]** として [`Encryption`] を選択します。
10. **Create** をクリックしてください。

## <a name="add-a-claims-provider"></a>クレーム プロバイダーを追加する

ユーザーが Twitter アカウントを使用してサインインするようにするには、そのアカウントを Azure AD B2C がエンドポイント経由で通信できる相手のクレーム プロバイダーとして定義する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使う一連の要求を提供します。

Twitter アカウントをクレーム プロバイダーとして定義するには、そのアカウントをポリシーの拡張ファイル内の **ClaimsProviders** 要素に追加します。

1. *TrustFrameworkExtensions.xml* を開きます。
2. **ClaimsProviders** 要素を見つけます。 存在しない場合は、それをルート要素の下に追加します。
3. 新しい **ClaimsProvider** を次のように追加します。

    ```xml
    <ClaimsProvider>
      <Domain>twitter.com</Domain>
      <DisplayName>Twitter</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAUTH1">
          <DisplayName>Twitter</DisplayName>
          <Protocol Name="OAuth1" />
          <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application consumer key</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
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

4. **client_id** の値を、前に記録したコンシューマー キーに置き換えます。
5. ファイルを保存します。

### <a name="upload-the-extension-file-for-verification"></a>拡張ファイルのアップロードによる確認

ここまでで、Azure AD B2C が Twitter アカウントと通信する方法を認識できるようにポリシーを構成しました。 ポリシーの拡張ファイルをアップロードして、現時点で問題がないことを確認してみます。

1. Azure AD B2C テナントの **[カスタム ポリシー]** ページで、 **[ポリシーのアップロード]** を選択します。
2. **[ポリシーが存在する場合は上書きする]** を有効にし、*TrustFrameworkExtensions.xml* ファイルを参照して選択します。
3. **[アップロード]** をクリックします。

## <a name="register-the-claims-provider"></a>クレーム プロバイダーを登録する

この時点で、ID プロバイダーは設定されていますが、まだどのサインアップまたはサインイン画面でも使用できません。 これを使用できるようにするには、既存のテンプレート ユーザー体験の複製を作成してから、Twitter ID プロバイダーも含まれるようにそれを変更します。

1. スターター パックから *TrustFrameworkBase.xml* ファイルを開きます。
2. `Id="SignUpOrSignIn"` を含む **UserJourney** 要素を見つけ、その内容全体をコピーします。
3. *TrustFrameworkExtensions.xml* を開き、**UserJourneys** 要素を見つけます。 要素が存在しない場合は追加します。
4. コピーした **UserJourney** 要素の内容全体を **UserJourneys** 要素の子として貼り付けます。
5. ユーザー体験の ID の名前を変更します。 たとえば、「 `SignUpSignInTwitter` 」のように入力します。

### <a name="display-the-button"></a>ボタンを表示する

**ClaimsProviderSelection** 要素は、サインアップまたはサインイン画面の ID プロバイダーのボタンに類似しています。 Twitter アカウントのために **ClaimsProviderSelection** 要素を追加すると、ユーザーがこのページにアクセスしたときに新しいボタンが表示されます。

1. 作成したユーザー体験内で、`Order="1"` を含む **OrchestrationStep** 要素を見つけます。
2. **ClaimsProviderSelects** の下に、次の要素を追加します。 **TargetClaimsExchangeId** の値を適切な値 (`TwitterExchange` など) に設定します。

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>ボタンのアクションへのリンク

ボタンが所定の位置に配置されたので、ボタンをアクションにリンクする必要があります。 ここでのアクションは、Azure AD B2C が Twitter アカウントと通信してトークンを受信するためのアクションです。

1. ユーザー体験内で、`Order="2"` を含む **OrchestrationStep** を見つけます。
2. 次の **ClaimsExchange** 要素を追加します。ID には、**TargetClaimsExchangeId** に使用したのと同じ値を使用するようにしてください。

    ```XML
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    **TechnicalProfileReferenceId** の値を、前に作成した技術プロファイルの ID に更新します。 たとえば、「 `Twitter-OAUTH1` 」のように入力します。

3. *TrustFrameworkExtensions.xml* ファイルを保存し、確認のために再度アップロードします。

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C アプリケーションを作成する

Azure AD B2C との通信は、B2C テナントで登録したアプリケーションを介して行われます。 このセクションでは、テスト アプリケーションをまだ作成していない場合にそれを作成するための省略可能な手順を紹介します。

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>証明書利用者ファイルを更新し、テストする

作成したユーザー体験を開始する証明書利用者 (RP) ファイルを更新します。

1. 作業ディレクトリに *SignUpOrSignIn.xml* のコピーを作成し、名前を変更します。 たとえば、*SignUpSignInTwitter.xml* に名前を変更します。
2. 新しいファイルを開き、**TrustFrameworkPolicy** の **PolicyId** 属性の値を一意の値で更新します。 たとえば、「 `SignUpSignInTwitter` 」のように入力します。
3. **PublicPolicyUri** の値をポリシーの URI に更新します。 たとえば、`http://contoso.com/B2C_1A_signup_signin_twitter` にします。
4. **DefaultUserJourney** の **ReferenceId** 属性の値を、作成した新しいユーザー体験の ID (SignUpSignTwitter) と一致するように更新します。
5. 変更を保存し、ファイルをアップロードし、一覧から新しいポリシーを選択します。
6. 作成した Azure AD B2C アプリケーションが **[アプリケーションの選択]** フィールドで選択されていることを確認し、 **[今すぐ実行]** をクリックしてテストします。
