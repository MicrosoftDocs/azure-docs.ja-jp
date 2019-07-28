---
title: Azure Active Directory B2C のカスタム ポリシーで Azure Active Directory 技術プロファイルを定義する | Microsoft Docs
description: Azure Active Directory B2C のカスタム ポリシーで Azure Active Directory 技術プロファイルを定義します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: b2c
ms.openlocfilehash: 8b8bbe540d9e296b0f6a0c11a62d3b861e0115d3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66507447"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C カスタム ポリシーで Azure Active Directory 検証技術プロファイルを定義します。

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C は、Azure Active Directory ユーザー管理をサポートしています。 この記事では、この標準化されたプロトコルをサポートするクレーム プロバイダーとやりとりするための、技術プロファイルの詳細について説明します。

## <a name="protocol"></a>Protocol

**Protocol** 要素の **Name** 属性は `Proprietary` に設定する必要があります。 **handler** 属性には、プロトコル ハンドラー アセンブリ `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null` の完全修飾名が含まれている必要があります。

すべての Azure AD 技術プロファイルには、**AAD-Common** 技術プロファイルが含まれています。 以下の技術プロファイルではプロトコルが指定されていません。これは、プロトコルが **AAD-Common** 技術プロファイルで構成されているためです。

- **AAD-UserReadUsingAlternativeSecurityId** および **AAD-UserReadUsingAlternativeSecurityId-NoError** - ディレクトリ内のソーシャル アカウントを検索します。
- **AAD-UserWriteUsingAlternativeSecurityId** - 新しいソーシャル アカウントを作成します。
- **AAD-UserReadUsingEmailAddress** - ディレクトリ内のローカル アカウントを検索します。 
- **AAD-UserWriteUsingLogonEmail** - 新しいローカル アカウントを作成します。
- **AAD-UserWritePasswordUsingObjectId** - ローカル アカウントのパスワードを更新します。
- **AAD-UserWriteProfileUsingObjectId** - ローカルまたはソーシャル アカウントのユーザー プロファイルを更新します。
- **AAD-UserReadUsingObjectId** - ローカルまたはソーシャル アカウントのユーザー プロファイルを読み取ります。
- **AAD-UserWritePhoneNumberUsingObjectId** -  ローカルまたはソーシャル アカウントの MFA 電話番号を書き込みます。

次の例は、**AAD-Common** 技術プロファイルを示しています。

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>

  <!-- We need this here to suppress the SelfAsserted provider from invoking SSO on validation profiles. -->
  <IncludeInSso>false</IncludeInSso>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="input-claims"></a>入力要求

以下の技術プロファイルには、ソーシャル アカウントとローカル アカウントの **InputClaims** が含まれています。

- ソーシャル アカウント技術プロファイル **AAD-UserReadUsingAlternativeSecurityId** および **AAD-UserWriteUsingAlternativeSecurityId** には、**AlternativeSecurityId** 要求が含まれています。 この要求には、ソーシャル アカウント ユーザー識別子が含まれています。
- ローカル アカウント技術プロファイル **AAD-UserReadUsingEmailAddress** および **AAD-UserWriteUsingLogonEmail** には、**email** 要求が含まれています。 この要求には、ローカル アカウントのサインイン名が含まれています。
- 統合された (ローカルおよびソーシャル) 技術プロファイル **AAD-UserReadUsingObjectId**、**AAD-UserWritePasswordUsingObjectId**、**AAD-UserWriteProfileUsingObjectId**、および **AAD-UserWritePhoneNumberUsingObjectId** には、**objectId** 要求が含まれています。 アカウントの一意識別子。

**InputClaimsTransformations** 要素には、入力要求を変更したり新しい要求を生成するために使用される、**InputClaimsTransformation** 要素のコレクションが含まれている場合があります。

## <a name="output-claims"></a>出力要求

**OutputClaims** 要素には、Azure AD 技術プロファイルによって返された要求の一覧が含まれています。 ポリシーに定義されている要求の名前を、Azure Active Directory で定義されている名前にマップすることが必要になる場合があります。 また、`DefaultValue` 属性を設定している限り、Azure Active Directory によって返されない要求を含めることもできます。

**OutputClaimsTransformations** 要素には、出力要求を変更したり新しい要求を生成するために使用される、**OutputClaimsTransformation** 要素のコレクションが含まれている場合があります。

たとえば、**AAD-UserWriteUsingLogonEmail**  技術プロファイルはローカル アカウントを作成し、以下の要求を返します。

- **objectId**、新しいアカウントの識別子です
- **newUser**、ユーザーが新規であるかどうかを示します
- **authenticationSource**、認証を `localAccountAuthentication` に設定します
- **userPrincipalName**、新しいアカウントのユーザー プリンシパル名です
- **signInNames.emailAddress**、アカウントのサインイン名で、**email** 入力要求に似ています

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>PersistedClaims

**PersistedClaims** 要素には、ポリシー内の ClaimsSchema セクションに既に定義されている要求の種類と Azure AD 属性名の間の使用可能なマッピング情報を使用して、Azure AD によって保持される必要があるすべての値が含まれています。 

新しいローカル アカウントを作成する **AAD-UserWriteUsingLogonEmail** 技術プロファイルは、以下の要求を保持します。

```XML
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
    <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />

    <!-- Optional claims. -->
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
```

要求の名前は、Azure AD 属性名を含む  **PartnerClaimType** 属性が指定されている場合を除き、Azure AD 属性の名前です。

## <a name="requirements-of-an-operation"></a>操作の要件

- すべての Azure AD 技術プロファイルには、**InputClaim** 要素が要求バッグに 1 つだけ存在する必要があります。 
- 操作が `Write` または `DeleteClaims` の場合は、**PersistedClaims** 要素にも表示する必要があります。
- **userPrincipalName** 要求の値の形式は、`user@tenant.onmicrosoft.com` である必要があります。
- **displayName** 要求は必須であり、空の文字列にすることはできません。

## <a name="azure-ad-technical-provider-operations"></a>Azure AD 技術プロバイダー操作

### <a name="read"></a>読み取り

**読み取り**操作は、単一のユーザー アカウントに関するデータを読み取ります。 ユーザー データを読み取るには、**objectId**、**userPrincipalName**、**signInNames**(任意の型、ユーザー名および電子メールベースのアカウント) または**alternativeSecurityId** などの入力要求としてキーを指定する必要があります。  

以下の技術プロファイルは、ユーザーの objectId を使用してユーザー アカウントに関するデータを読み取ります。

```XML
<TechnicalProfile Id="AAD-UserReadUsingObjectId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims>

    <!-- Required claims -->
    <OutputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />

    <!-- Optional claims -->
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="write"></a>書き込み

**書き込み**操作は、単一のユーザー アカウントを作成または更新します。 ユーザー アカウントを書き込むには、**objectId**、**userPrincipalName**、**signInNames.emailAddress**、または **alternativeSecurityId** などの入力要求としてキーを指定する必要があります。  

以下の技術プロファイルは、新しいソーシャル アカウントを作成します。

```XML
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Write</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />

    <!-- Optional claims -->
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

### <a name="deleteclaims"></a>DeleteClaims

**DeleteClaims** 操作は、提供された要求の一覧から情報を消去します。 要求から情報を削除するには、**objectId**、**userPrincipalName**、**signInNames.emailAddress** または **alternativeSecurityId**などの入力要求としてキーを指定する必要があります。  

以下の技術プロファイルは、要求を削除します。

```XML
<TechnicalProfile Id="AAD-DeleteClaimsUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaims</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims />
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="deleteclaimsprincipal"></a>DeleteClaimsPrincipal

**DeleteClaimsPrincipal** 操作は、ディレクトリから単一のユーザー アカウントを削除します。 ユーザー アカウントを削除するには、**objectId**、**userPrincipalName**、**signInNames.emailAddress** または **alternativeSecurityId** などの入力要求としてキーを指定する必要があります。  

以下の技術プロファイルは、ユーザー プリンシパル名を使用してディレクトリからユーザー アカウントを削除します。

```XML
<TechnicalProfile Id="AAD-DeleteUserUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

以下の技術プロファイルは、**alternativeSecurityId** を使用してソーシャル ユーザー アカウントを削除します。

```XML
<TechnicalProfile Id="AAD-DeleteUserUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```
## <a name="metadata"></a>Metadata

| Attribute | 必須 | 説明 |
| --------- | -------- | ----------- |
| Operation | はい | 実行する操作。 指定できる値: `Read`、`Write`、`DeleteClaims`、または `DeleteClaimsPrincipal`。 | 
| RaiseErrorIfClaimsPrincipalDoesNotExist | いいえ | ユーザー オブジェクトがディレクトリに存在しない場合、エラーを発生させます。 指定できる値: `true` または `false`。 | 
| UserMessageIfClaimsPrincipalDoesNotExist | いいえ | エラーが発生する場合 (RaiseErrorIfClaimsPrincipalDoesNotExist 属性の説明を参照)、ユーザー オブジェクトが存在しない場合にユーザーに表示するメッセージを指定します。 値を[ローカライズ](localization.md)することができます。| 
| RaiseErrorIfClaimsPrincipalAlreadyExists | いいえ | ユーザー オブジェクトが既に存在する場合、エラーを発生させます。 指定できる値: `true` または `false`。| 
| UserMessageIfClaimsPrincipalAlreadyExists | いいえ | エラーが発生する場合 (RaiseErrorIfClaimsPrincipalAlreadyExists 属性の説明を参照)、ユーザー オブジェクトが既に存在する場合にユーザーに表示するメッセージを指定します。 値を[ローカライズ](localization.md)することができます。| 
| ApplicationObjectId | いいえ | 拡張属性のアプリケーション オブジェクト識別子。 値:アプリケーションの ObjectId。 詳細については、「[カスタム プロファイル編集ポリシーのカスタム属性を使用](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)」を参照してください。 | 
| ClientId | いいえ | サード パーティとしてテナントにアクセスするためのクライアント識別子。 詳細については、「[カスタム プロファイル編集ポリシーのカスタム属性を使用](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)」を参照してください。 | 














