---
title: カスタム ポリシーで ID トークン ヒントの技術プロファイルを定義する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C のカスタム ポリシーで ID トークン ヒントの技術プロファイルを定義します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d77e145cabcef2931d5fe6e76599da7931e576e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97669161"
---
# <a name="define-an-id-token-hint-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C カスタム ポリシーで ID トークン ヒントの技術プロファイルを定義する

Azure AD B2C により、証明書利用者アプリケーションは OAuth2 認可要求の一部として受信 JWT を送信できます。 JWT トークンは、証明書利用者アプリケーションまたは ID プロバイダーが発行でき、これによって、ユーザーまたは認可要求に関するヒントを渡すことができます。 Azure AD B2C は、署名、発行者名、トークン対象ユーザーを検証し、受信トークンから要求を抽出します。

## <a name="use-cases"></a>ユース ケース

このソリューションを使用すると、データを 1 つの JWT トークンにカプセル化して Azure AD B2C に送信できます。 システム管理者が署名済み招待状をユーザーに送信できる[電子メール招待ソリューションによるサインアップ](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md)は、id_token_hint に基づきます。 招待メールにアクセスできるユーザーのみが、ディレクトリでアカウントを作成できます。

## <a name="token-signing-approach"></a>トークンの署名アプローチ

id_token_hint では、トークン発行者 (証明書利用者アプリケーションまたは ID プロバイダー) がトークンを作成し、署名キーを使用してそれに署名し、トークンが信頼されたソースからのものであることを証明します。 署名キーは、対称または非対称にすることができます。 対称暗号化 (秘密キー暗号化) では、共有シークレットを使用して署名と署名の検証の両方を行います。 非対称暗号化 (公開キー暗号化) は、秘密キーと公開キーの両方を使用する暗号化システムです。 秘密キーはトークン発行者のみが認識しており、トークンの署名に使用されます。 公開キーは、トークンの署名を検証するために Azure AD B2C ポリシーで共有されます。

## <a name="token-format"></a>トークンの形式

id_token_hint は、有効な JWT トークンである必要があります。 次の表に、必須の要求の一覧を示します。 その他の要求はオプションです。

| 名前 | 要求 | 値の例 | 説明 |
| ---- | ----- | ------------- | ----------- |
| 対象ユーザー | `aud` | `a489fc44-3cc0-4a78-92f6-e413cd853eae` | トークンの受信者を示します。 対象ユーザーは、トークン発行者によって定義される任意の文字列です。 Azure AD B2C によってこの値が検証され、一致しない場合はトークンが拒否されます。  |
| 発行者 | `iss` |`https://localhost` | セキュリティ トークン サービス (トークン発行者) を識別します。 発行者は、トークン発行者によって定義される任意の URI です。 Azure AD B2C によってこの値が検証され、一致しない場合はトークンが拒否されます。  |
| 期限切れ日時 | `exp` | `1600087315` | トークンが無効になる日時です。エポック時間で表されます。 Azure AD B2C によってこの値が検証され、期限切れになっている場合、トークンは拒否されます。|
| 期間の開始時刻 | `nbf` | `1599482515` | トークンが有効になる日時です。エポック時間で表されます。 この日時は、通常、トークンが発行されたのと同じ日時です。 Azure AD B2C によってこの値が検証され、有効期間が有効でない場合、トークンは拒否されます。 |

 次のトークンは、有効な ID トークンの例です。

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "displayName": " John Smith",
  "userId": "john.s@contoso.com",
  "nbf": 1599482515,
  "exp": 1600087315,
  "iss": "https://localhost",
  "aud": "a489fc44-3cc0-4a78-92f6-e413cd853eae"
}
```

## <a name="protocol"></a>Protocol

**Protocol** 要素の **Name** 属性は `None` に設定する必要があります。 たとえば、**IdTokenHint_ExtractClaims** 技術プロファイルのプロトコルは `None` です。

```xml
<TechnicalProfile Id="IdTokenHint_ExtractClaims">
  <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
  <Protocol Name="None" />
  ...
```

技術プロファイルは、種類が `GetClaims` のオーケストレーション ステップから呼び出されます。

```xml
<OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
``` 

## <a name="output-claims"></a>出力クレーム

**OutputClaims** 要素には、JWT トークンから抽出する要求の一覧が含まれます。 ポリシーで定義されている要求の名前を、JWT トークンで定義されている名前にマップする必要がある場合があります。 `DefaultValue` 属性を設定している限り、JWT トークンによって返されない要求も含めることができます。

## <a name="metadata"></a>Metadata

次のメタデータは、対称キーを使用する場合に関連します。 

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| 発行者 | Yes | セキュリティ トークン サービス (トークン発行者) を識別します。 この値は、JWT トークン要求内の `iss` 要求と同じである必要があります。 | 
| IdTokenAudience | Yes | トークンの受信者を示します。 JWT トークン要求内の `aud` 要求と同じである必要があります。 | 

次のメタデータは、非対称キーを使用する場合に関連します。 

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| METADATA| Yes | トークン発行者の構成ドキュメントを指す URL。これは、OpenID の既知の構成エンドポイントとも呼ばれます。   |
| 発行者 | No | セキュリティ トークン サービス (トークン発行者) を識別します。 この値は、メタデータで構成されている値を上書きするために使用できます。また、JWT トークン要求内の `iss` 要求と同じである必要があります。 |  
| IdTokenAudience | No | トークンの受信者を示します。 JWT トークン要求内の `aud` 要求と同じである必要があります。 |  

## <a name="cryptographic-keys"></a>暗号化キー

対称キーを使用する場合、**CryptographicKeys** 要素には次の属性が含まれます。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| client_secret | Yes | JWT トークンの署名を検証するために使用される暗号化キー。|


## <a name="how-to-guide"></a>ハウツー ガイド

### <a name="issue-a-token-with-symmetric-keys"></a>対称キーを使用してトークンを発行する

#### <a name="step-1-create-a-shared-key"></a>手順 1. 共有キーを作成する 

トークンに署名するために使用できるキーを作成します。 たとえば、次の PowerShell コードを使用してキーを生成します。

```powershell
$bytes = New-Object Byte[] 32
$rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
$rand.GetBytes($bytes)
$rand.Dispose()
$newClientSecret = [System.Convert]::ToBase64String($bytes)
$newClientSecret
```

このコードにより、`VK62QTn0m1hMcn0DQ3RPYDAr6yIiSvYgdRwjZtU5QhI=` のようなシークレット文字列が作成されます。

#### <a name="step-2-add-the-signing-key-to-azure-ad-b2c"></a>手順 2. Azure AD B2C に署名キーを追加する

トークン発行者が使用するのと同じキーを、Azure AD B2C ポリシー キーで作成する必要があります。  

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. [概要] ページで、 **[ポリシー]** を選択してから **[Identity Experience Framework]** を選択します。
1. **[ポリシー キー]** を選択します。 
1. **[Manual] \(手動)** を選択します。
1. **[名前]** には `IdTokenHintKey` を使用します。  
   プレフィックス `B2C_1A_` が自動的に追加される場合があります。
1. **[シークレット]** ボックスに、先ほど生成したサインイン キーを入力します。
1. **[キー使用法]** には **[暗号化]** を使用します。
1. **［作成］** を選択します
1. キー `B2C_1A_IdTokenHintKey` を作成したことを確認します。


#### <a name="step-3-add-the-id-token-hint-technical-profile"></a>手順 3. ID トークン ヒントの技術プロファイルを追加する

次の技術プロファイルにより、トークンが検証され、要求が抽出されます。 

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <Item Key="IdTokenAudience">a489fc44-3cc0-4a78-92f6-e413cd853eae</Item>
        <Item Key="issuer">https://localhost</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_IdTokenHintKey" />
      </CryptographicKeys>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-4-prepare-your-policy"></a>手順 4. ドライブを準備する

[ポリシーの構成](#configure-your-policy)手順を完了します。

#### <a name="step-5-prepare-the-code"></a>手順 5. コードを準備する  

[GitHub サンプル](https://github.com/azure-ad-b2c/id_token_hint/tree/master/dotnet_core_symmetric_key)は、対称キーを使用して署名される ID トークンを生成する ASP.NET Web アプリケーションとコンソール アプリです。 


### <a name="issue-a-token-with-asymmetric-keys"></a>非対称キーを使用してトークンを発行する

非対称キーでは、トークンは RSA 証明書を使用して署名されます。 このアプリケーションは、ID トークンの署名を検証するために Azure AD B2C によって使用される Open ID Connect メタデータ エンドポイントと JSON Web キー (JWK) エンドポイントをホストします。

トークン発行者は、次のエンドポイントを提供する必要があります。

* `/.well-known/openid-configuration` - トークン発行者名、JWK エンドポイントへのリンクなど、トークンに関する関連情報を含む既知の構成エンドポイント。 
* `/.well-known/keys` - (証明書の秘密キー部分を含む) キーに署名するために使用される公開キーを含む JSON Web キー (JWK) エンドポイント。

[TokenMetadataController.cs](https://github.com/azure-ad-b2c/id-token-builder/blob/master/source-code/B2CIdTokenBuilder/Controllers/TokenMetadataController.cs) .Net MVC コントローラーのサンプルを参照してください。

#### <a name="step-1-prepare-a-self-signed-certificate"></a>手順 1. 自己署名証明書を準備する

証明書がまだない場合、このハウツー ガイドでは自己署名証明書を使用できます。 Windows では、PowerShell の [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) コマンドレットを使用して証明書を生成できます。

この PowerShell コマンドを実行して、自己署名証明書を生成します。 アプリケーションと Azure AD B2C のテナント名に合わせて `-Subject` 引数を変更します。 また、証明書に別の有効期限を指定するように `-NotAfter` 日付を調整することもできます。

```PowerShell
New-SelfSignedCertificate `
    -KeyExportPolicy Exportable `
    -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
    -KeyAlgorithm RSA `
    -KeyLength 2048 `
    -KeyUsage DigitalSignature `
    -NotAfter (Get-Date).AddMonths(12) `
    -CertStoreLocation "Cert:\CurrentUser\My"
```


#### <a name="step-2-add-the-id-token-hint-technical-profile"></a>手順 2. ID トークン ヒントの技術プロファイルを追加する 

次の技術プロファイルにより、トークンが検証され、要求が抽出されます。 メタデータ URI をトークン発行者の既知の構成エンドポイントに変更します。  

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <!-- Replace with your endpoint location -->
        <Item Key="METADATA">https://your-app.azurewebsites.net/.well-known/openid-configuration</Item>
        <Item Key="IdTokenAudience">your_optional_audience</Item> -->
        <!-- <Item Key="issuer">your_optional_token_issuer_override</Item> -->
      </Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-3-prepare-your-policy"></a>手順 3. ドライブを準備する

[ポリシーの構成](#configure-your-policy)手順を完了します。

#### <a name="step-4-prepare-the-code"></a>手順 4. コードを準備する 

この [GitHub サンプル](https://github.com/azure-ad-b2c/id-token-builder) ASP.NET Web アプリケーションは、ID トークンを生成し、Azure AD B2C で "id_token_hint" パラメーターを使用するために必要なメタデータ エンドポイントをホストします。


### <a name="configure-your-policy"></a>ポリシーを構成する

対称と非対称の両方のアプローチで、`id_token_hint` 技術プロファイルは、種類が `GetClaims` のオーケストレーション ステップから呼び出されます。そのプロファイルでは、証明書利用者ポリシーの入力要求を指定する必要があります。

1. 拡張ポリシーに IdTokenHint_ExtractClaims 技術プロファイルを追加します。
1. 次のオーケストレーション ステップを、最初の項目としてユーザー体験に追加します。  

    ```xml
    <OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
    ``` 
1. 証明書利用者ポリシーで、IdTokenHint_ExtractClaims 技術プロファイルで構成したのと同じ入力要求を繰り返します。 次に例を示します。
    ```xml
   <RelyingParty>
     <DefaultUserJourney ReferenceId="SignUp" />
     <TechnicalProfile Id="PolicyProfile">
       <DisplayName>PolicyProfile</DisplayName>
       <Protocol Name="OpenIdConnect" />
       <InputClaims>
         <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
        </InputClaims>
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

ビジネス要件によっては、トークンの検証 (電子メール アドレスの形式確認など) の追加が必要になる場合があります。 これを行うには、[要求変換技術プロファイル](claims-transformation-technical-profile.md)を呼び出すオーケストレーション ステップを追加します。 また、エラー メッセージを表示するために、[セルフアサート技術プロファイル](self-asserted-technical-profile.md)を追加します。 

### <a name="create-and-sign-a-token"></a>トークンを作成して署名する

この GitHub サンプルは、後で `id_token_hint` クエリ文字列パラメーターとして送信される JWT のようなトークン発行を作成する方法を示しています。 id_token_hint パラメーターを使用した認可要求の例を次に示します。
 
```html
https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize?client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&id_token_hint=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkaXNwbGF5TmFtZSI6IiBKb2huIFNtaXRoIiwidXNlcklkIjoiam9obi5zQGNvbnRvc28uY29tIiwibmJmIjoxNTk5NDgyNTE1LCJleHAiOjE2MDAwODczMTUsImlzcyI6Imh0dHBzOi8vbG9jYWxob3N0IiwiYXVkIjoiYTQ4OWZjNDQtM2NjMC00YTc4LTkyZjYtZTQxM2NkODUzZWFlIn0.nPmLXydI83PQCk5lRBYUZRu_aX58pL1khahHyQuupig
```

## <a name="next-steps"></a>次の手順

- Azure AD B2C コミュニティの GitHub リポジトリにある[招待メールでのサインアップ](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md) ソリューションを確認する。
