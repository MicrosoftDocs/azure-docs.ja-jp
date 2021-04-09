---
title: ワンタイム パスワード (OTP) の確認を有効にする
titleSuffix: Azure AD B2C
description: Azure AD B2C カスタム ポリシーを使用してワンタイム パスワード (OTP) シナリオを設定する方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/19/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 12b9639342e2e35b9229aa15bb9cfb4695427606
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97881193"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Azure AD B2C カスタム ポリシーでワンタイム パスワードの技術プロファイルを定義する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) では、ワンタイム パスワードの生成と確認の管理がサポートされています。 技術プロファイルを使用してコードを生成し、後でそのコードを確認します。

ワンタイム パスワード技術プロファイルでは、コードの確認中にエラー メッセージを返すこともできます。 **検証技術プロファイル** を使用して、ワンタイム パスワードとの統合を設計します。 検証技術プロファイルにより、コードを確認するためのワンタイム パスワード技術プロファイルを呼び出します。 検証技術プロファイルでは、ユーザー体験を続ける前に、ユーザーが入力したデータを検証します。 検証技術プロファイルにより、エラー メッセージがセルフアサート ページに表示されます。

## <a name="protocol"></a>Protocol

**Protocol** 要素の **Name** 属性は `Proprietary` に設定する必要があります。 **handler** 属性には、Azure AD B2C により使用される、プロトコル ハンドラー アセンブリの完全修飾名が含まれている必要があります。

```xml
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

ワンタイム パスワードの技術プロファイルの例を次に示します。

```xml
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>コードを生成する

この技術プロファイルの最初のモードでは、コードを生成します。 このモードで構成できるオプションを以下に示します。 生成されたコードと試行回数は、セッション内で追跡されます。 

### <a name="input-claims"></a>入力クレーム

**InputClaims** 要素には、ワンタイム パスワード プロトコル プロバイダーに送信する必要がある要求の一覧が含まれています。 要求の名前を以下に定義されている名前にマップすることもできます。

| ClaimReferenceId | 必須 | 説明 |
| --------- | -------- | ----------- |
| identifier | はい | 後でコードを確認する必要があるユーザーを識別する識別子。 一般に、電子メール アドレスや電話番号など、コードが配信される宛先の識別子として使用されます。 |

**InputClaimsTransformations** 要素には、ワンタイム パスワード プロトコル プロバイダーに送信する前に、入力要求を変更するため、または新しい要求を生成するために使用される、**InputClaimsTransformation** 要素のコレクションが含まれる場合があります。

### <a name="output-claims"></a>出力クレーム

**OutputClaims** 要素には、ワンタイム パスワード プロトコル プロバイダーによって生成される要求の一覧が含まれています。 要求の名前を以下に定義されている名前にマップすることもできます。

| ClaimReferenceId | 必須 | 説明 |
| --------- | -------- | ----------- |
| otpGenerated | はい | セッションが Azure AD B2C によって管理される、生成されたコード。 |

**OutputClaimsTransformations** 要素には、出力要求を修正したり新しい要求を生成するために使用される、**OutputClaimsTransformation** 要素のコレクションが含まれている場合があります。

### <a name="metadata"></a>Metadata

次の設定を使用して、コードの生成モードを構成できます。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| CodeExpirationInSeconds | いいえ | コードの有効期限までの時間 (秒)。 最小値: `60`、最大値: `1200`、既定値: `600`。 コード (`ReuseSameCode` を使用した同じコード、または新しいコード) が提供されるたびに、コードの有効期限が延長されます。 この時間は、再試行タイムアウトの設定にも使用されます (最大試行回数に達すると、この時間が経過するまで、ユーザーは新しいコードの取得を試行できなくなります)。 |
| CodeLength | いいえ | コードの長さ。 既定値は `6` です。 |
| CharacterSet | いいえ | 正規表現で使用するように書式設定された、コードの文字セット。 たとえば、「 `a-z0-9A-Z` 」のように入力します。 既定値は `0-9` です。 文字セットには、指定したセット内の少なくとも 10 個の異なる文字を含める必要があります。 |
| NumRetryAttempts | いいえ | コードが無効と見なされるまでの確認の試行回数。 既定値は `5` です。 |
| NumCodeGenerationAttempts | No | 識別子ごとの最大コード生成試行回数。 指定しない場合の既定値は 10 です。 |
| 操作 | はい | 実行する操作。 指定できる値: `GenerateCode`。 |
| ReuseSameCode | いいえ | 指定されたコードの有効期限が切れておらず、まだ有効である場合に、新しいコードを生成するのではなく、同じコードを指定する必要があるかどうか。 既定値は `false` です。  |



### <a name="example"></a>例

次の例の `TechnicalProfile` は、コードを生成するために使用されます。

```xml
<TechnicalProfile Id="GenerateCode">
  <DisplayName>Generate Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">GenerateCode</Item>
    <Item Key="CodeExpirationInSeconds">600</Item>
    <Item Key="CodeLength">6</Item>
    <Item Key="CharacterSet">0-9</Item>
    <Item Key="NumRetryAttempts">5</Item>
    <Item Key="NumCodeGenerationAttempts">15</Item>
    <Item Key="ReuseSameCode">false</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpGenerated" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>コードの確認

この技術プロファイルの 2 番目のモードでは、コードを確認します。 このモードで構成できるオプションを以下に示します。

### <a name="input-claims"></a>入力クレーム

**InputClaims** 要素には、ワンタイム パスワード プロトコル プロバイダーに送信する必要がある要求の一覧が含まれています。 要求の名前を以下に定義されている名前にマップすることもできます。

| ClaimReferenceId | 必須 | 説明 |
| --------- | -------- | ----------- |
| identifier | はい | 以前にコードを生成したユーザーを識別する識別子。 一般に、電子メール アドレスや電話番号など、コードが配信される宛先の識別子として使用されます。 |
| otpToVerify | はい | ユーザーによって指定された確認コード。 |

**InputClaimsTransformations** 要素には、ワンタイム パスワード プロトコル プロバイダーに送信する前に、入力要求を変更するため、または新しい要求を生成するために使用される、**InputClaimsTransformation** 要素のコレクションが含まれる場合があります。

### <a name="output-claims"></a>出力クレーム

このプロトコル プロバイダーのコード確認中に提供される出力要求はありません。

**OutputClaimsTransformations** 要素には、出力要求を修正したり新しい要求を生成するために使用される、**OutputClaimsTransformation** 要素のコレクションが含まれている場合があります。

### <a name="metadata"></a>Metadata

次の設定を使用して、コード確認モードを構成できます。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| Operation | はい | 実行する操作。 指定できる値: `VerifyCode`。 |


### <a name="ui-elements"></a>UI 要素

次のメタデータを使用して、コード確認に失敗したときに表示されるエラー メッセージを構成できます。 メタデータは、[セルフアサート](self-asserted-technical-profile.md)技術プロファイルで構成する必要があります。 エラー メッセージは、[ローカライズ](localization-string-ids.md#one-time-password-error-messages)できます。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| UserMessageIfSessionDoesNotExist | いいえ | コード確認セッションの有効期限が切れた場合にユーザーに表示するメッセージ。 コードの有効期限が切れているか、指定された識別子に対してコードが生成されたことがないかのいずれかです。 |
| UserMessageIfMaxRetryAttempted | いいえ | 許容される確認の最大試行回数を超えた場合に、ユーザーに表示するメッセージ。 |
| UserMessageIfMaxNumberOfCodeGenerated | いいえ | コード生成が許容される最大試行回数を超えた場合にユーザーに表示するメッセージ。 |
| UserMessageIfInvalidCode | いいえ | 無効なコードが指定された場合にユーザーに表示するメッセージ。 |
| UserMessageIfVerificationFailedRetryAllowed | いいえ | 無効なコードを指定した場合にユーザーに表示するメッセージ。ユーザーは正しいコードを指定できます。  |
|UserMessageIfSessionConflict|いいえ| コードを確認できない場合にユーザーに表示するメッセージ。|

### <a name="example"></a>例

次の例の `TechnicalProfile` は、コードの確認に使用されます。

```xml
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
    <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>次のステップ

カスタム メール確認でのワンタイム パスワードの技術プロファイルの使用例については、次の記事を参照してください。

- Azure Active Directory B2C でのカスタム電子メール検証 ([Mailjet](custom-email-mailjet.md)、[SendGrid](custom-email-sendgrid.md))

