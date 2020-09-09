---
title: カスタム ポリシーを使用してパスワードの複雑さを構成する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C でカスタム ポリシーを使用してパスワードの複雑さの要件を構成する方法。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4ab196e894fc53b1243ac363f9863d5c7d4e328f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85389005"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でカスタム ポリシーを使用してパスワードの複雑さを構成する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) では、アカウントの作成時にユーザーが指定するパスワードの複雑さの要件を構成することができます。 既定では、Azure AD B2C では**強力な**パスワードを使います。 この記事では、[カスタム ポリシー](custom-policy-overview.md)にパスワードの複雑さを構成する方法について説明します。 [ユーザー フロー](user-flow-password-complexity.md)でパスワードの複雑さを構成することもできます。

## <a name="prerequisites"></a>前提条件

[カスタム ポリシーの概要](custom-policy-get-started.md)に関するページの手順を完了します。 ローカル アカウントでのサインアップとサインインのために作業用カスタム ポリシーを持つ必要があります。


## <a name="add-the-elements"></a>要素を追加する

パスワードの複雑さを構成するには、[述語検証](predicates.md#predicatevalidations)への参照を使用して、`newPassword` と `reenterPassword` の[要求の種類](claimsschema.md)をオーバーライドします。 PredicateValidations 要素は、一連の述語をグループ化して、要求の種類に適用できるユーザー入力の検証を形成します。 お使いのポリシーの拡張ファイルを開きます。 たとえば、<em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** </em>です。

1. [BuildingBlocks](buildingblocks.md) 要素を検索します。 要素が存在しない場合は追加します。
1. [ClaimsSchema](claimsschema.md) 要素を見つけます。 要素が存在しない場合は追加します。
1. `newPassword` と `reenterPassword` の要求を **ClaimsSchema** 要素に追加します。

    ```xml
    <ClaimType Id="newPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    <ClaimType Id="reenterPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    ```

1. [述語](predicates.md)によって、要求の種類の値をチェックする基本的な検証が定義され、true または false が返されます。 検証は、指定されたメソッド要素と、そのメソッドに関連するパラメーターのセットを使用して行われます。 次の述語を **BuildingBlocks** 要素の `</ClaimsSchema>` 要素の直後に追加します。

    ```xml
    <Predicates>
      <Predicate Id="LengthRange" Method="IsLengthRange">
        <UserHelpText>The password must be between 6 and 64 characters.</UserHelpText>
        <Parameters>
          <Parameter Id="Minimum">6</Parameter>
          <Parameter Id="Maximum">64</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Lowercase" Method="IncludesCharacters">
        <UserHelpText>a lowercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">a-z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="IncludesCharacters">
        <UserHelpText>an uppercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">A-Z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="IncludesCharacters">
        <UserHelpText>a digit</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">0-9</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="IncludesCharacters">
        <UserHelpText>a symbol</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

1. 次の述語検証を **BuildingBlocks** 要素の `</Predicates>` 要素の直後に追加します。

    ```xml
    <PredicateValidations>
      <PredicateValidation Id="CustomPassword">
        <PredicateGroups>
          <PredicateGroup Id="LengthGroup">
            <PredicateReferences MatchAtLeast="1">
              <PredicateReference Id="LengthRange" />
            </PredicateReferences>
          </PredicateGroup>
          <PredicateGroup Id="CharacterClasses">
            <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
            <PredicateReferences MatchAtLeast="3">
              <PredicateReference Id="Lowercase" />
              <PredicateReference Id="Uppercase" />
              <PredicateReference Id="Number" />
              <PredicateReference Id="Symbol" />
            </PredicateReferences>
          </PredicateGroup>
        </PredicateGroups>
      </PredicateValidation>
    </PredicateValidations>
    ```

1. 次の技術プロファイルは、Azure Active Directory へのデータの読み取りと書き込みを行う [Active Directory 技術プロファイル](active-directory-technical-profile.md)です。 拡張ファイル内のこれらの技術プロファイルをオーバーライドします。 `PersistedClaims` を使用して、強力なパスワード ポリシーを無効にします。 **ClaimsProviders** 要素を見つけます。  次の要求プロバイダーを以下のように追加します。

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
          <PersistedClaims>
            <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
          </PersistedClaims>
        </TechnicalProfile>
        <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
          <PersistedClaims>
            <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
          </PersistedClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. ポリシー ファイルを保存します。

## <a name="test-your-policy"></a>ポリシーのテスト

### <a name="upload-the-files"></a>ファイルのアップロード

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
4. **[Identity Experience Framework]** を選択します。
5. [カスタム ポリシー] ページで、 **[ポリシーのアップロード]** をクリックします。
6. **[ポリシーが存在する場合は上書きする]** を選択し、*TrustFrameworkExtensions.xml* ファイルを検索して選択します。
7. **[アップロード]** をクリックします。

### <a name="run-the-policy"></a>ポリシーを実行する

1. サインアップまたはサインイン ポリシーを開きます。 たとえば、*B2C_1A_signup_signin* などです。
2. **[アプリケーション]** には、前に登録したアプリケーションを選択します。 トークンを表示するには、 **[応答 URL]** に `https://jwt.ms` が表示される必要があります。
3. **[今すぐ実行]** をクリックします。
4. **[今すぐサインアップ]** を選択し、メール アドレスを入力して、新しいパスワードを入力します。 パスワード制限に関するガイダンスが表示されます。 ユーザー情報の入力を終了して、 **[作成]** をクリックします。 返されたトークンの内容が表示されます。

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory B2C でカスタム ポリシーを使用してパスワードの変更を構成する](custom-policy-password-change.md)方法について学習します。
- IEF リファレンスの [Predicates](predicates.md) と [PredicateValidations](predicates.md#predicatevalidations) 要素について学習します。
