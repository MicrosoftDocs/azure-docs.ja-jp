---
title: Azure Active Directory B2C でのオーケストレーション手順としての REST API 要求の交換 | Microsoft Docs
description: API と統合する Azure Active Directory B2C のカスタム ポリシーに関するトピック。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/24/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 55740b74aef5ce3d2def5ad22cfe3ededa1204d8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55189699"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-an-orchestration-step"></a>チュートリアル:REST API 要求交換をオーケストレーション手順として Azure AD B2C ユーザー体験に統合する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

ID 開発者は、Azure Active Directory B2C (Azure AD B2C) の基盤となる Identity Experience Framework (IEF) を使用して、RESTful API との対話をユーザー体験に統合できます。  

このチュートリアルの最後では、RESTful サービスと対話する Azure AD B2C ユーザー体験を作成することができます。

IEF では、要求を介してデータが送受信されます。 REST API 要求の交換は、

- オーケストレーション手順として設計できます。
- 外部アクションをトリガーできます。 たとえば、外部データベースにイベントを記録できます。
- 値をフェッチしてユーザー データベースに格納するために使用できます。

受信した要求は、後で実行フローを変更するために使用できます。

検証プロファイルとして対話を設計することもできます。 詳細については、「[チュートリアル:REST API 要求交換をユーザー入力の検証として Azure AD B2C ユーザー体験に統合する](active-directory-b2c-rest-api-validation-custom.md)」をご覧ください。

このシナリオでは、ユーザーがプロファイルの編集を実行するときに、以下の操作を実行できるようにします。

1. 外部システム内のユーザーを検索します。
2. そのユーザーが登録されている都市を取得します。
3. その属性を要求としてアプリケーションに返します。

## <a name="prerequisites"></a>前提条件

- [概要](active-directory-b2c-get-started-custom.md)に関するページに記載されたローカル アカウントのサインアップ/サインインを完了するように構成された Azure AD B2C テナント。
- 対話する REST API エンドポイント。 このチュートリアルでは、単純な Azure 関数アプリの Webhook を例として使用します。
- *推奨*:[検証手順として REST API 要求交換のチュートリアル](active-directory-b2c-rest-api-validation-custom.md)を完了します。

## <a name="step-1-prepare-the-rest-api-function"></a>手順 1:REST API 関数を準備する

> [!NOTE]
> REST API 関数の設定は、この記事の範囲外です。 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) に、クラウドで RESTful サービスを作成するための優れたツールキットが用意されています。

`email` という名前の要求を受け取った後、値 `Redmond` が割り当てられた要求 `city` を返す Azure 関数が既に設定されています。 このサンプル Azure 関数は [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples) から入手できます。

Azure 関数によって返される `userMessage` 要求はこのコンテキストでは省略可能であり、IEF はそれを無視します。 これは、アプリケーションに渡して後でユーザーに表示されるメッセージとして使用できます。

```csharp
if (requestContentAsJObject.email == null)
{
    return request.CreateResponse(HttpStatusCode.BadRequest);
}

var email = ((string) requestContentAsJObject.email).ToLower();

return request.CreateResponse<ResponseContent>(
    HttpStatusCode.OK,
    new ResponseContent
    {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        userMessage = "User Found",
        city = "Redmond"
    },
    new JsonMediaTypeFormatter(),
    "application/json");
```

Azure Function App を使用すると、特定の関数の識別子を含む関数の URL を簡単に取得できます。 ここでは、URL は https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw== となります。 それは、テストの目的で使用できます。

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworextensionsxml-file"></a>手順 2:RESTful API 要求交換を技術プロファイルとして TrustFrameworkExtensions.xml ファイルに構成する

技術プロファイルは、RESTful サービスで必要となる交換の完全な構成です。 TrustFrameworkExtensions.xml ファイルを開き、次の XML スニペットを `<ClaimsProvider>` 要素の中に追加します。

> [!NOTE]
> 次の XML では、RESTful プロバイダー `Version=1.0.0.0` はプロトコルとして記述されます。 これを外部サービスと対話する関数と考えてください。 <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```XML
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-LookUpLoyaltyWebHook">
            <DisplayName>Check LookUpLoyalty Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==</Item>
                <Item Key="AuthenticationType">None</Item>
                <Item Key="SendClaimsIn">Body</Item>
                <Item Key="AllowInsecureAuthInProduction">true</Item>
            </Metadata>
            <InputClaims>
                <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="email" />
            </InputClaims>
            <OutputClaims>
                <OutputClaim ClaimTypeReferenceId="city" PartnerClaimType="city" />
            </OutputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

`<InputClaims>` 要素は、IEF から REST サービスに送信される要求を定義します。 この例では、要求 `givenName` の内容が要求 `email` として REST サービスに送信されます。  

`<OutputClaims>` 要素は、IEF が期待する REST サービスからの要求を定義します。 受信した要求の数に関係なく、IEF はここで識別されている要求のみを使用します。 この例では、`city` として受信された要求は、`city` という名前の IEF 要求にマッピングされます。

## <a name="step-3-add-the-new-claim-city-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>手順 3:新しい要求 `city` を TrustFrameworkExtensions.xml ファイルのスキーマに追加する

要求 `city` は、まだスキーマのどこにも定義されていません。 そのため、要素 `<BuildingBlocks>` の中に定義を追加します。 この要素は、TrustFrameworkExtensions.xml ファイルの先頭で見つけることができます。

```XML
<BuildingBlocks>
    <!--The claimtype city must be added to the TrustFrameworkPolicy-->
    <!-- You can add new claims in the BASE file Section III, or in the extensions file-->
    <ClaimsSchema>
        <ClaimType Id="city">
            <DisplayName>City</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Your city</UserHelpText>
            <UserInputType>TextBox</UserInputType>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-4-include-the-rest-service-claims-exchange-as-an-orchestration-step-in-your-profile-edit-user-journey-in-trustframeworkextensionsxml"></a>手順 4:TrustFrameworkExtensions.xml のプロファイルの編集ユーザー体験に REST サービス要求交換をオーケストレーション手順として含める

プロファイルの編集ユーザー体験に手順を追加します。追加場所は、ユーザーが認証され (次の XML 内のオーケストレーション手順 1 ～ 4)、ユーザーから更新されたプロファイル情報が提供された (手順 5) 後です。

> [!NOTE]
> REST API 呼び出しをオーケストレーション手順として使用できる多くのユース ケースがあります。 オーケストレーション手順としては、ユーザーが最初の登録などのタスクを正常に完了した後の外部システムに対する更新として、または情報の同期状態を保つためのプロファイルの更新として使用できます。 このケースでは、プロファイルの編集後にアプリケーションに提供される情報を増やすために使用されています。

プロファイルの編集ユーザー体験の XML コードを、TrustFrameworkBase.xml ファイルから TrustFrameworkExtensions.xml ファイルの `<UserJourneys>` 要素の中にコピーします。 次に、手順 6 の変更を行います。

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
    <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
    </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> 使用しているバージョンと順序が一致しない場合は、`ClaimsExchange` 型 `SendClaims` の前に、コードを手順として挿入してください。

ユーザー体験の最終的な XML は次のようになります。

```XML
<UserJourney Id="ProfileEdit">
    <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
            <ClaimsProviderSelections>
                <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
                <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
                <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
                <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="ClaimsExchange">
            <Preconditions>
                <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
                    <Value>authenticationSource</Value>
                    <Value>localAccountAuthentication</Value>
                    <Action>SkipThisOrchestrationStep</Action>
                </Precondition>
            </Preconditions>
            <ClaimsExchanges>
                <ClaimsExchange Id="AADUserRead" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="4" Type="ClaimsExchange">
            <Preconditions>
                <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
                    <Value>authenticationSource</Value>
                    <Value>socialIdpAuthentication</Value>
                    <Action>SkipThisOrchestrationStep</Action>
                </Precondition>
            </Preconditions>
            <ClaimsExchanges>
                <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="5" Type="ClaimsExchange">
            <ClaimsExchanges>
                <ClaimsExchange Id="B2CUserProfileUpdateExchange" TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <!-- Add a step 6 to the user journey before the JWT token is created-->
        <OrchestrationStep Order="6" Type="ClaimsExchange">
            <ClaimsExchanges>
                <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    </OrchestrationSteps>
    <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="step-5-add-the-claim-city-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>手順 5:要求 `city` がアプリケーションに送信されるように、その要求を証明書利用者ポリシー ファイルに追加する

ProfileEdit.xml 証明書利用者 (RP) ファイルを編集し、`<TechnicalProfile Id="PolicyProfile">` 要素を修正して `<OutputClaim ClaimTypeReferenceId="city" />` を追加します。

新しい要求を追加した後の技術プロファイルは次のようになります。

```XML
<DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="city" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="step-6-upload-your-changes-and-test"></a>手順 6:変更内容をアップロードしてテストする

ポリシーの既存のバージョンを上書きします。

1.  (省略可能:)続行する前に、拡張機能ファイルの既存のバージョンを (ダウンロードして) 保存します。 最初の複雑さを低く抑えるために、拡張機能ファイルの複数のバージョンをアップロードしないことをお勧めします。
2.  (省略可能:)ポリシー編集ファイルに対するポリシー ID の新しいバージョンを、`PolicyId="B2C_1A_TrustFrameworkProfileEdit"` を変更することで、その名前を変更します。
3.  拡張機能ファイルをアップロードします。
4.  ポリシー編集 RP ファイルをアップロードします。
5.  **[今すぐ実行]** を使用してポリシーをテストします。 IEF からアプリケーションに返されるトークンを確認します。

すべてが適切に設定されている場合、トークンに新しい要求 `city` が含まれ、その値が `Redmond` となります。

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>次の手順

[REST API を検証手順として使用する](active-directory-b2c-rest-api-validation-custom.md)

[プロファイルの編集を変更してユーザーから追加情報を収集する方法](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)
