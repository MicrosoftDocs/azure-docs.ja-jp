---
title: カスタム ポリシーで検証技術プロファイルを定義する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C のカスタム ポリシーで検証技術プロファイルを使用して要求を検証します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2d4c538a9292698fecc8b44c055ab201748e292c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85202995"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C のカスタム ポリシーで検証技術プロファイルを定義する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

検証技術プロファイルは、[Azure Active Directory](active-directory-technical-profile.md) や [REST API](restful-technical-profile.md) などの、あらゆるプロトコルの通常の技術プロファイルです。 検証技術プロファイルでは、出力要求を返すか、または次のデータによって 4xx HTTP 状態コードを返します。 詳細については、「[エラー メッセージを返す](restful-technical-profile.md#returning-validation-error-message)」を参照してください。

```json
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

検証技術プロファイルの出力要求の範囲は、検証技術プロファイルを呼び出す[セルフアサート技術プロファイル](self-asserted-technical-profile.md)と、その検証技術プロファイルに限定されます。 次のオーケストレーションの手順で出力要求を使用する場合は、検証技術プロファイルを呼び出すセルフアサート技術プロファイルに出力要求を追加します。

検証技術プロファイルは、**ValidationTechnicalProfiles** 要素内に出現する順序で実行されます。 検証技術プロファイルでは、検証技術プロファイルでエラーが発生したか、または成功した場合に、後続の検証技術プロファイルの実行を続行するかどうかを構成できます。

検証技術プロファイルは、**ValidationTechnicalProfile** 要素で定義されている前提条件に基づいて、条件付きで実行できます。 たとえば、特定の要求が存在するかどうか、要求が指定の値と等しいかどうかなどを確認できます。

セルフアサート技術プロファイルでは、その出力要求の一部またはすべてを検証するために使用する検証技術プロファイルを定義できます。 参照先の技術プロファイルのすべての入力要求は、参照元の検証技術プロファイルの出力要求に表示する必要があります。

> [!NOTE]
> セルフアサート技術プロファイルでのみ、検証技術プロファイルを使用できます。 セルフアサートではない技術プロファイルからの出力要求を検証する必要がある場合、ユーザー体験で追加のオーケストレーションを使用し、検証を受け持つ技術プロファイルに対応することを検討してください。

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

**ValidationTechnicalProfiles** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | 参照元の技術プロファイルの出力要求の一部またはすべてを検証するために使用する技術プロファイル。 |

**ValidationTechnicalProfile** 要素には、次の属性が含まれています。

| 属性 | 必須 | Description |
| --------- | -------- | ----------- |
| ReferenceId | はい | ポリシーまたは親ポリシーで既に定義されている技術プロファイルの識別子。 |
|ContinueOnError|いいえ| この検証技術プロファイルでエラーが発生した場合に後続の検証技術プロファイルの検証を続行するかどうかを示します。 有効な値: `true` または `false` (既定値。以降の検証プロファイルの処理が停止され、エラーが返されます)。 |
|ContinueOnSuccess | いいえ | この検証技術プロファイルが成功した場合に後続の検証プロファイルの検証を続行するかどうかを示します。 指定できる値: `true` または `false`。 既定値は `true` で、以降の検証プロファイルの処理が続行されることを意味します。 |

**ValidationTechnicalProfile** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| Preconditions | 0:1 | 検証技術プロファイルを実行するために満たす必要がある前提条件の一覧。 |

**Precondition** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| `Type` | はい | 前提条件に対して実行するチェックまたはクエリの種類。 指定した要求がユーザーの現在の要求セット内に存在する場合にアクションが実行されるようにするには、`ClaimsExist` を指定する必要があります。または、指定した要求が存在し、その値が指定値と等しい場合にアクションが実行されるようにするには、`ClaimEquals` を指定する必要があります。 |
| `ExecuteActionsIf` | はい | テストが true または false の場合に前提条件のアクションを実行するかどうかを示します。 |

**Precondition** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| 値 | 1:n | チェックで使用されるデータ。 このチェックの種類が `ClaimsExist` の場合、このフィールドではクエリ対象の ClaimTypeReferenceId が指定されます。 チェックの種類が `ClaimEquals` の場合、このフィールドではクエリ対象の ClaimTypeReferenceId が指定されます。 一方、別の値要素には、チェック対象の値が含まれています。|
| アクション | 1:1 | オーケストレーション手順内の前提条件チェックが true の場合に実行する必要があるアクション。 **Action** の値は `SkipThisValidationTechnicalProfile` に設定されます。 関連付けられている検証技術プロファイルを実行しないことを指定します。 |

### <a name="example"></a>例

次の例では、これらの検証技術プロファイルを使用します。

1. 最初の検証技術プロファイルは、ユーザーの資格情報を確認し、無効なユーザー名や不正なパスワードなどのエラーが発生した場合は続行されません。
2. その次の検証技術プロファイルは、userType 要求が存在しないか、または userType の値が `Partner` の場合は実行されません。 この検証技術プロファイルは、内部顧客データベースからのユーザー プロファイルの読み取りを試行し、使用不可な REST API サービスや内部エラーなどのエラーが発生した場合も続行されます。
3. 最後の検証技術プロファイルは、userType 要求が存在しないか、または userType の値が `Customer` の場合は実行されません。 この検証技術プロファイルは、内部パートナー データベースからのユーザー プロファイルの読み取りを試行し、使用不可な REST API サービスや内部エラーなどのエラーが発生した場合も続行されます。

```xml
<ValidationTechnicalProfiles>
  <ValidationTechnicalProfile ReferenceId="login-NonInteractive" ContinueOnError="false" />
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromCustomertsDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Partner</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromPartnersDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Customer</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
</ValidationTechnicalProfiles>
```
