---
title: REST API 要求の交換 - Azure Active Directory B2C
description: Active Directory B2C で REST API 要求の交換をカスタム ポリシーに追加します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0bdef508e12a3b11143149b330da73838b53f860
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439008"
---
# <a name="add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C で REST API 要求の交換をカスタム ポリシーに追加する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C で RESTful API との対話を[カスタム ポリシー](active-directory-b2c-overview-custom.md)に追加できます。 この記事では、RESTful サービスと対話する Azure AD B2C ユーザー体験を作成する方法について説明します。

対話には REST API 要求と Azure AD B2C の間の情報の要求の交換が含まれています。 要求の交換の特性を次に示します。

- オーケストレーション手順として設計できます。
- 外部アクションをトリガーできます。 たとえば、外部データベースにイベントを記録できます。
- 値をフェッチしてユーザー データベースに格納するために使用できます。
- 実行フローを変更します。

この記事のシナリオには次のアクションが含まれています。

1. 外部システム内のユーザーを検索します。
2. そのユーザーが登録されている都市を取得します。
3. その属性を要求としてアプリケーションに返します。

## <a name="prerequisites"></a>前提条件

- [カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)に関するページの手順を完了します。
- 対話する REST API エンドポイント。 このチュートリアルでは、単純な Azure 関数を例として使用します。 Azure 関数を作成するには、「[Azure portal で初めての関数を作成する](../azure-functions/functions-create-first-azure-function.md)」を参照してください。

## <a name="prepare-the-api"></a>API を準備する

このセクションでは、`email` の値を受け取ってから、Azure AD B2C が要求として使用できる `city` の値を返すように Azure 関数を準備します。

作成した Azure 関数の run.csx ファイルを、次のコードを使用するように変更します。

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
  string email = req.Query["email"];
  string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
  dynamic data = JsonConvert.DeserializeObject(requestBody);
  email = email ?? data?.email;

  return email != null
    ? (ActionResult)new OkObjectResult(
      new ResponseContent
      {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        city = "Redmond"
      })
      : new BadRequestObjectResult("Please pass an email on the query string or in the request body");
}

public class ResponseContent
{
    public string version { get; set; }
    public int status { get; set; }
    public string city {get; set; }
}
```

## <a name="configure-the-claims-exchange"></a>要求の交換を構成する

技術プロファイルには、要求の交換に対する構成が用意されています。

*TrustFrameworkExtensions.xml* ファイルを開き、次の **ClaimsProvider** XML 要素を **ClaimsProviders** 要素内に追加します。

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureFunctions-WebHook">
      <DisplayName>Azure Function Web Hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://myfunction.azurewebsites.net/api/HttpTrigger1?code=bAZ4lLy//ZHZxmncM8rI7AgjQsrMKmVXBpP0vd9smOzdXDDUIaLljA==</Item>
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

**InputClaims** 要素によって、REST サービスに送信される要求が定義されます。 この例では、要求 `givenName` の値が要求 `email` として REST サービスに送信されます。 **OutputClaims** 要素によって、REST サービスから期待される要求が定義されます。

## <a name="add-the-claim-definition"></a>要求の定義を追加する

`city` の定義を **BuildingBlocks** 要素内に追加します。 この要素は、TrustFrameworkExtensions.xml ファイルの先頭で見つけることができます。

```XML
<BuildingBlocks>
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

## <a name="add-an-orchestration-step"></a>オーケストレーション手順を追加する

REST API 呼び出しをオーケストレーション手順として使用できる多くのユース ケースがあります。 オーケストレーション手順としては、ユーザーが最初の登録などのタスクを正常に完了した後の外部システムに対する更新として、または情報の同期状態を保つためのプロファイルの更新として使用できます。 このケースでは、プロファイルの編集後にアプリケーションに提供される情報を増やすために使用されています。

ステップをプロファイル編集ユーザー体験に追加します。 ユーザーは、自身が認証されてから (次の XML 内のオーケストレーション手順 1 ～ 4)、更新されたプロファイル情報が提供します (手順 5)。 プロファイル編集ユーザー体験の XML コードを、*TrustFrameworkBase.xml* ファイルから *TrustFrameworkExtensions.xml* ファイルの **UserJourneys** 要素内にコピーします。 次に、手順 6 を変更します。

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
  </ClaimsExchanges>
</OrchestrationStep>
```

ユーザー体験の最終的な XML は次の例のようになります。

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
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
  </OrchestrationSteps>
  <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="add-the-claim"></a>要求を追加する

*ProfileEdit.xml* ファイルを編集し、`<OutputClaim ClaimTypeReferenceId="city" />` を **OutputClaims** 要素に追加します。

新しい要求を追加した後の技術プロファイルは次の例のようになります。

```XML
<TechnicalProfile Id="PolicyProfile">
  <DisplayName>PolicyProfile</DisplayName>
  <Protocol Name="OpenIdConnect" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
    <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    <OutputClaim ClaimTypeReferenceId="city" />
  </OutputClaims>
  <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="upload-your-changes-and-test"></a>変更内容をアップロードしてテストする

1. (省略可能:)続行する前に、ファイルの既存のバージョンを (ダウンロードして) 保存します。
2. *TrustFrameworkExtensions.xml* と *ProfileEdit.xml* をアップロードし、選択して、既存のファイルを上書きします。
3. **B2C_1A_ProfileEdit** を選択します。
4. カスタム ポリシーの概要ページの **[アプリケーションの選択]** で、以前に登録した *webapp1* という名前の Web アプリケーションを選択します。 **[返信 URL]** が `https://jwt.ms` であることを確認します。
4. **[今すぐ実行]** を選択します。 お使いのアカウントの資格情報を使用してサインインし、 **[続行]** をクリックします。

すべてが適切に設定されている場合、トークンには新しい要求 `city` が追加され、値 `Redmond` が設定されています。

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_profileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>次の手順

- 検証プロファイルとして対話を設計することもできます。 詳細については、「[チュートリアル:REST API 要求交換をユーザー入力の検証として Azure AD B2C ユーザー体験に統合する](active-directory-b2c-rest-api-validation-custom.md)」をご覧ください。
- [プロファイルの編集を変更してユーザーから追加情報を収集する方法](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)
