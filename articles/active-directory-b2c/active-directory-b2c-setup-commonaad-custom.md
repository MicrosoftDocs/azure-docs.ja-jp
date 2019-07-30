---
title: Azure Active Directory B2C のカスタム ポリシーを使用してマルチテナント Azure AD ID プロバイダーのサインインを設定する | Microsoft Docs
description: カスタム ポリシーを使用してマルチテナントの Azure AD ID プロバイダーを追加する - Azure Active Directory B2C。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1938164e957daa84b22fa83e9cb9fa8d51ffeb15
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67654081"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C のカスタム ポリシーを使用してマルチテナント Azure Active Directory を設定する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、Azure AD B2C 内で[カスタム ポリシー](active-directory-b2c-overview-custom.md)を使用し、Azure Active Directory (Azure AD) のマルチテナント エンドポイントによってユーザーのサインインを有効にする方法について説明します。 これにより、複数の Azure AD テナントのユーザーが、テナントごとにテクニカル プロバイダーを構成せずに Azure AD B2C にサインインできるようになります。 ただし、これらのテナントのいずれのゲスト メンバーもサインインは**できません**。 そのため、[各テナントを個別に構成する](active-directory-b2c-setup-aad-custom.md)必要があります。

>[!NOTE]
>以下の手順では、組織の Azure AD テナントには `Contoso.com` を使用し、Azure AD B2C テナントとして `fabrikamb2c.onmicrosoft.com` を使用します。

## <a name="prerequisites"></a>前提条件

「[Azure Active Directory B2C でのカスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)」にある手順を完了する。

## <a name="register-an-application"></a>アプリケーションを登録する

特定の Azure AD 組織のユーザーのサインインを有効にするには、組織の Azure AD テナント内でアプリケーションを登録する必要があります。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. お使いの Azure AD テナント (contoso.com) を含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択し、 **[アプリの登録]** を検索して選択します。
4. **[新しいアプリケーションの登録]** を選択します。
5. アプリケーションの名前を入力します。 たとえば、「 `Azure AD B2C App` 」のように入力します。
6. **[アプリケーションの種類]** には `Web app / API` を選択します。
7. **[サインオン URL]** には、次の URL を全部小文字で入力します。`your-tenant` は、お使いの Azure AD B2C テナント (fabrikamb2c.onmicrosoft.com) の名前に変更します。

    ```
    https://yourtenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp
    ```

8. **Create** をクリックしてください。 後で使用するために **[アプリケーション ID]** をコピーします。
9. アプリケーションを選択し、 **[設定]** を選択します。
10. **[キー]** を選択し、キーの説明を入力し、期間を選択し、 **[保存]** をクリックします。 キーの値が表示されたら、後で使用するためにコピーします。
11. **[設定]** で **[プロパティ]** を選択し、 **[マルチテナント]** を `Yes` に設定し、 **[保存]** をクリックします。

## <a name="create-a-policy-key"></a>ポリシー キーを作成する

作成したアプリケーション キーを Azure AD B2C テナントに格納する必要があります。

1. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
2. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
3. [概要] ページで、 **[Identity Experience Framework]** を選択します。
4. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
5. **オプション**については、`Manual`を選択します。
6. ポリシー キーの**名前**を入力します。 たとえば、「 `ContosoAppSecret` 」のように入力します。  プレフィックス `B2C_1A_` がキーの名前に自動的に追加されます。
7. **[シークレット]** に、記録しておいたアプリケーション キーを入力します。
8. **[キー使用法]** として [`Signature`] を選択します。
9. **Create** をクリックしてください。

## <a name="add-a-claims-provider"></a>クレーム プロバイダーを追加する

ユーザーが Azure AD を使用してサインインできるようにするには、エンドポイント経由で Azure AD B2C が通信できるクレーム プロバイダーとして Azure AD を定義する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使う一連の要求を提供します。

ポリシーの拡張ファイル内で Azure AD を **ClaimsProvider** 要素に追加することで、Azure AD をクレーム プロバイダーとして定義できます。

1. *TrustFrameworkExtensions.xml* を開きます。
2. **ClaimsProviders** 要素を見つけます。 存在しない場合は、それをルート要素の下に追加します。
3. 新しい **ClaimsProvider** を次のように追加します。

    ```XML
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="METADATA">https://login.microsoftonline.com/common/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>

            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>

            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <!-- Make sure to update the reference ID of the client secret below you just created (B2C_1A_AADAppSecret) -->
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
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

4. **ClaimsProvider** 要素の下で、**Domain** の値を、他の ID プロバイダーと区別するために使用できる一意の値に更新します。
5. **TechnicalProfile** 要素の下で、**DisplayName** の値を更新します。 この値は、サインイン画面のサインイン ボタン上に表示されます。
6. **client_id** を、Azure AD マルチテナント アプリ登録のアプリケーション ID に設定します。

### <a name="restrict-access"></a>アクセスを制限する

> [!NOTE]
> **ValidTokenIssuerPrefixes** の値として `https://sts.windows.net` を使用すると、すべての Azure AD ユーザーがアプリケーションにサインインできるようになります。

有効なトークン発行者の一覧を更新し、サインインできる Azure AD テナント ユーザーの特定の一覧へのアクセスを制限する必要があります。 値を取得するには、サインインするユーザーの特定の Azure AD テナントごとのメタデータを調べる必要があります。 データの形式は、`https://login.windows.net/your-tenant/.well-known/openid-configuration` のようになります。ここで、`your-tenant` は Azure AD テナント (contoso.com またはその他の Azure AD テナント) の名前です。

1. ブラウザーを起動し、**METADATA** URL に移動し、**発行者**オブジェクトを検索し、その値をコピーします。 値は `https://sts.windows.net/tenant-id/` のようになります。
2. **ValidTokenIssuerPrefixes** キーの値をコピーして貼り付けます。 コンマで区切って複数の値を追加することもできます。 その例は、上のサンプル XML でコメントされています。

### <a name="upload-the-extension-file-for-verification"></a>拡張ファイルのアップロードによる確認

ここまでで、Azure AD B2C が Azure AD ディレクトリと通信する方法を認識するようにポリシーを設定しました。 ポリシーの拡張ファイルをアップロードして、現時点で問題がないことを確認してみます。

1. Azure AD B2C テナントの **[カスタム ポリシー]** ページで、 **[ポリシーのアップロード]** を選択します。
2. **[ポリシーが存在する場合は上書きする]** を有効にし、*TrustFrameworkExtensions.xml* ファイルを参照して選択します。
3. **[アップロード]** をクリックします。

## <a name="register-the-claims-provider"></a>クレーム プロバイダーを登録する

この時点では、ID プロバイダーはセットアップされていますが、サインアップ/サインイン画面で使用することはできません。 これを使用できるようにするには、既存のテンプレート ユーザー体験の複製を作成してから、Azure AD の ID プロバイダーも含まれるように変更します。

1. スターター パックの *TrustFrameworkBase.xml* ファイルを開きます。
2. `Id="SignUpOrSignIn"` を含む **UserJourney** 要素を見つけ、その内容全体をコピーします。
3. *TrustFrameworkExtensions.xml* を開き、**UserJourneys** 要素を見つけます。 要素が存在しない場合は追加します。
4. コピーした **UserJourney** 要素の内容全体を **UserJourneys** 要素の子として貼り付けます。
5. ユーザー体験の ID の名前を変更します。 たとえば、「 `SignUpSignInContoso` 」のように入力します。

### <a name="display-the-button"></a>ボタンを表示する

**ClaimsProviderSelection** 要素は、サインアップおよびサインイン画面の ID プロバイダー ボタンに類似しています。 Azure AD 用の **ClaimsProviderSelection** 要素を追加すると、ユーザーがページにアクセスしたときに新しいボタンが表示されます。

1. 作成したユーザー体験内で、`Order="1"` を含む **OrchestrationStep** 要素を見つけます。
2. **ClaimsProviderSelects** の下に、次の要素を追加します。 **TargetClaimsExchangeId** の値を適切な値 (`AzureADExchange` など) に設定します。

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>ボタンのアクションへのリンク

ボタンが所定の位置に配置されたので、ボタンをアクションにリンクする必要があります。 この場合のアクションでは、Azure AD B2C が Azure AD と通信してトークンを受信します。 Azure AD 要求プロバイダーの技術プロファイルをリンクすることで、ボタンをアクションにリンクします。

1. ユーザー体験内で、`Order="2"` を含む **OrchestrationStep** を見つけます。
2. 次の **ClaimsExchange** 要素を追加します。**TargetClaimsExchangeId** に使用した **Id** と同じ値を必ずご使用ください。

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

    **TechnicalProfileReferenceId** の値を、前に作成した技術プロファイルの **Id** に更新します。 たとえば、「 `Common-AAD` 」のように入力します。

3. *TrustFrameworkExtensions.xml* ファイルを保存し、確認のために再度アップロードします。

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C アプリケーションを作成する

Azure AD B2C との通信は、テナントで作成したアプリケーション経由で行われます。 このセクションでは、テスト アプリケーションをまだ作成していない場合にそれを作成するための省略可能な手順を紹介します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
4. **[アプリケーション]** を選択し、 **[追加]** を選択します。
5. アプリケーションの名前を入力します (*testapp1* など)。
6. **[Web アプリ / Web API]** には `Yes` を選択し、 **[応答 URL]** に `https://jwt.ms` を入力します。
7. **Create** をクリックしてください。

## <a name="update-and-test-the-relying-party-file"></a>証明書利用者ファイルを更新し、テストする

作成したユーザー体験を開始する証明書利用者 (RP) ファイルを更新します。

1. 作業ディレクトリに *SignUpOrSignIn.xml* のコピーを作成し、名前を変更します。 たとえば、*SignUpSignContoso.xml* に名前を変更します。
2. 新しいファイルを開き、**TrustFrameworkPolicy** の **PolicyId** 属性の値を一意の値で更新します。 たとえば、「 `SignUpSignInContoso` 」のように入力します。
3. **PublicPolicyUri** の値をポリシーの URI に更新します。 たとえば、`http://contoso.com/B2C_1A_signup_signin_contoso` にします。
4. **DefaultUserJourney** の **ReferenceId** 属性の値を、作成した新しいユーザー体験の ID (SignUpSignContoso) と一致するように変更します。
5. 変更を保存し、ファイルをアップロードし、一覧から新しいポリシーを選択します。
6. 作成した Azure AD B2C アプリケーションが **[アプリケーションの選択]** フィールドで選択されていることを確認し、 **[今すぐ実行]** をクリックしてテストします。
