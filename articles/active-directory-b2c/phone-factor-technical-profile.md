---
title: カスタム ポリシーで電話ファクター技術プロファイルを定義する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C 内のカスタム ポリシーで電話ファクター技術プロファイルを定義する。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 322e4b78fbfb38f1822fb7a7cdcdbfcc0738b303
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91950399"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C カスタム ポリシーで電話ファクター技術プロファイルを定義する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) では、電話番号の登録および検証のサポートを提供しています。 この技術プロファイル:

- ユーザーとやり取りして、電話番号を検証または登録するためのユーザー インターフェイスを用意します。
- 電話番号を検証するために、電話とテキスト メッセージがサポートされています。
- 複数の電話番号をサポートしています。 ユーザーは、いずれかの電話番号を選択して確認できます。  
- ユーザーが新しい電話番号を指定したかどうかを示す要求を返します。 この要求を使用して、電話番号を Azure AD B2C ユーザー プロファイルに保存するかどうかを決定できます。  
- [コンテンツ定義](contentdefinitions.md)を使用して外観を制御します。

## <a name="protocol"></a>Protocol

**Protocol** 要素の **Name** 属性は `Proprietary` に設定する必要があります。 電話ファクターのために **ハンドラー** 属性は、Azure AD B2C で使用されるプロトコル ハンドラーのアセンブリの完全修飾名を含める必要があります。`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

次の例は、登録と検証のための電話ファクター技術プロファイルを示しています。

```xml
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims-transformations"></a>入力要求変換

InputClaimsTransformations 要素には、入力要求を変更したり新しいものを生成したりために使用される、入力要求変換のコレクションを含めることができます。 次の入力要求変換によって、後で入力要求コレクションで使用される `UserId` 要求が生成されます。

```xml
<InputClaimsTransformations>
  <InputClaimsTransformation ReferenceId="CreateUserIdForMFA" />
</InputClaimsTransformations>
```

## <a name="input-claims"></a>入力クレーム

InputClaims 要素には、次の要求が含まれている必要があります。 要求の名前を、電話ファクター技術プロファイルで定義されている名前にマップすることもできます。 

|  データ型| 必須 | 説明 |
| --------- | -------- | ----------- | 
| string| はい | ユーザーの一意の ID。 要求名または PartnerClaimType は `UserId` に設定されている必要があります。 この要求には個人を特定できる情報を含めないでください。|
| string| はい | 要求の種類の一覧。 各要求には、1 つの電話番号が含まれます。 どの入力要求にも電話番号が含まれていない場合、ユーザーは新しい電話番号を登録して検証するように求められます。 検証済みの電話番号が、出力要求として返されます。 入力要求のいずれかに電話番号が含まれている場合は、ユーザーにその検証を求めるメッセージが表示されます。 複数の入力要求に電話番号が含まれている場合、ユーザーは電話番号の 1 つを選択して検証するように求められます。 |

次の例は、複数の電話番号を使用した場合を示しています。 詳細については、[サンプル ポリシー](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa)を参照してください。

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

## <a name="output-claims"></a>出力クレーム

OutputClaims 要素には、電話ファクター技術プロファイルによって返された要求の一覧が含まれています。

|  データ型| 必須 | 説明 |
|  -------- | ----------- |----------- |
| boolean | はい | ユーザーによって新しい電話番号が入力されたかどうかを示します。 要求名または PartnerClaimType は `newPhoneNumberEntered` に設定されている必要があります|
| string| はい | 検証済みの電話番号。 要求名または PartnerClaimType は `Verified.OfficePhone` に設定されている必要があります。|

OutputClaimsTransformations 要素には、出力要求を修正したり、新しい要求を生成したりするために使用される、OutputClaimsTransformation 要素のコレクションが含まれている場合があります。

## <a name="cryptographic-keys"></a>暗号化キー

**CryptographicKeys** 要素は使用されません。


## <a name="metadata"></a>Metadata

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | はい | [コンテンツ定義](contentdefinitions.md)の識別子は、この技術プロファイルに関連付けられています。 |
| ManualPhoneNumberEntryAllowed| いいえ | ユーザーが手動で電話番号を入力できるようにするかどうかを指定します。 指定できる値: `true` または `false` (既定値)。|
| setting.authenticationMode | いいえ | 電話番号を検証するメソッド。 指定できる値: `sms`、`phone`、または `mixed` (既定値)。|
| setting.autodial| いいえ| 技術プロファイルで自動ダイヤルするか、SMS を自動送信するかを指定します。 指定できる値: `true` または `false` (既定値)。 自動ダイヤルには、`setting.authenticationMode` メタデータを `sms` または `phone` に設定する必要があります。 入力要求のコレクションには、1 つの電話番号が必要です。 |

### <a name="ui-elements"></a>UI 要素

電話ファクター認証ページのユーザー インターフェイス要素は、[ローカライズ](localization-string-ids.md#phone-factor-authentication-page-user-interface-elements)できます。

## <a name="next-steps"></a>次のステップ

- MFA スターター パックを使用して[ソーシャル アカウントとローカル アカウント](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa)を確認します。
