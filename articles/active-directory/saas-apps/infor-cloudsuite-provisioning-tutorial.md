---
title: チュートリアル:Infor CloudSuite を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Infor CloudSuite に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
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
ms.openlocfilehash: 8fdd2c8a326fbdc68d1aec65377f4c465c5ee4c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96353903"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>チュートリアル:Infor CloudSuite を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure Active Directory (Azure AD) が自動的にユーザーまたはグループを Infor CloudSuite にプロビジョニングまたは Infor CloudSuite からプロビジョニング解除するように構成するために、Infor CloudSuite と Azure AD で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [Infor CloudSuite テナント](https://www.infor.com/products/infor-os)
* Admin アクセス許可がある Infor CloudSuite のユーザー アカウント。

## <a name="assigning-users-to-infor-cloudsuite"></a>Infor CloudSuite へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Infor CloudSuite へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Infor CloudSuite に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>ユーザーを Infor CloudSuite に割り当てるときの重要なヒント

* 単一の Azure AD ユーザーを Infor CloudSuite に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Infor CloudSuite にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>プロビジョニングのために Infor CloudSuite を設定する

1. [Infor CloudSuite 管理コンソール](https://www.infor.com/customer-center)にサインインします。 ユーザー アイコンをクリックし、**ユーザー管理** に移動します。

    ![Infor CloudSuite 管理コンソール](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  画面の左上隅にあるメニュー アイコンをクリックします。 **[Manage]\(管理\)** をクリックします。

    ![Infor CloudSuite SCIM の追加](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  **[SCIM Accounts]\(SCIM アカウント\)** に移動します。

    ![Infor CloudSuite SCIM アカウント](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  プラス記号のアイコンをクリックして、管理者ユーザーを追加します。 **[SCIM Password]\(SCIM パスワード\)** を入力し、 **[Confirm Password]\(パスワードの確認\)** に同じパスワードを入力します。 フォルダー アイコンをクリックしてパスワードを保存します。 管理者ユーザーに対して生成された **[User Identifier]\(ユーザー ID\)** が表示されます。

    ![Infor CloudSuite 管理者ユーザー](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![Infor CloudSuite パスワード](media/infor-cloudsuite-provisioning-tutorial/password.png)

    :::image type="content" source="media/infor-cloudsuite-provisioning-tutorial/identifier.png" alt-text="強調表示されたテーブルの行が表示されている Infor CloudSuite 管理コンソールのスクリーンショット。この行には、ユーザー ID、パスワード、およびタイムスタンプが含まれています。" border="false":::

5. ベアラー トークンを生成するには、 **[User Identifier]\(ユーザー ID\)** と **[SCIM Password]\(SCIM パスワード\)** をコピーします。 コロンで区切って notepad++ に貼り付けます。 **[Plugins] (プラグイン) > MIME Tools (MIME ツール) > Basic64 Encode (Basic64 エンコード)** の順に選択して文字列値をエンコードします。 

    :::image type="content" source="media/infor-cloudsuite-provisioning-tutorial/token.png" alt-text="Notepad++ ドキュメントのスクリーンショット。[Plugins]\(プラグイン\) メニューの [MIME tools]\(MIME ツール\) が強調表示されています。[MIME tools]\(MIME ツール\) メニューの [Base64 encode]\(Base64 エンコード\) が強調表示されています。" border="false":::

3.  ベアラー トークンをコピーします。 この値を、Azure portal で Infor CloudSuite アプリケーションの [プロビジョニング] タブ内の [シークレット トークン] フィールドに入力します。

## <a name="add-infor-cloudsuite-from-the-gallery"></a>ギャラリーから Infor CloudSuite を追加する

Azure AD での自動ユーザー プロビジョニング用に Infor CloudSuite を構成する前に、Azure AD アプリケーション ギャラリーから Infor CloudSuite をマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Infor CloudSuite を追加するには、次の手順を行います。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Infor CloudSuite**」と入力し、結果ウィンドウで **[Infor CloudSuite]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果リストの Infor CloudSuite](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>Infor CloudSuite への自動ユーザー プロビジョニングの構成 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Infor CloudSuite のユーザーやグループを作成、更新、無効化する手順について説明します。

> [!TIP]
> Infor CloudSuite で SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Infor CloudSuite シングル サインオンのチュートリアル](./infor-cloud-suite-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

> [!NOTE]
> Infor CloudSuite の SCIM エンドポイントの詳細については、[こちら](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#)を参照してください。

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>Azure AD で Infor CloudSuite の自動ユーザー プロビジョニングを構成するには、次の手順を行います。

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Infor CloudSuite]** を選択します。

    ![アプリケーション一覧の Infor CloudSuite のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim`」と入力します。 **[シークレット トークン]** に、先ほど取得したベアラー トークンの値を入力します。 **[テスト接続]** をクリックして、Azure AD から Infor CloudSuite への接続を確保します。 接続できない場合は、使用中の Infor CloudSuite アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Users to Infor CloudSuite]\(Azure Active Directory ユーザーを Infor CloudSuite に同期する\)** を選択します。

    ![Infor CloudSuite ユーザー マッピング](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. **[属性マッピング]** セクションで、Azure AD から Infor CloudSuite に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Infor CloudSuite のユーザー アカウントとの照合に使用されることに注意してください。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Infor CloudSuite ユーザー属性](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Groups to Infor CloudSuite]\(Azure Active Directory グループを Infor CloudSuite に同期する\)** を選択します。

    ![Infor CloudSuite グループ マッピング](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. **[属性マッピング]** セクションで、Azure AD から Infor CloudSuite に同期されるグループ属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Infor CloudSuite のグループとの照合に使用されることに注意してください。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Infor CloudSuite グループ属性](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Infor CloudSuite に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Infor CloudSuite にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Infor CloudSuite に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)