---
title: Azure AD の Multi-Factor Authentication を有効にする
description: このチュートリアルでは、ユーザーのグループに対して Azure AD Multi-Factor Authentication を有効にし、サインイン イベント時に第 2 要素のプロンプトをテストする方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2e8bf2ccbf7a53563013c7ba653a6f6e8905337
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97881312"
---
# <a name="tutorial-secure-user-sign-in-events-with-azure-ad-multi-factor-authentication"></a>チュートリアル:Azure AD Multi-Factor Authentication を使用してユーザーのサインイン イベントのセキュリティを確保する

Multi-Factor Authentication (MFA) は、サインイン イベント中に追加で本人確認できるものをユーザーに求めるプロセスです。 その確認として、各自の携帯電話にコードを入力したり、指紋スキャンを行ったりする方法が考えられます。 2 つ目の認証形式を義務付ければ、その二次的な要素は攻撃者が容易に取得したり複製したりできるようなものではないため、セキュリティが向上します。

Azure AD Multi-Factor Authentication と条件付きアクセス ポリシーを使用すると、特定のサインイン イベント時にユーザーの MFA を有効にするための柔軟性が得られます。 [多要素認証を構成してテナントに適用する方法](https://www.youtube.com/watch?v=qNndxl7gqVM)に関するビデオをご覧ください (**推奨**)。

> [!IMPORTANT]
> このチュートリアルでは、Azure AD Multi-Factor Authentication を有効にする方法を管理者に示します。
>
> IT チームが Azure AD Multi-Factor Authentication を使用する機能を有効にしていない場合、またはサインイン時に問題が発生した場合は、ヘルプデスクに連絡して追加のサポートを依頼してください。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * ユーザーのグループに対して Azure AD Multi-Factor Authentication を有効にする条件付きアクセス ポリシーを作成する
> * MFA を求めるポリシーの条件を構成する
> * ユーザーとして MFA プロセスをテストする

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のリソースと特権が必要です。

* 少なくとも Azure AD Premium P1 または試用版ライセンスが有効になっている、動作している Azure AD テナント。
    * 必要に応じて、[無料で作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* "*グローバル管理者*" 特権を持つアカウント。
* パスワードがわかっている管理者以外のユーザー (*testuser* など)。 このチュートリアルでは、このアカウントを使用してエンドユーザーの Azure AD Multi-Factor Authentication エクスペリエンスをテストします。
    * ユーザーを作成する必要がある場合は、「[クイックスタート: Azure Active Directory に新しいユーザーを追加する](../fundamentals/add-users-azure-active-directory.md)」を参照してください。
* 管理者以外のユーザーが所属するグループ (*MFA-Test-Group* など)。 このチュートリアルでは、このグループに対して Azure AD Multi-Factor Authentication を有効にします。
    * グループを作成する必要がある場合は、[Azure Active Directory でグループを作成し、メンバーを追加する](../fundamentals/active-directory-groups-create-azure-portal.md)方法を参照してください。

## <a name="create-a-conditional-access-policy"></a>条件付きアクセス ポリシーを作成する

Azure AD Multi-Factor Authentication を有効にして使用する手段として、条件付きアクセス ポリシーを使った方法が推奨されます。 条件付きアクセスを使用すると、サインイン イベントに反応して二次的なアクションを要求したうえで、アプリケーションまたはサービスへのアクセスをユーザーに許可するポリシーを作成、定義することができます。

![条件付きアクセスによってサインイン プロセスにセキュリティを確保するしくみを示す概要図](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

条件付きアクセス ポリシーは、自分の組織を保護しながらも、時間や場所を問わず常にユーザーの生産性を高めることを目指し、粒度の細かい具体的な内容にすることができます。 このチュートリアルでは、ユーザーが Azure portal にサインインしたときに MFA を求める基本的な条件付きアクセス ポリシーを作成してみましょう。 このシリーズの後続のチュートリアルでは、リスクベースの条件付きアクセス ポリシーを使用して Azure AD Multi-Factor Authentication を構成します。

まず、次のとおり条件付きアクセス ポリシーを作成し、ユーザーのテスト グループを割り当てます。

1. "*グローバル管理者*" のアクセス許可を持つアカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. **Azure Active Directory** を検索して選択し、左側のメニューから **[セキュリティ]** を選択します。
1. **[条件付きアクセス]** を選択し、 **[+ 新しいポリシー]** を選択します。
1. ポリシーの名前を入力します (例: *MFA Pilot*)。
1. **[割り当て]** の **[ユーザーとグループ]** を選択し、 **[ユーザーとグループの選択]** をクリックします。
1. **[ユーザーとグループ]** のチェック ボックスをオンにし、 **[選択]** をクリックして、選択可能な Azure AD ユーザーとグループを参照します。
1. 使用する Azure AD グループ (*MFA-Test-Group* など) を参照して選択し、 **[選択]** を選択します。

    [ ![条件付きアクセス ポリシーで使用する Azure AD グループを選択する](media/tutorial-enable-azure-mfa/select-group-for-conditional-access-cropped.png) ](media/tutorial-enable-azure-mfa/select-group-for-conditional-access.png#lightbox)

1. グループに対して条件付きアクセス ポリシーを適用するには、 **[完了]** を選択します。

## <a name="configure-the-conditions-for-multi-factor-authentication"></a>多要素認証の条件を構成する

条件付きアクセス ポリシーを作成し、ユーザーのテスト グループを割り当てたら、ポリシーをトリガーするクラウド アプリまたはアクションを定義します。 これらのクラウド アプリまたはアクションは、追加の処理 (MFA を求めるなど) が必要なシナリオとして、ご自身で決めることになります。 たとえば、財務アプリケーションへのアクセスまたは管理ツールの使用には、追加認証プロンプトが必要という方針にすることが考えられます。

このチュートリアルでは、ユーザーが Azure portal にサインインするときに MFA を要求するよう条件付きアクセス ポリシーを構成します。

1. **[クラウド アプリまたは操作]** を選択します。 条件付きアクセス ポリシーの適用先として、 *[すべてのクラウド アプリ]* または *[アプリの選択]* を選択できます。 柔軟に、特定のアプリをポリシーから除外することもできます。

    このチュートリアルでは、 *[Include]\(対象\)* ページで **[アプリの選択]** をクリックします。

1. **[選択]** を選択してから、使用可能なサインイン イベントの一覧を参照します。

    このチュートリアルでは、Azure portal へのサインイン イベントにポリシーが適用されるよう **[Microsoft Azure Management]\(Microsoft Azure の管理\)** を選択します。

1. 選択したアプリを適用するために、 **[選択]** 、 **[完了]** の順に選択します。

    ![条件付きアクセス ポリシーの対象として [Microsoft Azure Management]\(Microsoft Azure の管理\) アプリを選択する](media/tutorial-enable-azure-mfa/select-azure-management-app.png)

アクセス制御を使用すると、アクセスの許可を得るためにユーザーが満たすべき要件を定義することができます (承認済みのクライアント アプリが必要、Hybrid Azure AD に参加済みのデバイスを使用、など)。 このチュートリアルでは、Azure portal へのサインイン イベント時に MFA を要求するようにアクセスの制御を構成します。

1. *[アクセス制御]* の **[許可]** を選択し、 **[アクセス権の付与]** が選択されていることを確認します。
1. **[多要素認証を要求する]** チェック ボックスをオンにし、 **[選択]** を選択します。

構成がユーザーに及ぼす影響を確認したい場合、条件付きアクセス ポリシーを "*レポート専用*" に設定することができます。ポリシーをすぐには使用しない場合は、"*オフ*" に設定することもできます。 このチュートリアルでは、ユーザーのテスト グループが対象になっているので、ポリシーを有効にしたうえで、Azure AD Multi-Factor Authentication をテストしましょう。

1. *[ポリシーを有効にする]* トグルを **[オン]** に設定します。
1. 条件付きアクセス ポリシーを適用するには、 **[作成]** を選択します。

## <a name="test-azure-ad-multi-factor-authentication"></a>Azure AD の Multi-Factor Authentication をテストする

条件付きアクセス ポリシーと Azure AD Multi-Factor Authentication が機能していることを確認しましょう。 まず、次のとおり MFA が求められないリソースにサインインします。

1. InPrivate またはシークレット モードで新しいブラウザー ウィンドウを開き、[https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com) に移動します。
1. 管理者ではないテスト ユーザー (*testuser* など) でサインインします。 MFA を求めるプロンプトは表示されません。
1. ブラウザー ウィンドウを閉じます。

今度は、Azure portal にサインインします。 Azure portal は、追加認証を要求するよう条件付きアクセス ポリシーで構成されているため、Azure AD Multi-Factor Authentication のプロンプトが表示されます。

1. InPrivate または incognito モードで新しいブラウザー ウィンドウを開き、[https://portal.azure.com](https://portal.azure.com) に移動します。
1. 管理者ではないテスト ユーザー (*testuser* など) でサインインします。 Azure AD Multi-Factor Authentication への登録とその使用を求められます。 プロンプトに従って手順を完了し、Azure portal にサインインできることを確認します。

    ![ブラウザーのプロンプトに従ったうえで、登録済みの多要素認証プロンプトでサインインする](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

1. ブラウザー ウィンドウを閉じます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルの中で構成した、Azure AD Multi-Factor Authentication を有効にする条件付きアクセス ポリシーを今後使用したくない場合は、次の手順に従ってポリシーを削除してください。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **Azure Active Directory** を検索して選択し、左側のメニューから **[セキュリティ]** を選択します。
1. **[条件付きアクセス]** を選択し、作成したポリシー (*MFA Pilot* など) を選択します。
1. **[削除]** を選択し、ポリシーの削除を確定します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、選択したユーザーのグループを対象に、条件付きアクセス ポリシーを使用して Azure AD Multi-Factor Authentication を有効にしました。 以下の方法を学習しました。

> [!div class="checklist"]
> * Azure AD ユーザーのグループに対して Azure AD Multi-Factor Authentication を有効にする条件付きアクセス ポリシーを作成する
> * MFA を求めるポリシーの条件を構成する
> * ユーザーとして MFA プロセスをテストする

> [!div class="nextstepaction"]
> [セルフサービス パスワード リセット (SSPR) のパスワード ライトバックを有効にする](./tutorial-enable-sspr-writeback.md)
