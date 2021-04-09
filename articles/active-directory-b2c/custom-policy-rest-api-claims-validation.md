---
title: 検証としての REST API 要求交換
titleSuffix: Azure AD B2C
description: RESTful サービスと対話する Azure AD B2C ユーザー体験を作成するためのチュートリアル。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 761bc4db7760ef5e84e3fc3c8a5deea5d4508f51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94951929"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-to-validate-user-input"></a>チュートリアル:ユーザー入力の検証として REST API 要求交換を Azure AD B2C ユーザー体験に統合する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

ID 開発者は、Azure Active Directory B2C (Azure AD B2C) の基盤となる Identity Experience Framework (IEF) を使用して、RESTful API との対話をユーザー体験に統合できます。  このチュートリアルの最後では、ユーザー入力を検証するために、[RESTful サービス](custom-policy-rest-api-intro.md)と対話する Azure AD B2C ユーザー体験を作成できます。

このシナリオでは、ユーザーが Azure AD B2C サインアップ ページにロイヤルティ番号を入力する機能を追加します。 このデータを REST API に送信することによって、電子メールとロイヤルティ番号のこの組み合わせがプロモーション コードにマップされているかどうかを検証します。 REST API がこのユーザーのプロモーション コードを見つけると、Azure AD B2C に返されます。 最後に、アプリケーションが使用するトークン要求にプロモーション コードが挿入されます。

対話は、オーケストレーション手順として設計することもできます。 これは、REST API が画面上のデータを検証せず、常に要求を返す場合に適しています。 詳細については、「[チュートリアル:REST API 要求交換をオーケストレーション手順として Azure AD B2C ユーザー体験に統合する](custom-policy-rest-api-claims-exchange.md)を参照してください。

## <a name="prerequisites"></a>前提条件

- [カスタム ポリシーの概要](custom-policy-get-started.md)に関するページの手順を完了します。 ローカル アカウントでのサインアップとサインインのために作業用カスタム ポリシーを持つ必要があります。
- [REST API 要求交換のお使いの Azure Active Directory B2C カスタム ポリシーへの統合](custom-policy-rest-api-intro.md)に関する方法を参照してください。

## <a name="prepare-a-rest-api-endpoint"></a>REST API エンドポイントを準備する

このチュートリアルでは、メール アドレスがバックエンド システムに登録されているかどうかを、ロイヤルティ ID を使用して検証する REST API が必要です。 登録されている場合、REST API は、顧客がアプリケーション内で商品を購入するために使用できる登録プロモーション コードを返す必要があります。 それ以外の場合、REST API は次の HTTP 409 エラー メッセージを返します: "Loyalty ID '{loyalty ID}' is not associated with '{email}' email address." (ロイヤルティ ID '{loyalty ID}' は '{email}' 電子メール アドレスに関連付けられていません。)

次の JSON コードでは、お使いの REST API エンドポイントに送信される Azure AD B2C のデータを示しています。 

```json
{
    "email": "User email address",
    "language": "Current UI language",
    "loyaltyId": "User loyalty ID"
}
```

データがお使いの REST API によって検証されると、次の JSON データと共に HTTP 200 (Ok) が返されます。

```json
{
    "promoCode": "24534"
}
```

検証が失敗した場合、REST API は `userMessage` JSON 要素と共に HTTP 409 (Conflict) を返す必要があります。 IEF は、REST API が返す `userMessage` 要求を予期しています。 検証が失敗した場合、この要求はユーザーに文字列として表示されます。

```json
{
    "version": "1.0.1",
    "status": 409,
    "userMessage": "LoyaltyId ID '1234' is not associated with 'david@contoso.com' email address."
}
```

REST API エンドポイントの設定は、この記事では扱っていません。 [Azure Functions](../azure-functions/functions-reference.md) のサンプルを作成しました。 Azure 関数の完全なコードは、[GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function) から入手できます。

## <a name="define-claims"></a>要求を定義する

要求は、Azure AD B2C ポリシーの実行時に、データの一時的なストレージとなります。 要求は、[claims schema](claimsschema.md) セクションで宣言できます。 

1. お使いのポリシーの拡張ファイルを開きます。 たとえば、<em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>です。
1. [BuildingBlocks](buildingblocks.md) 要素を検索します。 要素が存在しない場合は追加します。
1. [ClaimsSchema](claimsschema.md) 要素を見つけます。 要素が存在しない場合は追加します。
1. 次の要求を **ClaimsSchema** 要素に追加します。  

```xml
<ClaimType Id="loyaltyId">
  <DisplayName>Your loyalty ID</DisplayName>
  <DataType>string</DataType>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
<ClaimType Id="promoCode">
  <DisplayName>Your promo code</DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
</ClaimType>
  <ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>RESTful API 技術プロファイルを追加する 

[Restful 技術プロファイル](restful-technical-profile.md)では、お使いの独自の RESTful サービスへのインターフェイスをサポートしています。 Azure AD B2C は、`InputClaims` コレクションでデータを RESTful サービスに送信し、`OutputClaims` コレクションで返却データを受信します。 **ClaimsProviders** 要素を見つけて、次のように新しい要求プロバイダーを追加します。

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Check loyaltyId Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/ValidateProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="loyaltyId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

この例では、`userLanguage` が JSON ペイロード内の `lang` として REST サービスに送信されます。 `userLanguage` 要求の値には、現在のユーザーの言語 ID が含まれます。 詳細については、[要求リゾルバー](claim-resolver-overview.md)に関するページを参照してください。

### <a name="configure-the-restful-api-technical-profile"></a>RESTful API 技術プロファイルを構成する 

REST API をデプロイした後、`REST-ValidateProfile` 技術プロファイルのメタデータを、次のような独自の REST API を反映させるように設定します。

- **ServiceUrl**: REST API エンドポイントの URL を設定します。
- **SendClaimsIn**: RESTful クレーム プロバイダーへの入力要求の送信方法を指定します。
- **AuthenticationType**: RESTful 要求プロバイダーにより実行されている認証の種類を設定します。 
- **AllowInsecureAuthInProduction**: 運用環境では、このメタデータを必ず `true` に設定してください。
    
詳細な構成については、[RESTful 技術プロファイルのメタデータ](restful-technical-profile.md#metadata)に関する記事を参照してください。

`AuthenticationType` と `AllowInsecureAuthInProduction` の上のコメントに、運用環境に移行するときに行う必要がある変更が指定されています。 お使いの RESTful API を実稼働用にセキュリティで保護する方法については、[RESTful API のセキュリティ保護](secure-rest-api.md)に関するページを参照してください。

## <a name="validate-the-user-input"></a>ユーザー入力の検証

サインアップ時にユーザーのロイヤルティ番号を取得するには、ユーザーが画面上でこのデータを入力できるようにする必要があります。 **loyaltyId** 出力要求を、既存のサインアップ技術プロファイル セクションの `OutputClaims` 要素に追加することによって、サインアップページに追加します。 要求が画面に表示される順序を制御するには、出力要求の一覧全体を指定します。  

検証技術プロファイルの参照を、`REST-ValidateProfile` を呼び出すサインアップ技術プロファイルに追加します。 新しい検証技術プロファイルが、基本ポリシーで定義されている `<ValidationTechnicalProfiles>` コレクションの先頭に追加されます。 この動作は、検証が成功した後にのみ、Azure AD B2C がディレクトリにアカウントを作成することを意味します。   

1. **ClaimsProviders** 要素を見つけます。 新しい要求プロバイダーを次のように追加します。

    ```xml
    <ClaimsProvider>
      <DisplayName>Local Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surName"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Self Asserted</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SelfAsserted-Social">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" />
          </InputClaims>
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surname"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile"/>
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

## <a name="include-a-claim-in-the-token"></a>トークンに要求を含める 

プロモーション コード要求を証明書利用者アプリケーションに返すには、出力要求を <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> ファイルに追加します。 ユーザー体験が成功した後、出力要求によって要求がトークンに追加され、アプリケーションに送信されます。 証明書利用者セクション内の技術プロファイル要素を変更して、`promoCode` を出力要求として追加します。
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="promoCode" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>カスタム ポリシーをテストする

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご利用の Azure AD テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターをクリックして、ご利用の Azure AD テナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択し、 **[アプリの登録]** を検索して選択します。
1. **[Identity Experience Framework]** を選択します。
1. **[カスタム ポリシーのアップロード]** を選択し、変更した次のポリシー ファイルをアップロードします。*TrustFrameworkExtensions.xml*、および *SignUpOrSignin.xml*。 
1. アップロードしたサインアップまたはサインイン ポリシーを選択し、 **[今すぐ実行]** ボタンをクリックします。
1. メール アドレスを使用してサインアップできることを確認します。
1. **[Sign-up now]\(今すぐサインアップ\)** リンクをクリックします。
1. **[Your loyalty ID]\(ロイヤルティ ID\)** に「1234」と入力して **[続行]** をクリックします。 この時点で、検証エラー メッセージが表示されるはずです。
1. 別の値に変更して **[続行]** をクリックします。
1. アプリケーションに返送されるトークンには、`promoCode` 要求が含まれています。

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584295703,
  "nbf": 1584292103,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584292103,
  "auth_time": 1584292103,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "promoCode": "84362"
  ...
}
```

## <a name="next-steps"></a>次のステップ

API をセキュリティで保護する方法については、次の記事を参照してください。

- [チュートリアル:REST API 要求交換をオーケストレーション手順として Azure AD B2C ユーザー体験に統合する](custom-policy-rest-api-claims-exchange.md)
- [お使いの RESTful API を保護する](secure-rest-api.md)
- [リファレンス: RESTful 技術プロファイル](restful-technical-profile.md)