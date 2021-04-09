---
title: カスタム ポリシーでの Azure AD SSPR の技術プロファイル
titleSuffix: Azure AD B2C
description: Azure AD B2C での Azure AD SSPR 技術プロファイルのカスタム ポリシー リファレンス。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3e6fcf956639d827a8654c5ee80e7cab8cadf930
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85383599"
---
# <a name="define-an-azure-ad-sspr-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Azure AD B2C カスタム ポリシーで Azure AD SSPR 技術プロファイルを定義する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) では、セルフサービス パスワード リセット (SSPR) のメール アドレスの確認がサポートされています。 Azure AD SSPR 技術プロファイルを使用して、コードを生成し、メール アドレスに送信してから、コードを確認します。 Azure AD SSPR 技術プロファイルからは、エラー メッセージが返される場合もあります。 検証技術プロファイルでは、ユーザー体験を続ける前に、ユーザーが入力したデータを検証します。 検証技術プロファイルにより、エラー メッセージがセルフアサート ページに表示されます。

この技術プロファイル:

- ユーザーとやり取りするためのインターフェイスは用意していません。 代わりに、ユーザー インターフェイスは、[セルフアサート](self-asserted-technical-profile.md)技術プロファイルから、または[検証技術プロファイル](validation-technical-profile.md)としての[表示制御](display-controls.md)から呼び出されます。
- Azure AD SSPR サービスを使用して、コードを生成し、メール アドレスに送信してから、コードを確認します。  
- 確認コードを使用してメール アドレスを検証します。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocol

**Protocol** 要素の **Name** 属性は `Proprietary` に設定する必要があります。 **handler** 属性には、Azure AD B2C により使用されるプロトコル ハンドラー アセンブリの完全修飾名が含まれる必要があります。

```
Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

次の例では、Azure AD SSPR の技術プロファイルを示します。

```XML
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-email"></a>電子メールの送信

この技術プロファイルの最初のモードでは、コードが生成されて送信されます。 このモードに対しては、次のオプションを構成できます。

### <a name="input-claims"></a>入力クレーム

**InputClaims** 要素には、Azure AD SSPR に送信する要求の一覧が含まれます。 要求の名前を、SSPR 技術プロファイルで定義されている名前にマップすることもできます。

| ClaimReferenceId | 必須 | 説明 |
| --------- | -------- | ----------- |
| emailAddress | はい | メール アドレスを所有しているユーザーの識別子。 入力要求の `PartnerClaimType` プロパティを `emailAddress` に設定する必要があります。 |


**InputClaimsTransformations** 要素には、Azure AD SSPR サービスに送信する前に、入力要求の変更または新しい入力要求の生成に使用される、**InputClaimsTransformation** 要素のコレクションが含まれる場合があります。

### <a name="output-claims"></a>出力クレーム

Azure AD SSPR プロトコル プロバイダーでは **OutputClaims** は返されないため、出力要求を指定する必要はありません。 ただし、`DefaultValue` 属性を設定している限り、Azure AD SSPR プロトコル プロバイダーによって返されない要求を含めることができます。

**OutputClaimsTransformations** 要素には、出力要求を修正したり新しい要求を生成するために使用される、**OutputClaimsTransformation** 要素のコレクションが含まれている場合があります。

### <a name="metadata"></a>Metadata

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| Operation | はい | **SendCode** である必要があります。  |

#### <a name="ui-elements"></a>UI 要素

次のメタデータを使用して、SMS 送信に失敗したときに表示されるエラー メッセージを構成できます。 メタデータは、[セルフアサート](self-asserted-technical-profile.md)技術プロファイルで構成する必要があります。 エラー メッセージは、[ローカライズ](localization-string-ids.md#azure-ad-sspr)できます。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| UserMessageIfInternalError | いいえ | サーバーで内部エラーが発生した場合のユーザー エラー メッセージ。 |
| UserMessageIfThrottled| いいえ | 要求が調整された場合のユーザー エラー メッセージ。|


### <a name="example-send-an-email"></a>例: メールの送信

次の例では、電子メールでコードを送信するために使用される Azure AD SSPR 技術プロファイルを示します。

```XML
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">SendCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>コードの確認

この技術プロファイルの 2 番目のモードでは、コードが検証されます。 このモードに対しては、次のオプションを構成できます。

### <a name="input-claims"></a>入力クレーム

**InputClaims** 要素には、Azure AD SSPR に送信する要求の一覧が含まれます。 要求の名前を、SSPR 技術プロファイルで定義されている名前にマップすることもできます。

| ClaimReferenceId | 必須 | 説明 |
| --------- | -------- | ----------- | ----------- |
| emailAddress| はい | 以前にコードを送信するために使用したのと同じメール アドレス。 また、電子メール検証セッションを探すためにも使用されます。 入力要求の `PartnerClaimType` プロパティを `emailAddress` に設定する必要があります。|
| verificationCode  | はい | 確認のためにユーザーによって指定された確認コード。 入力要求の `PartnerClaimType` プロパティを `verificationCode` に設定する必要があります。 |

**InputClaimsTransformations** 要素には、Azure AD SSPR サービスの呼び出しの前に、入力要求の変更または新しい入力要求の生成に使用される、**InputClaimsTransformation** 要素のコレクションが含まれる場合があります。

### <a name="output-claims"></a>出力クレーム

Azure AD SSPR プロトコル プロバイダーでは **OutputClaims** は返されないため、出力要求を指定する必要はありません。 ただし、`DefaultValue` 属性を設定している限り、Azure AD SSPR プロトコル プロバイダーによって返されない要求を含めることができます。

**OutputClaimsTransformations** 要素には、出力要求を修正したり新しい要求を生成するために使用される、**OutputClaimsTransformation** 要素のコレクションが含まれている場合があります。

### <a name="metadata"></a>Metadata

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| Operation | はい | **VerifyCode** になっている必要があります |

#### <a name="ui-elements"></a>UI 要素

次のメタデータを使用して、コード確認に失敗したときに表示されるエラー メッセージを構成できます。 メタデータは、[セルフアサート](self-asserted-technical-profile.md)技術プロファイルで構成する必要があります。 エラー メッセージは、[ローカライズ](localization-string-ids.md#azure-ad-sspr)できます。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
|UserMessageIfChallengeExpired | コード確認セッションの有効期限が切れた場合にユーザーに表示するメッセージ。 コードの有効期限が切れているか、指定された識別子に対してコードが生成されたことがないかのいずれかです。|
|UserMessageIfInternalError | サーバーで内部エラーが発生した場合のユーザー エラー メッセージ。|
|UserMessageIfThrottled | 要求が調整された場合のユーザー エラー メッセージ。|
|UserMessageIfVerificationFailedNoRetry | 無効なコードを指定した場合にユーザーに表示するメッセージ。ユーザーは正しいコードを指定できません。|
|UserMessageIfVerificationFailedRetryAllowed | 無効なコードを指定した場合にユーザーに表示するメッセージ。ユーザーは正しいコードを指定できます。|

### <a name="example-verify-a-code"></a>例: コードを検証する

次の例では、コードの検証に使用される Azure AD SSPR の技術プロファイルを示します。

```XML
<TechnicalProfile Id="AadSspr-VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="verificationCode" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```