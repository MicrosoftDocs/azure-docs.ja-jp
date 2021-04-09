---
title: チュートリアル:GitHub のユーザー プロビジョニング - Azure AD
description: Azure Active Directory を構成して、ユーザー アカウントを GitHub に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: Zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: Zhchia
ms.openlocfilehash: f1600dfc5705ca97f16e8966a796b54fc556d216
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94359264"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>チュートリアル:GitHub を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Azure AD から GitHub にユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除するうえで GitHub と Azure AD で実行する必要がある手順について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* Azure Active Directory テナント
* [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) に作成された GitHub 組織 ([GitHub Enterprise 課金プラン](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)が必要)
* 組織への Admin アクセス許可がある GitHub のユーザー アカウント
* [GitHub Enterprise Cloud 組織用に構成された SAML](./github-tutorial.md)
* [こちら](https://help.github.com/en/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization)で説明しているように、組織に OAuth アクセスが提供されていることを確認します
* 1 つの組織に対する SCIM のプロビジョニングは、組織レベルで SSO が有効になっている場合のみサポートされます

> [!NOTE]
> Azure AD プロビジョニング統合では、[GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) のお客様が [GitHub Enterprise 課金プラン](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)で使用できる [GitHub SCIM API](https://developer.github.com/v3/scim/) が必要です。

## <a name="assigning-users-to-github"></a>GitHub へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー アカウント プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。 

プロビジョニング サービスを構成して有効にする前に、GitHub アプリへのアクセスが必要なユーザーを表す Azure AD 内のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順でこれらのユーザーを GitHub アプリに割り当てることができます。

[エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>ユーザーを GitHub に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを GitHub に割り当てて、プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* GitHub にユーザーを割り当てるときは、**ユーザー** ロールまたは別の有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選ぶ必要があります。 **[既定のアクセス]** ロールはプロビジョニングでは使うことができず、このロールのユーザーはスキップされます。

## <a name="configuring-user-provisioning-to-github"></a>GitHub へのユーザー プロビジョニングの構成

このセクションでは、Azure AD を GitHub のユーザー アカウント プロビジョニング API に接続する手順のほか、プロビジョニング サービスを構成して、Azure AD のユーザーとグループの割り当てに基づいて割り当て済みユーザーのアカウントを GitHub で作成、更新、無効化する手順を説明します。

### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Azure AD で GitHub への自動ユーザー アカウント プロビジョニングを構成する

1. [Azure Portal](https://portal.azure.com) で、 **[Azure Active Directory]、[エンタープライズ アプリ]、[すべてのアプリケーション]** セクションの順に移動します。

2. シングル サインオンのために GitHub を既に構成している場合は、検索フィールドで GitHub のインスタンスを検索します。 構成していない場合は、**[追加]** を選択してアプリケーション ギャラリーで **GitHub** を検索します。 検索結果から GitHub を選択してアプリケーションの一覧に追加します。

3. GitHub のインスタンスを選択してから、**[プロビジョニング]** タブを選択します。

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![GitHub のプロビジョニング](./media/github-provisioning-tutorial/GitHub1.png)

5. **[管理者資格情報]** セクションにある **[承認する]** をクリックします。 これで、ブラウザーの新しいウィンドウで GitHub 承認ダイアログが開きます。 アクセスの承認が承認されていることを確認する必要があることに注意してください。 [こちら](https://help.github.com/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization)に記載されている指示に従ってください。

6. 新しいウィンドウで、Admin アカウントを使用して GitHub にサインインします。 表示された承認ダイアログで、プロビジョニングを有効にしたい GitHub チームを選択して **[承認する]** を選択します。 終わったら Azure Portal に戻り、プロビジョニング構成を完了します。

    ![GitHub の [サインイン] ページを示すスクリーンショット。](./media/github-provisioning-tutorial/GitHub2.png)

7. Azure Portal で、**[テナント URL]** を入力し、**[テスト接続]** をクリックして、Azure AD が GitHub アプリに接続できることを確認します。 接続できない場合は、GitHub アカウントに Admin アクセス許可があり、**[テナント URL]** が正しく入力されていることを確認した後、"承認" ステップを再度試します (**テナント URL** はルール `https://api.github.com/scim/v2/organizations/<Organization_name>` で作成でき、組織は GitHub アカウントの **[Settings]\(設定\)** > **[Organizations]\(組織\)** でわかります)。

    ![GitHub の [組織] ページを示すスクリーンショット。](./media/github-provisioning-tutorial/GitHub3.png)

8. プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを **[通知用メール]** フィールドに入力して、[エラーが発生したときにメール通知を送信します] チェック ボックスをオンにします。

9. **[保存]** をクリックします。

10. [マッピング] セクションの **[Synchronize Azure Active Directory Users to GitHub]\(Azure Active Directory ユーザーを GitHub に同期する\)** を選びます。

11. **[属性マッピング]** セクションで、Azure AD から GitHub に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で GitHub のユーザー アカウントとの照合に使用されます。 [保存] ボタンをクリックして変更をコミットします。

12. GitHub に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

13. **[保存]** をクリックします。

これにより、[ユーザーとグループ] セクションで GitHub に割り当てたユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ ログを取得できます。このログには、プロビジョニング サービスによって実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)