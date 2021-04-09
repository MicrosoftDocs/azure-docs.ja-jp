---
title: 'チュートリアル: Wrike を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs'
description: Azure Active Directory を構成して、ユーザー アカウントを Wrike に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: Zhchia
ms.openlocfilehash: 53b1db1a8c4da59055c0af5f448fa0c8a6933daf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "95988129"
---
# <a name="tutorial-configure-wrike-for-automatic-user-provisioning"></a>チュートリアル: Wrike を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure AD が自動的にユーザーまたはグループを Wrike にプロビジョニングまたは Wrike からプロビジョニング解除するように構成するために、Wrike と Azure Active Directory (Azure AD) で実行する手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスで実行されること、しくみ、よく寄せられる質問の重要な詳細については、[Azure Active Directory によるサービスとしてのソフトウェア (SaaS) アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)に関する記事を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure の使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [Wrike テナント](https://www.wrike.com/price/)
* 管理者アクセス許可がある Wrike のユーザー アカウント

## <a name="assign-users-to-wrike"></a>ユーザーを Wrike に割り当てる
Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD でアプリケーションに割り当てられているユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Wrike にアクセスする必要がある Azure AD のユーザーまたはグループを決定しておく必要があります。 その後、次の手順に従って、これらのユーザーまたはグループを Wrike に割り当てます。

* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-wrike"></a>ユーザーを Wrike に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを Wrike に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後で追加のユーザーまたはグループを割り当てることができます。

* Wrike にユーザーを割り当てるときに、有効なアプリケーション固有ロール (ある場合) を割り当てダイアログ ボックスで選択する必要があります。 既定のアクセス ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-wrike-for-provisioning"></a>プロビジョニングする Wrike を設定する

Azure AD を使用した自動ユーザー プロビジョニング用に Wrike を構成する前に、Wrike で System for Cross-domain Identity Management (SCIM) プロビジョニングを有効にする必要があります。

1. [Wrike 管理コンソール](https://www.Wrike.com/login/)にサインインします。 テナント ID に移動します。 **[アプリおよび統合]** を選択します。

    ![アプリおよび統合](media/Wrike-provisioning-tutorial/admin.png)

2.  **Azure AD** に移動して選択します。

    ![Azure AD](media/Wrike-provisioning-tutorial/Capture01.png)

3.  [SCIM] を選択します。 **[ベース URL]** をコピーします。

    ![ベース URL](media/Wrike-provisioning-tutorial/Wrike-tenanturl.png)

4. **[API]**  >  **[Azure SCIM]** の順に選択します。

    ![Azure SCIM](media/Wrike-provisioning-tutorial/Wrike-add-scim.png)

5.  ポップアップが表示されます。 先ほどアカウントを作成するために作成したものと同じパスワードを入力します。

    ![Wrike のトークンの作成](media/Wrike-provisioning-tutorial/password.png)

6.  **[シークレット トークン]** をコピーし、Azure AD に貼り付けます。 **[保存]** を選択し、Wrike でのプロビジョニングの設定を完了します。

    ![永続的なアクセス トークン](media/Wrike-provisioning-tutorial/Wrike-create-token.png)


## <a name="add-wrike-from-the-gallery"></a>ギャラリーからの Wrike の追加

Azure AD を使用した自動ユーザー プロビジョニング用に Wrike を構成する前に、Wrike を Azure AD アプリケーション ギャラリーからマネージド SaaS アプリケーションの一覧に追加します。

Azure AD ギャラリーから Wrike を追加するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Wrike**」と入力し、結果ウィンドウで **[Wrike]** を選択してから、**[追加]** を選択してアプリケーションを追加します。

    ![結果一覧の Wrike](common/search-new-app.png)


## <a name="configure-automatic-user-provisioning-to-wrike"></a>Wrike への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Wrike のユーザーやグループを作成、更新、無効化する手順について説明します。

> [!TIP]
> Wrike で SAML ベースのシングル サインオンを有効にするには、[Wrike シングル サインオンのチュートリアル](wrike-tutorial.md)の手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="configure-automatic-user-provisioning-for-wrike-in-azure-ad"></a>Azure AD で Wrike 用に自動ユーザー プロビジョニングを構成する

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。

    ![すべてのアプリケーション](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Wrike]** を選択します。

    ![アプリケーションの一覧の Wrike のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング モード] が [自動] に設定されている](common/provisioning-automatic.png)

5. [管理者資格情報] セクションで、先ほど取得した **[ベース URL]** と **[Permanent access token]\(永続的なアクセス トークン\)** の値を、それぞれ **[テナント URL]** と **[シークレット トークン]** に入力します。 Azure AD が Wrike に接続できることを確認するために、**[テスト接続]** を選択します。 接続できない場合は、使用中の Wrike アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

7. プロビジョニングのエラー通知を受け取るユーザーまたはグループのメール アドレスを **[通知用メール]** フィールドに入力し、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

8. **[保存]** を選択します。

9. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Wrike]\(Azure Active Directory ユーザーを Wrike に同期する\)** を選択します。

    ![Wrike のユーザー マッピング](media/Wrike-provisioning-tutorial/Wrike-user-mappings.png)

10. **[属性マッピング]** セクションで、Azure AD から Wrike に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Wrike のユーザー アカウントとの照合に使用されます。 すべての変更をコミットするには、 **[保存]** を選択します。

    ![Wrike のユーザー属性](media/Wrike-provisioning-tutorial/Wrike-user-attributes.png)

11. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の手順を参照してください。

12. Wrike に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

13. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Wrike にプロビジョニングするユーザーまたはグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

14. プロビジョニングの準備ができたら、 **[保存]** を選択します。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

この操作によって、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーまたはグループの初期同期が開始されます。 初期同期は、後続の同期よりも実行に時間がかかります。 ユーザーやグループのプロビジョニングにかかる時間の詳細については、「[ユーザーをプロビジョニングするにはどのくらいの時間がかかりますか](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)」を参照してください。

**[現在の状態]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Wrike に対して実行されたすべてのアクションが記載されています。 詳細については、「[ユーザー プロビジョニングの状態を確認する](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)」を参照してください。 Azure AD プロビジョニング ログを読むには、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)