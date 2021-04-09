---
title: プロファイル編集フローを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C でプロファイル編集フローを設定する方法を説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a0f4f785feed022226ed533d378a8fa52080b9ac
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581900"
---
# <a name="set-up-a-profile-editing-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でプロファイル編集フローを設定する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="profile-editing-flow"></a>プロファイル編集フロー

プロファイル編集ポリシーを使用すると、ユーザーは、表示名、姓、名、市区町村などのプロファイル属性を管理できます。 プロファイル編集フローには、次の手順が含まれます。 

1. ローカルまたはソーシャル アカウントによるサインアップまたはサインイン。 セッションがまだアクティブである場合、Azure AD B2C によってユーザーが承認され、次の手順にスキップします。
1. Azure AD B2C によってディレクトリからユーザー プロファイルが読み取られ、ユーザーが属性を編集できるようになります。

![プロファイル編集フロー](./media/add-profile-editing-policy/profile-editing-flow.png)


## <a name="prerequisites"></a>[前提条件]

まだそうしていない場合は、[Azure Active Directory B2C に Web アプリケーションを登録](tutorial-register-applications.md)します。

::: zone pivot="b2c-user-flow"

## <a name="create-a-profile-editing-user-flow"></a>プロファイル編集ユーザー フローを作成する

アプリケーションでユーザーによるプロファイル編集を有効にする場合は、プロファイル編集ユーザー フローを使用します。

1. Azure AD B2C テナントの [概要] ページのメニューで、 **[ユーザー フロー]** を選択し、 **[新しいユーザー フロー]** を選択します。
1. **[ユーザー フローを作成する]** ページで、 **[プロファイル編集]** ユーザー フローを選択します。 
1. **[バージョンの選択]** で **[Recommended]\(推奨\)** を選択して、 **[作成]** を選択します。
1. ユーザー フローの **[名前]** を入力します。 たとえば、「*profileediting1*」と入力します。
1. **[ID プロバイダー]** で、 **[電子メールのサインイン]** を選択します。
1. **[ユーザー属性]** で、ユーザーが自分のプロファイルで編集できる属性を選択します。 たとえば、 **[Show more]\(さらに表示\)** を選択し、 **[表示名]** と **[役職]** の属性と要求を選択します。 **[OK]** をクリックします。
1. **[作成]** をクリックして、ユーザー フローを追加します。 *B2C_1* というプレフィックスが自動的に名前に追加されます。

### <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. 作成したユーザー フローを選択してその概要ページを開き、 **[ユーザー フローの実行]** を選択します。
1. **[アプリケーション]** で、以前に登録した *webapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** をクリックし、以前に作成したアカウントでサインインします。
1. これで、ユーザーの表示名と役職を変更できるようになりました。 **[Continue]** をクリックします。 トークンが `https://jwt.ms` に返され、表示されます。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-profile-editing-policy"></a>プロファイル編集ポリシーを作成する

カスタム ポリシーは、ユーザー体験を定義するために Azure AD B2C テナントにアップロードされる XML ファイルのセットです。 サインアップとサインイン、パスワードのリセット、プロファイル編集ポリシーなど、いくつかの事前に構築されたポリシーを含むスターター パックが用意されています。 詳細については、「[Azure AD B2C でのカスタム ポリシーの概要](custom-policy-get-started.md)」を参照してください。

::: zone-end

## <a name="next-steps"></a>次のステップ

* [ソーシャル ID プロバイダーを使用するサインイン](add-identity-provider.md)を追加します。