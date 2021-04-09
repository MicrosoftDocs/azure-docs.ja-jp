---
title: UserInfo エンドポイント | Microsoft Docs
description: Azure Active Directory B2C 内のカスタム ポリシーで UserInfo エンドポイントを定義する。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/09/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c060a029b1cdbdd890ced96cab732966cb652de0
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102500582"
---
# <a name="userinfo-endpoint"></a>UserInfo エンドポイント

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

UserInfo エンドポイントは、[OpenID Connect 標準](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo) (OIDC) 仕様の一部で、認証されたユーザーに関する要求を返すためのものです。 UserInfo エンドポイントは、[EndPoint](relyingparty.md#endpoints) 要素を使用して、証明書利用者ポリシーで定義されます。  

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="userinfo-endpoint-overview"></a>UserInfo エンドポイントの概要

ユーザー情報の UserJourney では、次の点を指定します。

- **承認**: UserInfo エンドポイントは、ベアラー トークンで保護されています。 発行されたアクセス トークンは、UserInfo エンドポイントへの Authorization ヘッダーに示されます。 このポリシーは、受信トークンを検証し、ユーザーの objectId などの要求を抽出する技術プロファイルを指定します。 ユーザーの objectId は、UserInfo エンドポイント過程の応答で返されるために、要求の取得に使用されます。 
- **オーケストレーション ステップ**: 
  - オーケストレーション ステップは、ユーザーに関する情報を収集するために使用されます。 ユーザー体験は、受信アクセス トークン内の要求に基づいて、[Azure Active Directory 技術プロファイル](active-directory-technical-profile.md)を呼び出して、たとえば、objectId によってユーザーを読み取るなどユーザーに関するデータを取得します。 
  - **省略可能なオーケストレーション手順** - REST API の技術プロファイルなど、ユーザーに関する詳細情報を取得するためのオーケストレーション手順を追加できます。 
  - **UserInfo 発行者** - UserInfo エンドポイントが返す要求の一覧を指定します。

## <a name="create-a-userinfo-endpoint"></a>UserInfo エンドポイントを作成する

### <a name="1-add-the-token-issuer-technical-profile"></a>1.トークン発行者の技術プロファイルを追加する

1. *TrustFrameworkExtensions.xml* ファイルを開きます。
1. まだ存在しない場合は、ClaimsProvider 要素とその子要素を、BuildingBlocks 要素の下の最初の要素として追加します。
1. 次のクレーム プロバイダーを追加します。

    ```xml
    <!-- 
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Token Issuer</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="UserInfoIssuer">
            <DisplayName>JSON Issuer</DisplayName>
            <Protocol Name="None" />
            <OutputTokenFormat>JSON</OutputTokenFormat>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <!-- The Below claims are what will be returned on the UserInfo Endpoint if in the Claims Bag-->
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId"/>
              <InputClaim ClaimTypeReferenceId="givenName"/>
              <InputClaim ClaimTypeReferenceId="surname"/>
              <InputClaim ClaimTypeReferenceId="displayName"/>
              <InputClaim ClaimTypeReferenceId="signInNames.emailAddress"/>
            </InputClaims>
          </TechnicalProfile>
          <TechnicalProfile Id="UserInfoAuthorization">
            <DisplayName>UserInfo authorization</DisplayName>
            <Protocol Name="None" />
            <InputTokenFormat>JWT</InputTokenFormat>
            <Metadata>
              <!-- Update the Issuer and Audience below -->
              <!-- Audience is optional, Issuer is required-->
              <Item Key="issuer">https://yourtenant.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/</Item>
              <Item Key="audience">[ "22222222-2222-2222-2222-222222222222", "33333333-3333-3333-3333-333333333333" ]</Item>
              <Item Key="client_assertion_type">urn:ietf:params:oauth:client-assertion-type:jwt-bearer</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
              <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" PartnerClaimType="email"/>
              <!-- Optional claims to read from the access token. -->
              <!-- <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
                 <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
                 <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/> -->
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!-- 
    </ClaimsProviders> -->
    ``` 

1. **UserInfoIssuer** 技術プロファイル内の IutputClaims セクションは、返される属性を指定します。 UserInfoIssuer 技術プロファイルは、ユーザー体験の最後に呼び出されます。 
1. **UserInfoAuthorization** 技術プロファイルは、署名、発行者名、トークン対象ユーザーを検証し、受信トークンから要求を抽出します。 環境に合わせて次のメタデータを変更します。
    1. **発行者** - この値は、アクセス トークン要求内の `iss` 要求と同じである必要があります。 Azure AD B2C によって発行されたトークンは、形式 `https://yourtenant.b2clogin.com/your-tenant-id/v2.0/` の発行者を使用します。 詳細については、[トークン カスタマイズ](configure-tokens.md)に関するページをご覧ください。
    1. **IdTokenAudience** - この値は、アクセス トークン要求内の `aud` 要求と同じである必要があります。 Azure AD B2C では、`aud` 要求は、証明書利用者アプリケーションの ID です。 この値はコレクションであり、コンマ区切り記号を使用して複数の値をサポートします。

        次のアクセス トークンでは、`iss` 要求の値は `https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/` です。 `aud` 要求の値は `22222222-2222-2222-2222-222222222222` です。

        ```json
        {
          "exp": 1605549468,
          "nbf": 1605545868,
          "ver": "1.0",
          "iss": "https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/",
          "sub": "44444444-4444-4444-4444-444444444444",
          "aud": "22222222-2222-2222-2222-222222222222",
          "acr": "b2c_1a_signup_signin",
          "nonce": "defaultNonce",
          "iat": 1605545868,
          "auth_time": 1605545868,
          "name": "John Smith",
          "given_name": "John",
          "family_name": "Smith",
          "tid": "11111111-1111-1111-1111-111111111111"
        }
        ```
    
1.  **UserInfoAuthorization** 技術プロファイルの OutputClaims 要素は、アクセス トークンから読み取る属性を指定します。 **ClaimTypeReferenceId** は、要求の種類への参照です。 オプションの **PartnerClaimType** は、アクセス トークンで定義されている要求の名前です。



### <a name="2-add-the-userjourney-element"></a>2.UserJourney 要素を追加します 

[UserJourney](userjourneys.md) 要素は、ユーザーがアプリケーションとやり取りするときに取るパスを定義します。 `UserInfoJourney` として識別される **UserJourney** に存在していない場合は、**UserJourneys** 要素を追加します。

```xml
<!-- 
<UserJourneys> -->
  <UserJourney Id="UserInfoJourney" DefaultCpimIssuerTechnicalProfileReferenceId="UserInfoIssuer">
    <Authorization>
      <AuthorizationTechnicalProfiles>
        <AuthorizationTechnicalProfile ReferenceId="UserInfoAuthorization" />
      </AuthorizationTechnicalProfiles>
    </Authorization>
    <OrchestrationSteps >
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>objectId</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges UserIdentity="false">
          <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="UserInfoIssuer" />
    </OrchestrationSteps>
  </UserJourney>
<!-- 
</UserJourneys> -->
```

### <a name="3-include-the-endpoint-to-the-relying-party-policy"></a>3.証明書利用者ポリシーにエンドポイントを含める

証明書利用者アプリケーションに UserInfo エンドポイントを追加するには、[Endpoint](relyingparty.md#endpoints) 要素を *SocialAndLocalAccounts/SignUpOrSignIn.xml* ファイルに追加します。 

```xml
<!--
<RelyingParty> -->
  <Endpoints>
    <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
  </Endpoints>
<!-- 
</RelyingParty> -->
```

完成した証明書利用者要素は次のようになります。

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="yourtenant.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin">
  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <Endpoints>
      <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
    </Endpoints>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

### <a name="4-upload-the-files"></a>4.ファイルのアップロード

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[Identity Experience Framework]** を選択します。
1. **[カスタム ポリシー]** ページで、 **[カスタムポリシーのアップロード]** を選択します。
1. **[カスタム ポリシーが既に存在する場合は上書きする]** を選択し、*TrustframeworkExtensions.xml* ファイルを検索して選択します。
1. **[アップロード]** をクリックします。
1. その証明書利用者ファイル (*SignUpOrSignIn.xml* など) で、手順 5 から 7 を繰り返します。

## <a name="calling-the-userinfo-endpoint"></a>UserInfo エンドポイントを呼び出す

UserInfo エンドポイントは、アプリケーションのトークンを取得するときに受信したアクセス トークンを使用して呼び出される標準の OAuth2 ベアラー トークン API を利用します。 これにより、ユーザーに関する要求が含まれる JSON 応答が返されます。 UserInfo エンドポイントは、次の場所にある Azure AD B2C でホストされます。

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/openid/v2.0/userinfo
```

/.well-known 構成エンドポイント (OpenID Connect 検出ドキュメント) に `userinfo_endpoint` フィールドが一覧表示されます。 UserInfo エンドポイントは、プログラムを使って検出することができます。具体的には、次の場所にある/.well-known 構成エンドポイントを使用します。 

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/v2.0/.well-known/openid-configuration 
```

### <a name="test-the-policy"></a>ポリシーをテストする

1. **[カスタム ポリシー]** の下で、UserInfo エンドポイントを統合したポリシーを選択します。 たとえば、*B2C_1A_SignUpOrSignIn* などです。
1. **[今すぐ実行]** を選択します。 
1. **[アプリケーションの選択]** の下で、前に登録したアプリケーションを選択します。 **[応答 URL の選択]** で、`https://jwt.ms` を選択します。 詳細については、「[Azure Active Directory B2C に Web アプリケーションを登録する](tutorial-register-applications.md)」をご覧ください。
1. メール アドレスまたはソーシャル アカウントを使用してサインアップまたはサインインします。
1. [https://jwt.ms](https://jwt.ms) Web サイトから、id_token をエンコードされた形式でコピーします。 これを使用して、UserInfo エンドポイントに GET 要求を送信して、ユーザー情報を取得できます。
1. GET 要求を UserInfo エンドポイントに送信し、ユーザー情報を取得します。

```http
GET /yourtenant.onmicrosoft.com/b2c_1a_signup_signin/openid/v2.0/userinfo
Host: b2cninja.b2clogin.com
Authorization: Bearer <your access token>
```

正常な応答は次のようになります。

```json
{
    "objectId": "44444444-4444-4444-4444-444444444444",
    "givenName": "John",
    "surname": "Smith",
    "displayName": "John Smith",
    "signInNames.emailAddress": "john.s@contoso.com"
}
```

## <a name="next-steps"></a>次の手順

- UserInfo エンドポイントの例については、[GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/user-info-endpoint) を参照してください。

::: zone-end
