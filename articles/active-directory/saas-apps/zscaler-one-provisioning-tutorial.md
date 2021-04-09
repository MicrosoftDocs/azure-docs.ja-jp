---
title: チュートリアル:Zscaler One を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: ユーザー アカウントを Zscaler One に自動的にプロビジョニング/プロビジョニング解除するように Azure Active Directory を構成する方法について説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: f6725045064b74079e00ca5bbe1d560f3b19f3ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937129"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>チュートリアル:Zscaler One を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Zscaler One に対するユーザーとグループのプロビジョニングとプロビジョニング解除を自動的に実行するように Azure AD を構成するために、Zscaler One と Azure Active Directory (Azure AD) で実行する手順を示します。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービス上に構築されるコネクタについて説明します。 このサービスで実行されること、しくみ、およびよく寄せられるについては、「[Azure Active Directory によるサービスとしてのソフトウェア (SaaS) アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。


## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、以下を所有していることを前提としています。

* Azure AD テナント。
* Zscaler One テナント。
* 管理者アクセス許可がある Zscaler One のユーザー アカウント。

> [!NOTE]
> Azure AD プロビジョニング統合は、Zscaler One SCIM API に依存しています。 この API は、Zscaler One の開発者が Enterprise パッケージを含むアカウントで使用できます。

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Azure Marketplace から Zscaler One を追加する

Azure AD での自動ユーザー プロビジョニング用に Zscaler One を構成する前に、Zscaler One を Azure Marketplace から管理対象の SaaS アプリケーションの一覧に追加する必要があります。

Marketplace から Zscaler One を追加するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のアイコン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部の **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Zscaler One**」と入力し、結果パネルで **[Zscaler One]** を選択します。 アプリケーションを追加するには、 **[追加]** を選択します。

    ![結果一覧の Zscaler One](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Zscaler One にユーザーを割り当てる

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD でアプリケーションに割り当てられているユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Zscaler One にアクセスする必要がある Azure AD のユーザーまたはグループを決定しておく必要があります。 これらのユーザーまたはグループを Zscaler One に割り当てるには、「[エンタープライズ アプリにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)」の指示に従います。

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>ユーザーを Zscaler One に割り当てるときの重要なヒント

* 単一の Azure AD ユーザーを Zscaler One に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後で、追加のユーザーまたはグループを割り当てることができます。

* Zscaler One にユーザーを割り当てるとき、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログ ボックスで選択します。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Zscaler One への自動ユーザー プロビジョニングを構成する

このセクションでは、Azure AD プロビジョニング サービスを構成する手順を説明します。 これを使用して、Azure AD でのユーザーまたはグループの割り当てに基づいて、Zscaler One でのユーザーまたはグループの作成、更新、および無効化を行います。

> [!TIP]
> Zscaler One に対する SAML ベースのシングル サインオンを有効にすることもできます。 [Zscaler One のシングル サインオンに関するチュートリアル](zscaler-One-tutorial.md)の手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

> [!NOTE]
> ユーザーとグループをプロビジョニングしたりプロビジョニング解除したりする際は、グループ メンバーシップが適切に更新されるよう、定期的にプロビジョニングをやり直すことをお勧めします。 そうすることによって、サービスによって強制的にすべてのグループが再評価され、メンバーシップが更新されます。  

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Azure AD で Zscaler One に対する自動ユーザー プロビジョニングを構成する

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]**  >  **[Zscaler One]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[Zscaler One]** を選択します。

    ![アプリケーションの一覧の Zscaler One のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![Zscaler One プロビジョニング](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![Zscaler One のプロビジョニング モード](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. **[管理者資格情報]** セクションで、**[テナント URL]** ボックスと **[シークレット トークン]** ボックスに、手順 6 で説明する Zscaler One アカウントの設定を入力します。

6. テナント URL とシークレット トークンを取得するには、Zscaler One ポータル UI で **[Administration]\(管理\)**  >  **[Authentication Settings]\(認証設定\)** の順に移動します。 **[Authentication Type]\(認証タイプ\)** で **[SAML]** を選択します。

    ![Zscaler One の認証設定](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. **[Configure SAML]\(SAML の構成\)** を選択して **[Configure SAML]\(SAML の構成\)** ウィンドウを開きます。

    ![Zscaler One での SAML の構成](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. **[Enable SCIM-Based Provisioning]\(SCIM ベースのプロビジョニングを有効にする\)** を選択して、**[Base URL]\(ベース URL\)** と **[Bearer Token]\(ベアラー トークン\)** の設定を取得します。 それらの設定を保存します。 **[Base URL]\(ベース URL\)** の設定を、Azure portal の **[テナント URL]** にコピーします。 **[Bearer Token]\(ベアラー トークン\)** の設定を、Azure portal の **[シークレット トークン]** にコピーします。

7. 手順 5 に示されているボックスに入力したら、**[テスト接続]** を選択して、Azure AD が Zscaler One に接続できることを確認します。 接続できない場合は、使用中の Zscaler One アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![Zscaler One のテスト接続](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループのメール アドレスを入力します。 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![Zscaler One の通知メール](./media/zscaler-one-provisioning-tutorial/notification.png)

9. **[保存]** を選択します。

10. **[マッピング]** セクションで **[Synchronize Azure Active Directory Users to Zscaler One]\(Azure Active Directory ユーザーを Zscaler One に同期する\)** を選択します。

    ![Zscaler One のユーザーの同期](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. **[属性マッピング]** セクションで、Azure AD から Zscaler One に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Zscaler One のユーザー アカウントとの照合に使用されます。 すべての変更を保存するために、 **[保存]** を選択します。

    ![Zscaler One の一致するユーザー属性](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. **[マッピング]** セクションで **[Synchronize Azure Active Directory Groups to Zscaler One]\(Azure Active Directory グループを Zscaler One に同期する\)** を選択します。

    ![Zscaler One のグループの同期](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. **[属性マッピング]** セクションで、Azure AD から Zscaler One に同期されるグループ属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Zscaler One のグループとの照合に使用されます。 すべての変更を保存するために、 **[保存]** を選択します。

    ![Zscaler One の一致するグループ属性](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の手順を参照してください。

15. Zscaler One に対する Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![Zscaler One のプロビジョニングの状態](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Zscaler One にプロビジョニングするユーザーまたはグループを定義します。 **[設定]** セクションで、 **[スコープ]** として指定する値を選択します。

    ![Zscaler One のスコープ](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. プロビジョニングの準備ができたら、 **[保存]** を選択します。

    ![Zscaler One の [保存]](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

この操作によって、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーまたはグループの初期同期が開始されます。 初期同期は、以降の同期よりも実行に時間がかかります。 それらは、Azure AD プロビジョニング サービスが実行されている限り、約 40 分ごとに発生します。 

**[同期の詳細]** セクションを使用して進行状況を監視し、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。 このレポートには、Azure AD プロビジョニング サービスによって Zscaler One で実行されたすべてのアクションが記述されます。

Azure AD プロビジョニング ログの見方について詳しくは、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png