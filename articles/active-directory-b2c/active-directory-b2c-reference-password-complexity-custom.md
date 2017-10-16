---
title: "カスタム ポリシーでのパスワードの複雑さ - Azure AD B2C | Microsoft Docs"
description: "カスタム ポリシーでパスワードの複雑さの要件を構成する方法"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: 53ef86c4-1586-45dc-9952-dbbd62f68afc
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeeda
ms.openlocfilehash: 5be521a08ab70be891ddd5fcb374adc52d5c0705
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="configure-password-complexity-in-custom-policies"></a>カスタム ポリシーでパスワードの複雑さを構成する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、パスワードの複雑さのしくみと、Azure AD B2C カスタム ポリシーを使ってそれを有効にする方法についてを詳しく説明します。

## <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Azure AD B2C: パスワードの複雑さの要件を構成する

Azure Active Directory B2C (Azure AD B2C) では、アカウントの作成時にエンド ユーザーが指定するパスワードの複雑さの要件の変更がサポートされます。  既定では、Azure AD B2C では**強力な**パスワードを使います。  Azure AD B2C では、顧客が使用できるパスワードの複雑さを制御する構成オプションもサポートしています。  この記事では、カスタム ポリシーでパスワードの複雑さを構成する方法について説明します。  [組み込みポリシーでパスワードの複雑さを構成する](active-directory-b2c-reference-password-complexity.md)こともできます。

## <a name="prerequisites"></a>前提条件

[概要](active-directory-b2c-get-started-custom.md)に関するページに記載されたローカル アカウントのサインアップ/サインインを完了するように構成された Azure AD B2C テナント。

## <a name="how-to-configure-password-complexity-in-custom-policy"></a>カスタム ポリシーでパスワードの複雑さを構成する方法

カスタム ポリシーでパスワードの複雑さを構成するには、カスタム ポリシーの全体的な構造で `BuildingBlocks` の内部に `ClaimsSchema`、`Predicates`、`InputValidations` 要素を含める必要があります。

```XML
  <BuildingBlocks>
    <ClaimsSchema>...</ClaimsSchema>
    <Predicates>...</Predicates>
    <InputValidations>...</InputValidations>
  </BuildingBlocks>
```

これらの要素の目的は次のとおりです。

- 各 `Predicate` 要素は、true または false を返す基本的な文字列検証チェックを定義します。
- `InputValidations` 要素には、1 つ以上の `InputValidation` 要素が含まれます。  各 `InputValidation` は、一連の `Predicate` 要素を使って構成されます。 この要素では、ブール値の集計を実行できます (`and` や `or` と同様)。
- `ClaimsSchema` はどの要求が検証対象かを定義します。  その要求の検証に使う `InputValidation` ルールを定義します。

### <a name="defining-a-predicate-element"></a>述語要素を定義する

述語には、IsLengthRange と MatchesRegex の 2 つのメソッドの種類があります。 それぞれの例を見てみましょう。  まず、正規表現の照合に使われる MatchesRegex の例です。  この例では、数値を含む文字列を照合します。

```XML
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
```

次に、IsLengthRange の例を見てみましょう。  このメソッドは、最小と最大の文字列の長さを取得します。

```XML
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
```

チェックが失敗した場合にエンド ユーザーにエラー メッセージを提供するには `HelpText` 属性を使います。  この文字列は、[言語カスタマイズ機能](active-directory-b2c-reference-language-customization.md)を使ってローカライズすることができます。

### <a name="defining-an-inputvalidation-element"></a>InputValidation 要素を定義する

`InputValidation` は `PredicateReferences` の集計です。 `InputValidation` が成功するためには各 `PredicateReferences` が true である必要があります。  ただし、`PredicateReferences` 要素の内部では、`MatchAtLeast` と呼ばれる属性を使って、true を返す必要のある `PredicateReference` チェックの数を指定します。  必要に応じて、`HelpText` 属性を定義して、参照する `Predicate` 要素で定義されたエラー メッセージを上書きします。

```XML
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
```

### <a name="defining-a-claimsschema-element"></a>ClaimsSchema 要素を定義する

要求の種類 `newPassword` と `reenterPassword` は特別な要求と見なされるため、名前を変更しないでください。  UI は、これらの `ClaimType` 要素に基づいて、アカウントの作成時にユーザーが自分のパスワードを正しく再入力したことを検証します。  同じ `ClaimType` 要素を探すには、スターター パックで TrustFrameworkBase.xml を参照します。  この例の新しい点は、これらの要素を上書きして `InputValidationReference` を定義していることです。 この新しい要素の `ID` 属性は、定義した `InputValidation` 要素をポイントしています。

```XML
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
```

### <a name="putting-it-all-together"></a>まとめ

この例では、すべての要素を組み合わせて、機能するポリシーを作成する方法を示しています。  この例を使うには:

1. 前提条件の[概要](active-directory-b2c-get-started-custom.md)の手順に従って、TrustFrameworkBase.xml と TrustFrameworkExtensions.xml をダウンロード、構成、アップロードします
1. このセクションのコンテンツの例を使って SignUporSignIn.xml ファイルを作成します。
1. Azure AD B2C テナント名で `yourtenant` を置き換えて SignUporSignIn.xml を更新します。
1. 最後に SignUporSignIn.xml ポリシー ファイルをアップロードします。

この例には、PIN パスワードの検証と強力なパスワードの検証が含まれています。

- `PINpassword`を参照してください。 この `InputValidation` 要素は、任意の長さの PIN を検証します。  `ClaimType` の内部の `InputValidationReference` 要素では参照されていないため、現時点では使われません。 
- `PasswordValidation`を参照してください。 この `InputValidation` 要素は、パスワードが 8 ～ 16 文字で、数字、大文字、小文字、記号の 4 種類のうち 3 種が含まれていることを検証します。  `ClaimType` で参照されています。  そのため、このルールがこのポリシーで適用されています。

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="yourtenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin">
 <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <BuildingBlocks>
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
    <Predicates>
      <Predicate Id="Lowercase" Method="MatchesRegex" HelpText="a lowercase">
        <Parameters>
          <Parameter Id="RegularExpression">^[a-z]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="MatchesRegex" HelpText="an uppercase">
        <Parameters>
          <Parameter Id="RegularExpression">^[A-Z]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="MatchesRegex" HelpText="a number">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="MatchesRegex" HelpText="a symbol">
        <Parameters>
          <Parameter Id="RegularExpression">^[!@#$%^*()]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    <InputValidations>
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
      <InputValidation Id="PINpassword">
        <PredicateReferences Id="PINGroup">
          <PredicateReference Id="PIN" />
        </PredicateReferences>
      </InputValidation>
    </InputValidations>
  </BuildingBlocks>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```
