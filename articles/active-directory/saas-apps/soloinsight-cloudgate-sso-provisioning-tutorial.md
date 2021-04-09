---
title: チュートリアル:Soloinsight-CloudGate SSO を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Soloinsight-CloudGate SSO に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 5c42102c0704d7212411d6b86f4210ad8ecd885c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96347499"
---
# <a name="tutorial-configure-soloinsight-cloudgate-sso-for-automatic-user-provisioning"></a>チュートリアル:Soloinsight-CloudGate SSO を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure AD によって自動的にユーザーまたはグループが Soloinsight-CloudGate SSO にプロビジョニングされる、または Soloinsight-CloudGate SSO からプロビジョニング解除されるように構成するため、Soloinsight-CloudGate SSO と Azure Active Directory (Azure AD) で実行する手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [Soloinsight-CloudGate SSO テナント](https://www.soloinsight.com/)
* 管理者のアクセス許可が与えられている Soloinsight-CloudGate SSO のユーザー アカウント。

## <a name="assigning-users-to-soloinsight-cloudgate-sso"></a>Soloinsight-CloudGate SSO にユーザーを割り当てる

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Soloinsight-CloudGate SSO へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Soloinsight-CloudGate SSO に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-soloinsight-cloudgate-sso"></a>Soloinsight-CloudGate SSO にユーザーを割り当てる際の重要なヒント

* 1 名の Azure AD ユーザーを Soloinsight-CloudGate SSO に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Soloinsight-CloudGate SSO にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-soloinsight-cloudgate-sso-for-provisioning"></a>Soloinsight-CloudGate SSO を設定し、プロビジョニングに対応させる

1. [Soloinsight-CloudGate SSO 管理者コンソール](https://soloinsight.sigateway.com/login)にサインインします。 **[管理]、[システム設定]** の順に移動します。

    ![Soloinsight-CloudGate SSO 管理者コンソール](media/soloinsight-cloudgate-sso-provisioning-tutorial/admin.png)

2.  **[全般]** に移動します。

    ![Soloinsight-CloudGate SSO の SCIM 追加](media/soloinsight-cloudgate-sso-provisioning-tutorial/config.png)

3.  下にスクロールし、ページの終わりにある **[テナント URL]** と **[シークレット トークン]** を表示します。 **シークレット トークン** をコピーします。 この値は、Azure portal で Soloinsight-CloudGate SSO アプリケーションの [プロビジョニング] タブ内の [シークレット トークン] フィールドに入力されます。

    ![Soloinsight-CloudGate SSO のトークン作成](media/soloinsight-cloudgate-sso-provisioning-tutorial/token.png)

## <a name="add-soloinsight-cloudgate-sso-from-the-gallery"></a>ギャラリーから Soloinsight-CloudGate SSO を追加する

Azure AD での自動ユーザー プロビジョニング用に Soloinsight-CloudGate SSO を構成する前に、Azure AD アプリケーション ギャラリーから Soloinsight-CloudGate SSO をマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Soloinsight-CloudGate SSO を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Soloinsight-CloudGate SSO**」と入力し、結果パネルで **[Soloinsight-CloudGate SSO]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の Soloinsight-CloudGate SSO](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-soloinsight-cloudgate-sso"></a>Soloinsight-CloudGate SSO にユーザーが自動的にプロビジョニングされるように構成する 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Soloinsight-CloudGate SSO のユーザーやグループを作成、更新、無効化する手順について説明します。

> [!TIP]
> Soloinsight-CloudGate SSO で SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Soloinsight-CloudGate SSO シングル サインオンのチュートリアル](./soloinsight-cloudgate-sso-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-soloinsight-cloudgate-sso-in-azure-ad"></a>Azure AD で Soloinsight-CloudGate SSO の自動ユーザー プロビジョニングを構成するには、次の操作を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Soloinsight-CloudGate SSO]** を選択します。

    ![アプリケーションの一覧の [Soloinsight-CloudGate SSO] リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://sigateway.com/scim/v2/sync/serviceproviderconfig`」と入力します。 **[シークレット トークン]** に先ほど取得した **SCIM 認証トークン** の値を入力します。 **[テスト接続]** をクリックして、Azure AD から Soloinsight-CloudGate SSO への接続を確保します。 接続に失敗する場合は、使用中の Soloinsight-CloudGate SSO アカウントに管理者アクセス許可があることを確認してから、もう一度お試しください。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションの **[Azure Active Directory ユーザーを Soloinsight-CloudGate SSO に同期する]** を選択します。

    ![Soloinsight-CloudGate SSO のユーザー マッピング](media/soloinsight-cloudgate-sso-provisioning-tutorial/usermappings.png)

9. **[属性マッピング]** セクションで、Azure AD から Soloinsight-CloudGate SSO に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Soloinsight-CloudGate SSO のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Soloinsight-CloudGate SSO のユーザー属性](media/soloinsight-cloudgate-sso-provisioning-tutorial/userattributes.png)

10. **[マッピング]** セクションの **[Azure Active Directory グループを Soloinsight-CloudGate SSO に同期する]** を選択します。

    ![Soloinsight-CloudGate SSO のグループ マッピング](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupmappings.png)

11. **[属性マッピング]** セクションで、Azure AD から Soloinsight-CloudGate SSO に同期されるグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Soloinsight-CloudGate SSO のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Soloinsight-CloudGate SSO のグループ属性](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupattributes.png)

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Soloinsight-CloudGate SSO に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Soloinsight-CloudGate SSO にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Soloinsight-CloudGate SSO に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)