---
title: 表示コントロール リファレンス
titleSuffix: Azure AD B2C
description: Azure AD B2C 表示コントロールのリファレンスです。 カスタム ポリシーで定義されているユーザー体験をカスタマイズするには、表示コントロールを使用します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 441a77823c77305e567e9e1436715bc51ca48c11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97387056"
---
# <a name="display-controls"></a>表示コントロール

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**表示コントロール** は、特別な機能を備え、Azure Active Directory B2C (Azure AD B2C) バックエンド サービスと作用するユーザー インターフェイス要素です。 これにより、ユーザーがバックエンドで[検証技術プロファイル](validation-technical-profile.md)を呼び出すページで操作を実行できるようになります。 ページに表示コントロールが表示され、[セルフアサート技術プロファイル](self-asserted-technical-profile.md)によって参照されます。

次の図は、プライマリとセカンダリの電子メール アドレスを検証する 2 つの表示コントロールを含むセルフアサート サインアップ ページを示しています。

![レンダリングされた表示コントロールの例](media/display-controls/display-control-email.png)

## <a name="prerequisites"></a>前提条件

 [セルフアサート技術プロファイル](self-asserted-technical-profile.md)の [Metadata](self-asserted-technical-profile.md#metadata) セクションで参照された [ContentDefinition](contentdefinitions.md) の `DataUri` をページ コントラクトのバージョン 2.0.0 以上に設定する必要があります。 次に例を示します。

```xml
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>表示コントロールの定義

**DisplayControl** 要素には、次の属性が含まれています。

| Attribute | 必須 | 説明 |
| --------- | -------- | ----------- |
| Id | はい | 表示コントロールに使用される識別子です。 これは[参照](#referencing-display-controls)できます。 |
| UserInterfaceControlType | はい | 表示コントロールの種類。 現在サポートされているのは [VerificationControl](display-control-verification.md) です。 |

**DisplayControl** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| InputClaims | 0:1 | **InputClaims** は、ユーザーから収集されるクレームの値を事前に設定するために使用されます。 詳細については、「[InputClaims](technicalprofiles.md#input-claims)」要素を参照してください。 |
| DisplayClaims | 0:1 | **DisplayClaims** は、ユーザーから収集されるクレームを表すために使用されます。 詳細については、「[DisplayClaim](technicalprofiles.md#displayclaim)」要素を参照してください。|
| OutputClaims | 0:1 | **OutputClaims** は、この **DisplayControl** のために一時的に保存されるクレームを表すために使用されます。 詳細については、「[OutputClaims](technicalprofiles.md#output-claims)」要素を参照してください。|
| アクション | 0:1 | **Actions** は、フロントエンドで発生しているユーザー アクションに対して呼び出す検証技術プロファイルを一覧表示するために使用されます。 |

### <a name="input-claims"></a>入力クレーム

表示コントロールでは、**InputClaims** 要素を使用して、ユーザーから収集するクレームの値をページ上に事前入力できます。 この表示コントロールを参照するセルフアサート技術プロファイルでは、どのような **InputClaimsTransformations** でも定義することができます。

次の例では、既に存在するアドレスを使用して検証するメール アドレスを事前に入力します。

```xml
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>クレーム表示

各種類の表示コントロールには、クレーム表示、[クレーム出力](#output-claims)、および実行される[アクション](#display-control-actions)の異なるセットが必要です。

[セルフアサート技術プロファイル](self-asserted-technical-profile.md#display-claims)で定義されている **クレーム表示** と同様に、このクレーム表示では、表示コントロール内でユーザーから収集されるクレームを表します。 参照される **ClaimType** 要素では、**UserInputType** 要素として、`TextBox` または `DropdownSingleSelect` など Azure AD B2C がサポートするユーザー入力タイプを指定する必要があります。 **Action** にクレーム表示の値が必要な場合は、**Required** 属性を `true` に設定して、ユーザーが特定のクレーム表示の値を指定しなければならないようにします。

特定の種類の表示コントロールには、特定のクレーム表示が必要です。 たとえば、種類が **VerificationControl** の表示コントロールには、**VerificationCode** が必要です。 属性 **ControlClaimType** を使用して、その必須のクレームに対して指定される DisplayClaim を指定します。 次に例を示します。

```xml
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>出力クレーム

表示コントロールの **クレーム出力** は、次のオーケストレーション ステップには送信されません。 これらは、現在の表示コントロール セッションに対してのみ一時的に保存されます。 これらの一時的なクレームは、同じ表示コントロールの異なるアクション間で共有できます。

クレーム出力を次のオーケストレーション ステップにバブルアップするには、この表示コントロールを参照する実際のセルフアサート技術プロファイルの **OutputClaims** を使用します。

### <a name="display-control-actions"></a>表示コントロールのアクション

表示コントロールの **アクション** は、ユーザーがクライアント側 (ブラウザー) で特定のアクションを実行したときに Azure AD B2C バックエンドで発生するプロシージャです。 たとえば、ユーザーがページのボタンを選択したときに実行する検証です。

アクションは、**検証技術プロファイル** の一覧を定義します。 これらは、表示コントロールのクレーム表示の一部またはすべてを検証するために使用されます。 検証技術プロファイルは、ユーザー入力を検証し、ユーザーにエラーを返す場合があります。 セルフアサート技術プロファイルで [検証技術プロファイル](validation-technical-profile.md)に使用される方法と同様に、**ContinueOnError**、**ContinueOnSuccess**、および **Preconditions** 表示コントロールのアクションで使用することができます。

#### <a name="actions"></a>アクション

**Actions** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| アクション | 1:n | 実行されるアクションの一覧。 |

#### <a name="action"></a>アクション

**Action** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| Id | はい | 操作の種類。 指定できる値: `SendCode` または `VerifyCode`。 `SendCode` 値を指定すると、ユーザーにコードが送信されます。 このアクションには、2 つの検証技術プロファイルが含まれている場合があります。1 つはコードを生成するためのもので、もう 1 つはそれを送信するためのものです。 `VerifyCode` 値を指定すると、ユーザーが入力テキストボックスに入力したコードが検証されます。 |

**Action** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| ValidationClaimsExchange | 1:1 | 参照元の技術プロファイルの要求表示の一部またはすべてを検証するために使用される技術プロファイルの識別子。 参照先の技術プロファイルのすべての要求入力は、参照元の技術プロファイルの要求表示に含まれている必要があります。 |

#### <a name="validationclaimsexchange"></a>ValidationClaimsExchange

**ValidationClaimsExchange** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | 参照元の技術プロファイルの要求表示の一部またはすべてを検証するために使用する技術プロファイル。 |

**ValidationTechnicalProfile** 要素には、次の属性が含まれています。

| 属性 | 必須 | Description |
| --------- | -------- | ----------- |
| ReferenceId | はい | ポリシーまたは親ポリシーで既に定義されている技術プロファイルの識別子。 |
|ContinueOnError|いいえ| この検証技術プロファイルでエラーが発生した場合に、後続の検証技術プロファイルの検証を続行するかどうかを示します。 有効な値: `true` または `false` (既定値。以降の検証プロファイルの処理が停止され、エラーが返されます)。 |
|ContinueOnSuccess | いいえ | この検証技術プロファイルが成功した場合に、後続の検証プロファイルの検証を続行するかどうかを示します。 指定できる値: `true` または `false`。 既定値は `true` で、以降の検証プロファイルの処理が続行されることを意味します。 |

**ValidationTechnicalProfile** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| Preconditions | 0:1 | 検証技術プロファイルを実行するために満たす必要がある前提条件の一覧。 |

**Precondition** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| `Type` | はい | 前提条件に対して実行するチェックまたはクエリの種類。 指定できる値: `ClaimsExist` または `ClaimEquals`。 `ClaimsExist` を指定すると、指定した要求がユーザーの現在の要求セットに存在する場合に、アクションを実行することが指定されます。 `ClaimEquals` を指定すると、指定した要求が存在し、その値が指定した値と等しい場合に、アクションを実行することが指定されます。 |
| `ExecuteActionsIf` | はい | テストが true または false の場合に前提条件のアクションを実行するかどうかを示します。 |

**Precondition** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| 値 | 1:n | チェックで使用されるデータ。 このチェックの種類が `ClaimsExist` の場合、このフィールドではクエリ対象の ClaimTypeReferenceId が指定されます。 チェックの種類が `ClaimEquals` の場合、このフィールドではクエリ対象の ClaimTypeReferenceId が指定されます。 別の値要素でチェックされる値を指定します。|
| アクション | 1:1 | オーケストレーション手順内の前提条件チェックが true の場合に実行する必要があるアクション。 **Action** の値が `SkipThisValidationTechnicalProfile` に設定されます。これは、関連付けられている検証技術プロファイルを実行しないことを指定します。 |

次の例では、[Azure AD SSPR 技術プロファイル](aad-sspr-technical-profile.md) を使用して、電子メール アドレスを送信および確認します。

```xml
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <InputClaims></InputClaims>
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims></OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendCode" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-VerifyCode" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```

次の例では、ユーザーが選択した前提条件付きの **mfaType** 要求に基づいてメールまたは SMS でコードを送信します。

```xml
<Action Id="SendCode">
  <ValidationClaimsExchange>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AzureMfa-SendSms">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>email</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendEmail">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>phone</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
  </ValidationClaimsExchange>
</Action>
```

## <a name="referencing-display-controls"></a>表示コントロールの参照

表示コントロールは、[セルフアサート技術プロファイル](self-asserted-technical-profile.md)の[クレーム表示](self-asserted-technical-profile.md#display-claims)で参照されます。

次に例を示します。

```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
  ...
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="PhoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
```

## <a name="next-steps"></a>次のステップ

表示コントロールの使用例については、次を参照してください。 

- [Mailjet を使用するカスタム メール確認](custom-email-mailjet.md)
- [SendGrid を使用するカスタム メール確認](custom-email-sendgrid.md)
