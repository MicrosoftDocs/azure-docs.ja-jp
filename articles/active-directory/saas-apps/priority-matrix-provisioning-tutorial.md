---
title: 'チュートリアル: Azure Active Directory での自動ユーザー プロビジョニング用に Priority Matrix を構成する | Microsoft Docs'
description: Priority Matrix に対してユーザー アカウントが自動的にプロビジョニングおよびプロビジョニング解除されるように、Azure Active Directory を構成する方法について説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/08/2019
ms.author: Zhchia
ms.openlocfilehash: e79f21300325c6b451dd564bf2c69830f003f55c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94357863"
---
# <a name="tutorial-configure-priority-matrix-for-automatic-user-provisioning"></a>チュートリアル: 自動ユーザー プロビジョニング用に Priority Matrix を構成する

このチュートリアルの目的は、ユーザーまたはグループの Priority Matrix へのプロビジョニングとプロビジョニング解除が自動的に行われるよう Azure AD を構成するために、Priority Matrix と Azure Active Directory (Azure AD) で実行する手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [Priority Matrix テナント](https://appfluence.com/pricing/)
* 管理者アクセス許可を持つ Priority Matrix上のユーザー アカウント。

## <a name="assign-users-to-priority-matrix"></a>Priority Matrix にユーザーを割り当てる

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Priority Matrix へのアクセスが必要な Azure AD 内のユーザーやグループを決定しておく必要があります。 決定した後、次の手順に従って、これらのユーザーやグループを Priority Matrix に割り当てることができます。

* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-priority-matrix"></a>Priority Matrix へのユーザーの割り当てに関する重要なヒント

* 1 人の Azure AD ユーザーを Priority Matrix に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Priority Matrix にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-priority-matrix-for-provisioning"></a>プロビジョニング用に Priority Matrix を設定する

Azure AD での自動ユーザー プロビジョニング用に Priority Matrix を構成する前に、Priority Matrix からプロビジョニング情報を取得する必要があります。

1. [Priority Matrix 管理コンソール](https://sync.appfluence.com/accounts/login/?next=/accounts/provisioning)にサインインします。

3. Priority Matrix の **[Oauth login token]\(OAuth ログイン トークン\)** をクリックします

    ![Priority Matrix での SCIM の追加](media/priority-matrix-provisioning-tutorial/oauthlogin.png)

4. **[GET NEW TOKEN]\(新しいトークンの取得\)** ボタンをクリックします。 **[Token String]\(トークン文字列\)** をコピーします。 この値を、Azure portal で Priority Matrix アプリケーションの [プロビジョニング] タブの **[シークレット トークン]** フィールドに入力します。 

    ![Priority Matrix でのトークンの作成](media/priority-matrix-provisioning-tutorial/token.png)

## <a name="add-priority-matrix-from-the-gallery"></a>ギャラリーから Priority Matrix を追加する

Azure AD で自動ユーザー プロビジョニング用に Priority Matrix を構成するには、Azure AD アプリケーション ギャラリーから管理対象 SaaS アプリケーションの一覧に Priority Matrix を追加する必要があります。

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Priority Matrix**」と入力し、結果パネルで **Priority Matrix** を選択します。 

    ![結果一覧の Priority Matrix](common/search-new-app.png)

5. **[Priority Matrix にサインアップ]** ボタンを選択します。Priority Matrix のログイン ページにリダイレクトされます。 

    ![Priority Matrix での OIDC の追加](media/priority-matrix-provisioning-tutorial/signup.png)

6. Priority Matrix は OpenIDConnect アプリであるため、Microsoft の職場アカウントを使用して Priority Matrix にログインすることを選択します。

    ![Priority Matrix での OIDC のログイン](media/priority-matrix-provisioning-tutorial/msftsignin.png)

7. 認証に成功した後、同意ページの同意プロンプトを受け入れます。 その後、ご使用のテナントにアプリケーションが自動的に追加され、Priority Matrix アカウントにリダイレクトされます。

    ![Priority Matrix での OIDC の同意](media/priority-matrix-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-priority-matrix"></a>Priority Matrix への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Priority Matrix のユーザーやグループを作成、更新、無効化する手順について説明します。

> [!NOTE]
> Priority Matrix の SCIM エンドポイントについて詳しくは、「[ユーザー プロビジョニングと Priority Matrix](https://appfluence.com/help/article/user-provisioning/)」をご覧ください。

### <a name="to-configure-automatic-user-provisioning-for-priority-matrix-in-azure-ad"></a>Azure AD で Priority Matrix の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Priority Matrix]** を選択します。

    ![アプリケーション一覧での [Priority Matrix] リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://sync.appfluence.com/scim/v2/`」と入力します。 前の手順で Priority Matrix から取得して保存した値を **[シークレット トークン]** に入力します。 **[接続テスト]** をクリックして、Azure AD から Priority Matrix に接続できることを確認します。 接続できない場合は、使用中の Priority Matrix アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Priority Matrix]\(Azure Active Directory ユーザーを Priority Matrix に同期する\)** を選択します。

    ![Priority Matrix のユーザー マッピング](media/priority-matrix-provisioning-tutorial/usermappings.png)

9. **[属性マッピング]** セクションで、Azure AD から Priority Matrix に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Priority Matrix のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Priority Matrix のユーザー属性](media/priority-matrix-provisioning-tutorial/userattributes.png)

10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

11. Priority Matrix に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

12. **[設定]** セクションの **[Scope]\(スコープ\)** で目的の値を選択して、Priority Matrix にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

13. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Priority Matrix に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)


