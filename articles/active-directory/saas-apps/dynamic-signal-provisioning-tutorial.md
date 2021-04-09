---
title: チュートリアル:Azure Active Directory で自動ユーザー プロビジョニング用に Dynamic Signal を構成する | Microsoft Docs
description: ユーザー アカウントを Dynamic Signal に自動的にプロビジョニングおよびプロビジョニング解除するよう Azure Active Directory を構成する方法について説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 263a67fd8fba2c336d1ed4d91475386a8ae175dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005794"
---
# <a name="tutorial-configure-dynamic-signal-for-automatic-user-provisioning"></a>チュートリアル:自動ユーザー プロビジョニング用に Dynamic Signal を構成する

このチュートリアルの目的は、ユーザーまたはグループを Dynamic Signal に自動的にプロビジョニングおよびプロビジョニング解除するよう Azure AD を構成するために、Dynamic Signal と Azure Active Directory (Azure AD) で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [Dynamic Signal テナント](https://dynamicsignal.com/)
* 管理者アクセス許可がある Dynamic Signal のユーザー アカウント

## <a name="add-dynamic-signal-from-the-gallery"></a>ギャラリーから Dynamic Signal を追加

Azure AD で自動ユーザー プロビジョニング用に Dynamic Signal を構成する前に、Dynamic Signal を Azure AD アプリケーション ギャラリーからマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Dynamic Signal を追加するには、次の手順に従います。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Dynamic Signal**」と入力し、結果ウィンドウで **[Dynamic Signal]** を選択してから、 **[追加]** をクリックしてアプリケーションを追加します。

    ![結果リストの Dynamic Signal](common/search-new-app.png)

## <a name="assigning-users-to-dynamic-signal"></a>Dynamic Signal へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Dynamic Signal へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定したら、次の手順に従って、これらのユーザーやグループを Dynamic Signal に割り当てることができます。

* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dynamic-signal"></a>Dynamic Signal にユーザーを割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを Dynamic Signal に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Dynamic Signal にユーザーを割り当てるときは、割り当てダイアログで有効なアプリケーション固有ロール (使用可能な場合) を選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="configuring-automatic-user-provisioning-to-dynamic-signal"></a>Dynamic Signal への自動ユーザー プロビジョニングの構成 

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて Dynamic Signal のユーザーやグループを作成、更新、無効化するよう、Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> Dynamic Signal では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Dynamic Signal シングル サインオンのチュートリアル](dynamicsignal-tutorial.md)に関するページで説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-dynamic-signal-in-azure-ad"></a>Azure AD で Dynamic Signal 用に自動ユーザー プロビジョニングを構成するには

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Dynamic Signal]** を選択します。

    ![アプリケーションの一覧の Dynamic Signal リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションで、手順 6 で説明するように、Dynamic Signal アカウントの **[テナント URL]** と **[シークレット トークン]** を入力します。

6. Dynamic Signal 管理コンソールで、 **[Admin]\(管理者\) > [Advanced]\(詳細設定\) > [API]** に移動します。

    :::image type="content" source="./media/dynamic-signal-provisioning-tutorial/secret-token-1.png" alt-text="Dynamic Signal 管理コンソールのスクリーンショット。[Admin]\(管理\) メニューの [Advanced]\(詳細設定\) が強調表示されています。[詳細設定] メニューも表示されており、A P I が強調表示されています。" border="false":::

    **[SCIM API URL]** を **[Tenant URL]\(テナント URL\)** にコピーします。 **[Generate New Token]\(新しいトークンの生成\)** をクリックして、 **[Bearer Token]\(ベアラー トークン\)** を生成し、その値を **[Secret Token]\(シークレット トークン\)** にコピーします。

    :::image type="content" source="./media/dynamic-signal-provisioning-tutorial/secret-token-2.png" alt-text="S C I M A P I U R L、[Generate New Token]\(新しいトークンの生成\)、および [Bearer Token]\(ベアラー トークン\) が強調表示され、[Bearer Token]\(ベアラー トークン\) ボックスにプレース ホルダーが表示されているスクリーンショット。" border="false":::

7. 手順 5 の各フィールドに値を入力したら、 **[テスト接続]** をクリックして、Azure AD が Dynamic Signal に接続できることを確認します。 接続できない場合は、使用中の Dynamic Signal アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

8. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

9. **[保存]** をクリックします。

10. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Users to Dynamic Signal]\(Azure Active Directory ユーザーを Dynamic Signal に同期する\)** を選択します。

    ![Dynamic Signal ユーザー マッピング](media/dynamic-signal-provisioning-tutorial/user-mappings.png)

11. **[属性マッピング]** セクションで、Azure AD から Dynamic Signal に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新操作で Dynamic Signal のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Dynamic Signal のユーザー属性](media/dynamic-signal-provisioning-tutorial/user-mapping-attributes.png)

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Dynamic Signal に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Dynamic Signal にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Dynamic Signal に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="connector-limitations"></a>コネクタの制限事項

* Dynamic Signal では、Azure AD からユーザーを完全に削除する操作はサポートされていません。 Dynamic Signal でユーザーを完全に削除するには、Dynamic Signal 管理コンソール UI で操作を行う必要があります。 
* 現時点では、Dynamic Signal ではグループがサポートされていません。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)

