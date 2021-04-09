---
title: サインアップおよびサインイン フローを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C でサインアップおよびサインイン フローを設定する方法を説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 29dd67e9e6e15aaafec0cc47d89da32cbf369938
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97618627"
---
# <a name="set-up-a-sign-up-and-sign-in-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でサインアップおよびサインイン フローを設定する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-up-and-sign-in-flow"></a>サインアップおよびサインイン フロー

サインアップおよびサインイン ポリシーによって、ユーザーは以下を実行できます。 

* ローカル アカウントでサインアップする
* ローカル アカウントでサインインする
* ソーシャル アカウントでサインアップまたはサインインする
* パスワードのリセット

![プロファイル編集フロー](./media/add-sign-up-and-sign-in-policy/add-sign-up-and-sign-in-flow.png)

## <a name="prerequisites"></a>[前提条件]

まだそうしていない場合は、[Azure Active Directory B2C に Web アプリケーションを登録](tutorial-register-applications.md)します。

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>サインアップとサインイン ユーザー フローを作成する

サインアップとサインイン ユーザー フローは、サインアップ エクスペリエンスとサインイン エクスペリエンスの両方を 1 つの構成で処理します。 アプリケーションのユーザーは、状況に応じて正しいパスに誘導されます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[ポリシー]** で、 **[ユーザー フロー]** を選択し、 **[新しいユーザー フロー]** を選択します。

    ![[新しいユーザー フロー] ボタンが強調表示されているポータル内の [ユーザー フロー] ページ](./media/add-sign-up-and-sign-in-policy/signup-signin-user-flow.png)

1. **[ユーザー フローを作成する]** ページで、 **[サインアップとサインイン]** ユーザー フローを選択します。

    ![サインアップとサインイン フローが強調表示されている [ユーザー フローの選択] ページ](./media/add-sign-up-and-sign-in-policy/select-user-flow-type.png)

1. **[バージョンの選択]** で **[Recommended]\(推奨\)** を選択して、 **[作成]** を選択します。 (ユーザー フローのバージョンに関する[詳細情報](user-flow-versions.md))。

    ![プロパティが強調表示されている、Azure portal 内の [ユーザー フローの作成] ページ](./media/add-sign-up-and-sign-in-policy/select-version.png)

1. ユーザー フローの **[名前]** を入力します。 たとえば、「*signupsignin1*」と入力します。
1. **[ID プロバイダー]** で、 **[電子メールのサインアップ]** を選択します。
1. **[ユーザー属性と要求]** で、サインアップ中にユーザーから収集して送信する要求と属性を選択します。 たとえば、 **[Show more]\(さらに表示\)** を選択し、 **[国/リージョン]** 、 **[表示名]** 、 **[郵便番号]** の属性と要求を選択します。 **[OK]** をクリックします。

    ![3 つの要求が選択されている [属性と要求の選択] ページ](./media/add-sign-up-and-sign-in-policy/signup-signin-attributes.png)

1. **[作成]** をクリックして、ユーザー フローを追加します。 *B2C_1* というプレフィックスが自動的に名前に追加されます。

### <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. 作成したユーザー フローを選択してその概要ページを開き、 **[ユーザー フローの実行]** を選択します。
1. **[アプリケーション]** で、以前に登録した *webapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** をクリックしてから、 **[今すぐサインアップ]** を選択します。

    ![[ユーザー フローを実行します] ボタンが強調表示されているポータル内の [ユーザー フローを実行します] ページ](./media/add-sign-up-and-sign-in-policy/signup-signin-run-now.PNG)

1. 有効なメール アドレスを入力し、 **[確認コードの送信]** をクリックします。受信した確認コードを入力し、 **[コードの確認]** を選択します。
1. 新しいパスワードを入力し、そのパスワードを確認します。
1. 国とリージョンを選択し、表示する名前を入力し、郵便番号を入力したら、 **[作成]** をクリックします。 トークンが `https://jwt.ms` に返され、表示されます。
1. これでユーザー フローをもう一度実行できるようになりました。作成したアカウントでサインインできる必要があります。 返されたトークンには、選択した国/リージョン、名前、および郵便番号の要求が含まれています。

> [!NOTE]
> [ユーザー フローを実行します] エクスペリエンスは、現在、認可コード フローを使用する、種類が SPA の応答 URL と互換性がありません。 これらの種類のアプリで [ユーザー フローを実行します] エクスペリエンスを使用するには、種類が "Web" の応答 URL を登録し、[こちら](tutorial-register-spa.md)の説明に従って暗黙的なフローを有効にします。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-sign-up-and-sign-in-policy"></a>サインアップおよびサインイン ポリシーの作成

カスタム ポリシーは、ユーザー体験を定義するために Azure AD B2C テナントにアップロードされる XML ファイルのセットです。 サインアップとサインイン、パスワードのリセット、プロファイル編集ポリシーなど、いくつかの事前に構築されたポリシーを含むスターター パックが用意されています。 詳細については、「[Azure AD B2C でのカスタム ポリシーの概要](custom-policy-get-started.md)」を参照してください。

::: zone-end

## <a name="next-steps"></a>次のステップ

* [ソーシャル ID プロバイダーを使用するサインイン](add-identity-provider.md)を追加します。
* [パスワードのリセット フロー](add-password-reset-policy.md)を設定します。
