---
title: 'チュートリアル: Azure Active Directory での自動ユーザー プロビジョニング用に Brivo Onair Identity Connector を構成する | Microsoft Docs'
description: Brivo Onair Identity Connector に対してユーザー アカウントが自動的にプロビジョニングおよびプロビジョニング解除されるように、Azure Active Directory を構成する方法について説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: 4f230a70ae87c7c0ec9db347e7cf975c0252bb18
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "95998091"
---
# <a name="tutorial-configure-brivo-onair-identity-connector-for-automatic-user-provisioning"></a>チュートリアル: 自動ユーザー プロビジョニング用に Brivo Onair Identity Connector を構成する

このチュートリアルの目的は、ユーザーまたはグループの Brivo Onair Identity Connector へのプロビジョニングとプロビジョニング解除が自動的に行われるよう Azure AD を構成するために、Brivo Onair Identity Connector と Azure Active Directory (Azure AD) で実行する手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [Brivo Onair Identity Connector テナント](https://www.brivo.com/lp/quote)
* 上級管理者アクセス許可を持つ Brivo Onair Identity Connector のユーザー アカウント。

## <a name="assigning-users-to-brivo-onair-identity-connector"></a>Brivo Onair Identity Connector へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Brivo Onair Identity Connector へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Brivo Onair Identity Connector に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-brivo-onair-identity-connector"></a>Brivo Onair Identity Connector へのユーザーの割り当てに関する重要なヒント

* 1 人の Azure AD ユーザーを Brivo Onair Identity Connector に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Brivo Onair Identity Connector にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="setup-brivo-onair-identity-connector-for-provisioning"></a>プロビジョニング用に Brivo Onair Identity Connector を設定する

1. [Brivo Onair Identity Connector 管理コンソール](https://acs.brivo.com/login/)にサインインします。 **[Account]\(アカウント\) > [Account Settings]\(アカウントの設定\)** に移動します。

   ![Brivo Onair Identity Connector 管理コンソール](media/brivo-onair-identity-connector-provisioning-tutorial/admin.png)

2. **[Azure AD]** タブをクリックします。 **[Azure AD]** の詳細ページで、上級管理者アカウントのパスワードを再入力します。 **[Submit]\(送信\)** をクリックします。

   ![Brivo Onair Identity Connector での Azure](media/brivo-onair-identity-connector-provisioning-tutorial/azuread.png)

3. **[Copy Token]\(トークンのコピー\)** ボタンをクリックし、**[Secret Token]\(シークレット トークン\)** を保存します。 この値を、Azure portal で Brivo Onair Identity Connector アプリケーションの [プロビジョニング] タブにある [シークレット トークン] フィールドに入力します。

   ![Brivo Onair Identity Connector でのトークン](media/brivo-onair-identity-connector-provisioning-tutorial/token.png)

## <a name="add-brivo-onair-identity-connector-from-the-gallery"></a>ギャラリーから Brivo Onair Identity Connector を追加する

Azure AD を使用して自動的にユーザー プロビジョニングを行うように Brivo Onair Identity Connector を構成する前に、Azure AD アプリケーション ギャラリーからご利用のマネージド SaaS アプリケーションの一覧に Brivo Onair Identity Connector を追加する必要があります。

**Azure AD アプリケーション ギャラリーから Brivo Onair Identity Connector を追加するには、次の手順のようにします。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Brivo Onair Identity Connector**」と入力して、結果パネルから **Brivo Onair Identity Connector** を選択し、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果リストでの Brivo Onair Identity Connector](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-brivo-onair-identity-connector"></a>Brivo Onair Identity Connector に対して自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Brivo Onair Identity Connector のユーザーやグループを作成、更新、無効化する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-brivo-onair-identity-connector-in-azure-ad"></a>Azure AD で Brivo Onair Identity Connector に対して自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、**[Brivo Onair Identity Connector]** を選択します。

    ![アプリケーション一覧での Brivo Onair Identity Connector のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://scim.brivo.com/ActiveDirectory/v2/`」と入力します。 **[シークレット トークン]** に先ほど取得した **SCIM 認証トークン** の値を入力します。 **[テスト接続]** をクリックして、Azure AD から Brivo Onair Identity Connector に接続できることを確認します。 接続できない場合は、使用中の Brivo Onair Identity Connector アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Brivo Onair Identity Connector]\(Azure Active Directory ユーザーを Brivo Onair Identity Connector に同期する\)** を選択します。

    ![Brivo Onair Identity Connector のユーザー マッピング](media/brivo-onair-identity-connector-provisioning-tutorial/user-mappings.png )

9. **[属性マッピング]** セクションで、Azure AD から Brivo Onair Identity Connector に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Brivo Onair Identity Connector のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Brivo Onair Identity Connector のユーザー属性](media/brivo-onair-identity-connector-provisioning-tutorial/user-attributes.png)

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to Brivo Onair Identity Connector]\(Azure Active Directory グループを Brivo Onair Identity Connector に同期する\)** を選択します。

    ![Brivo Onair Identity Connector のグループ マッピング](media/brivo-onair-identity-connector-provisioning-tutorial/group-mappings.png)

11. **[属性マッピング]** セクションで、Azure AD から Brivo Onair Identity Connector に同期されるグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Brivo Onair Identity Connector のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Brivo Onair Identity Connector のグループ属性](media/brivo-onair-identity-connector-provisioning-tutorial/group-attributes.png)

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Brivo Onair Identity Connector に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Brivo Onair Identity Connector にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Brivo Onair Identity Connector に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)

