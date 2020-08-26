---
title: チュートリアル:Netskope Administrator Console を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Netskope Administrator Console に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 11/07/2019
ms.author: Zhchia
ms.openlocfilehash: cef356619d437d53d94c8f5d3be67cf6ac95501c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554555"
---
# <a name="tutorial-configure-netskope-administrator-console-for-automatic-user-provisioning"></a>チュートリアル: Netskope Administrator Console を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure Active Directory (Azure AD) が自動的にユーザーまたはグループを Netskope Administrator Console に対してプロビジョニングおよびプロビジョニング解除するように構成するために、Netskope Administrator Console と Azure AD で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [Netskope Administrator Console テナント](https://www.netskope.com/)
* Admin アクセス許可がある Netskope Administrator Console のユーザー アカウント。

## <a name="assigning-users-to-netskope-administrator-console"></a>Netskope Administrator Console へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Netskope Administrator Console へのアクセスが必要な Azure AD 内のユーザーやグループを決定しておく必要があります。 決定したら、次の手順に従って、これらのユーザーやグループを Netskope Administrator Console に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-administrator-console"></a>ユーザーを Netskope Administrator Console に割り当てるときの重要なヒント

* 単一の Azure AD ユーザーを Netskope Administrator Console に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Netskope Administrator Console にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-netskope-administrator-console-for-provisioning"></a>プロビジョニングのために Netskope Administrator Console を設定する

1. [Netskope Administrator Console の管理コンソール](https://netskope.goskope.com/)にサインインします。 **[Home]\(ホーム\) > [Settings]\(設定\)** に移動します。

    ![Netskope Administrator Console の管理コンソール](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  **[Tools]\(ツール\)** に移動します。 **[Tools]\(ツール\)** メニューで、 **[Directory Tools]\(ディレクトリ ツール\) > [SCIM INTEGRATION]\(SCIM 統合\)** に移動します。

    ![Netskope Administrator Console のツール](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Netskope Administrator Console での SCIM の追加](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. 下にスクロールし、 **[Add Token]\(トークンの追加\)** ボタンをクリックします。 **[Add OAuth Client Name]\(OAuth クライアント名の追加\)** ダイアログボックスで、 **[CLIENT NAME]\(クライアント名\)** を指定し、 **[Save]\(保存\)** ボタンをクリックします。

    ![Netskope Administrator Console でのトークンの追加](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![Netskope Administrator Console のクライアント名](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  **[SCIM Server URL]\(SCIM サーバー URL\)** と **[TOKEN]\(トークン\)** をコピーします。 これらの値を、Azure portal の Netskope Administrator Console アプリケーションの [プロビジョニング] タブで、[テナント URL] および [シークレット トークン] フィールドにそれぞれ入力します。

    ![Netskope Administrator Console でのトークンの作成](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-administrator-console-from-the-gallery"></a>ギャラリーから Netskope Administrator Console を追加する

Azure AD での自動ユーザー プロビジョニング用に Netskope Administrator Console を構成する前に、Azure AD アプリケーション ギャラリーから Netskope Administrator Console をマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Netskope Administrator Console を追加するには、次の手順を行います。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Netskope Administrator Console**」と入力し、結果ウィンドウで **[Netskope Administrator Console]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果の一覧の Netskope Administrator Console](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-administrator-console"></a>自動ユーザー プロビジョニングを Netskope Administrator Console に構成する 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Netskope Administrator Console のユーザーやグループを作成、更新、無効化する手順について説明します。

> [!TIP]
> Netskope Administrator Console では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Netskope Administrator Console シングル サインオンのチュートリアル](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

> [!NOTE]
> Netskope Administrator Console の SCIM エンドポイントの詳細については、[こちら](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon)を参照してください。

### <a name="to-configure-automatic-user-provisioning-for-netskope-administrator-console-in-azure-ad"></a>Azure AD で Netskope Administrator Console の自動ユーザー プロビジョニングを構成するには、次の操作を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Netskope Administrator Console]** を選択します。

    ![アプリケーションの一覧の Netskope Administrator Console リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションで、以前に取得した **SCIM Server URL** の値を **[テナント URL]** に入力します。 先ほど取得した**トークン**の値を **[シークレット トークン]** に、入力します。 **[テスト接続]** をクリックして、Azure AD から Netskope Administrator Console への接続を確保します。 接続できない場合は、使用中の Netskope Administrator Console アカウントに管理者アクセス許可があることを確認してから、再試行します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Users to Netskope Administrator Console]\(Azure Active Directory ユーザーを Netskope Administrator Console に同期する\)** を選択します。

    ![Netskope Administrator Console でのユーザーのマッピング](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. **[属性マッピング]** セクションで、Azure AD から Netskope Administrator Console に同期されるユーザー属性を確認します。 **一致する**プロパティとして選択されている属性は、更新処理で Netskope Administrator Console のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Netskope Administrator Console でのユーザー属性](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Groups to Netskope Administrator Console]\(Azure Active Directory グループを Netskope Administrator Console に同期する\)** を選択します。

    ![Netskope Administrator Console でのグループのマッピング](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. **[属性マッピング]** セクションで、Azure AD から Netskope Administrator Console に同期されるグループ属性を確認します。 **一致する**プロパティとして選択されている属性は、更新処理で Netskope Administrator Console のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Netskope Administrator Console のグループ属性](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Netskope Administrator Console に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Netskope Administrator Console にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Netskope Administrator Console に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)

