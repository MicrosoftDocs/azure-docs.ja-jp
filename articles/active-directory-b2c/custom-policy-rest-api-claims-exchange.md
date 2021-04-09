---
title: REST API 要求の交換 - Azure Active Directory B2C
description: Active Directory B2C で REST API 要求の交換をカスタム ポリシーに追加します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 84053df34ffda0d4686ad80a9e5f3af00ac53d72
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94949498"
---
# <a name="walkthrough-add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>チュートリアル:Azure Active Directory B2C で REST API 要求の交換をカスタム ポリシーに追加する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

ID 開発者は、Azure Active Directory B2C (Azure AD B2C) で、RESTful API との対話をユーザー体験に統合できます。 このチュートリアルの最後では、[RESTful サービス](custom-policy-rest-api-intro.md)と対話する Azure AD B2C ユーザー体験を作成することができます。

このシナリオでは、企業の基幹業務ワークフローと統合し、ユーザーのトークン データを強化します。 Azure AD B2C は、ローカルまたはフェデレーション アカウントを使用したサインアップまたはサインイン時に REST API を呼び出し、リモート データ ソースからユーザーの拡張プロファイル データを取得します。 このサンプルでは、Azure AD B2C は、ユーザーの一意の識別子である objectId を送信します。 これにより REST API は、ユーザーのアカウント残高 (ランダムな数値) を返します。 このサンプルは、お使いのご自分の CRM システム、マーケティング データベース、または任意の基幹業務ワークフローと統合して、出発点として使用することができます。

また、対話を検証技術プロファイルとして設計することもできます。 これは、REST API が画面上のデータを検証して要求を返す場合に適しています。 詳細については、「[チュートリアル:ユーザー入力の検証として REST API 要求交換を Azure AD B2C ユーザー体験に統合する](custom-policy-rest-api-claims-validation.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

- [カスタム ポリシーの概要](custom-policy-get-started.md)に関するページの手順を完了します。 ローカル アカウントでのサインアップとサインインのために作業用カスタム ポリシーを持つ必要があります。
- [REST API 要求交換のお使いの Azure Active Directory B2C カスタム ポリシーへの統合](custom-policy-rest-api-intro.md)に関する方法を参照してください。

## <a name="prepare-a-rest-api-endpoint"></a>REST API エンドポイントを準備する

このチュートリアルでは、ユーザーの Azure AD B2C の objectId がご自分のバックエンド システムに登録されているかどうかを検証する REST API が必要です。 登録されている場合、REST API によってユーザー アカウントの残高が返されます。 それ以外の場合は、REST API によってディレクトリに新しいアカウントが登録され、`50.00` の開始残高が返されます。

次の JSON コードでは、お使いの REST API エンドポイントに送信される Azure AD B2C のデータを示しています。 

```json
{
    "objectId": "User objectId",
    "lang": "Current UI language"
}
```

データがお使いの REST API によって検証されると、次の JSON データと共に HTTP 200 (Ok) が返されます。

```json
{
    "balance": "760.50"
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
<ClaimType Id="balance">
  <DisplayName>Your Balance</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>RESTful API 技術プロファイルを追加する 

[Restful 技術プロファイル](restful-technical-profile.md)では、お使いの独自の RESTful サービスとのインターフェイスをサポートしています。 Azure AD B2C は、`InputClaims` コレクションでデータを RESTful サービスに送信し、`OutputClaims` コレクションで返却データを受信します。 お使いの <em> **`TrustFrameworkExtensions.xml`**</em> ファイルで **ClaimsProviders** を見つけ、次のように新しい要求プロバイダーを追加します。

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="balance" />
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

## <a name="add-an-orchestration-step"></a>オーケストレーション手順を追加する

[ユーザー体験](userjourneys.md)では、証明書利用者アプリケーションがユーザーの任意の要求を取得できるようにする、ポリシーの明示的なパスを指定します。 ユーザー体験は、トランザクションを成功させるために従う必要のあるオーケストレーション シーケンスとして表されます。 オーケストレーションのステップは追加または削除できます。 この場合は、ユーザーのサインアップまたはサインイン後に、REST API の呼び出しでアプリケーションに提供する、情報を拡張する新しいオーケストレーション ステップを追加します。

1. ポリシーの基本ファイルを開きます。 たとえば、<em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em>です。
1. `<UserJourneys>` 要素を検索します。 要素全体をコピーしてから、削除します。
1. お使いのポリシーの拡張ファイルを開きます。 たとえば、<em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>です。
1. `<ClaimsProviders>` 要素を閉じた後、拡張ファイルに `<UserJourneys>` を貼り付けます。
1. `<UserJourney Id="SignUpOrSignIn">` を見つけて、最後のオーケストレーション ステップの前に次のオーケストレーション ステップを追加します。

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. `Order` を `8` に変更して、最後のオーケストレーション ステップをリファクターします。 お使いの最後の 2 つのオーケストレーション ステップは次のようになります。

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. **ProfileEdit** と **PasswordReset** のユーザー体験で、最後の 2 つのステップを繰り返します。


## <a name="include-a-claim-in-the-token"></a>トークンに要求を含める 

`balance` 要求を証明書利用者アプリケーションに返すには、出力要求を <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> ファイルに追加します。 出力要求を追加すると、ユーザー体験が成功した後、その要求がトークンに追加され、アプリケーションに送信されます。 証明書利用者セクション内の技術プロファイル要素を変更して、`balance` を出力要求として追加します。
 
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
      <OutputClaim ClaimTypeReferenceId="balance" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

**ProfileEdit.xml** および **PasswordReset.xml** のユーザー体験にこの手順を繰り返します。

変更した次のファイルを保存します。*TrustFrameworkBase.xml* および *TrustFrameworkExtensions.xml*、*SignUpOrSignin.xml*、*ProfileEdit.xml*、および *PasswordReset.xml*。 

## <a name="test-the-custom-policy"></a>カスタム ポリシーをテストする

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご利用の Azure AD テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターをクリックして、ご利用の Azure AD テナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択し、 **[アプリの登録]** を検索して選択します。
1. **[Identity Experience Framework]** を選択します。
1. **[カスタム ポリシーのアップロード]** を選択し、変更した次のポリシー ファイルをアップロードします。*TrustFrameworkBase.xml* および *TrustFrameworkExtensions.xml*、*SignUpOrSignin.xml*、*ProfileEdit.xml*、および *PasswordReset.xml*。 
1. アップロードしたサインアップまたはサインイン ポリシーを選択し、 **[今すぐ実行]** ボタンをクリックします。
1. メール アドレスまたは Facebook のアカウントを使用してサインアップできるはずです。
1. アプリケーションに返送されるトークンには、`balance` 要求が含まれています。

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584961516,
  "nbf": 1584957916,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584957916,
  "auth_time": 1584957916,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "balance": "202.75"
  ...
}
```

## <a name="next-steps"></a>次のステップ

API をセキュリティで保護する方法については、次の記事を参照してください。

- [チュートリアル:REST API 要求交換をオーケストレーション手順として Azure AD B2C ユーザー体験に統合する](custom-policy-rest-api-claims-exchange.md)
- [お使いの RESTful API を保護する](secure-rest-api.md)
- [リファレンス: RESTful 技術プロファイル](restful-technical-profile.md)