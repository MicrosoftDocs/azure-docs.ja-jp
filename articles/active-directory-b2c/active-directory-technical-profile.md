---
title: カスタム ポリシーで Azure AD 技術プロファイルを定義する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C のカスタム ポリシーで Azure Active Directory 技術プロファイルを定義します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3f92bfe98a45117264c957481a75493de652abc9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97508103"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C カスタム ポリシーで Azure Active Directory 検証技術プロファイルを定義します。

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) は、Azure Active Directory ユーザー管理をサポートしています。 この記事では、この標準化されたプロトコルをサポートするクレーム プロバイダーとやりとりするための、技術プロファイルの詳細について説明します。

## <a name="protocol"></a>Protocol

**Protocol** 要素の **Name** 属性は `Proprietary` に設定する必要があります。 **handler** 属性には、プロトコル ハンドラー アセンブリ `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null` の完全修飾名が含まれている必要があります。

次の [カスタム ポリシー スターター パック](custom-policy-get-started.md#custom-policy-starter-pack) Azure AD 技術プロファイルには、**AAD-Common** 技術プロファイルが含まれています。 Azure AD 技術プロファイルではプロトコルが指定されていません。これは、プロトコルが **AAD-Common** 技術プロファイルで構成されているためです。
 
- **AAD-UserReadUsingAlternativeSecurityId** および **AAD-UserReadUsingAlternativeSecurityId-NoError** - ディレクトリ内のソーシャル アカウントを検索します。
- **AAD-UserWriteUsingAlternativeSecurityId** - 新しいソーシャル アカウントを作成します。
- **AAD-UserReadUsingEmailAddress** - ディレクトリ内のローカル アカウントを検索します。
- **AAD-UserWriteUsingLogonEmail** - 新しいローカル アカウントを作成します。
- **AAD-UserWritePasswordUsingObjectId** - ローカル アカウントのパスワードを更新します。
- **AAD-UserWriteProfileUsingObjectId** - ローカルまたはソーシャル アカウントのユーザー プロファイルを更新します。
- **AAD-UserReadUsingObjectId** - ローカルまたはソーシャル アカウントのユーザー プロファイルを読み取ります。
- **AAD-UserWritePhoneNumberUsingObjectId** -  ローカルまたはソーシャル アカウントの MFA 電話番号を書き込みます。

次の例は、**AAD-Common** 技術プロファイルを示しています。

```xml
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

## <a name="inputclaims"></a>InputClaims

InputClaims 要素には、ディレクトリ内のアカウントを検索したり、新しいものを作成したりするために使用される要求が含まれています。 すべての Azure AD 技術プロファイルには、InputClaim 要素が入力要求コレクションに 1 つだけ存在する必要があります。 ポリシーに定義されている要求の名前を、Azure Active Directory で定義されている名前にマップすることが必要になる場合があります。

既存のユーザー アカウントの読み取り、更新、または削除を行う場合、入力要求は Azure AD ディレクトリ内のアカウントを一意に識別するキーです。 たとえば、**objectId**、**userPrincipalName**、**signInNames.emailAddress**、**signInNames.userName**、**alternativeSecurityId** などです。 

新しいユーザー アカウントを作成する場合、入力要求はローカルまたはフェデレーション アカウントを一意に識別するキーです。 たとえば、ローカル アカウントの場合、**signInNames.emailAddress** や **signInNames.userName** です。 フェデレーション アカウントの場合は、**alternativeSecurityId** です。

[InputClaimsTransformations](technicalprofiles.md#input-claims-transformations) 要素には、入力要求を変更したり新しいものを生成するために使用される、入力要求変換の要素のコレクションを含めることができます。

## <a name="outputclaims"></a>OutputClaims

**OutputClaims** 要素には、Azure AD 技術プロファイルによって返された要求の一覧が含まれています。 ポリシーに定義されている要求の名前を、Azure Active Directory で定義されている名前にマップすることが必要になる場合があります。 また、`DefaultValue` 属性を設定している限り、Azure Active Directory によって返されない要求を含めることもできます。

[OutputClaimsTransformations](technicalprofiles.md#output-claims-transformations) 要素には、出力要求を修正したり新しい要求を生成するために使用される、**OutputClaimsTransformation** 要素のコレクションが含まれている場合があります。

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

**PersistedClaims** 要素には、ポリシー内の [ClaimsSchema](claimsschema.md) セクションに既に定義されている要求の種類と Azure AD 属性名の間の可能なマッピング情報と共に、Azure AD によって保持される必要があるすべての値が含まれています。

新しいローカル アカウントを作成する **AAD-UserWriteUsingLogonEmail** 技術プロファイルは、以下の要求を保持します。

```xml
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
- [ユーザー プロファイル属性に関する記事](user-profile-attributes.md)では、入力要求、出力要求、および永続化された要求で使用できる、サポートされている Azure AD B2C ユーザー プロファイル属性について説明しています。 
- 操作が `Write` または `DeleteClaims` の場合は、**PersistedClaims** 要素にも表示する必要があります。
- **userPrincipalName** 要求の値の形式は、`user@tenant.onmicrosoft.com` である必要があります。
- **displayName** 要求は必須であり、空の文字列にすることはできません。

## <a name="azure-ad-technical-provider-operations"></a>Azure AD 技術プロバイダー操作

### <a name="read"></a>Read

**読み取り** 操作は、単一のユーザー アカウントに関するデータを読み取ります。 以下の技術プロファイルは、ユーザーの objectId を使用してユーザー アカウントに関するデータを読み取ります。

```xml
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

### <a name="write"></a>Write

**書き込み** 操作は、単一のユーザー アカウントを作成または更新します。 以下の技術プロファイルは、新しいソーシャル アカウントを作成します。

```xml
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

**DeleteClaims** 操作は、提供された要求の一覧から情報を消去します。 以下の技術プロファイルは、要求を削除します。

```xml
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

**DeleteClaimsPrincipal** 操作は、ディレクトリから単一のユーザー アカウントを削除します。 以下の技術プロファイルは、ユーザー プリンシパル名を使用してディレクトリからユーザー アカウントを削除します。

```xml
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

```xml
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

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| Operation | はい | 実行する操作。 指定できる値: `Read`、`Write`、`DeleteClaims`、または `DeleteClaimsPrincipal`。 |
| RaiseErrorIfClaimsPrincipalDoesNotExist | いいえ | ユーザー オブジェクトがディレクトリに存在しない場合、エラーを発生させます。 指定できる値: `true` または `false`。 |
| RaiseErrorIfClaimsPrincipalAlreadyExists | いいえ | ユーザー オブジェクトが既に存在する場合、エラーを発生させます。 指定できる値: `true` または `false`。|
| ApplicationObjectId | いいえ | 拡張属性のアプリケーション オブジェクト識別子。 値:アプリケーションの ObjectId。 詳細については、[カスタム 属性を使用する](user-flow-custom-attributes.md?pivots=b2c-custom-policy)方法に関する記事を参照してください。 |
| ClientId | いいえ | サード パーティとしてテナントにアクセスするためのクライアント識別子。 詳細については、「[カスタム プロファイル編集ポリシーのカスタム属性を使用](user-flow-custom-attributes.md?pivots=b2c-custom-policy)」を参照してください。 |
| IncludeClaimResolvingInClaimsHandling  | いいえ | 入力と出力の要求について、[要求の解決](claim-resolver-overview.md)を技術プロファイルに含めるかどうかを指定します。 指定できる値: `true` または `false` (既定値)。 技術プロファイルで要求リゾルバーを使用する場合は、これを `true` に設定します。 |

### <a name="ui-elements"></a>UI 要素
 
次の設定を使用して、失敗したときに表示されるエラー メッセージを構成できます。 メタデータは、[セルフアサート](self-asserted-technical-profile.md)技術プロファイルで構成する必要があります。 エラー メッセージは、[ローカライズ](localization.md)できます。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalAlreadyExists | いいえ | エラーが発生する場合 (RaiseErrorIfClaimsPrincipalAlreadyExists 属性の説明を参照)、ユーザー オブジェクトが既に存在する場合にユーザーに表示するメッセージを指定します。 |
| UserMessageIfClaimsPrincipalDoesNotExist | いいえ | エラーが発生する場合 (RaiseErrorIfClaimsPrincipalDoesNotExist 属性の説明を参照)、ユーザー オブジェクトが存在しない場合にユーザーに表示するメッセージを指定します。 |


## <a name="next-steps"></a>次のステップ

Azure AD 技術プロファイルの使用例については、次の記事を参照してください。

- [Azure Active Directory B2C のカスタム ポリシーを使用した要求の追加とユーザー入力のカスタマイズ](configure-user-input.md)














