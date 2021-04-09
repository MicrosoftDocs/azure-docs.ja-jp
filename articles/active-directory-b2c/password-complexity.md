---
title: パスワードの複雑さの要件を構成する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C でコンシューマーによって指定されるパスワードの複雑さの要件を構成する方法。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/12/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 81c6e58e34f30d5736c40c77a308321dee28ae34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103224267"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でパスワードの複雑さの要件を構成する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) では、アカウントの作成時にエンド ユーザーが指定するパスワードの複雑さの要件の変更がサポートされます。 既定では、Azure AD B2C では **強力な** パスワードを使います。 Azure AD B2C では、顧客が使用できるパスワードの複雑さを制御する構成オプションもサポートしています。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="password-rule-enforcement"></a>パスワード ルールの適用

サインアップ時またはパスワードのリセット時に、エンド ユーザーは、複雑さのルールを満たすパスワードを指定する必要があります。 パスワードの複雑さのルールはユーザー フローごとに適用されます。 あるユーザー フローがサインアップ時に 4 桁の PIN を要求し、別のユーザー フローがサインアップ時に 8 文字の文字列を要求することができます。 たとえば、大人と子供とで異なるパスワードの複雑さを持つユーザー フローを使用できます。

パスワードの複雑さは、サインイン時には適用されません。 ユーザーは、現在の複雑さの要件を満たしていないためにサインイン時にパスワードの変更を求められることはありません。

パスワードの複雑さは次の種類のユーザー フローで構成できます。

- サインアップまたはサインインのユーザー フロー
- パスワード リセットのユーザー フロー

カスタム ポリシーを使用している場合、([カスタム ポリシーでパスワードの複雑さを構成する](password-complexity.md)) ことができます。

## <a name="configure-password-complexity"></a>パスワードの複雑さの構成

1. [Azure portal](https://portal.azure.com) にサインインします。
2. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
3. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
4. **[ユーザー フロー]** を選択します。
2. ユーザー フローを選択し、 **[プロパティ]** をクリックします。
3. **[パスワードの複雑さ]** で、このユーザー フローのパスワードの複雑さを **[シンプル]** 、 **[強]** 、 **[カスタム]** のいずれかに変更します。

### <a name="comparison-chart"></a>比較チャート

| 複雑さ | 説明 |
| --- | --- |
| シンプル | 少なくとも 8 ～ 64 文字のパスワード。 |
| Strong | 少なくとも 8 ～ 64 文字のパスワード。 小文字、大文字、数字、記号の 4 種類のうち 3 種が必要です。 |
| Custom | このオプションでは、パスワードの複雑さのルールを最も細かく制御できます。  カスタムの長さを構成できます。  数字のみのパスワード (PIN) を受け入れることもできます。 |

## <a name="custom-options"></a>カスタム オプション

### <a name="character-set"></a>文字セット

数字のみ (PIN) または完全な文字セットを受け入れることができます。

- **[数字のみ]** では、パスワードを入力するときに数字 (0 ～ 9) のみを使用できます。
- **[すべて]** では、任意の文字、数字、記号を使用できます。

### <a name="length"></a>長さ

パスワードの長さの要件を制御できます。

- **[最短]** は、少なくとも 4 にする必要があります。
- **[最大長]** は最小の長さ以上で、最大 256 文字にすることができます。

### <a name="character-classes"></a>文字クラス

パスワードで使われる異なる文字の種類を制御できます。

- **[2 of 4:Lowercase character, Uppercase character, Number (0-9), Symbol]\(3/4: 小文字、大文字、数字 (0 ～ 9)、記号\)** では、パスワードに少なくとも 3 種類の文字が含まれることを保証します。 たとえば、数値と小文字です。
- **3 of 4:Lowercase character, Uppercase character, Number (0-9), Symbol \(次の 4 つの文字クラスのうち 3 つ - 大文字、小文字、数字 (0 から 9)、記号\)** では、パスワードに少なくとも 3 種類の文字が含まれることを保証します。 たとえば、数値、小文字、大文字です。
- **4 of 4:Lowercase character, Uppercase character, Number (0-9), Symbol\(4/4: 小文字、大文字、数字 (0 ～ 9)、記号\)** では、パスワードにすべての文字種が含まれることを保証します。

    > [!NOTE]
    > **4/4** を要求すると、エンドユーザーが不満を感じます。 いくつかの調査では、この要件にによってパスワードのエントロピは向上しないことが示されています。 [NIST パスワード ガイドライン](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)をご覧ください

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="password-predicate-validation"></a>パスワードの述語の検証

パスワードの複雑さを構成するには、[述語検証](predicates.md#predicatevalidations)への参照を使用して、`newPassword` と `reenterPassword` の[要求の種類](claimsschema.md)をオーバーライドします。 PredicateValidations 要素は、一連の述語をグループ化して、要求の種類に適用できるユーザー入力の検証を形成します。 お使いのポリシーの拡張ファイルを開きます。 たとえば、<em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>です。

1. [BuildingBlocks](buildingblocks.md) 要素を検索します。 要素が存在しない場合は追加します。
1. [ClaimsSchema](claimsschema.md) 要素を見つけます。 要素が存在しない場合は追加します。
1. `newPassword` と `reenterPassword` の要求を **ClaimsSchema** 要素に追加します。

    ```xml
    <!-- 
    <BuildingBlocks>
      <ClaimsSchema> -->
        <ClaimType Id="newPassword">
          <PredicateValidationReference Id="CustomPassword" />
        </ClaimType>
        <ClaimType Id="reenterPassword">
          <PredicateValidationReference Id="CustomPassword" />
        </ClaimType>
      <!-- 
      </ClaimsSchema>
    </BuildingBlocks>-->
    ```

1. [述語](predicates.md)によって、要求の種類の値をチェックする基本的な検証が定義され、true または false が返されます。 検証は、指定されたメソッド要素と、そのメソッドに関連するパラメーターのセットを使用して行われます。 次の述語を **BuildingBlocks** 要素の `</ClaimsSchema>` 要素の直後に追加します。

    ```xml
    <!-- 
    <BuildingBlocks>-->
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
    <!-- 
    </BuildingBlocks>-->
    ```

1. 次の述語検証を **BuildingBlocks** 要素の `</Predicates>` 要素の直後に追加します。

    ```xml
    <!-- 
    <BuildingBlocks>-->
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
    <!-- 
    </BuildingBlocks>-->
    ```

## <a name="disable-strong-password"></a>強力なパスワードを無効にする 

次の技術プロファイルは、Azure Active Directory へのデータの読み取りと書き込みを行う [Active Directory 技術プロファイル](active-directory-technical-profile.md)です。 拡張ファイル内のこれらの技術プロファイルをオーバーライドします。 `PersistedClaims` を使用して、強力なパスワード ポリシーを無効にします。 **ClaimsProviders** 要素を見つけます。  次の要求プロバイダーを以下のように追加します。

```xml
<!-- 
<ClaimsProviders>-->
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
<!-- 
</ClaimsProviders>-->
```

[ユーザー名ベースのサインイン](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin) ポリシーを使用する場合は、`AAD-UserWriteUsingLogonEmail`、`AAD-UserWritePasswordUsingObjectId`、および `LocalAccountWritePasswordUsingObjectId` 技術プロファイルを *DisableStorongPassword* ポリシーを使用して更新します。

ポリシー ファイルを保存します。

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
4. **[今すぐサインアップ]** を選択し、メール アドレスを入力して、新しいパスワードを入力します。 パスワード制限に関するガイダンスが表示されます。 ユーザー情報の入力を終了して、**[作成]** をクリックします。 返されたトークンの内容が表示されます。

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory B2C でパスワードの変更を構成する](add-password-change-policy.md)方法を確認します。
- IEF リファレンスの [Predicates](predicates.md) と [PredicateValidations](predicates.md#predicatevalidations) 要素について学習します。


::: zone-end
