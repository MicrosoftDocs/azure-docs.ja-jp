---
title: Azure AD B2C でパスワードの強制リセット フローを構成する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C でパスワードの強制リセット フローを設定する方法を説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 0477153b466702bec0fa2d5221fee1e054d06314
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033770"
---
# <a name="set-up-a-force-password-reset-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でパスワードの強制リセット フローを設定する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

管理者は、ユーザーが自分のパスワードを忘れた場合に、ユーザーの[パスワードをリセット](manage-users-portal.md#reset-a-users-password)できます。 または、パスワードを強制的にリセットさせたいと考えています。 この記事では、こうしたシナリオでパスワードを強制的にリセットする方法について説明します。

## <a name="overview"></a>概要

管理者が Azure portal を使用してユーザーのパスワードをリセットすると、[forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) 属性の値が `true` に設定されます。

[サインインとサインアップの過程](add-sign-up-and-sign-in-policy.md)で、この属性の値がチェックされます。 ユーザーがサインインを完了した後、属性が `true` に設定されている場合、ユーザーはパスワードをリセットする必要があります。 その後、この属性の値は `false` に戻ります。

![パスワードの強制リセット フロー](./media/force-password-reset/force-password-reset-flow.png)

パスワードのリセット フローは、パスワードが含まれる[電子メール アドレス](identity-provider-local.md#email-sign-in)や[ユーザー名](identity-provider-local.md#username-sign-in)を使ってサインインを行う Azure AD B2C のローカル アカウントに適用されます。

### <a name="force-a-password-reset-after-90-days"></a>90 日後にパスワードを強制的にリセットする

管理者は、[MS Graph](microsoft-graph-operations.md) を使用して、ユーザーのパスワードの有効期限を 90 日に設定できます。 90 日後に、[forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) 属性の値が自動的に `true` に設定されます。 ユーザーのパスワードの有効期限ポリシーを設定する方法の詳細については、「[パスワード ポリシー属性](user-profile-attributes.md#password-policy-attribute)」を参照してください。

パスワードの有効期限ポリシーが設定されている場合は、この記事の説明に従って、パスワードの強制リセット フローを構成する必要もあります。  

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="configure-your-policy"></a>ポリシーを構成する

::: zone pivot="b2c-user-flow"

サインアップまたはサインイン ユーザー フローで **パスワードの強制リセット** 設定を有効にするには、次の操作を行います。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[ユーザー フロー]** を選択します。
1. カスタマイズする ( **[推奨]** タイプの) サインアップおよびサインイン、またはサインイン ユーザー フロー を選択します。
1. 左側のメニューの **[設定]** で、 **[プロパティ]** を選択します。
1. **[パスワードの複雑さ]** で、 **[パスワードの強制リセット]** を選択します。
1. **[保存]** を選択します。

### <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. [Azure portal](https://portal.azure.com) にユーザー管理者またはパスワード管理者としてサインインします。 使用可能なロールについて詳しくは、[Azure Active Directory での管理者ロールの割り当て](../active-directory/roles/permissions-reference.md#all-roles)に関するページを参照してください。
1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[ユーザー]** を選択します。 パスワードのリセットをテストするために使用するユーザーを検索して選択した後、 **[パスワードのリセット]** を選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[ユーザー フロー]** を選択します。
1. テストする ( **[推奨]** タイプの) サインアップまたはサインイン ユーザー フローを選択します。
1. **[ユーザー フローを実行します]** を選択します。
1. **[アプリケーション]** で、以前に登録した *webapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** を選択します。
1. パスワードをリセットしたユーザー アカウントでサインインします。
1. ここで、ユーザーのパスワードを変更する必要があります。 パスワードを変更し、 **[続行]** を選択します。 トークンが `https://jwt.ms` に返され、表示されます。

::: zone-end

::: zone pivot="b2c-custom-policy"

1. [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/force-password-reset) でパスワード リセット強制のサンプルを取得します。
1. 各ファイル内で、文字列 `yourtenant` を、使用している Azure AD B2C テナントの名前に置き換えます。 たとえば、B2C テナントの名前が *contosob2c* であれば、`yourtenant.onmicrosoft.com` のすべてのインスタンスは `contosob2c.onmicrosoft.com` になります。
1. 最初に拡張機能ポリシー `TrustFrameworkExtensionsCustomForcePasswordReset.xml`、次に証明書利用者ポリシー `SignUpOrSigninCustomForcePasswordReset.xml` という順序でポリシー ファイルをアップロードします。

### <a name="test-the-policy"></a>ポリシーをテストする

1. [Azure portal](https://portal.azure.com) にユーザー管理者またはパスワード管理者としてサインインします。 使用可能なロールについて詳しくは、[Azure Active Directory での管理者ロールの割り当て](../active-directory/roles/permissions-reference.md#all-roles)に関するページを参照してください。
1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[ユーザー]** を選択します。 パスワードのリセットをテストするために使用するユーザーを検索して選択した後、 **[パスワードのリセット]** を選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[ポリシー]** で **[Identity Experience Framework]** を選択します。
1. `B2C_1A_signup_signin_Custom_ForcePasswordReset` ポリシーを選択してそれを開きます。 
1. **[アプリケーション]** には、[前に登録した](troubleshoot-custom-policies.md#troubleshoot-the-runtime) Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[今すぐ実行]** ボタンを選択します。
1. パスワードをリセットしたユーザー アカウントでサインインします。
1. ここで、ユーザーのパスワードを変更する必要があります。 パスワードを変更し、 **[続行]** を選択します。 トークンが `https://jwt.ms` に返され、表示されます。

::: zone-end

## <a name="next-steps"></a>次のステップ

[セルフサービス パスワード リセット](add-password-reset-policy.md)を設定します。
