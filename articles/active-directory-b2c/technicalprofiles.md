---
title: 技術プロファイル
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C でカスタム ポリシーの TechnicalProfiles 要素を指定します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bcff1ffd574db910c3206d82e4da0e9428db788f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102631797"
---
# <a name="technical-profiles"></a>技術プロファイル

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

*技術プロファイル* は、さまざまな種類の利用者と通信するためのメカニズムが組み込まれたフレームワークを提供します。 技術プロファイルは、ユーザーの作成やユーザー プロファイルの読み取りを行うために、ご利用の Azure Active Directory B2C (Azure AD B2C) テナントとの通信に使用されます。 ユーザーとの相互作用を有効にするには、技術プロファイルをセルフアサートすることができます。 たとえば、技術プロファイルによって、ユーザーの資格情報を収集してサインインした後、サインアップ ページまたはパスワードのリセット ページをレンダリングすることができます。

## <a name="types-of-technical-profiles"></a>技術プロファイルの種類

技術プロファイルでは、これらの種類のシナリオを有効にします。

- [Application Insights](analytics-with-application-insights.md) - イベント データを [Application Insights](../azure-monitor/app/app-insights-overview.md)に送信します。
- [Azure AD](active-directory-technical-profile.md): Azure AD B2C ユーザー管理のサポートを提供します。
- [Azure AD 多要素認証](multi-factor-auth-technical-profile.md): Azure AD 多要素認証を使用して電話番号を確認するためのサポートを提供します。
- [要求変換](claims-transformation-technical-profile.md): 出力要求変換を呼び出して、要求の値を操作したり、要求を検証したり、一連の出力要求の既定値を設定したりすることができます。
- [ID トークン ヒント](id-token-hint.md): `id_token_hint` JWT トークンの署名、発行者名、トークン対象ユーザーが検証され、受信トークンから要求が抽出されます。
- [JWT トークン発行者](jwt-issuer-technical-profile.md): 証明書利用者アプリケーションに戻された JWT トークンを発行します。
- [OAuth1](oauth1-technical-profile.md): 任意の OAuth 1.0 プロトコル ID プロバイダーとのフェデレーション。
- [OAuth2](oauth2-technical-profile.md): 任意の OAuth 2.0 プロトコル ID プロバイダーとのフェデレーション。
- [ワンタイム パスワード](one-time-password-technical-profile.md): ワンタイム パスワードの生成と確認を管理するためのサポートが提供されています。
- [OpenID Connect](openid-connect-technical-profile.md): 任意の OpenID Connect プロトコル ID プロバイダーとのフェデレーション。
- [電話要素](phone-factor-technical-profile.md): 電話番号の登録と確認をサポートします。
- [RESTful プロバイダー](restful-technical-profile.md): ユーザーの入力の検証、ユーザー データの促進、基幹業務アプリケーションとの統合など、REST API サービスを呼び出します。
- [SAML ID プロバイダー](identity-provider-generic-saml.md): 任意の SAML プロトコル ID プロバイダーとのフェデレーション。
- [SAML トークン発行者](saml-service-provider.md): 証明書利用者アプリケーションに戻された SAML トークンを発行します。
- [セルフアサート](self-asserted-technical-profile.md): ユーザーとやりとりします。 たとえば、サインインのためにユーザーの資格情報を収集したり、サインイン ページをレンダリングしたり、パスワードをリセットしたりします。
- [セッション管理](custom-policy-reference-sso.md): さまざまな種類のセッションを処理します。

## <a name="technical-profile-flow"></a>技術プロファイルのフロー

技術プロファイルのすべての種類で同じ概念を共有します。 入力要求の読み取りから開始して、要求の変換を実行します。 次に、ID プロバイダー、REST API、Azure AD ディレクトリ サービスなど、構成されているパーティーと通信します。 プロセスが完了すると、技術プロファイルによって出力要求が戻され、出力要求変換が実行される場合があります。 次の図は、技術プロファイルで参照される変換とマッピングがどのように処理されるかを示しています。 要求変換が実行された後、技術プロファイルでやりとりするパーティーに関係なく、出力要求が要求バッグにすぐに格納されます。

![技術プロファイルのフローを示す図。](./media/technical-profiles/technical-profile-flow.png)

1. **シングル サインオン (SSO) セッション管理**: [SSO セッション管理](custom-policy-reference-sso.md)を使用して、技術プロファイルのセッション状態を復元します。
1. **入力要求変換**: 技術プロファイルが開始される前に、Azure AD B2C によって入力[要求変換](claimstransformations.md)が実行されます。
1. **入力要求**: 要求バッグから要求がピックアップされ、技術プロファイルに使用されます。
1. **技術プロファイルの実行**: 技術プロファイルでは、その要求を構成されたパーティと交換します。 次に例を示します。
    - ユーザーを ID プロバイダーにリダイレクトして、サインインを完了します。 サインインが成功したら、ユーザーが返され、技術プロファイルの実行が続行されます。
    - InputClaims としてパラメーターを送信し、OutputClaims として情報を戻しながら、REST API を呼び出します。
    - ユーザー アカウントを作成または更新します。
    - 多要素認証テキスト メッセージを送信および検証します。
1. **検証技術プロファイル**: [セルフアサート技術プロファイル](self-asserted-technical-profile.md)からは、[検証技術プロファイル](validation-technical-profile.md)を呼び出して、ユーザーによってプロファイルされたデータを検証することができます。
1. **出力要求**: 要求は要求バッグに戻されます。 次のオーケストレーション手順または出力要求変換で、これらの要求を使用できます。
1. **出力要求変換**: 技術プロファイルの完了後、Azure AD B2C によって出力[要求変換](claimstransformations.md)が実行されます。
1. **SSO セッション管理**: [SSO セッション管理](custom-policy-reference-sso.md)を使用して、技術プロファイルのデータをセッションに永続化します。

**TechnicalProfiles** 要素には、クレーム プロバイダーによってサポートされる一連の技術プロファイルが含まれています。 すべてのクレーム プロバイダーには、1 つ以上の技術プロファイルが必要です。 技術プロファイルにより、そのクレーム プロバイダーと通信するために必要なエンドポイントとプロトコルが決定されます。 1 つのクレーム プロバイダーが複数の技術プロファイルを持つことができます。

```xml
<ClaimsProvider>
  <DisplayName>Display name</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Technical profile identifier">
      <DisplayName>Display name of technical profile</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        ...
      </Metadata>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

**TechnicalProfile** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
|---------|---------|---------|
| Id | はい | 技術プロファイルの一意の識別子。 ポリシー ファイル内の他の要素からこの識別子を使用して、技術プロファイルを参照することができます。 例として、**OrchestrationSteps** や **ValidationTechnicalProfile** があります。 |

**TechnicalProfile** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| Domain | 0:1 | 技術プロファイルのドメイン名。 たとえば、技術プロファイルで Facebook ID プロバイダーが指定されている場合、ドメイン名は Facebook.com です。 |
| DisplayName | 1:1 | 技術プロファイルの表示名。 |
| Description | 0:1 | 技術プロファイルの説明。 |
| Protocol | 1:1 | 他の利用者との通信に使用されるプロトコル。 |
| Metadata | 0:1 | 技術プロファイルの動作を制御するキーと値のセット。 |
| InputTokenFormat | 0:1 | 入力トークンの形式。 指定できる値は、`JSON`、`JWT`、`SAML11`、および `SAML2` です。 `JWT` 値は、IETF 仕様に従って JSON Web トークンを表します。 `SAML11` 値は、OASIS 仕様に従って SAML 1.1 セキュリティ トークンを表します。 `SAML2` 値は、OASIS 仕様に従って SAML 2.0 セキュリティ トークンを表します。 |
| OutputTokenFormat | 0:1 | 出力トークンの形式。 指定できる値は、`JSON`、`JWT`、`SAML11`、および `SAML2` です。 |
| CryptographicKeys | 0:1 | 技術プロファイルで使用される暗号化キーの一覧。 |
| InputClaimsTransformations | 0:1 | 要求がクレーム プロバイダーまたは証明書利用者に送信される前に実行する必要がある、要求変換に対する定義済みの参照の一覧。 |
| InputClaims | 0:1 | 技術プロファイルで入力として取得される、要求の種類に対して以前に定義された参照の一覧。 |
| PersistedClaims | 0:1 | 技術プロファイルによって保持される、要求の種類に対して以前に定義された参照の一覧。 |
| DisplayClaims | 0:1 | [セルフアサート技術プロファイル](self-asserted-technical-profile.md)によって提示される、要求の種類に対して以前に定義された参照の一覧。 現在、DisplayClaims 機能はプレビュー段階です。 |
| OutputClaims | 0:1 | 技術プロファイルで出力として取得される、要求の種類に対する定義済みの参照の一覧。 |
| OutputClaimsTransformations | 0:1 | クレーム プロバイダーから要求を受け取った後に実行する必要がある、要求変換に対する定義済みの参照の一覧。 |
| ValidationTechnicalProfiles | 0:n | 技術プロファイルが検証目的で使用する、他の技術プロファイルへの参照の一覧。 詳細については、「[検証技術プロファイル](validation-technical-profile.md)」を参照してください。|
| SubjectNamingInfo | 0:1 | サブジェクト名が要求とは別に指定されているトークンのサブジェクト名の生成を制御します。 例としては、OAuth または SAML です。 |
| IncludeInSso | 0:1 | この技術プロファイルを使用する場合、セッションに SSO 動作を適用するか、明示的な対話が必要です。 この要素は、検証技術プロファイル内で使用される SelfAsserted プロファイルでのみ有効です。 指定できる値は、`true` (既定値) または `false`です。 |
| IncludeClaimsFromTechnicalProfile | 0:1 | この技術プロファイルに対するすべての入力要求と出力要求の追加元となる、技術プロファイルの識別子。 参照先の技術プロファイルは、同じポリシー ファイルで定義する必要があります。 |
| IncludeTechnicalProfile |0:1 | この技術プロファイルに対するすべてのデータの追加元となる、技術プロファイルの識別子。 |
| UseTechnicalProfileForSessionManagement | 0:1 | セッション管理に使用される別の技術プロファイル。 |
|EnabledForUserJourneys| 0:1 |ユーザー体験で技術プロファイルを実行するかどうかを制御します。 |

## <a name="protocol"></a>Protocol

**Protocol** 要素により、他の利用者との通信に使用されるプロトコルが指定されます。 **Protocol** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| 名前 | はい | 技術プロファイルの一部として使用される Azure AD B2C によってサポートされている有効なプロトコルの名前。 可能な値は `OAuth1`、`OAuth2`、`SAML2`、`OpenIdConnect`、`Proprietary`、または `None` です。 |
| Handler | いいえ | プロトコル名が `Proprietary` に設定されている場合は、プロトコル ハンドラーを決定するために Azure AD B2C によって使用されるアセンブリの名前を指定します。 |

## <a name="metadata"></a>Metadata

**Metadata** 要素には、特定のプロトコルに関連する構成オプションが含まれています。 サポートされているメタデータの一覧は、対応する[技術プロファイル](#types-of-technical-profiles)仕様に記載されています。 **Metadata** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| Item | 0:n | 技術プロファイルに関連するメタデータ。 技術プロファイルの種類ごとに、異なるメタデータ項目のセットがあります。 詳細については、技術プロファイルの種類に関するセクションを参照してください。 |

### <a name="item"></a>Item

**Metadata** 要素の **Item** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| Key | はい | メタデータ キー。 メタデータ項目の一覧については、各[技術プロファイルの種類](#types-of-technical-profiles)を参照してください。 |

次の例では、[OAuth2 技術プロファイル](oauth2-technical-profile.md#metadata)に関連するメタデータの使用方法を示します。

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  ...
  <Metadata>
    <Item Key="ProviderName">facebook</Item>
    <Item Key="authorization_endpoint">https://www.facebook.com/dialog/oauth</Item>
    <Item Key="AccessTokenEndpoint">https://graph.facebook.com/oauth/access_token</Item>
    <Item Key="HttpBinding">GET</Item>
    <Item Key="UsePolicyInRedirectUri">0</Item>
    ...
  </Metadata>
  ...
</TechnicalProfile>
```

次の例では、[REST API 技術プロファイル](restful-technical-profile.md#metadata)に関連するメタデータの使用方法を示します。

```xml
<TechnicalProfile Id="REST-Validate-Email">
  ...
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    ...
  </Metadata>
  ...
</TechnicalProfile>
```

## <a name="cryptographic-keys"></a>暗号化キー

統合対象のサービスとの信頼を確立するため、Azure AD B2C により、シークレットと証明書が[ポリシー キー](policy-keys-overview.md)の形式で保存されます。 技術プロファイルの実行中、Azure AD B2C によって Azure AD B2C ポリシー キーから暗号化キーが取得されます。 その後、Azure AD B2C ではそのキーを使用して、信頼の確立や、トークンの暗号化または署名が行われます。 これらの信頼は次のもので構成されます。

- [OAuth1](oauth1-technical-profile.md#cryptographic-keys)、[OAuth2](oauth2-technical-profile.md#cryptographic-keys)、および [SAML](identity-provider-generic-saml.md) ID プロバイダーとのフェデレーション。
- [REST API サービス](secure-rest-api.md)との接続のセキュリティ保護。
- [JWT](jwt-issuer-technical-profile.md#cryptographic-keys) および [SAML](saml-service-provider.md) トークンの署名と暗号化。

**CryptographicKeys** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| Key | 1:n | この技術プロファイルで使用される暗号化キー。 |

### <a name="key"></a>Key

**Key** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| Id | いいえ | ポリシー ファイル内の他の要素から参照される特定のキー ペアの一意の識別子。 |
| StorageReferenceId | はい | ポリシー ファイル内の他の要素から参照されるストレージ キー コンテナーの識別子。 |

## <a name="input-claims-transformations"></a>入力要求変換

**InputClaimsTransformations** 要素には、入力要求を変更したり新しいものを生成したりするために使用される、入力要求変換の要素のコレクションを含めることができます。

要求変換コレクション内の以前の要求変換の出力要求は、後続の入力要求変換の入力要求になる場合があります。 このようにして、一連の要求変換を互いに依存させることができます。

**InputClaimsTransformations** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | Description |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1:n | 要求がクレーム プロバイダーまたは証明書利用者に送信される前に実行する必要がある、要求変換の識別子。 要求変換は、既存の ClaimsSchema の要求を変更したり、新しい要求を生成するために使用できます。 |

### <a name="inputclaimstransformation"></a>InputClaimsTransformation

**InputClaimsTransformation** 要素には、次の属性が含まれています。

| 属性 | 必須 | Description |
| --------- | -------- | ----------- |
| ReferenceId | はい | ポリシー ファイルまたは親ポリシー ファイルで定義済みの要求変換の識別子。 |

次の技術プロファイルは、**CreateOtherMailsFromEmail** 要求変換を参照しています。 要求変換により、データがディレクトリに保持される前に、`email` 要求の値が `otherMails` コレクションに追加されます。

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  ...
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  ...
</TechnicalProfile>
```

## <a name="input-claims"></a>入力クレーム

**InputClaims** 要素により、技術プロファイルに使用される要求バッグから要求がピックアップされます。 たとえば、[セルフアサート技術プロファイル](self-asserted-technical-profile.md)では、入力要求を使用して、ユーザーが提供する出力要求を事前作成します。 REST API 技術プロファイルでは、入力要求を使用し、入力パラメーターを REST API エンドポイントに送信します。 Azure AD では、アカウントの読み取り、更新、削除を行うために、一意識別子として入力要求を使用します。

**InputClaims** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | Description |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | 予想される入力要求の種類。 |

### <a name="inputclaim"></a>InputClaim

**InputClaim** 要素には、次の属性が含まれています。

| 属性 | 必須 | Description |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | はい | 要求の種類の識別子。 要求は、ポリシー ファイルまたは親ポリシー ファイルの要求スキーマ セクションで、既に定義されています。 |
| DefaultValue | いいえ | ClaimTypeReferenceId で示された要求が存在しない場合に、要求を作成するために使用する既定値。これにより、作成された要求を技術プロファイルで InputClaim 要素として使用できます。 |
| PartnerClaimType | いいえ | 指定されたポリシーの要求の種類のマップ先となる外部パートナーの要求の種類の識別子。 PartnerClaimType 属性が指定されていない場合、指定されたポリシーの要求の種類が、同じ名前のパートナーの要求の種類にマップされます。 このプロパティは、要求の種類の名前が他の利用者と異なる場合に使用します。 たとえば、最初の要求の名前が *givenName* で、パートナーが *first_name* という要求を使用する場合です。 |

## <a name="display-claims"></a>表示要求

**DisplayClaims** 要素には、ユーザーからデータを収集するために画面に表示される要求の一覧が含まれます。 表示要求コレクションに、作成した[要求の種類](claimsschema.md)または[表示コントロール](display-controls.md)への参照を含めることができます。

- 要求の種類は、画面に表示される要求への参照です。
  - 特定の要求に対する値の指定をユーザーに強制するには、**DisplayClaim** 要素の **Required** 属性を `true` に設定します。
  - 表示要求の値を事前入力するには、以前に説明した入力要求を使用します。 要素には、既定値を含めることもできます。
  - **DisplayClaims** コレクション内の **ClaimType** 要素では、Azure AD B2C でサポートされている **UserInputType** 要素を任意のユーザー入力タイプに設定する必要があります。 たとえば、`TextBox` や `DropdownSingleSelect` などです。
- 表示コントロールは、特別な機能を備えた、Azure AD B2C バックエンド サービスと対話するユーザー インターフェイス要素です。 これにより、バックエンドで検証技術プロファイルを呼び出すページでユーザーが操作を実行できます。 例としては、電子メール アドレス、電話番号、または顧客のロイヤルティ番号の検証があります。

**DisplayClaims** 内の要素の順序によって、Azure AD B2C による画面への要求の表示順序が指定されます。

**DisplayClaims** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | Description |
| ------- | ----------- | ----------- |
| DisplayClaim | 1:n | 予想される入力要求の種類。 |

### <a name="displayclaim"></a>DisplayClaim

**DisplayClaim** 要素には、次の属性が含まれています。

| 属性 | 必須 | Description |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | いいえ | ポリシーファイルまたは親ポリシーファイルの　ClaimsSchema セクションで定義済みの要求の種類の識別子。 |
| DisplayControlReferenceId | いいえ | ポリシー ファイルまたは親ポリシー ファイルの ClaimsSchema セクションで定義されている[表示コントロール](display-controls.md)の識別子。 |
| Required | いいえ | 表示要求が必須かどうかを示します。 |

次の例では、セルフアサート技術プロファイルで表示要求と表示コントロールを使用する方法を示します。

![表示要求を含むセルフアサート技術プロファイルを示すスクリーンショット。](./media/technical-profiles/display-claims.png)

次の技術プロファイルには、以下の項目が含まれています。

- 最初の表示要求では、電子メール アドレスを収集して検証する `emailVerificationControl` 表示コントロールが参照されます。
- 5 番目の表示要求では、電話番号を収集して検証する `phoneVerificationControl` 表示コントロールが参照されます。
- その他の表示要求は、ユーザーからデータが収集される ClaimTypes 要素です。

```xml
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

## <a name="persisted-claims"></a>永続化された要求

**PersistedClaims** 要素には、ポリシー内の [ClaimsSchema](claimsschema.md) セクションに既に定義されている要求の種類と Azure AD 属性名の間の可能なマッピング情報と共に、[Azure AD 技術プロファイル](active-directory-technical-profile.md)によって保持される必要があるすべての値が含まれています。

要求の名前は、[Azure AD 属性](user-profile-attributes.md)名を含む **PartnerClaimType** 属性が指定されている場合を除き、Azure AD 属性の名前です。

**PersistedClaims** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | Description |
| ------- | ----------- | ----------- |
| PersistedClaim | 1:n | 保持する要求の種類。 |

### <a name="persistedclaim"></a>PersistedClaim

**PersistedClaim** 要素には、次の属性が含まれています。

| 属性 | 必須 | Description |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | はい | ポリシーファイルまたは親ポリシーファイルの　ClaimsSchema セクションで定義済みの要求の種類の識別子。 |
| DefaultValue | いいえ | 要求が存在しない場合に要求を作成するために使用される既定値。 |
| PartnerClaimType | いいえ | 指定されたポリシーの要求の種類のマップ先となる外部パートナーの要求の種類の識別子。 PartnerClaimType 属性が指定されていない場合、指定されたポリシーの要求の種類が、同じ名前のパートナーの要求の種類にマップされます。 このプロパティは、要求の種類の名前が他の利用者と異なる場合に使用します。 たとえば、最初の要求の名前が *givenName* で、パートナーが *first_name* という要求を使用する場合です。 |

次の例では、新しいローカル アカウントを作成する **AAD-UserWriteUsingLogonEmail** 技術プロファイル ([スターター パック](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts)) によって以下の要求が保持されます。

```xml
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
  <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
  <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
  <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />
  <PersistedClaim ClaimTypeReferenceId="givenName" />
  <PersistedClaim ClaimTypeReferenceId="surname" />
</PersistedClaims>
```

## <a name="output-claims"></a>出力クレーム

**OutputClaims** 要素は、技術プロファイルの完了後に要求バッグに戻される要求のコレクションです。 次のオーケストレーション手順または出力要求変換で、これらの要求を使用できます。 **OutputClaims** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | Description |
| ------- | ----------- | ----------- |
| OutputClaim | 1:n | 予想される出力要求の種類。 |

### <a name="outputclaim"></a>OutputClaim

**OutputClaim** 要素には、次の属性が含まれています。

| 属性 | 必須 | Description |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | はい | ポリシーファイルまたは親ポリシーファイルの　ClaimsSchema セクションで定義済みの要求の種類の識別子。 |
| DefaultValue | いいえ | 要求が存在しない場合に要求を作成するために使用される既定値。 |
|AlwaysUseDefaultValue |いいえ |既定値の使用を強制します。 |
| PartnerClaimType | いいえ | 指定されたポリシーの要求の種類のマップ先となる外部パートナーの要求の種類の識別子。 パートナー要求種類属性が指定されていない場合は、指定されたポリシーの要求の種類が、同じ名前のパートナー要求種類にマップされます。 このプロパティは、要求の種類の名前が他の利用者と異なる場合に使用します。 たとえば、最初の要求の名前が *givenName* で、パートナーが *first_name* という要求を使用する場合です。 |

## <a name="output-claims-transformations"></a>出力要求変換

**OutputClaimsTransformations** 要素には、**OutputClaimsTransformation** 要素のコレクションが含まれる場合があります。 出力要求変換は、出力要求を変更したり、新しく生成したりするために使用されます。 実行後、出力要求は要求バッグに戻されます。 次のオーケストレーションの手順でこれらの要求を使用できます。

要求変換コレクション内の以前の要求変換の出力要求は、後続の入力要求変換の入力要求になる場合があります。 このようにして、一連の要求変換を互いに依存させることができます。

**OutputClaimsTransformations** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | Description |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1:n | 要求がクレーム プロバイダーまたは証明書利用者に送信される前に実行する必要がある、要求変換の識別子。 要求変換は、既存の ClaimsSchema の要求を変更したり、新しい要求を生成するために使用できます。 |

### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

**OutputClaimsTransformation** 要素には、次の属性が含まれています。

| 属性 | 必須 | Description |
| --------- | -------- | ----------- |
| ReferenceId | はい | ポリシー ファイルまたは親ポリシー ファイルで定義済みの要求変換の識別子。 |

次の技術プロファイルは、ディレクトリから `accountEnabled` 要求を読み取った後に、アカウントが有効になっているかどうかを評価するために AssertAccountEnabledIsTrue 要求変換を参照します。

```xml
<TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
  ...
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="accountEnabled" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
  ...
</TechnicalProfile>
```

## <a name="validation-technical-profiles"></a>検証技術プロファイル

検証技術プロファイルは、[セルフアサート技術プロファイル](self-asserted-technical-profile.md#validation-technical-profiles)内の出力要求を検証するために使用されます。 検証技術プロファイルは、[Azure AD](active-directory-technical-profile.md) や [REST API](restful-technical-profile.md) などの、あらゆるプロトコルの通常の技術プロファイルです。 検証技術プロファイルによって、出力要求が返されるか、またはエラー コードが返されます。 画面上でエラー メッセージがユーザーに表示され、ユーザーは再試行することができます。

次の図は、Azure AD B2C で検証技術プロファイルを使用してユーザーの資格情報を検証する方法を示しています。

![検証技術プロファイルのフローを示す図。](./media/technical-profiles/validation-technical-profile.png)

**ValidationTechnicalProfiles** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | 参照元の技術プロファイルの出力要求の一部またはすべてを検証するために使用される技術プロファイルの識別子。 参照先の技術プロファイルのすべての入力要求は、参照元の技術プロファイルの出力要求に表示する必要があります。 |

### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

**ValidationTechnicalProfile** 要素には、次の属性が含まれています。

| 属性 | 必須 | Description |
| --------- | -------- | ----------- |
| ReferenceId | はい | ポリシー ファイルまたは親ポリシー ファイルで定義済みの技術プロファイルの識別子。 |

## <a name="subjectnaminginfo"></a>SubjectNamingInfo

**SubjectNamingInfo** 要素により、[証明書利用者ポリシー](relyingparty.md#subjectnaminginfo)内のトークンで使用されるサブジェクト名が定義されます。 **SubjectNamingInfo** 要素には、次の属性が含まれています。

| 属性 | 必須 | Description |
| --------- | -------- | ----------- |
| ClaimType | はい | ポリシー ファイルの ClaimsSchema セクションで定義済みの要求の種類の識別子。 |

## <a name="include-technical-profile"></a>包含技術プロファイル

技術プロファイルには、設定を変更したり、新しい機能を追加したりするために、別の技術プロファイルを含めることができます。 **IncludeTechnicalProfile** 要素は、技術プロファイルの派生元の共通技術プロファイルへの参照です。 ポリシー要素の冗長性と複雑さを軽減するには、コア要素を共有する複数の技術プロファイルがある場合に包含を使用します。 共通技術プロファイルを含む特定のタスク技術プロファイルと共に、共通構成セットを含む共通技術プロファイルを使用します。

さまざまなシナリオでさまざまな要求セットを送信するために必要な 1 つのエンドポイントが含まれる [REST API 技術プロファイル](restful-technical-profile.md)があるとします。 REST API エンドポイント URI、メタデータ、認証の種類、暗号化キーなどの共有機能を備えた共通技術プロファイルを作成します。 共通技術プロファイルが含まれる特定のタスク技術プロファイルを作成します。 それから、入力および出力要求を追加するか、またはその技術プロファイルに関連する REST API エンドポイント URI を上書きします。

**IncludeTechnicalProfile** 要素には、次の属性が含まれています。

| 属性 | 必須 | Description |
| --------- | -------- | ----------- |
| ReferenceId | はい | ポリシー ファイルまたは親ポリシー ファイルで定義済みの技術プロファイルの識別子。 |

次の例では、包含の使用方法を示します。

- **REST-API-Common**: 基本構成を含む共通技術プロファイル。
- **REST-ValidateProfile**: **REST-API-Common** 技術プロファイルを含み、入力および出力要求を指定します。
- **REST-UpdateProfile**: **REST-API-Common** 技術プロファイルを含み、入力要求を指定し、`ServiceUrl` メタデータを上書きします。

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-API-Common">
      <DisplayName>Base REST API configuration</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="SendClaimsIn">Body</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
      </CryptographicKeys>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Validate the account and return promo code</DisplayName>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="REST-API-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="REST-UpdateProfile">
       <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/update</Item>
      </Metadata>
      <DisplayName>Update the user profile</DisplayName>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="REST-API-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

### <a name="multilevel-inclusion"></a>複数レベルの包含

技術プロファイルには、1 つの技術プロファイルを含めることができます。 包含のレベルの数に制限はありません。 たとえば、**AAD-UserReadUsingAlternativeSecurityId-NoError** 技術プロファイルには、**AAD-UserReadUsingAlternativeSecurityId** が含まれます。 この技術プロファイルを使うと、`RaiseErrorIfClaimsPrincipalDoesNotExist` メタデータ項目を `true` に設定し、ディレクトリにソーシャル アカウントが存在しない場合はエラーを発生させることができます。 **AAD-UserReadUsingAlternativeSecurityId-NoError** によってこの動作がオーバーライドされ、そのエラー メッセージは無効になります。

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** には、`AAD-Common` 技術プロファイルが含まれます。

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId-NoError** と **AAD-UserReadUsingAlternativeSecurityId** はどちらも、**AAD-Common** 技術プロファイルで指定されているため、必須の **Protocol** 要素は指定されません。

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

## <a name="use-technical-profile-for-session-management"></a>セッション管理に技術プロファイルを使用する

**UseTechnicalProfileForSessionManagement** 要素は [SSO セッション技術プロファイル](custom-policy-reference-sso.md)を参照します。 **UseTechnicalProfileForSessionManagement** 要素には、次の属性が含まれています。

| 属性 | 必須 | Description |
| --------- | -------- | ----------- |
| ReferenceId | はい | ポリシー ファイルまたは親ポリシー ファイルで定義済みの技術プロファイルの識別子。 |

## <a name="enabled-for-user-journeys"></a>ユーザー体験に対して有効

ユーザー体験の [ClaimsProviderSelections](userjourneys.md#claims-provider-selection) は、クレーム プロバイダー選択オプションの一覧とその順序を定義します。 **EnabledForUserJourneys** 要素を使用して、ユーザーが利用できるクレーム プロバイダーをフィルタリングします。 **EnabledForUserJourneys** 要素には、次の値のいずれかが含まれています。

- **Always**: 技術プロファイルを実行します。
- **Never**: 技術プロファイルをスキップします。
- **OnClaimsExistence**: 技術プロファイルで指定された特定の要求が存在する場合にのみ実行します。
- **OnItemExistenceInStringCollectionClaim**: 文字列コレクション要求に項目が存在する場合にのみ実行します。
- **OnItemAbsenceInStringCollectionClaim**: 文字列コレクション要求に項目が存在しない場合にのみ実行します。

**OnClaimsExistence**、**OnItemExistenceInStringCollectionClaim**、または **OnItemAbsenceInStringCollectionClaim** を使用するには、以下のメタデータを指定する必要があります。

- **ClaimTypeOnWhichToEnable**: 評価対象の要求の種類を指定します。
- **ClaimValueOnWhichToEnable**: 比較対象の値を指定します。

次の技術プロファイルは、**identityProviders** 文字列コレクションに `facebook.com` という値が含まれている場合にのみ実行されます。

```xml
<TechnicalProfile Id="UnLink-Facebook-OAUTH">
  <DisplayName>Unlink Facebook</DisplayName>
...
    <Metadata>
      <Item Key="ClaimTypeOnWhichToEnable">identityProviders</Item>
      <Item Key="ClaimValueOnWhichToEnable">facebook.com</Item>
    </Metadata>
...
  <EnabledForUserJourneys>OnItemExistenceInStringCollectionClaim</EnabledForUserJourneys>
</TechnicalProfile>
```
