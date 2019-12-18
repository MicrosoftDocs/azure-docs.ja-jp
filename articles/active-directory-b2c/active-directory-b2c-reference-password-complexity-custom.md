---
title: カスタム ポリシーを使用してパスワードの複雑さを構成する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C でカスタム ポリシーを使用してパスワードの複雑さの要件を構成する方法。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e8718a04f9d63897b2d2472dd0cdffb196c41435
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949792"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でカスタム ポリシーを使用してパスワードの複雑さを構成する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) では、アカウントの作成時にユーザーが指定するパスワードの複雑さの要件を構成することができます。 既定では、Azure AD B2C では**強力な**パスワードを使います。 この記事では、[カスタム ポリシー](active-directory-b2c-overview-custom.md)にパスワードの複雑さを構成する方法について説明します。 [ユーザー フロー](active-directory-b2c-reference-password-complexity.md)でパスワードの複雑さを構成することもできます。

## <a name="prerequisites"></a>前提条件

「[Active Directory B2C でのカスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)」にある手順を完了する。

## <a name="add-the-elements"></a>要素を追加する

1. スターター パックと一緒にダウンロードした *SignUpOrSignIn.xml* ファイルをコピーして、*SingUpOrSignInPasswordComplexity.xml* という名前を付けます。
2. *SingUpOrSignInPasswordComplexity.xml* ファイルを開き、**PolicyId** および **PublicPolicyUri** を新しいポリシー名に変更します。 たとえば、*B2C_1A_signup_signin_password_complexity* にします。
3. 識別子 `newPassword` と `reenterPassword` を含む次の **ClaimType** 要素を追加します。

    ```XML
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
    ```

4. [Predicates](predicates.md) には、`IsLengthRange` または `MatchesRegex` のメソッドの型があります。 `MatchesRegex` 型は、正規表現に一致させるために使用します。 `IsLengthRange` 型は、最小と最大の文字列の長さを受け取ります。 次の **Predicate** 要素が存在しない場合は、**BuildingBlocks** 要素に **Predicates** 要素を追加します。

    ```XML
    <Predicates>
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

5. 各 **InputValidation** 要素は、定義された **Predicate** 要素を使用して構成されます。 この要素により、`and` や `or` に似たブール値の集計を実行できます。 次の **InputValidation** 要素が存在しない場合は、**BuildingBlocks** 要素に **InputValidations** 要素を追加します。

    ```XML
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
    </InputValidations>
    ```

6. **PolicyProfile** 技術プロファイルに次の要素が含まれていることを確認します。

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn"/>
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
        </InputClaims>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="displayName"/>
          <OutputClaim ClaimTypeReferenceId="givenName"/>
          <OutputClaim ClaimTypeReferenceId="surname"/>
          <OutputClaim ClaimTypeReferenceId="email"/>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub"/>
      </TechnicalProfile>
    </RelyingParty>
    ```

7. ポリシー ファイルを保存します。

## <a name="test-your-policy"></a>ポリシーのテスト

Azure AD B2C でアプリケーションをテスト中に、Azure AD B2C トークンを `https://jwt.ms` に返して、その中の要求を見直すことができると便利なことがあります。

### <a name="upload-the-files"></a>ファイルのアップロード

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
4. **[Identity Experience Framework]** を選択します。
5. [カスタム ポリシー] ページで、 **[ポリシーのアップロード]** をクリックします。
6. **[ポリシーが存在する場合は上書きする]** を選択し、*SingUpOrSignInPasswordComplexity.xml* ファイルを検索して選択します。
7. **[アップロード]** をクリックします。

### <a name="run-the-policy"></a>ポリシーを実行する

1. 変更したポリシーを開きます。 たとえば、*B2C_1A_signup_signin_password_complexity* にします。
2. **[アプリケーション]** には、前に登録したアプリケーションを選択します。 トークンを表示するには、 **[応答 URL]** に `https://jwt.ms` が表示される必要があります。
3. **[今すぐ実行]** をクリックします。
4. **[今すぐサインアップ]** を選択し、メール アドレスを入力して、新しいパスワードを入力します。 パスワード制限に関するガイダンスが表示されます。 ユーザー情報の入力を終了して、 **[作成]** をクリックします。 返されたトークンの内容が表示されます。

## <a name="next-steps"></a>次の手順

- [Azure Active Directory B2C でカスタム ポリシーを使用してパスワードの変更を構成する](active-directory-b2c-reference-password-change-custom.md)方法について学習します。


