---
title: カスタム ポリシーでの Azure AD MFA の技術プロファイル
titleSuffix: Azure AD B2C
description: Azure AD B2C での Azure AD Multi-Factor Authentication (MFA) 技術プロファイルのカスタム ポリシー リファレンス。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e81ac35555e6653cecb602e5af2f19aa3e2f05e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94840595"
---
# <a name="define-an-azure-ad-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Azure AD B2C カスタム ポリシーで Azure AD MFA 技術プロファイルを定義する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) では、Azure AD Multi-Factor Authentication (MFA) を使用して電話番号を確認するためのサポートが提供されています。 この技術プロファイルを使用して、コードを生成し、電話番号に送信してから、コードを確認します。 Azure AD MFA 技術プロファイルからは、エラー メッセージが返される場合もあります。  検証技術プロファイルでは、ユーザー体験を続ける前に、ユーザーが入力したデータを検証します。 検証技術プロファイルにより、エラー メッセージがセルフアサート ページに表示されます。

この技術プロファイル:

- ユーザーとやり取りするためのインターフェイスは用意していません。 代わりに、ユーザー インターフェイスは、[セルフアサート](self-asserted-technical-profile.md)技術プロファイルから、または[検証技術プロファイル](validation-technical-profile.md)としての[表示制御](display-controls.md)から呼び出されます。
- Azure AD MFA サービスを使用して、コードを生成し、電話番号に送信してから、コードを確認します。  
- テキスト メッセージを介して電話番号を検証します。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocol

**Protocol** 要素の **Name** 属性は `Proprietary` に設定する必要があります。 **handler** 属性には、Azure AD B2C により使用されるプロトコル ハンドラー アセンブリの完全修飾名が含まれる必要があります。

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

次の例では、Azure AD MFA の技術プロファイルを示します。

```xml
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>SMS を送信する

この技術プロファイルの最初のモードでは、コードが生成されて送信されます。 このモードに対しては、次のオプションを構成できます。

### <a name="input-claims"></a>入力クレーム

**InputClaims** 要素には、Azure AD MFA に送信する要求の一覧が含まれます。 要求の名前を、MFA 技術プロファイルで定義されている名前にマップすることもできます。

| ClaimReferenceId | 必須 | 説明 |
| --------- | -------- | ----------- |
| userPrincipalName | はい | 電話番号を所有しているユーザーの識別子。 |
| phoneNumber | はい | SMS コードを送信する先の電話番号。 |
| companyName | いいえ |SMS の会社名。 指定されていない場合は、アプリケーションの名前が使用されます。 |
| locale | いいえ | SMS のロケール。 指定されていない場合は、ユーザーのブラウザーのロケールが使用されます。 |

**InputClaimsTransformations** 要素には、Azure AD MFA サービスに送信する前に、入力要求の変更または新しい入力要求の生成に使用される、**InputClaimsTransformation** 要素のコレクションが含まれる場合があります。

### <a name="output-claims"></a>出力クレーム

Azure AD MFA プロトコル プロバイダーでは **OutputClaims** は返されないため、出力要求を指定する必要はありません。 ただし、`DefaultValue` 属性を設定している限り、Azure AD MFA ID プロバイダーによって返されない要求を含めることができます。

**OutputClaimsTransformations** 要素には、出力要求を修正したり新しい要求を生成するために使用される、**OutputClaimsTransformation** 要素のコレクションが含まれている場合があります。

### <a name="metadata"></a>Metadata

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| Operation | はい | **OneWaySMS** になっている必要があります。  |

#### <a name="ui-elements"></a>UI 要素

次のメタデータを使用して、SMS 送信に失敗したときに表示されるエラー メッセージを構成できます。 メタデータは、[セルフアサート](self-asserted-technical-profile.md)技術プロファイルで構成する必要があります。 エラー メッセージは、[ローカライズ](localization-string-ids.md#azure-ad-mfa-error-messages)できます。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| UserMessageIfCouldntSendSms | いいえ | 指定された電話番号で SMS が受け付けられない場合のユーザー エラー メッセージ。 |
| UserMessageIfInvalidFormat | いいえ | 指定された電話番号が有効な電話番号ではない場合のユーザー エラー メッセージ。 |
| UserMessageIfServerError | いいえ | サーバーで内部エラーが発生した場合のユーザー エラー メッセージ。 |
| UserMessageIfThrottled| いいえ | 要求が調整された場合のユーザー エラー メッセージ。|

### <a name="example-send-an-sms"></a>例: SMS を送信する

次の例では、SMS でコードを送信するために使用される Azure AD MFA 技術プロファイルを示します。

```xml
<TechnicalProfile Id="AzureMfa-SendSms">
  <DisplayName>Send Sms</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">OneWaySMS</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CombinePhoneAndCountryCode" />
    <InputClaimsTransformation ReferenceId="ConvertStringToPhoneNumber" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="fullPhoneNumber" PartnerClaimType="phoneNumber" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>コードの確認

この技術プロファイルの 2 番目のモードでは、コードが検証されます。 このモードに対しては、次のオプションを構成できます。

### <a name="input-claims"></a>入力クレーム

**InputClaims** 要素には、Azure AD MFA に送信する要求の一覧が含まれます。 要求の名前を、MFA 技術プロファイルで定義されている名前にマップすることもできます。

| ClaimReferenceId | 必須 | 説明 |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| はい | 以前にコードを送信するために使用したのと同じ電話番号。 また、電話検証セッションを探すためにも使用されます。 |
| verificationCode  | はい | 確認のためにユーザーによって指定された確認コード |

**InputClaimsTransformations** 要素には、Azure AD MFA サービスの呼び出しの前に、入力要求の変更または新しい入力要求の生成に使用される、**InputClaimsTransformation** 要素のコレクションが含まれる場合があります。

### <a name="output-claims"></a>出力クレーム

Azure AD MFA プロトコル プロバイダーでは **OutputClaims** は返されないため、出力要求を指定する必要はありません。 ただし、`DefaultValue` 属性を設定している限り、Azure AD MFA ID プロバイダーによって返されない要求を含めることができます。

**OutputClaimsTransformations** 要素には、出力要求を修正したり新しい要求を生成するために使用される、**OutputClaimsTransformation** 要素のコレクションが含まれている場合があります。

### <a name="metadata"></a>Metadata

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| Operation | はい | **Verify** になっている必要があります |

#### <a name="ui-elements"></a>UI 要素

次のメタデータを使用して、コード確認に失敗したときに表示されるエラー メッセージを構成できます。 メタデータは、[セルフアサート](self-asserted-technical-profile.md)技術プロファイルで構成する必要があります。 エラー メッセージは、[ローカライズ](localization-string-ids.md#azure-ad-mfa-error-messages)できます。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| UserMessageIfMaxAllowedCodeRetryReached| いいえ | ユーザーによる確認コードの試行回数が多すぎる場合のユーザー エラー メッセージ。 |
| UserMessageIfServerError | いいえ | サーバーで内部エラーが発生した場合のユーザー エラー メッセージ。 |
| UserMessageIfThrottled| いいえ | 要求が調整されている場合のユーザー エラー メッセージ。|
| UserMessageIfWrongCodeEntered| いいえ| 検証のために入力されたコードが間違っている場合のユーザー エラー メッセージ。|

### <a name="example-verify-a-code"></a>例: コードを検証する

次の例では、コードの検証に使用される Azure AD MFA の技術プロファイルを示します。

```xml
<TechnicalProfile Id="AzureMfa-VerifySms">
    <DisplayName>Verify Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">Verify</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="phoneNumber" PartnerClaimType="phoneNumber" />
        <InputClaim ClaimTypeReferenceId="verificationCode" />
    </InputClaims>
</TechnicalProfile>
```
