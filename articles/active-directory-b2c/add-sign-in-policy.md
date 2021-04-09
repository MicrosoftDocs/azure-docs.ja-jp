---
title: サインイン フローの設定
titleSuffix: Azure Active Directory B2C
description: Azure Active Directory B2C でサインイン フローを設定する方法を学習します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/04/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2310bd39c39036b6d6ac919517fa5539d7b70779
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581866"
---
# <a name="set-up-a-sign-in-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でサインイン フローを設定する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-in-flow-overview"></a>サインイン フローの概要

サインイン ポリシーによって、ユーザーは以下を実行できます。 

* ユーザーは Azure AD B2C ローカルアカウントでサインインできる
* ソーシャル アカウントでサインアップまたはサインインする
* パスワードのリセット
* ユーザーは Azure AD B2C ローカル アカウントにサインインできません。 アカウントを作成するために、管理者は [Azure portal](manage-users-portal.md#create-a-consumer-user)、または [MS Graph API](microsoft-graph-operations.md) を使用できます。

![プロファイル編集フロー](./media/add-sign-in-policy/sign-in-user-flow.png)

## <a name="prerequisites"></a>[前提条件]

まだそうしていない場合は、[Azure Active Directory B2C に Web アプリケーションを登録](tutorial-register-applications.md)します。

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-in-user-flow"></a>サインイン ユーザー フローを作成する

サインイン ポリシーを追加するには:

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[ポリシー]** で、 **[ユーザー フロー]** を選択し、 **[新しいユーザー フロー]** を選択します。
1. **[ユーザー フローを作成する]** ページで、 **[サインイン]** ユーザー フローを選択します。
1. **[バージョンの選択]** で **[Recommended]\(推奨\)** を選択して、 **[作成]** を選択します。 (ユーザー フローのバージョンに関する[詳細情報](user-flow-versions.md))。
1. ユーザー フローの **[名前]** を入力します。 たとえば、「*signupsignin1*」と入力します。
1. **[ID プロバイダー]** で、 **[電子メールのサインイン]** を選択します。
1. **[アプリケーション要求]** には、アプリケーションに返信する要求と属性を選択します。 たとえば、 **[Show more]\(さらに表示\)** を選択し、 **[表示名]** 、 **[名]** 、 **[姓]** 、 **[ユーザーのオブジェクト ID]** の属性と要求を選択します。 **[OK]** をクリックします。
1. **[作成]** をクリックして、ユーザー フローを追加します。 *B2C_1* というプレフィックスが自動的に名前に追加されます。

### <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. 作成したユーザー フローを選択してその概要ページを開き、 **[ユーザー フローの実行]** を選択します。
1. **[アプリケーション]** で、以前に登録した *webapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** をクリックします。
1. サインアップ リンクなしで、(MS Graph API を使用して) 作成したアカウントでサインインできるはずです。 返されたトークンには、選択した要求が含まれています。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="remove-the-sign-up-link"></a>サインアップ リンクを削除する

**SelfAsserted-LocalAccountSignin-Email** 技術プロファイルは [セルフアサート](self-asserted-technical-profile.md)であり、サインアップまたはサインイン フローの間に呼び出されます。 サインアップ リンクを削除するには、`setting.showSignupLink` メタデータを `false` に設定します。 拡張ファイルで SelfAsserted-LocalAccountSignin-Email 技術プロファイルを上書きします。 

1. お使いのポリシーの拡張ファイルを開きます。 たとえば、_`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`**_ です。
1. `ClaimsProviders` 要素を見つけます。 要素が存在しない場合は追加します。
1. 次のクレーム プロバイダーを `ClaimsProviders` 要素に追加します。

    ```xml
    <!--
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Local Account</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
            <Metadata>
              <Item Key="setting.showSignupLink">false</Item>
            </Metadata>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!--
    </ClaimsProviders> -->
    ```

1. `<BuildingBlocks>` 要素内で、1.2.0 以降のデータ URI を参照するように次の [ContentDefinition](contentdefinitions.md) を追加します。

    ```XML
    <!-- 
    <BuildingBlocks> 
      <ContentDefinitions>-->
        <ContentDefinition Id="api.localaccountsignup">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
        </ContentDefinition>
      <!--
      </ContentDefinitions>
    </BuildingBlocks> -->
    ```

## <a name="update-and-test-your-policy"></a>ポリシーを更新してテストする

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご利用の Azure AD テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターをクリックして、ご利用の Azure AD テナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択し、 **[アプリの登録]** を検索して選択します。
1. **[Identity Experience Framework]** を選択します。
1. **[カスタム ポリシーのアップロード]** を選択し、変更したポリシー ファイルである *TrustFrameworkExtensions.xml* をアップロードします。
1. アップロードしたサインイン ポリシーを選択し、 **[今すぐ実行]** ボタンをクリックします。
1. サインアップ リンクなしで、(MS Graph API を使用して) 作成したアカウントでサインインできるはずです。

::: zone-end

## <a name="next-steps"></a>次のステップ

* [ソーシャル ID プロバイダーを使用するサインイン](add-identity-provider.md)を追加します。
* [パスワードのリセット フロー](add-password-reset-policy.md)を設定します。
