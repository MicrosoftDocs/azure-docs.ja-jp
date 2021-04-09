---
title: B2C カスタム ポリシーでの REST API 要求の交換
titleSuffix: Azure AD B2C
description: RESTful サービスと情報をやり取りする Azure AD B2C ユーザー体験を作成するための概要。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/18/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bc2b72779460c2b7e3999204ace50ca57388b9a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89594188"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-custom-policy"></a>REST API 要求交換の Azure AD B2C カスタム ポリシーへの統合

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) の基盤となる Identity Experience Framework は、ユーザー体験における RESTful API と統合することができます。 この記事では、[RESTful 技術プロファイル](restful-technical-profile.md) を使用して RESTful サービスと対話するユーザー体験を作成する方法について説明します。

Azure AD B2C を使用すると、自分の RESTful サービスを呼び出すことで、独自のビジネス ロジックをユーザー体験に追加できます。 Identity Experience Framework は、RESTful サービスからデータを送受信して、要求を交換できます。 たとえば、次のように操作できます。

- **ユーザー入力データの検証**。 たとえば、ユーザーによって入力されたメール アドレスが顧客データベースに存在するかどうかを確認できます。存在しない場合、エラーが表示されます。
- **要求を処理します**。 ユーザーが名をすべて小文字または大文字で入力する場合に、お使いの REST API は名の最初の文字だけを大文字にするように書式設定でき、それを Azure AD B2C に返すことができます。
- **企業の基幹業務アプリケーションとさらに緊密に統合することでユーザー データを拡充します**。 RESTful サービスでは、ユーザーのメール アドレスを受け取り、顧客のデータベースを照会し、ユーザーのロイヤルティ番号を Azure AD B2C に返すことができます。 返された要求は、ユーザーの Azure AD アカウントに保存するか、次のオーケストレーション手順で評価するか、アクセス トークンに含めることができます。
- **カスタム ビジネス ロジックを実行します**。 プッシュ通知の送信、企業データベースの更新、ユーザーの移行プロセスの実行、アクセス許可の管理、データベースの監査、およびその他のワークフローを実行できます。

![RESTful サービスの要求交換の図](media/custom-policy-rest-api-intro/restful-service-claims-exchange.png)

> [!NOTE]
> RESTful サービスから Azure AD B2C への応答が遅い場合または応答がない場合、タイムアウトは 30 秒であり、再試行回数は 2 回です (つまり、合計 3 回試行されます)。 タイムアウトと再試行回数の設定は現在構成できません。

## <a name="calling-a-restful-service"></a>RESTful サービスの呼び出し

対話には REST API 要求と Azure AD B2C の間の情報の要求の交換が含まれています。 次の方法で、RESTful サービスとの統合を設計できます。

- **検証技術プロファイル**。 RESTful サービスへの呼び出しは、指定された [セルフアサート技術プロファイル](self-asserted-technical-profile.md) の [検証技術プロファイル](validation-technical-profile.md) 内、または [表示コントロール](display-controls.md) の [確認表示コントロール](display-control-verification.md) 内で行われます。 検証技術プロファイルは、ユーザー体験を進める前に、ユーザーが入力したデータを検証します。 検証技術プロファイルでは、次を行うことができます。

  - REST API に要求を送信します。
  - 要求を検証し、ユーザーに表示されるカスタム エラー メッセージをスローします。
  - REST API から後続のオーケストレーション手順に要求を送り返します。

- **要求の交換**。 [ユーザー体験](userjourneys.md)のオーケストレーションの手順から REST API 技術プロファイルを直接呼び出して、ダイレクトな要求交換を構成することができます。 この定義は次に制限されます。

  - REST API に要求を送信します。
  - 要求を検証し、アプリケーションに返されるカスタム エラー メッセージをスローします。
  - REST API から後続のオーケストレーション手順に要求を送り返します。

カスタム ポリシーによって定義されているユーザー体験の任意のステップで、REST API 呼び出しを追加できます。 たとえば、次のタイミングで REST API を呼び出すことができます。

- サインイン時の、Azure AD B2C によって資格情報が検証される直前。
- サインインの直後。
- Azure AD B2C によってディレクトリに新しいアカウントが作成される前。
- Azure AD B2C によってディレクトリに新しいアカウントが作成された後。
- Azure AD B2C によってアクセス トークンが発行される前。

![検証技術プロファイル コレクション](media/custom-policy-rest-api-intro/validation-technical-profile.png)

## <a name="sending-data"></a>データの送信

[RESTful 技術プロファイル](restful-technical-profile.md) では、`InputClaims` 要素には、RESTful サービスに送信する要求のリストが含まれています。 要求の名前を、RESTful サービスで定義されている名前にマップし、既定値を設定して、[要求リゾルバー](claim-resolver-overview.md) を使用できます。

SendClaimsIn 属性を使用して、RESTful 要求プロバイダーに入力要求を送信する方法を構成できます。 指定できる値は、

- **本文**、JSON 形式で HTTP POST 要求本文で送信されます。
- **書式**、 キーの値をアンパサンド「 &」で区切った形式で HTTP POST 要求本文で送信されます。
- **ヘッダー**、HTTP GET 要求ヘッダーで送信されます。
- **QueryString**、HTTP GET 要求クエリ文字列で送信されます。

**本文** のオプションが構成されると、REST API 技術プロファイルで複雑な JSON ペイロードをエンドポイントに送信できます。 詳細については、[JSON ペイロードの送信](restful-technical-profile.md#send-a-json-payload) を参照してください。

## <a name="receiving-data"></a>データの受信

[RESTful 技術プロファイル](restful-technical-profile.md) の `OutputClaims` 要素には、REST API によって返された要求のリストが含まれています。 お使いのポリシーで定義される要求の名前を、REST API で定義される名前にマップする必要があるかもしれません。 また、DefaultValue 属性を設定している限り、REST API ID プロバイダーにより返されない要求を追加することもできます。

RESTful 要求プロバイダーが解析する出力要求は、次のような、常にフラットな JSON 本文の応答を解析することを想定しています。

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

出力要求は次のようなものです。

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

入れ子になった JSON 本文の応答を解析するには、ResolveJsonPathsInJsonTokens メタデータを true に設定します。 出力要求で、PartnerClaimType を出力する JSON パス要素に設定します。

```json
"contacts": [
  {
    "id": "MAINCONTACT_1",
    "person": {
      "name": "Emily Smith",
      "loyaltyNumber":  1234,
      "emails": [
        {
          "id": "EMAIL_1",
          "type": "WORK",
          "email": "email@domain.com"
        }
      ]
    }
  }
],
```


出力要求は次のようになります。

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts[0].person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts[0].person.emails[0].email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts[0].person.loyaltyNumber" />
</OutputClaims>
```

## <a name="security-considerations"></a>セキュリティに関する考慮事項

認証されたクライアントだけが REST API エンドポイントと通信できるように、その REST API エンドポイントを保護する必要があります。 REST API は、HTTPS エンドポイントを使用する必要があります。 AuthenticationType メタデータを、次のいずれかの認証方法に設定します。

- **クライアント証明書** は、クライアント証明書を使用してアクセスを制限します。 適切な証明書を持つサービスのみが、ご利用の API にアクセスできます。 クライアント証明書を Azure AD B2C ポリシー キーに保存します。 [クライアント証明書を使用して RESTful サービスを保護する](secure-rest-api.md#https-client-certificate-authentication) 方法について説明します。
- **Basic** は、HTTP 基本認証を使用して REST API を保護します。 Azure AD B2C などの検証されたユーザーのみが API にアクセスできます。 ユーザー名とパスワードは Azure AD B2C ポリシー キーに保存されます。 [HTTP 基本認証を使用して RESTful サービスを保護する](secure-rest-api.md#http-basic-authentication) 方法を説明します。
- **ベアラー** は、クライアント OAuth2 アクセス トークンを使用してアクセスを制限します。 アクセス トークンは Azure AD B2C ポリシー キーに保存されます。 [ベアラー トークンを使用して RESTful サービスを保護する](secure-rest-api.md#oauth2-bearer-authentication) 方法を説明します。

## <a name="rest-api-platform"></a>REST API プラットフォーム
REST API は、セキュリティで保護され、[RESTful 技術プロファイル](restful-technical-profile.md) で指定されているように要求を送受信できる限り、任意のプラットフォームに基づいており、任意のプログラミング言語で記述できます。

## <a name="localize-the-rest-api"></a>REST API をローカライズする
RESTful 技術プロファイルでは、現在のセッションの言語/ロケールを送信し、必要に応じて、ローカライズされたエラー メッセージを生成することができます。 [要求リゾルバー](claim-resolver-overview.md)を使用すると、ユーザー言語などのコンテキスト要求を送信できます。 このシナリオを示す RESTful 技術プロファイルの例を次に示します。

```xml
<TechnicalProfile Id="REST-ValidateUserData">
  <DisplayName>Validate user input data</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="handling-error-messages"></a>エラー メッセージの処理

REST API は、「そのユーザーは CRM システムでは見つかりませんでした」などの、エラー メッセージを返す必要がある場合があります。 エラーが発生した場合、REST API は HTTP 409 エラー メッセージ (応答状態コードの競合) を返すことになります。 詳細については、「[RESTful 技術プロファイル](restful-technical-profile.md#returning-validation-error-message)」を参照してください。

これは、検証技術プロファイルから REST API 技術プロファイルを呼び出すことによってのみ実現できます。 これにより、ユーザーはページのデータを修正して、ページの送信時に検証を再度実行できます。

このオーケストレーションの手順内における後続の検証技術プロファイルが処理されないようにするには、HTTP 409 応答が必要です。

ユーザー体験から REST API の技術プロファイルを直接参照した場合、ユーザーは関連するエラー メッセージと共に証明書利用者アプリケーションにリダイレクトされます。

## <a name="publishing-your-rest-api"></a>REST API の公開

REST API サービスへの要求は Azure AD B2C サーバーから作成されます。 REST API サービスは、パブリックにアクセスできる HTTPS エンドポイントに公開する必要があります。 REST API の呼び出しは、Azure データ センターの IP アドレスから受け取ります。

REST API サービスとその基になるコンポーネント (データベースやファイル システムなど) を高可用性として設計します。

## <a name="next-steps"></a>次のステップ

RESTful 技術プロファイルの使用例については、次の記事を参照してください。

- [チュートリアル:ユーザー入力の検証として REST API 要求交換を Azure AD B2C ユーザー体験に統合する](custom-policy-rest-api-claims-validation.md)
- [チュートリアル:Azure Active Directory B2C で REST API 要求の交換をカスタム ポリシーに追加する](custom-policy-rest-api-claims-validation.md)
- [REST API サービスをセキュリティで保護する](secure-rest-api.md)
- [リファレンス: RESTful 技術プロファイル](restful-technical-profile.md)
