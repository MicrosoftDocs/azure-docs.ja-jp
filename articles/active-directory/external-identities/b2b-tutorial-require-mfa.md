---
title: チュートリアル - B2B の多要素認証 - Azure AD
description: このチュートリアルでは、Azure AD B2B を使用して外部ユーザーやパートナー組織とコラボレーションするときに多要素認証 (MFA) を要求する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f5f7f823d6c5f5860c2e78fe8cacdd1d788c581
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95974121"
---
# <a name="tutorial-enforce-multi-factor-authentication-for-b2b-guest-users"></a>チュートリアル: B2B ゲスト ユーザーに多要素認証を適用する

外部の B2B ゲスト ユーザーとコラボレーションするときは、多要素認証 (MFA) ポリシーを使用して貴社のアプリを保護することをお勧めします。 そうすると、外部ユーザーがリソースにアクセスするために必要なものがユーザー名とパスワードだけではなくなります。 Azure Active Directory (Azure AD) では、アクセスで MFA を要求する条件付きアクセス ポリシーを使用してこの目標を達成できます。 MFA ポリシーは、組織のメンバーに対して有効にするのと同じ方法で、テナント レベル、アプリ レベル、または個々のゲスト ユーザー レベルで適用できます。

例:

![会社のアプリにサインインしているゲスト ユーザーを示す図](media/tutorial-mfa/aad-b2b-mfa-example.png)

1.  会社 A の管理者または従業員は、アクセスで MFA を要求するように構成されたクラウドまたはオンプレミスのアプリケーションを使用するようにゲスト ユーザーを招待します。
2.  ゲスト ユーザーは、自分の職場、学校、またはソーシャルの ID を使用してサインインします。 
3.  ユーザーは、MFA チャレンジを完了するように求められます。 
4.  ユーザーは、会社 A で MFA を設定し、自分の MFA オプションを選択します。 ユーザーは、アプリケーションへのアクセスを許可されます。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * MFA をセットアップする前に、サインイン エクスペリエンスをテストします。
> * 環境内のクラウド アプリへのアクセスで MFA を要求する条件付きアクセス ポリシーを作成します。 このチュートリアルでは、Microsoft Azure Management アプリを使用してこのプロセスを説明します。
> * What If ツールを使用して MFA サインインをシミュレートします。
> * 条件付きアクセス ポリシーをテストします。
> * テスト ユーザーとポリシーをクリーンアップします。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルのシナリオを完了するための要件を次に示します。

 - **Azure AD Premium エディションへのアクセス**。条件付きアクセス ポリシー機能が含まれます。 MFA を適用するには、Azure AD 条件付きアクセス ポリシーを作成する必要があります。 パートナーが MFA 機能を持っているかどうかに関係なく、MFA ポリシーは常に自分の組織に適用されることに注意してください。 組織の MFA を設定する場合は、ゲスト ユーザーに対する十分な Azure AD Premium ライセンスがあるかどうかを確認する必要があります。 
 - **有効な外部電子メール アカウント**。ゲスト ユーザーとしてテナント ディレクトリに追加し、サインインするために使用できます。 ゲスト アカウントの作成方法がわからない場合は、[Azure portal での B2B ゲスト ユーザーの追加](add-users-administrator.md)に関する記事を参照してください。

## <a name="create-a-test-guest-user-in-azure-ad"></a>Azure AD にテスト用のゲスト ユーザーを作成する

1. [Azure Portal](https://portal.azure.com/) に Azure AD 管理者としてサインインします。
2. 左ウィンドウで、 **[Azure Active Directory]** を選択します。
3.  **[管理]** にある **[ユーザー]** を選択します。
4.  **[新しいゲスト ユーザー]** を選択します。

    ![[新しいゲスト ユーザー] オプションを選択する場所を示すスクリーンショット](media/tutorial-mfa/tutorial-mfa-user-3.png)

5.  **[ユーザー名]** に外部ユーザーの電子メール アドレスを入力します。 必要に応じて、ようこそメッセージを含めます。 

    ![ゲスト招待メッセージを入力する場所を示すスクリーンショット](media/tutorial-mfa/tutorial-mfa-user-4.png)

6.  **[招待]** を選択して、招待をゲスト ユーザーに自動的に送信します。 **ユーザーが正常に招待された** ことを示すメッセージが表示されます。 
7.  招待を送信すると、ユーザー アカウントがディレクトリにゲストとして自動的に追加されます。

## <a name="test-the-sign-in-experience-before-mfa-setup"></a>MFA を設定する前にサインイン エクスペリエンスをテストする
1.  テスト用のユーザー名とパスワードを使用して、[Azure portal](https://portal.azure.com/) にサインインします。
2.  サインイン資格情報だけを使用して Azure portal にアクセスできることに注意してください。 追加の認証は要求されません。
3.  サインアウトします。

## <a name="create-a-conditional-access-policy-that-requires-mfa"></a>MFA を要求する条件付きアクセス ポリシーを作成する
1.  [Azure portal](https://portal.azure.com/) にセキュリティ管理者または条件付きアクセス管理者としてサインインします。
2.  Azure Portal で、 **[Azure Active Directory]** を選びます。 
3.  **[Azure Active Directory]** ページの **[セキュリティ]** セクションで、 **[条件付きアクセス]** を選択します。
4.  **[条件付きアクセス]** ページで、上部のツール バーの **[新しいポリシー]** を選択します。
5.  **[新規]** ページの **[名前]** テキスト ボックスに、「**B2B ポータルにアクセスするには MFA が必要**」と入力します。
6.  **[割り当て]** セクションで、 **[ユーザーとグループ]** を選択します。
7.  **[ユーザーとグループ]** ページで、 **[ユーザーとグループの選択]** を選択し、 **[すべてのゲスト ユーザー (プレビュー)]** を選択します。

    ![すべてのゲスト ユーザーを選択する方法を示すスクリーンショット](media/tutorial-mfa/tutorial-mfa-policy-6.png)
9.  **[完了]** を選択します。
10. **[新規]** ページの **[割り当て]** セクションで、 **[クラウド アプリ]** を選択します。
11. **[クラウド アプリ]** ページで、 **[アプリを選択]** を選択し、 **[選択]** を選択します。

    ![[クラウド アプリ] ページと [選択] オプションを示すスクリーンショット](media/tutorial-mfa/tutorial-mfa-policy-10.png)

12. **[選択]** ページで **[Microsoft Azure Management]** を選択し、 **[選択]** を選択します。

    ![[Microsoft Azure Management] オプションが強調表示されているスクリーンショット。](media/tutorial-mfa/tutorial-mfa-policy-11.png)

13. **[クラウド アプリ]** ページで、 **[完了]** を選択します。
14. **[新規]** ページの **[アクセス制御]** セクションで、 **[付与]** を選択します。
15. **[付与]** ページで、 **[アクセスの許可]** を選択し、 **[多要素認証を要求する]** チェック ボックスをオンにし、 **[選択]** を選択します。

    ![[多要素認証を要求する] オプションを示すスクリーンショット](media/tutorial-mfa/tutorial-mfa-policy-13.png)

16. **[ポリシーの有効化]** で、 **[オン]** を選択します。

    ![[ポリシーの有効化] オプションが [オン] に設定された状態を示すスクリーンショット](media/tutorial-mfa/tutorial-mfa-policy-14.png)

17. **作成** を選択します。

## <a name="use-the-what-if-option-to-simulate-sign-in"></a>What If オプションを使用してサインインをシミュレートする

1.  **[条件付きアクセス - ポリシー]** ページで、 **[What If]** を選択します。 

    ![[条件付きアクセス - ポリシー] ページの [What-if] オプションを選択する場所を示すスクリーンショット。](media/tutorial-mfa/tutorial-mfa-whatif-1.png)

2.  **[ユーザー]** を選択し、テスト用のテスト ユーザーを選択し、 **[選択]** を選択します。

    ![ゲスト ユーザーが選択された状態を示すスクリーンショット](media/tutorial-mfa/tutorial-mfa-whatif-2.png)

3.  **[クラウド アプリ]** を選択します。
4.  **[クラウド アプリ]** ページで、 **[アプリを選択]** を選択し、 **[選択]** をクリックします。 アプリの一覧から **[Microsoft Azure の管理]** を選択し、 **[選択]** をクリックします。 

    ![[Microsoft Azure の管理] アプリが選択された状態を示すスクリーンショット](media/tutorial-mfa/tutorial-mfa-whatif-3.png)

5.  **[クラウド アプリ]** ページで、 **[完了]** を選択します。
6.  **[What If]** を選択し、 **[Policies that will apply]\(適用するポリシー\)** タブの **[評価結果]** の下に新しいポリシーが表示されることを確認します。

    ![[What if] オプションを選択する場所を示すスクリーンショット](media/tutorial-mfa/tutorial-mfa-whatif-4.png)

## <a name="test-your-conditional-access-policy"></a>条件付きアクセス ポリシーをテストする
1.  テスト用のユーザー名とパスワードを使用して、[Azure portal](https://portal.azure.com/) にサインインします。
2.  追加の認証方法を要求するメッセージが表示されます。 ポリシーが有効になるまで時間がかかる可能性があることに注意してください。

    !["詳細情報が必要" メッセージが表示された状態を示すスクリーンショット](media/tutorial-mfa/mfa-required.png)
 
3.  サインアウトします。

## <a name="clean-up-resources"></a>リソースをクリーンアップする
不要になったら、テスト ユーザーとテスト用の条件付きアクセス ポリシーを削除します。
1.  [Azure Portal](https://portal.azure.com/) に Azure AD 管理者としてサインインします。
2.  左ウィンドウで、 **[Azure Active Directory]** を選択します。
3.  **[管理]** にある **[ユーザー]** を選択します。
4.  テスト ユーザーを選択し、 **[ユーザーの削除]** を選択します。
5.  左ウィンドウで、 **[Azure Active Directory]** を選択します。
6.  **[セキュリティ]** で、 **[条件付きアクセス]** を選択します。
7.  **[ポリシー名]** の一覧で、テスト用のポリシーのコンテキスト メニュー (...) を選択し、 **[削除]** を選択します。 **[はい]** を選択して確定します。

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、ゲスト ユーザーがいずれかのクラウド アプリのサインインするときに MFA を使用することを要求する条件付きアクセス ポリシーを作成しました。 コラボレーションするためのゲスト ユーザーを追加する方法の詳細については、「[Azure Portal で Azure Active Directory B2B コラボレーション ユーザーを追加する](add-users-administrator.md)」を参照してください。
