---
title: RelyingParty - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C でカスタム ポリシーの RelyingParty 要素を指定します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b1c8bf5cb8944b990737d557326b2741716bab3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579758"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**RelyingParty** 要素は、現在の要求を Azure Active Directory B2C (Azure AD B2C) に対して適用するユーザー体験を指定します。 また、証明書利用者 (RP) アプリケーションが発行されたトークンの一部として必要とするクレームの一覧を指定します。 Web、モバイル、デスクトップ アプリケーションなどの RP アプリケーションは RP ポリシー ファイルを呼び出します。 RP ポリシー ファイルは、サインイン、パスワードのリセット、プロファイルの編集などの特定のタスクを実行します。 複数のアプリケーションが同じ RP ポリシーを使用でき、1 つのアプリケーションが複数のポリシーを使用できます。 すべての RP アプリケーションがクレームで同じトークンを受信し、ユーザーが同じユーザー体験を受けます。

次の例は、*B2C_1A_signup_signin* ポリシー ファイル内の **RelyingParty** 要素を示しています。

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="your-tenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://your-tenant.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>your-tenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="7"/>
      <SessionExpiryType>Rolling</SessionExpiryType>
      <SessionExpiryInSeconds>300</SessionExpiryInSeconds>
      <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="your-application-insights-key" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
      </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Description>The policy profile</Description>
      <Protocol Name="OpenIdConnect" />
      <Metadata>collection of key/value pairs of data</Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
  ...
```

省略可能な **RelyingParty** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | Description |
| ------- | ----------- | ----------- |
| DefaultUserJourney | 1:1 | RP アプリケーションの既定のユーザー体験。 |
| エンドポイント | 0:1 | エンドポイントの一覧。 詳細については、「[UserInfo エンドポイント](userinfo-endpoint.md)」を参照してください。 |
| UserJourneyBehaviors | 0:1 | ユーザー体験の動作の範囲です。 |
| TechnicalProfile | 1:1 | RP アプリケーションでサポートされている技術プロファイル。 技術プロファイルでは、Azure AD B2C に問い合わせるための RP アプリケーションの問い合わせ先が提供されます。 |

## <a name="endpoints"></a>エンドポイント

**Endpoints** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| エンドポイント | 1:1 | エンドポイントの参照。|

**Endpoint** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| Id | はい | エンドポイントの一意識別子。|
| UserJourneyReferenceId | はい | ポリシーのユーザー体験の識別子です。 詳細については、「[ユーザー体験](userjourneys.md)」を参照してください。  | 

次の例は、[UserInfo エンドポイント](userinfo-endpoint.md)を持つ証明書利用者を示しています。

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <Endpoints>
    <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
  </Endpoints>
  ...
```

## <a name="defaultuserjourney"></a>DefaultUserJourney

`DefaultUserJourney` 要素は、ベースまたは拡張機能ポリシーで定義されているユーザー体験の ID への参照を指定します。 次の例は、**RelyingParty** 要素で指定されるサインアップまたはサインイン ユーザー体験を示しています。

*B2C_1A_signup_signin* ポリシー:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

*B2C_1A_TrustFrameWorkBase* または *B2C_1A_TrustFrameworkExtensionPolicy*:

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

**DefaultUserJourney** 要素には、次の属性が含まれています。

| 属性 | 必須 | Description |
| --------- | -------- | ----------- |
| ReferenceId | はい | ポリシーのユーザー体験の識別子です。 詳細については、「[ユーザー体験](userjourneys.md)」を参照してください。 |

## <a name="userjourneybehaviors"></a>UserJourneyBehaviors

**UserJourneyBehaviors** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | Description |
| ------- | ----------- | ----------- |
| SingleSignOn | 0:1 | ユーザー体験のシングル サインオン (SSO) セッション動作の範囲です。 |
| SessionExpiryType |0:1 | セッションの認証動作です。 指定できる値: `Rolling` または `Absolute`。 `Rolling`値 (既定) では、ユーザーがアプリケーションでアクティブな状態を継続している限り、サインインしたままとなることを示します。 `Absolute`値は、アプリケーションのセッションで指定された期間の後にユーザーの再認証が強制されることを示します。 |
| SessionExpiryInSeconds | 0:1 | 認証の成功時にユーザーのブラウザーで保存される整数として指定された、Azure AD B2C のセッション Cookie の有効期間です。 |
| JourneyInsights | 0:1 | 使用される Azure Application Insights のインストルメンテーション キー。 |
| ContentDefinitionParameters | 0:1 | コンテンツ定義読み込み URI に追加するキー値ペアのリスト。 |
|ScriptExecution| 0:1| サポートされている [JavaScript](javascript-and-page-layout.md) 実行モード。 指定できる値: `Allow` または `Disallow`(既定値)。
| JourneyFraming | 0:1| このポリシーのユーザー インターフェイスを iframe に読み込むことができます。 |

### <a name="singlesignon"></a>SingleSignOn

**SingleSignOn** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| Scope | はい | シングル サインオン動作の範囲です。 指定できる値: `Suppressed`、`Tenant`、`Application`、または `Policy`。 `Suppressed` 値は動作が抑制されていることを示し、ユーザーは常に ID プロバイダーを選択することを求められます。  `Tenant` 値では、テナントのすべてのポリシーに動作が適用されることを示します。 たとえば、テナントの 2 つのポリシー体験間を移動するユーザーは ID プロバイダーの選択を求められません。 `Application` 値では、要求を行うアプリケーションのすべてのポリシーに動作が適用されることを示します。 たとえば、アプリケーションの 2 つのポリシー体験間を移動するユーザーは ID プロバイダーの選択を求められません。 `Policy` の値は、動作がポリシーに適用されるだけであることを示します。 たとえば、信頼フレームワークの 2 つのポリシー体験間を移動するユーザーは、ポリシー間を切り替える場合に ID プロバイダーの選択を求められます。 |
| KeepAliveInDays | いいえ | ユーザーのサインイン状態の継続期間を制御します。 値を 0 に設定すると、KMSI 機能がオフになります。 詳細については、[サインインしたままにする](session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi)方法に関するページを参照してください。 |
|EnforceIdTokenHintOnLogout| いいえ|  クライアントでのエンド ユーザーの現在の認証済みセッションに関するヒントとして、事前に発行された ID トークンをログアウト エンドポイントに強制的に渡します。 指定できる値は `false`(既定値) または`true`です。 詳細については、[OpenID Connect による Web サインイン](openid-connect.md)に関する記事を参照してください。  |


## <a name="journeyinsights"></a>JourneyInsights

**JourneyInsights** 要素には、次の属性が含まれています。

| 属性 | 必須 | Description |
| --------- | -------- | ----------- |
| TelemetryEngine | はい | 値は `ApplicationInsights` である必要があります。 |
| InstrumentationKey | はい | Application Insights 要素のインストルメンテーション キーを含む文字列。 |
| DeveloperMode | はい | 指定できる値: `true` または `false`。 `true` であれば、Application Insights はパイプラインの処理を通じてテレメトリを迅速化します。 この設定は開発には適していますが、大量の場合は制約があります。 詳細なアクティビティ ログは、カスタム ポリシーの開発を支援することだけを目的として設計されています。 実稼働環境では開発モードを使わないでください。 ログは、開発中に ID プロバイダーとの間で送受信されるすべての要求を収集します。 実稼働環境で使う場合、開発者は、自分が所有する App Insights ログに収集された個人データに対して責任を追うことになります。 これらの詳細なログは、この値が `true` に設定されている場合のみ収集されます。|
| ClientEnabled | はい | 指定できる値: `true` または `false`。 `true` とした場合、ページ ビューとクライアント側エラーを追跡するためのクライアント側スクリプトを Application Insights に送信します。 |
| ServerEnabled | はい | 指定できる値: `true` または `false`。 `true` では、既存の UserJourneyRecorder JSON をカスタム イベントとして Application Insights に送信します。 |
| TelemetryVersion | はい | 値は `1.0.0` である必要があります。 |

詳細については、「[ログの収集](troubleshoot-with-application-insights.md)」をご覧ください。

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

Azure AD B2C のカスタム ポリシーを使用すると、クエリ文字列でパラメーターを送信できます。 パラメーターを HTML エンドポイントに渡すことで、ページのコンテンツを動的に変更することができます。 たとえば、Web またはモバイル アプリケーションから渡すパラメーターに基づいて、Azure AD B2C サインアップまたはサインイン ページの背景イメージを変更できます。 Azure AD B2C では、aspx ファイルなど、動的な HTML ファイルにクエリ文字列パラメーターを渡します。

次の例は、`hawaii`クエリ文字列 のある `campaignId` という名前のパラメーターを渡します。

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

**ContentDefinitionParameters** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | Description |
| ------- | ----------- | ----------- |
| ContentDefinitionParameter | 0:n | コンテンツ定義読み込み URI のクエリ文字列に追加されている重要な値のペアを含む文字列。 |

**ContentDefinitionParameter** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| 名前 | はい | キーと値のペアの名前。 |

詳しくは、[カスタム ポリシーを使用して動的コンテンツを含む UI を構成する](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri)を参照してください

### <a name="journeyframing"></a>JourneyFraming

**JourneyFraming** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| Enabled | はい | このポリシーを iframe 内に読み込めるようにします。 指定できる値は `false`(既定値) または`true`です。 |
| 変換元 | はい | iframe を読み込んでホストするドメインが含まれています。 詳細については、[iframe での AZURE B2C の読み込み](embedded-login.md)に関するページを参照してください。 |

## <a name="technicalprofile"></a>TechnicalProfile

**TechnicalProfile** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| Id | はい | 値は `PolicyProfile` である必要があります。 |

**TechnicalProfile** には、次の属性が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | 技術プロファイルの名前を含む文字列。 |
| 説明 | 0:1 | 技術プロファイルの説明を含む文字列。 |
| Protocol | 1:1 | フェデレーションに使用されるプロトコル。 |
| Metadata | 0:1 | プロトコルによって使用されるキーと値のペアの *項目* のコレクション。これによってトランザクション中に証明書利用者と他のコミュニティ参加者との間の対話を構成するようにエンドポイントとやりとりされます。 |
| OutputClaims | 1:1 | 技術プロファイルの出力として実行される要求の種類の一覧。 これらの各要素には、**ClaimsSchema** セクションに、またはポリシー ファイルが継承したポリシー内に既に定義されている **ClaimType** への参照が含まれています。 |
| SubjectNamingInfo | 1:1 | トークンで使用されているサブジェクト名。 |

**プロトコル** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| 名前 | はい | 技術プロファイルの一部として使用される Azure AD B2C によってサポートされている有効なプロトコルの名前。 指定できる値: `OpenIdConnect` または `SAML2`。 `OpenIdConnect` 値は、OpenID 基盤の仕様に従って、OpenID Connect 1.0 プロトコルの標準を表します。 `SAML2` 値は、OASIS 仕様に従って、SAML 2.0 プロトコルの標準を表します。 |

### <a name="metadata"></a>Metadata

プロトコルが `SAML` である場合、メタデータ要素には次の要素が含まれます。 詳細については、[Azure AD B2C に SAML アプリケーションを登録するためのオプション](saml-service-provider-options.md)に関するページを参照してください。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| IdpInitiatedProfileEnabled | いいえ | IDP Initiated フローがサポートされているかどうかを示します。 指定できる値: `true` または `false`(既定値)。 | 
| XmlSignatureAlgorithm | いいえ | SAML 応答に署名するために Azure AD B2C で使用されるメソッド。 指定できる値: `Sha256`、`Sha384`、`Sha512`、または `Sha1`。 両方の側で同じ値の署名アルゴリズムを構成するようにします。 証明書でサポートされているアルゴリズムのみを使用してください。 SAML アサーションを構成するには、[SAML 発行者の技術プロファイルのメタデータ](saml-issuer-technical-profile.md#metadata)に関する記事を参照してください。 |
| DataEncryptionMethod | No | Advanced Encryption Standard (AES) アルゴリズムを使用してデータを暗号化するために Azure AD B2C で使用される方法を示します。 このメタデータにより、SAML 応答内の `<EncryptedData>` 要素の値が制御されます。 可能な値: `Aes256` (既定)、`Aes192`、`Sha512`、または ` Aes128`。 |
| KeyEncryptionMethod| No | データの暗号化に使用されたキーのコピーを暗号化するために Azure AD B2C で使用される方法を示します。 このメタデータにより、SAML 応答内の `<EncryptedKey>` 要素の値が制御されます。 使用可能な値: ` Rsa15` (既定) - RSA 公開鍵暗号化標準 (PKCS) バージョン 1.5 アルゴリズム、` RsaOaep` - RSA OAEP (Optimal Asymmetric Encryption Padding) 暗号化アルゴリズム。 |
| UseDetachedKeys | No |  指定できる値: `true` または `false` (既定値)。 値が `true` に設定されている場合、暗号化されたアサーションの形式が変わります。 デタッチされたキーを使用すると、暗号化されたアサーションが EncryptedData ではなく、EncrytedAssertion の子として追加されます。 |
| WantsSignedResponses| No | Azure AD B2C が SAML 応答の `Response` セクションに署名するかどうかを示します。 指定できる値: `true` (既定値) または `false`。  |
| RemoveMillisecondsFromDateTime| いいえ | SAML 応答内の datetime の値からミリ秒を削除するかどうかを示します (これには、IssueInstant、NotBefore、NotOnOrAfter、および AuthnInstant が含まれます)。 指定できる値: `false` (既定値) または `true`。  |


### <a name="outputclaims"></a>OutputClaims

**OutputClaims** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | Description |
| ------- | ----------- | ----------- |
| OutputClaim | 0:n | 証明書利用者のパーティが登録しているポリシーのサポートされている一覧で、想定される要求の種類の名前。 この要求は、技術プロファイルの出力として機能します。 |

**OutputClaim** 要素には、次の属性が含まれています。

| 属性 | 必須 | Description |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | はい | ポリシー ファイル内の **ClaimsSchema** セクションに既に定義されている **ClaimType** への参照。 |
| DefaultValue | いいえ | 既定値が空の場合に使用できる既定値。 |
| PartnerClaimType | いいえ | ClaimType 定義で構成されている別の名前で、要求を送信します。 |

### <a name="subjectnaminginfo"></a>SubjectNamingInfo

**SubjectNameingInfo** 要素によって、トークン サブジェクトの値を制御します。

- **JWT トークン** - `sub` 要求。 これは、トークンが情報をアサートするプリンシパルです (アプリケーションのユーザーなど)。 この値は変更不可で、再割り当ても再利用もできません。 そのため、この値を使用すると、トークンを使用してリソースにアクセスする場合などに安全に承認チェックができます。 既定では、サブジェクト要求には、ディレクトリ内のユーザーのオブジェクト ID が設定されます。 詳細は、[トークン、セッション、およびシングル サインオンの構成](session-behavior.md)をご覧ください。
- **SAML トークン** - subject 要素を識別する `<Subject><NameID>` 要素。 NameId 形式は変更することができます。

**SubjectNamingInfo** 要素には、次の属性が含まれています。

| 属性 | 必須 | Description |
| --------- | -------- | ----------- |
| ClaimType | はい | 出力要求の **PartnerClaimType** への参照。 証明書利用者ポリシー **OutputClaims** コレクションに出力要求を定義する必要があります。 |
| Format | いいえ | SAML アサーションで返される **NameId の形式** を設定する SAML 証明書利用者のために使用されます。 |

次の例では、OpenID Connect の証明書利用者を定義する方法を示します。 サブジェクト名情報は、`objectId` として構成されます。

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
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```
JWT トークンにはユーザー objectId による `sub` 要求が含まれます。

```json
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```

次の例では、SAML の証明書利用者を定義する方法を示します。 サブジェクト名情報は、`objectId` として構成され、NameId `format` が指定されています。

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient"/>
  </TechnicalProfile>
</RelyingParty>
```
