---
title: チュートリアル:Netskope User Authentication を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Netskope User Authentication に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/07/2019
ms.author: Zhchia
ms.openlocfilehash: 46766a7439185714648572f3f1b9d51ef96abba6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94357472"
---
# <a name="tutorial-configure-netskope-user-authentication-for-automatic-user-provisioning"></a>チュートリアル:自動ユーザー プロビジョニング用に Netskope User Authentication を構成する

このチュートリアルの目的は、Azure Active Directory (Azure AD) が自動的にユーザーまたはグループを Netskope User Authentication に対してプロビジョニングおよびプロビジョニング解除するように構成するために、Netskope User Authentication と Azure AD で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [Netskope User Authentication テナント](https://www.netskope.com/)
* 管理者アクセス許可を持つ Netskope User Authentication のユーザー アカウント。

## <a name="assigning-users-to-netskope-user-authentication"></a>Netskope User Authentication へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Netskope User Authentication へのアクセスが必要な Azure AD 内のユーザーやグループを決定しておく必要があります。 決定したら、次の手順に従って、これらのユーザーやグループを Netskope User Authentication に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-user-authentication"></a>ユーザーを Netskope User Authentication に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを Netskope User Authentication に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Netskope User Authentication にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-netskope-user-authentication-for-provisioning"></a>プロビジョニングのための Netskope User Authentication の設定

1. [Netskope User Authentication Admin Console](https://netskope.goskope.com/) にサインインします。 **[Home]\(ホーム\) > [Settings]\(設定\)** に移動します。

    ![Netskope User Authentication Admin Console](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  **[Tools]\(ツール\)** に移動します。 **[Tools]\(ツール\)** メニューで、 **[Directory Tools]\(ディレクトリ ツール\) > [SCIM INTEGRATION]\(SCIM 統合\)** に移動します。

    ![Netskope User Authentication ツール](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Netskope User Authentication の [Add SCIM]\(SCIM の追加\)](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. 下にスクロールし、 **[Add Token]\(トークンの追加\)** ボタンをクリックします。 **[Add OAuth Client Name]\(OAuth クライアント名の追加\)** ダイアログボックスで、 **[CLIENT NAME]\(クライアント名\)** を指定し、 **[Save]\(保存\)** ボタンをクリックします。

    ![Netskope User Authentication の [Add Token]\(トークンの追加\)](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![Netskope User Authentication の [CLient Name]\(クライアント名\)](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  **[SCIM Server URL]\(SCIM サーバー URL\)** と **[TOKEN]\(トークン\)** をコピーします。 これらの値を、Azure portal の Netskope User Authentication アプリケーションの [プロビジョニング] タブで、[テナント URL] および [シークレット トークン] フィールドにそれぞれ入力します。

    ![Netskope User Authentication の [Create Token]\(トークンの作成\)](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-user-authentication-from-the-gallery"></a>ギャラリーから Netskope User Authentication を追加する

Azure AD での自動ユーザー プロビジョニング用に Netskope User Authentication を構成する前に、Azure AD アプリケーション ギャラリーから Netskope User Authentication をマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Netskope User Authentication を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Netskope User Authentication**」と入力し、結果ウィンドウで **[Netskope User Authentication]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の Netskope User Authentication](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-user-authentication"></a>Netskope User Authentication への自動ユーザー プロビジョニングの構成 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Netskope User Authentication のユーザーやグループを作成、更新、無効化する手順について説明します。

> [!TIP]
> [Netskope User Authentication のシングル サインオンのチュートリアル](./netskope-cloud-security-tutorial.md)に記載されている手順に従って、Netskope User Authentication に対して SAML ベースのシングル サインオンを有効にすることもできます。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

> [!NOTE]
> Netskope User Authentication の SCIM エンドポイントの詳細については、[こちら](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon)を参照してください。

### <a name="to-configure-automatic-user-provisioning-for-netskope-user-authentication-in-azure-ad"></a>Azure AD で Netskope User Authentication の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[Netskope User Authentication]** を選択します。

    ![アプリケーションの一覧の Netskope User Authentication リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションで、以前に取得した **SCIM Server URL** の値を **[テナント URL]** に入力します。 先ほど取得した **トークン** の値を **[シークレット トークン]** に、入力します。 **[テスト接続]** をクリックして、Azure AD から Netskope User Authentication への接続を確認します。 接続できない場合は、使用中の Netskope User Authentication アカウントに管理者アクセス許可があることを確認してから、再試行します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Users to Netskope User Authentication]\(Azure Active Directory ユーザーを Netskope User Authentication に同期する\)** を選択します。

    ![Netskope User Authentication のユーザー マッピング](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. **[属性マッピング]** セクションで、Azure AD から Netskope User Authentication に同期されるユーザー属性を確認します。 **一致する** プロパティとして選択されている属性は、更新処理で Netskope User Authentication のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Netskope User Authentication のユーザー属性](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Groups to Netskope User Authentication]\(Azure Active Directory グループを Netskope User Authentication に同期する\)** を選択します。

    ![Netskope User Authentication のグループ マッピング](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. **[属性マッピング]** セクションで、Azure AD から Netskope User Authentication に同期されるグループ属性を確認します。 **一致する** プロパティとして選択されている属性は、更新処理で Netskope User Authentication のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Netskope User Authentication のグループ属性](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Netskope User Authentication に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Netskope User Authentication にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Netskope User Authentication に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)