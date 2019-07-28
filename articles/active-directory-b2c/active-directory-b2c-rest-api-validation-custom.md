---
title: Azure Active Directory B2C での検証としての REST API 要求の交換 | Microsoft Docs
description: Azure Active Directory B2C のカスタム ポリシーに関するトピック。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/24/2017
ms.author: marsma
ms.subservice: b2c
ms.openlocfilehash: 0779e4a93230a90b8eee76f1898154c1a5b82661
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508724"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>チュートリアル:ユーザー入力の検証として REST API 要求交換を Azure AD B2C ユーザー体験に統合する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

ID 開発者は、Azure Active Directory B2C (Azure AD B2C) の基盤となる Identity Experience Framework (IEF) を使用して、RESTful API との対話をユーザー体験に統合できます。  

このチュートリアルの最後では、RESTful サービスと対話する Azure AD B2C ユーザー体験を作成することができます。

IEF では、要求を介してデータが送受信されます。 API との対話は、

- REST API 要求交換として、またはオーケストレーション手順内で使用される検証プロファイルとして設計できます。
- 通常は、ユーザーからの入力を検証します。 ユーザーからの値を拒否する場合は、エラー メッセージを返して、ユーザーが有効な値を入力し直すことができるように設定できます。

対話は、オーケストレーション手順として設計することもできます。 詳細については、[チュートリアル:REST API 要求交換をオーケストレーション手順として Azure AD B2C ユーザー体験に統合する](active-directory-b2c-rest-api-step-custom.md)を参照してください。

検証プロファイルの例として、スターター パック ファイル ProfileEdit.xml のプロファイルの編集ユーザー体験を使用します。

プロファイルの編集でユーザーによって指定された名前が除外リストに含まれていないことを確認できます。

## <a name="prerequisites"></a>前提条件

- [概要](active-directory-b2c-get-started-custom.md)に関するページに記載されたローカル アカウントのサインアップ/サインインを完了するように構成された Azure AD B2C テナント。
- 対話する REST API エンドポイント。 このチュートリアルでは、REST API サービスを使用する [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/) という名前のデモ サイトを設定します。

## <a name="step-1-prepare-the-rest-api-function"></a>手順 1:REST API 関数を準備する

> [!NOTE]
> REST API 関数の設定は、この記事の範囲外です。 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) に、クラウドで RESTful サービスを作成するための優れたツールキットが用意されています。

`playerTag` と想定される要求を受信する Azure 関数が既に作成されています。 この関数は、この要求が存在するかどうかを検証します。 完全な Azure 関数のコードは、[GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples) から入手できます。

```csharp
if (requestContentAsJObject.playerTag == null)
{
  return request.CreateResponse(HttpStatusCode.BadRequest);
}

var playerTag = ((string) requestContentAsJObject.playerTag).ToLower();

if (playerTag == "mcvinny" || playerTag == "msgates123" || playerTag == "revcottonmarcus")
{
  return request.CreateResponse<ResponseContent>(
    HttpStatusCode.Conflict,
    new ResponseContent
    {
      version = "1.0.0",
      status = (int) HttpStatusCode.Conflict,
      userMessage = $"The player tag '{requestContentAsJObject.playerTag}' is already used."
    },
    new JsonMediaTypeFormatter(),
    "application/json");
}

return request.CreateResponse(HttpStatusCode.OK);
```

IEF は、Azure 関数が返す `userMessage` 要求を予期しています。 この要求は、検証が失敗した場合 (上の例では 409 競合ステータスが返った場合) に、文字列としてユーザーに提示されます。

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>手順 2:RESTful API 要求交換を技術プロファイルとして TrustFrameworkExtensions.xml ファイルに構成する

技術プロファイルは、RESTful サービスで必要となる交換の完全な構成です。 TrustFrameworkExtensions.xml ファイルを開き、次の XML スニペットを `<ClaimsProviders>` 要素の中に追加します。

> [!NOTE]
> 次の XML では、RESTful プロバイダー `Version=1.0.0.0` はプロトコルとして記述されます。 これを外部サービスと対話する関数と考えてください。 <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-CheckPlayerTagWebHook">
            <DisplayName>Check Player Tag Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/CheckPlayerTagWebHook?code=L/05YRSpojU0nECzM4Tp3LjBiA2ZGh3kTwwp1OVV7m0SelnvlRVLCg==</Item>
                <Item Key="AuthenticationType">None</Item>
                <Item Key="SendClaimsIn">Body</Item>
                <Item Key="AllowInsecureAuthInProduction">true</Item>
            </Metadata>
            <InputClaims>
                <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="playerTag" />
            </InputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
        <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
            <ValidationTechnicalProfiles>
                <ValidationTechnicalProfile ReferenceId="AzureFunctions-CheckPlayerTagWebHook" />
            </ValidationTechnicalProfiles>
        </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

`InputClaims` 要素は、IEF から REST サービスに送信される要求を定義します。 この例では、要求 `givenName` の内容が `playerTag` として REST サービスに送信されます。 この例では、IEF は、要求が戻ることを期待していません。 代わりに、REST サービスからの応答のために待機し、受信したステータス コードに基づいて動作します。

## <a name="step-3-include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-want-to-validate-the-user-input"></a>手順 3:ユーザー入力を検証するセルフアサート技術プロファイルに RESTful サービス要求交換を含める

検証手順は、ユーザーとの対話で使用するのが最も一般的です。 ユーザー入力が期待されるすべての対話は、*セルフアサート技術プロファイル*です。 この例では、この検証を Self-Asserted-ProfileUpdate 技術プロファイルに追加します。 これは、証明書利用者 (RP) ポリシー ファイル`Profile Edit` が使用する技術プロファイルです。

要求交換をセルフアサート技術プロファイルに追加するには:

1. TrustFrameworkBase.xml ファイルを開き、`<TechnicalProfile Id="SelfAsserted-ProfileUpdate">` を検索します。
2. この技術プロファイルの構成を確認します。 ユーザーとの交換が、ユーザーに対する要求 (入力要求) とセルフ アサート プロバイダーから返されることが期待される要求 (出力要求) として、どのように定義されているかを確認します。
3. `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate` を検索します。このプロファイルは、オーケストレーション手順 5 `<UserJourney Id="ProfileEdit">` として呼び出されます。

## <a name="step-4-upload-and-test-the-profile-edit-rp-policy-file"></a>手順 4:プロファイルの編集 RP ポリシー ファイルをアップロードしてテストする

1. TrustFrameworkExtensions.xml ファイルの新しいバージョンをアップロードします。
2. **[今すぐ実行]** を使用してプロファイルの編集 RP ポリシー ファイルをテストします。
3. **[名]** フィールドにいずれかの既存の名前 (例: mcvinny) を入力して、検証機能をテストします。 すべてが適切に設定されていれば、そのプレーヤー タグは既に使用されていることをユーザーに通知するメッセージが表示されます。

## <a name="next-steps"></a>次の手順

[プロファイルの編集とユーザー登録を変更してユーザーから追加情報を収集する](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

[チュートリアル:REST API 要求交換をオーケストレーション手順として Azure AD B2C ユーザー体験に統合する](active-directory-b2c-rest-api-step-custom.md)
