---
title: Azure AD B2C で Restful サービスを保護する
titleSuffix: Azure AD B2C
description: Azure AD B2C でカスタム REST API 要求交換を保護します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f6907db7f6e53247a8f2fc0042e8c8e6b081dbd3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97516367"
---
# <a name="secure-your-restful-services"></a>お使いの RESTful サービスを保護する 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C ユーザー体験内で REST API を統合する場合は、認証を使用して REST API エンドポイントを保護する必要があります。 これにより、Azure AD B2C などの適切な資格情報を持つサービスだけが REST API エンドポイントを呼び出すことができます。

[ユーザー入力の検証](custom-policy-rest-api-claims-validation.md)で Azure AD B2C ユーザー体験内の REST API を統合する方法、および[カスタム ポリシーに REST API 要求交換を追加](custom-policy-rest-api-claims-exchange.md)する方法の記事について説明します。

この記事では、HTTP 基本、クライアント証明書、OAuth2 認証のいずれかを使用して REST API を保護する方法について説明します。 

## <a name="prerequisites"></a>前提条件

次のいずれかの ’ハウツー’ ガイドの手順を実行します。

- [ユーザー入力の検証として REST API 要求交換を Azure AD B2C ユーザー体験に統合する方法](custom-policy-rest-api-claims-validation.md)に関するページ。
- [REST API 要求の交換をカスタム ポリシーに追加する方法](custom-policy-rest-api-claims-exchange.md)に関するページ

## <a name="http-basic-authentication"></a>HTTP 基本認証

HTTP 基本認証は [RFC 2617](https://tools.ietf.org/html/rfc2617) で定義されています。 基本認証は、次のように動作します。Azure AD B2C は、Authorization ヘッダー内にクライアント資格情報を持つ HTTP 要求を送信します。 資格情報は、Base64 でエンコードされた文字列 "name: password" として書式設定されます。  

### <a name="add-rest-api-username-and-password-policy-keys"></a>REST API ユーザー名とパスワードのポリシーのキーを追加する

HTTP 基本認証を使用して REST API の技術プロファイルを構成するには、ユーザー名とパスワードを格納するために次の暗号化キーを作成します。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用の Azure AD B2C ディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. [概要] ページで、 **[Identity Experience Framework]** を選択します。
1. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
1. **[オプション]** には **[手動]** を選択します。
1. **[名前]** に「**RestApiUsername**」と入力します。
    プレフィックス *B2C_1A_* が自動的に追加される場合があります。
1. **[秘密]** ボックスに、REST API ユーザー名を入力します。
1. **[キー使用法]** には **[暗号化]** を選択します。
1. **［作成］** を選択します
1. **[ポリシー キー]** を再度選択します。
1. **[追加]** を選択します。
1. **[オプション]** には **[手動]** を選択します。
1. **[名前]** に「**RestApiPassword**」と入力します。
    プレフィックス *B2C_1A_* が自動的に追加される場合があります。
1. **[秘密]** ボックスに、REST API パスワードを入力します。
1. **[キー使用法]** には **[暗号化]** を選択します。
1. **［作成］** を選択します

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>HTTP 基本認証を使用するように REST API の技術プロファイルを構成する

必要なキーを作成した後、資格情報を参照するように REST API の技術プロファイル メタデータを構成します。

1. 作業ディレクトリで、拡張ポリシー ファイル (TrustFrameworkExtensions.xml) を開きます。
1. REST API の技術プロファイルを検索します。 例: `REST-ValidateProfile`、または `REST-GetProfile`。
1. `<Metadata>` 要素を見つけます。
1. *AuthenticationType* を `Basic` に変更します。
1. *AllowInsecureAuthInProduction* を `false` に変更します。
1. `</Metadata>` 要素の終了直後に、次の XML スニペットを追加します。
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

HTTP 基本認証を使用して構成された RESTful 技術プロファイルの例を次に示します。

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="https-client-certificate-authentication"></a>HTTPS クライアント証明書認証

クライアント証明書の認証は証明書ベースの相互認証であり、クライアントである Azure AD B2C がクライアント証明書をサーバーに提供し、その ID を証明します。 これは、SSL ハンドシェイクの一部として発生します。 適切な証明書を持つサービス (Azure AD B2C など) のみが、REST API サービスにアクセスできます。 クライアント証明書は、X.509 デジタル証明書です。 運用環境では、証明機関によって署名されている必要があります。

### <a name="prepare-a-self-signed-certificate-optional"></a>自己署名証明書を準備する (省略可能)

非運用環境では、証明書をまだ持っていない場合は、自己署名証明書を使用できます。 Windows では、PowerShell の [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) コマンドレットを使用して証明書を生成できます。

1. この PowerShell コマンドを実行して、自己署名証明書を生成します。 アプリケーションと Azure AD B2C のテナント名に合わせて `-Subject` 引数を変更します。 また、証明書に別の有効期限を指定するように `-NotAfter` 日付を調整することもできます。
    ```powershell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```    
1. **[ユーザー証明書の管理]**  >  **[現在のユーザー]**  >  **[個人用]**  >  **[証明書]**  > *yourappname.yourtenant.onmicrosoft.com* を開きます。
1. 証明書 > **[アクション]**  >  **[すべてのタスク]**  >  **[エクスポート]** を選択します。
1. **[はい]**  >  **[次へ]**  >  **[はい、秘密キーをエクスポートします]**  >  **[次へ]** を選択します。
1. **[エクスポート ファイルの形式]** の既定値を受け入れます。
1. 証明書のパスワードを指定します。

### <a name="add-a-client-certificate-policy-key"></a>クライアント証明書ポリシー キーを追加する

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用の Azure AD B2C ディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. [概要] ページで、 **[Identity Experience Framework]** を選択します。
1. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
1. **[オプション]** ボックスで、 **[アップロード]** を選択します。
1. **[名前]** ボックスに「**RestApiClientCertificate**」と入力します。
    プレフィックス *B2C_1A_* が自動的に追加されます。
1. **[ファイルのアップロード]** ボックスで、秘密キーを備えた証明書の .pfx ファイルを選択します。
1. **[パスワード]** ボックスに、証明書のパスワードを入力します。
1. **［作成］** を選択します

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>クライアント証明書認証を使用するように REST API の技術プロファイルを構成する

必要なキーを作成した後、クライアント証明書を参照するように REST API の技術プロファイル メタデータを構成します。

1. 作業ディレクトリで、拡張ポリシー ファイル (TrustFrameworkExtensions.xml) を開きます。
1. REST API の技術プロファイルを検索します。 例: `REST-ValidateProfile`、または `REST-GetProfile`。
1. `<Metadata>` 要素を見つけます。
1. *AuthenticationType* を `ClientCertificate` に変更します。
1. *AllowInsecureAuthInProduction* を `false` に変更します。
1. `</Metadata>` 要素の終了直後に、次の XML スニペットを追加します。
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

HTTP クライアント証明書を使用して構成された RESTful 技術プロファイルの例を次に示します。

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ClientCertificate</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="oauth2-bearer-authentication"></a>OAuth2 ベアラー認証 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

ベアラー トークン認証の定義については、「[OAuth 2.0 Authorization Framework:Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)」(OAuth 2.0 承認フレームワーク: ベアラー トークンの使用法 (RFC 6750)) をご覧ください。 ベアラー トークン認証では、Azure AD B2C は、Authorization ヘッダーにトークンを含む HTTP 要求を送信します。

```http
Authorization: Bearer <token>
```

ベアラー トークンは、不透明な文字列です。 これは、JWT アクセス トークン、または REST API が Azure AD B2C によって Authorization ヘッダーで送信されることを想定する任意の文字列です。 Azure AD B2C では、次の種類がサポートされています。

- **ベアラー トークン**。 ベアラー トークンを Restful 技術プロファイルで送信できるようにするには、ポリシーでベアラー トークンを取得してから、それを RESTful 技術プロファイルで使用する必要があります。  
- **静的ベアラー トークン**。 REST API が長期アクセス トークンを発行する場合に、このアプローチを使用します。 静的ベアラー トークンを使用するには、ポリシー キーを作成し、RESTful 技術プロファイルからポリシー キーへの参照を作成します。 


## <a name="using-oauth2-bearer"></a>OAuth2 ベアラーの使用  

次の手順では、クライアント資格情報を使用してベアラー トークンを取得し、それを REST API 呼び出しの Authorization ヘッダーに渡す方法について説明します。  

### <a name="define-a-claim-to-store-the-bearer-token"></a>ベアラー トークンを格納する要求を定義する

要求は、Azure AD B2C ポリシーの実行時に、データの一時的なストレージとなります。 [要求スキーマ](claimsschema.md)は、要求を宣言する場所です。 アクセス トークンは、後で使用するために、要求に格納する必要があります。 

1. お使いのポリシーの拡張ファイルを開きます。 たとえば、<em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>です。
1. [BuildingBlocks](buildingblocks.md) 要素を検索します。 要素が存在しない場合は追加します。
1. [ClaimsSchema](claimsschema.md) 要素を見つけます。 要素が存在しない場合は追加します。
1. 次の要求を **ClaimsSchema** 要素に追加します。  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>Bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="grant_type">
  <DisplayName>Grant type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="scope">
  <DisplayName>scope</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>アクセス トークンの取得 

アクセス トークンは、[フェデレーション ID プロバイダーから](idp-pass-through-user-flow.md)取得する方法、アクセス トークンを返す REST API を呼び出す方法、[ROPC フロー](../active-directory/develop/v2-oauth-ropc.md)を使用する方法、または[クライアント資格情報フロー](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)を使用する方法のいずれかで取得できます。  

次の例では、REST API の技術プロファイルを使用し、HTTP 基本認証として渡されたクライアント資格情報を使用して Azure AD トークン エンドポイントに要求を行います。 Azure AD でこれを構成するには、「[Microsoft ID プラットフォームと OAuth 2.0 クライアント資格情報フロー](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)」をご覧ください。 お使いの ID プロバイダーとのインターフェイスを提供するようにこれを変更する必要があることがあります。 

ServiceUrl で、your-tenant-name を Azure AD テナントの名前に置き換えます。 使用可能なすべてのオプションについては、[RESTful 技術プロファイル](restful-technical-profile.md)のリファレンスを参照してください。

```xml
<TechnicalProfile Id="SecureREST-AccessToken">
  <DisplayName></DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://login.microsoftonline.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token</Item>
    <Item Key="AuthenticationType">Basic</Item>
     <Item Key="SendClaimsIn">Form</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_SecureRESTClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_SecureRESTClientSecret" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="client_credentials" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://secureb2cfunction.azurewebsites.net/.default" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>ベアラー トークン認証を使用するように REST 技術プロファイルを変更する

カスタム ポリシーでベアラー トークン認証をサポートするには、REST API の技術プロファイルを次のように変更します。

1. 作業ディレクトリで、*TrustFrameworkExtensions.xml* 拡張ポリシー ファイルを開きます。
1. `Id="REST-API-SignUp"` を含む `<TechnicalProfile>` ノードを探します。
1. `<Metadata>` 要素を見つけます。
1. 次のように、*AuthenticationType* を *Bearer* に変更します。
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. 次のように、*UseClaimAsBearerToken* を *bearerToken* に変更または追加します。 *bearerToken* は、ベアラー トークンの取得元になる要求の名前 (`SecureREST-AccessToken` からの出力要求) です。

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. 上記で使用した要求を入力要求として追加します。

    ```xml
    <InputClaim ClaimTypeReferenceId="bearerToken"/>
    ```    

上記のスニペットを追加すると、技術プロファイルは次の XML コードのようになります。

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="UseClaimAsBearerToken">bearerToken</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="using-a-static-oauth2-bearer"></a>静的 OAuth2 ベアラーの使用 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>OAuth2 ベアラー トークン ポリシー キーを追加する

OAuth2 ベアラー トークンを使用して REST API の技術プロファイルを構成するには、REST API 所有者からアクセス トークンを取得します。 次に、ベアラー トークンを格納するための次の暗号化キーを作成します。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用の Azure AD B2C ディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. [概要] ページで、 **[Identity Experience Framework]** を選択します。
1. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
1. **オプション** については、`Manual`を選択します。
1. ポリシー キーの **名前** を入力します。 たとえば、「 `RestApiBearerToken` 」のように入力します。 プレフィックス `B2C_1A_` がキーの名前に自動的に追加されます。
1. **[シークレット]** に、前に記録したクライアント シークレットを入力します。
1. **[キー使用法]** として [`Encryption`] を選択します。
1. **［作成］** を選択します

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>ベアラー トークン ポリシー キーを使用するように REST API の技術プロファイルを構成する

必要なキーを作成した後、ベアラー トークンを参照するように REST API の技術プロファイル メタデータを構成します。

1. 作業ディレクトリで、拡張ポリシー ファイル (TrustFrameworkExtensions.xml) を開きます。
1. REST API の技術プロファイルを検索します。 例: `REST-ValidateProfile`、または `REST-GetProfile`。
1. `<Metadata>` 要素を見つけます。
1. *AuthenticationType* を `Bearer` に変更します。
1. *AllowInsecureAuthInProduction* を `false` に変更します。
1. `</Metadata>` 要素の終了直後に、次の XML スニペットを追加します。
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

ベアラー トークン認証を使用して構成された RESTful 技術プロファイルの例を次に示します。

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="api-key-authentication"></a>API キー認証

API キーは、REST API エンドポイントにアクセスするユーザーを認証するために使用される一意の識別子です。 キーはカスタム HTTP ヘッダーで送信されます。 たとえば、[Azure Functions HTTP トリガー](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)は、`x-functions-key` HTTP ヘッダーを使用して要求者を識別します。  

### <a name="add-api-key-policy-keys"></a>API キーのポリシー キーを追加する

API キー認証を使用して REST API の技術プロファイルを構成するには、API キーを格納するために次の暗号化キーを作成します。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用の Azure AD B2C ディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. [概要] ページで、 **[Identity Experience Framework]** を選択します。
1. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
1. **[オプション]** には **[手動]** を選択します。
1. **[名前]** に「**RestApiKey**」と入力します。
    プレフィックス *B2C_1A_* が自動的に追加される場合があります。
1. **[秘密]** ボックスに、REST API キーを入力します。
1. **[キー使用法]** には **[暗号化]** を選択します。
1. **［作成］** を選択します


### <a name="configure-your-rest-api-technical-profile-to-use-api-key-authentication"></a>API キー認証を使用するように REST API の技術プロファイルを構成する

必要なキーを作成した後、資格情報を参照するように REST API の技術プロファイル メタデータを構成します。

1. 作業ディレクトリで、拡張ポリシー ファイル (TrustFrameworkExtensions.xml) を開きます。
1. REST API の技術プロファイルを検索します。 例: `REST-ValidateProfile`、または `REST-GetProfile`。
1. `<Metadata>` 要素を見つけます。
1. *AuthenticationType* を `ApiKeyHeader` に変更します。
1. *AllowInsecureAuthInProduction* を `false` に変更します。
1. `</Metadata>` 要素の終了直後に、次の XML スニペットを追加します。
    ```xml
    <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
    </CryptographicKeys>
    ```

暗号化キーの **ID** によって、HTTP ヘッダーが定義されます。 この例では、API キーは **x-functions-key** として送信されます。

API キー認証で Azure 関数を呼び出すように構成された RESTful 技術プロファイルの例を次に示します。

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ApiKeyHeader</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>次のステップ

- IEF のリファレンスで [Restful 技術プロファイル](restful-technical-profile.md)要素についてさらに学習します。