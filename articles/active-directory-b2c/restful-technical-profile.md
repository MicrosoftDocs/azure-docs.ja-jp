---
title: カスタム ポリシーで RESTful 技術プロファイルを定義する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C 内のカスタム ポリシーで RESTful 技術プロファイルを定義します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: eb6d82019cccd1da327461cb0a0635aea4f3647f
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174973"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C カスタム ポリシーで RESTful 技術プロファイルを定義する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) では、独自の RESTful サービスの統合に対するサポートを提供しています。 Azure AD B2C は、入力要求コレクションでデータを RESTful サービスに送信し、出力要求コレクションで返却データを受信します。 詳細については、「[REST API 要求交換の Azure AD B2C カスタム ポリシーへの統合](custom-policy-rest-api-intro.md)」を参照してください。  

## <a name="protocol"></a>Protocol

**Protocol** 要素の **Name** 属性は `Proprietary` に設定する必要があります。 **handler** 属性には、Azure AD B2C により使用される、プロトコルハンドラー アセンブリの完全修飾名が存在する必要があります `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`。

RESTful 技術プロファイルの例を次に示します。

```xml
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>入力クレーム

**InputClaims** 要素には、REST API に送信する要求の一覧が存在します。 REST API で定義される名前に、要求の名前をマップすることもできます。 次の例は、ポリシーと REST API の間のマッピングを示しています。 **givenName** 要求は **firstName** として、**surname** は **lastName** として、REST API に送信されます。 **email** 要求はそのまま送信されます。

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

**InputClaimsTransformations** 要素には、REST API に送信する前に、入力要求を修正したり新しい要求を生成するために使用される、**InputClaimsTransformation** 要素のコレクションが存在する場合があります。

## <a name="send-a-json-payload"></a>JSON ペイロードを送信する

REST API 技術プロファイルを使用すると、複雑な JSON ペイロードをエンドポイントに送信できます。

複雑な JSON ペイロードを送信するには、次のようにします。

1. [GenerateJson](json-transformations.md) 要求の変換を使用して JSON ペイロードをビルドします。
1. REST API の技術プロファイルで、次のようにします。
    1. `GenerateJson` 要求の変換への参照を使用して、入力要求の変換を追加します。
    1. `SendClaimsIn` メタデータ オプションを `body` に設定します。
    1. `ClaimUsedForRequestPayload` メタデータ オプションを、JSON ペイロードを含む要求の名前に設定します。
    1. 入力要求に、JSON ペイロードを含む入力要求への参照を追加します。

次の例 `TechnicalProfile` では、サードパーティの電子メール サービス (この場合は SendGrid) を使用して、確認メールを送信します。

```xml
<TechnicalProfile Id="SendGrid">
  <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
    <Item Key="DefaultUserMessageIfRequestFailed">Cannot process your request right now, please try again later.</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridApiKey" />
  </CryptographicKeys>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="output-claims"></a>出力クレーム

**OutputClaims** 要素には、REST API により返される要求の一覧が存在します。 お使いのポリシーで定義される要求の名前を、REST API で定義される名前にマップする必要があるかもしれません。 `DefaultValue` 属性を設定している限り、REST API の ID プロバイダーにより返されない要求を追加することもできます。

**OutputClaimsTransformations** 要素には、出力要求を修正したり新しい要求を生成するために使用される、**OutputClaimsTransformation** 要素のコレクションが含まれている場合があります。

次の例は、REST API により返される要求を示しています。

- **loyaltyNumber** 要求名にマップされている **MembershipId** 要求。

また、技術プロファイルは、ID プロバイダーにより返されない要求も返します。

- 既定値が `true` に設定されている **loyaltyNumberIsNew** 要求。

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| ServiceUrl | はい | REST API エンドポイントの URL。 |
| AuthenticationType | はい | RESTful 要求プロバイダーにより実行されている認証の種類。 指定できる値: `None`、`Basic`、`Bearer`、`ClientCertificate`、または `ApiKeyHeader`。 <br /><ul><li>`None` の値は、REST API が匿名であることを示します。 </li><li>`Basic` の値は、REST API が HTTP 基本認証で保護されていることを示します。 Azure AD B2C などの検証されたユーザーのみが API にアクセスできます。 </li><li>`ClientCertificate` の (推奨) 値は、REST API がクライアント証明書認証を使用してアクセスを制限していることを示します。 Azure AD B2C などの適切な証明書を持つサービスのみが、ご利用の API にアクセスできます。 </li><li>`Bearer` 値は、REST API ではクライアント OAuth2 ベアラー トークンを使用してアクセスが制限されることを示します。 </li><li>`ApiKeyHeader` 値は、REST API が API キーの HTTP ヘッダー (*x-functions-key* など) で保護されていることを示します。 </li></ul> |
| AllowInsecureAuthInProduction| いいえ| 運用環境で `AuthenticationType` を `none` に設定できるかどうかを示します ([TrustFrameworkPolicy](trustframeworkpolicy.md) の `DeploymentMode` を `Production` に設定するか、指定しません)。 有効な値: true または false (既定値)。 |
| SendClaimsIn | いいえ | RESTful クレーム プロバイダーへの入力要求の送信方法を指定します。 指定できる値: `Body` (既定)、`Form`、`Header`、`Url`、または `QueryString`。 `Body` の値は、要求本文で、JSON 形式で送信される入力要求です。 `Form` の値は、要求本文で、キーの値をアンパサンド ' &' で区切った形式で送信される入力要求です。 `Header` の値は、要求本文で送信される入力要求です。 `Url` 値は、URL で送信される入力要求です。例: https://{claim1}.example.com/{claim2}/{claim3}?{claim4}={claim5}。 `QueryString` の値は、要求クエリ文字列で送信される入力要求です。 それぞれによって呼び出される HTTP 動詞は次のとおりです。<br /><ul><li>`Body`:POST</li><li>`Form`:POST</li><li>`Header`:GET</li><li>`Url`:GET</li><li>`QueryString`:GET</li></ul> |
| ClaimsFormat | いいえ | 現在使用されていません。無視してもかまいません。 |
| ClaimUsedForRequestPayload| いいえ | REST API に送信されるペイロードを含む文字列要求の名前。 |
| DebugMode | いいえ | 技術プロファイルをデバッグ モードで実行します。 指定できる値: `true` または `false` (既定値)。 デバッグ モードでは、REST API はより多くの情報を返すことができます。 [返却エラー メッセージ](#returning-validation-error-message)のセクションを参照してください。 |
| IncludeClaimResolvingInClaimsHandling  | いいえ | 入力と出力の要求について、[要求の解決](claim-resolver-overview.md)を技術プロファイルに含めるかどうかを指定します。 指定できる値: `true` または `false` (既定値)。 技術プロファイルで要求リゾルバーを使用する場合は、これを `true` に設定します。 |
| ResolveJsonPathsInJsonTokens  | いいえ | 技術プロファイルが JSON パスを解決するかどうかを示します。 指定できる値: `true` または `false` (既定値)。 このメタデータを使用して、入れ子になった JSON 要素からデータを読み取ります。 [OutputClaim](technicalprofiles.md#output-claims) で、`PartnerClaimType` を、出力する JSON パス要素に設定します。 例: `firstName.localized`、または `data.0.to.0.email`。|
| UseClaimAsBearerToken| いいえ| ベアラー トークンを含む要求の名前。|

## <a name="error-handling"></a>エラー処理

次のメタデータを使用して、REST API の失敗時に表示されるエラー メッセージを構成できます。 エラー メッセージは、[ローカライズ](localization-string-ids.md#restful-service-error-messages)できます。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| DefaultUserMessageIfRequestFailed | いいえ | すべての REST API 例外に関する既定のカスタマイズされたエラーメッセージ。|
| UserMessageIfCircuitOpen | いいえ | REST API に到達できない場合のエラーメッセージ。 指定しない場合、DefaultUserMessageIfRequestFailed が返されます。 |
| UserMessageIfDnsResolutionFailed | いいえ | DNS 解決例外のエラーメッセージ。 指定しない場合、DefaultUserMessageIfRequestFailed が返されます。 | 
| UserMessageIfRequestTimeout | いいえ | 接続がタイムアウトしたときのエラーメッセージ。指定しない場合、DefaultUserMessageIfRequestFailed が返されます。 | 

## <a name="cryptographic-keys"></a>暗号化キー

認証の種類が `None` に設定されている場合、**CryptographicKeys** 要素は使用されません。

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
</TechnicalProfile>
```

認証の種類が `Basic` に設定されている場合、**CryptographicKeys** には次の属性が存在します。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | はい | 認証に使用されるユーザー名。 |
| BasicAuthenticationPassword | はい | 認証に使用されるパスワード。 |

次の例は、基本的な認証を用いた技術プロファイルを示しています。

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Basic</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
  </CryptographicKeys>
</TechnicalProfile>
```

認証の種類が `ClientCertificate` に設定されている場合、**CryptographicKeys** には次の属性が存在します。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| ClientCertificate | はい | 認証に使用する X509 証明書 (RSA キー セット)。 |

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ClientCertificate</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
  </CryptographicKeys>
</TechnicalProfile>
```

認証の種類が `Bearer` に設定されている場合、**CryptographicKeys** には次の属性が存在します。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| BearerAuthenticationToken | いいえ | OAuth 2.0 ベアラー トークン。 |

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_B2cRestClientAccessToken" />
  </CryptographicKeys>
</TechnicalProfile>
```

認証の種類が `ApiKeyHeader` に設定されている場合、**CryptographicKeys** には次の属性が存在します。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| HTTP ヘッダーの名前 (`x-functions-key`、`x-api-key` など)。 | はい | 認証に使用されるキー。 |

> [!NOTE]
> 現時点で Azure AD B2C が認証でサポートするのは、1 つの HTTP ヘッダーのみです。 RESTful 呼び出しでクライアント ID やクライアント シークレットなどの複数のヘッダーが必要な場合は、何らかの方法で要求をプロキシ経由にする必要があります。

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ApiKeyHeader</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
  </CryptographicKeys>
</TechnicalProfile>
```

## <a name="returning-validation-error-message"></a>検証エラー メッセージを返す

REST API は、「そのユーザーは CRM システムでは見つかりませんでした」などの、エラー メッセージを返す必要がある場合があります。 エラーが発生した場合、REST API によって 400 (無効な要求) や 409 (競合) の応答状態コードなど、HTTP 4xx エラー メッセージが返されます。 応答本文には、JSON で書式設定されたエラー メッセージが含まれています。

```json
{
  "version": "1.0.0",
  "status": 409,
  "code": "API12345",
  "requestId": "50f0bd91-2ff4-4b8f-828f-00f170519ddb",
  "userMessage": "Message for the user",
  "developerMessage": "Verbose description of problem and how to fix it.",
  "moreInfo": "https://restapi/error/API12345/moreinfo"
}
```

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| version | はい | ご利用の REST API バージョン。 次に例を示します。1.0.1 |
| status | はい | 409 である必要があります。 |
| code | いいえ | `DebugMode` が有効な場合に表示される、RESTful エンドポイント プロバイダーからのエラー コード。 |
| requestId | いいえ | `DebugMode` が有効な場合に表示される、RESTful エンドポイント プロバイダーからの要求識別子。 |
| userMessage | はい | ユーザーに示されるエラー メッセージ。 |
| developerMessage | いいえ | `DebugMode` が有効な場合に表示される、問題の詳細な説明とそれを修正する方法。 |
| moreInfo | いいえ | `DebugMode` が有効な場合に表示される、追加情報をポイントする URI。 |


次の例は、エラー メッセージを返す C# クラスを示しています。

```csharp
public class ResponseContent
{
  public string version { get; set; }
  public int status { get; set; }
  public string code { get; set; }
  public string userMessage { get; set; }
  public string developerMessage { get; set; }
  public string requestId { get; set; }
  public string moreInfo { get; set; }
}
```

## <a name="next-steps"></a>次のステップ

RESTful 技術プロファイルの使用例については、次の記事を参照してください。

- [REST API 要求交換の Azure AD B2C カスタム ポリシーへの統合](custom-policy-rest-api-intro.md)
- [チュートリアル:ユーザー入力の検証として REST API 要求交換を Azure AD B2C ユーザー体験に統合する](custom-policy-rest-api-claims-validation.md)
- [チュートリアル:Azure Active Directory B2C で REST API 要求の交換をカスタム ポリシーに追加する](custom-policy-rest-api-claims-validation.md)
- [REST API サービスをセキュリティで保護する](secure-rest-api.md)
