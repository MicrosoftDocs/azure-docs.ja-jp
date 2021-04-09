---
title: リソース所有者パスワード資格情報フローを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C でリソース所有者パスワード資格情報 (ROPC) フローを設定する方法を説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 3a7c93bb0e0dcc51e35bc27fa0799d8410e66df6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104581883"
---
# <a name="set-up-a-resource-owner-password-credentials-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でリソース所有者パスワード資格情報フローを設定する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) では、リソース所有者パスワード資格情報 (ROPC) フローが OAuth の標準的な認証フローです。 このフローでは、アプリケーション (証明書利用者とも呼ばれます) によって有効な資格情報がトークンと交換されます。 資格情報には、ユーザー ID とパスワードが含まれます。 返されるトークンは、ID トークン、アクセス トークン、および更新トークンです。

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]


## <a name="ropc-flow-notes"></a>ROPC フローの説明

Azure Active Directory B2C (Azure AD B2C) では、次のオプションがサポートされています。

- **ネイティブ クライアント**:認証時のユーザー操作は、ユーザー側のデバイスでコードが実行されているときに発生します。 デバイスは、Android や iOS などのネイティブ オペレーティング システムで実行されているモバイル アプリケーションである可能性があります。
- **パブリック クライアント フロー**:アプリケーションによって収集されたユーザーの資格情報のみが API 呼び出しで送信されます。 アプリケーションの資格情報は送信されません。
- **新しい要求の追加**:新しい要求を追加するために、ID トークンの内容を変更することができます。

次のフローはサポートされていません。

- **サーバー対サーバー**:ID 保護システムは、対話の一環として、呼び出し元 (ネイティブ クライアント) から収集された信頼できる IP アドレスを必要とします。 サーバー側の API 呼び出しでは、サーバーの IP アドレスのみが使用されます。 失敗した認証の動的しきい値を超えた場合、ID 保護システムによって、繰り返される IP アドレスが攻撃者として特定される可能性があります。
- **機密性の高いクライアント フロー**:アプリケーション クライアント ID は検証されますが、アプリケーション シークレットは検証されません。

ROPC フローを使用する場合は、次の点を考慮してください。

- ユーザーの操作が必要な認証フローが中断されている場合、ROPC は動作しません。 たとえば、パスワードの有効期限が切れた場合、パスワードを変更する必要がある場合、[多要素認証](multi-factor-authentication.md)が必要な場合、サインイン時に詳細情報を収集する必要がある (ユーザーの同意など) 場合です。
- ROPC ではローカル アカウントのみがサポートされています。 ユーザーは、Microsoft、Google +、Twitter、AD FS、Facebook などの[フェデレーション ID プロバイダー](add-identity-provider.md)を使用してサインインすることはできません。
- [[サインインしたままにする] (KMSI)](session-behavior.md#enable-keep-me-signed-in-kmsi) を含む[セッション管理](session-behavior.md) は適用されません。


## <a name="register-an-application"></a>アプリケーションを登録する

[!INCLUDE [active-directory-b2c-appreg-ropc](../../includes/active-directory-b2c-appreg-ropc.md)]

::: zone pivot="b2c-user-flow"

##  <a name="create-a-resource-owner-user-flow"></a>リソース所有者のユーザー フローを作成する

1. Azure AD B2C テナントの全体管理者として、Azure portal にサインインします。
2. Azure AD B2C テナントに切り替えるには、ポータルの右上隅で B2C ディレクトリを選択します。
3. **[ユーザー フロー]** を選択し、 **[新しいユーザー フロー]** を選択します。
4. **[リソース所有者のパスワード資格情報 (ROPC) を使用してサインインする]** を選択します。
5. **[バージョン]** で **[プレビュー]** が選択されていることを確認してから、 **[作成]** を選択します。
7. *ROPC_Auth* など、ユーザー フローの名前を入力します。
8. **[アプリケーション要求]** の **[さらに表示する]** をクリックします。
9. [表示名]、[メール アドレス]、[ID プロバイダー] など、アプリケーションに必要なアプリケーション要求を選択します。
10. **[OK]** を選択し、 **[作成]** を選択します。

::: zone-end

::: zone pivot="b2c-custom-policy"

##  <a name="create-a-resource-owner-policy"></a>リソース所有者のポリシーを作成する

1. *TrustFrameworkExtensions.xml* ファイルを開きます。
2. まだ存在しない場合は、**ClaimsSchema** 要素とその子要素を、**BuildingBlocks** 要素の下の最初の要素として追加します。

    ```xml
    <ClaimsSchema>
      <ClaimType Id="logonIdentifier">
        <DisplayName>User name or email address that the user can use to sign in</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="resource">
        <DisplayName>The resource parameter passes to the ROPC endpoint</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokenIssuedOnDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokensValidFromDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
    </ClaimsSchema>
    ```

3. **BuildingBlocks** 要素の **ClaimsSchema** 要素の後に、**ClaimsTransformations** 要素とその子要素を追加します。

    ```xml
    <ClaimsTransformations>
      <ClaimsTransformation Id="CreateSubjectClaimFromObjectID" TransformationMethod="CreateStringClaim">
        <InputParameters>
          <InputParameter Id="value" DataType="string" Value="Not supported currently. Use oid claim." />
        </InputParameters>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="sub" TransformationClaimType="createdClaim" />
        </OutputClaims>
      </ClaimsTransformation>

      <ClaimsTransformation Id="AssertRefreshTokenIssuedLaterThanValidFromDate" TransformationMethod="AssertDateTimeIsGreaterThan">
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" TransformationClaimType="leftOperand" />
          <InputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" TransformationClaimType="rightOperand" />
        </InputClaims>
        <InputParameters>
          <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
          <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
        </InputParameters>
      </ClaimsTransformation>
    </ClaimsTransformations>
    ```

4. **DisplayName** が `Local Account SignIn` である **ClaimsProvider** 要素を探して、次の技術プロファイルを追加します。

    ```xml
    <TechnicalProfile Id="ResourceOwnerPasswordCredentials-OAUTH2">
      <DisplayName>Local Account SignIn</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
        <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
        <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
        <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
        <Item Key="response_types">id_token</Item>
        <Item Key="response_mode">query</Item>
        <Item Key="scope">email openid</Item>
        <Item Key="grant_type">password</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="logonIdentifier" PartnerClaimType="username" Required="true" DefaultValue="{OIDC:Username}"/>
        <InputClaim ClaimTypeReferenceId="password" Required="true" DefaultValue="{OIDC:Password}" />
        <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
        <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
        <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
        <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppId" />
        <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppId" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
        <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
      </OutputClaimsTransformations>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
    ```

    **client_id** の **DefaultValue** を、前提条件のチュートリアルで作成した ProxyIdentityExperienceFramework アプリケーションのアプリケーション ID に置き換えます。 **resource_id** の **DefaultValue** も、前提条件のチュートリアルで作成した IdentityExperienceFramework アプリケーションのアプリケーション ID に置き換えます。

5. 次の **ClaimsProvider** 要素とその技術プロファイルを、**ClaimsProviders** 要素に追加します。

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserReadUsingObjectId-CheckRefreshTokenDate">
          <Metadata>
            <Item Key="Operation">Read</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="AssertRefreshTokenIssuedLaterThanValidFromDate" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
          </OutputClaimsTransformations>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-RefreshTokenReadAndSetup">
          <DisplayName>Trustframework Policy Engine Refresh Token Setup Technical Profile</DisplayName>
          <Protocol Name="None" />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" />
          </OutputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="JwtIssuer">
          <Metadata>
            <!-- Point to the redeem refresh token user journey-->
            <Item Key="RefreshTokenUserJourneyId">ResourceOwnerPasswordCredentials-RedeemRefreshToken</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

6. **UserJourneys** 要素とその子要素を、**TrustFrameworkPolicy** 要素に追加します。

    ```xml
    <UserJourney Id="ResourceOwnerPasswordCredentials">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="ResourceOwnerFlow" TechnicalProfileReferenceId="ResourceOwnerPasswordCredentials-OAUTH2" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    <UserJourney Id="ResourceOwnerPasswordCredentials-RedeemRefreshToken">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="RefreshTokenSetupExchange" TechnicalProfileReferenceId="SM-RefreshTokenReadAndSetup" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="CheckRefreshTokenDateFromAadExchange" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId-CheckRefreshTokenDate" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    ```

7. Azure AD B2C テナントの **[カスタム ポリシー]** ページで、 **[ポリシーのアップロード]** を選択します。
8. **[ポリシーが存在する場合は上書きする]** を有効にし、*TrustFrameworkExtensions.xml* ファイルを参照して選択します。
9. **[アップロード]** をクリックします。

## <a name="create-a-relying-party-file"></a>証明書利用者ファイルを作成する

次に、作成したユーザー体験を開始する証明書利用者ファイルを更新します。

1. 作業ディレクトリに *SignUpOrSignin.xml* ファイルのコピーを作成し、名前を *ROPC_Auth.xml* に変更します。
2. 新しいファイルを開き、**TrustFrameworkPolicy** の **PolicyId** 属性の値を一意の値に変更します。 ポリシー ID がポリシーの名前になります。 たとえば、**B2C_1A_ROPC_Auth** などとします。
3. **DefaultUserJourney** の **ReferenceId** 属性の値を `ResourceOwnerPasswordCredentials` に変更します。
4. **OutputClaims** 要素を、次の要求だけを含むように変更します。

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="givenName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="surname" DefaultValue="" />
    ```

5. Azure AD B2C テナントの **[カスタム ポリシー]** ページで、 **[ポリシーのアップロード]** を選択します。
6. **[ポリシーが存在する場合は上書きする]** を有効にし、*ROPC_Auth.xml* ファイルを参照して選択します。
7. **[アップロード]** をクリックします。


::: zone-end

## <a name="test-the-ropc-flow"></a>ROPC フローをテストする

お気に入りの API 開発アプリケーションを使用して API 呼び出しを生成し、応答を確認して、ポリシーをデバッグします。 POST 要求の本文として以下の情報を使用し、次の例のような呼び出しを作成します。

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

- `<tenant-name>`を Azure AD B2C テナントの名前に置き換えます。
- `B2C_1A_ROPC_Auth` は実際のリソース所有者のパスワード資格情報ポリシーのフル ネームに置き換えます。

| Key | 値 |
| --- | ----- |
| username | `user-account` |
| password | `password1` |
| grant_type | password |
| scope | openid `application-id` offline_access |
| client_id | `application-id` |
| response_type | token id_token |

- `user-account` は、テナント内のユーザー アカウントの名前に置き換えます。
- `password1` は、ユーザー アカウントのパスワードに置き換えます。
- `application-id` は、*ROPC_Auth_app* 登録のアプリケーション ID に置き換えます。
- 更新トークンを受信する場合、*Offline_access* は省略可能です。

実際の POST 要求は次の例のようになります。

```https
POST /<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

username=contosouser.outlook.com.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```

オフライン アクセスで成功した応答は、次の例のようになります。

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki...",
    "token_type": "Bearer",
    "expires_in": "3600",
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6Ij...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki..."
}
```

## <a name="redeem-a-refresh-token"></a>更新トークンを利用する

ここで示すような POST 呼び出しを作成します。 要求の本文として、次の表の情報を使用します。

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

- `<tenant-name>`を Azure AD B2C テナントの名前に置き換えます。
- `B2C_1A_ROPC_Auth` は実際のリソース所有者のパスワード資格情報ポリシーのフル ネームに置き換えます。

| Key | 値 |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | `application-id` |
| resource | `application-id` |
| refresh_token | `refresh-token` |

- `application-id` は、*ROPC_Auth_app* 登録のアプリケーション ID に置き換えます。
- `refresh-token` は、前の応答で返送された **refresh_token** に置き換えます。

成功した応答は、次の例のようになります。

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQndhT...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQn...",
    "token_type": "Bearer",
    "not_before": 1533672990,
    "expires_in": 3600,
    "expires_on": 1533676590,
    "resource": "bef2222d56-552f-4a5b-b90a-1988a7d634c3",
    "id_token_expires_in": 3600,
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI1MTZmYzA2NS1mZjM2LTRiOTMtYWE1YS1kNmVlZGE3Y2JhYzgiLCJzdWIiOm51bGwsIm5hbWUiOiJEYXZpZE11IiwicHJlZmVycmVkX3VzZXJuYW1lIjpudWxsLCJpZHAiOiJMb2NhbEFjY291bnQifQ",
    "refresh_token": "eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMCIsInppcCI6IkRlZmxhdGUiLCJzZXIiOiIxLjAi...",
    "refresh_token_expires_in": 1209600
}
```

## <a name="use-a-native-sdk-or-app-auth"></a>ネイティブ SDK または App-Auth を使用する

Azure AD B2C は、パブリック クライアント リソース所有者のパスワード認証情報に関する OAuth 2.0 標準を満たしており、ほとんどのクライアント SDK と互換性があります。 最新情報については、[最新のベスト プラクティスを実装する OAuth 2.0 と OpenID Connect のネイティブ App SDK](https://appauth.io/) に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Azure AD B2C で使用するように構成された作業サンプル ([Android 用](https://aka.ms/aadb2cappauthropc)、[iOS 用](https://aka.ms/aadb2ciosappauthropc)) を GitHub からダウンロードしてください。
