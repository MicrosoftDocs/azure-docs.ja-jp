---
title: 'チュートリアル: Tableau Online を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs'
description: Azure Active Directory を構成して、ユーザー アカウントを Tableau Online に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
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
ms.openlocfilehash: a42790e079985b003776b381c74f837b0ba619b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94359206"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>チュートリアル: Tableau Online を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Tableau Online に対するユーザーとグループのプロビジョニングとプロビジョニング解除を自動的に実行するように Azure AD を構成するために、Tableau Online と Azure Active Directory (Azure AD) で実行する手順を示します。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービス上に構築されるコネクタについて説明します。 このサービスで実行されること、しくみ、およびよく寄せられるについては、「[Azure Active Directory によるサービスとしてのソフトウェア (SaaS) アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、以下を所有していることを前提としています。

*   Azure AD テナント。
*   [Tableau Online テナント](https://www.tableau.com/)。
*   管理者アクセス許可がある Tableau Online のユーザー アカウント

> [!NOTE]
> Azure AD プロビジョニング統合は、[Tableau Online Rest API](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm) に依存しています。 この API は Tableau Online 開発者が利用できます。

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Azure Marketplace から Tableau Online を追加する
Azure AD での自動ユーザー プロビジョニング用に Tableau Online を構成する前に、Tableau Online を Azure Marketplace から管理対象の SaaS アプリケーションの一覧に追加する必要があります。

Marketplace から Tableau Online を追加するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のアイコン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部の **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Tableau Online**」と入力し、結果パネルから **[Tableau Online]** を選択します。 アプリケーションを追加するには、 **[追加]** を選択します。

    ![結果一覧の Tableau Online](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Tableau Online にユーザーを割り当てる

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD でアプリケーションに割り当てられているユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Tableau Online にアクセスする必要がある Azure AD のユーザーまたはグループを決定しておく必要があります。 これらのユーザーまたはグループを Tableau Online に割り当てるには、「[エンタープライズ アプリにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)」の指示に従います。

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>ユーザーを Tableau Online に割り当てる際の重要なヒント

*   単一の Azure AD ユーザーを Tableau Online に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後で、追加のユーザーまたはグループを割り当てることができます。

*   Tableau Online にユーザーを割り当てるとき、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログ ボックスで選択します。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Tableau Online への自動ユーザー プロビジョニングを構成する

このセクションでは、Azure AD プロビジョニング サービスを構成する手順を説明します。 これを使用して、Azure AD でのユーザーまたはグループの割り当てに基づいて、Tableau Online でのユーザーまたはグループの作成、更新、および無効化を行います。

> [!TIP]
> Tableau Online に対する SAML ベースのシングル サインオンを有効にすることもできます。 [Tableau Online のシングル サインオンに関するチュートリアル](tableauonline-tutorial.md)の手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Azure AD で Tableau Online の自動ユーザー プロビジョニングを構成する

1. [Azure portal](https://portal.azure.com) にサインインする **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]**  >  **[Tableau Online]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Tableau Online]** を選択します。

    ![アプリケーションの一覧の Tableau Online のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![Tableau Online のプロビジョニング](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![Tableau Online のプロビジョニング モード](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. **[管理者資格情報]** セクションのドメイン、管理ユーザー名、管理パスワード、コンテンツ URL に、Tableau Online アカウントの情報を入力します。

   * **[ドメイン]** ボックスに、手順 6 に基づいてサブドメインを入力します。

   * **[管理ユーザー名]** ボックスに、Tableau Online テナントの管理者アカウントのユーザー名を入力します。 たとえば admin@contoso.com です。

   * **[管理パスワード]** ボックスに、管理者ユーザー名に対応する管理者アカウントのパスワードを入力します。

   * **[コンテンツ URL]** ボックスに、手順 6 に基づいてサブドメインを入力します。

6. **[ドメイン]** および **[コンテンツ URL]** の値は、Tableau Online の管理者アカウントにサインインした後、管理者ページの URL から取得できます。

    * Tableau Online アカウントの **[ドメイン]** は、URL の中の次の部分からコピーできます。

        ![Tableau Online のドメイン](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * Tableau Online アカウントの **[コンテンツ URL]** は、このセッションからコピーできます。 アカウント設定時に定義される値です。 この例では、値は "contoso" です。

        ![Tableau Online のコンテンツの URL](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > 実際の **ドメイン** は、ここに示したものとは異なる場合があります。

7. 手順 5 に示されているボックスに入力したら、 **[テスト接続]** を選択して、Azure AD が Tableau Online に接続できることを確認します。 接続できない場合は、使用中の Tableau Online アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![Tableau Online のテスト接続](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループのメール アドレスを入力します。 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![Tableau Online の通知メール](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. **[保存]** を選択します。

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Tableau]\(Azure Active Directory ユーザーを Tableau に同期する\)** を選択します。

    ![Tableau Online のユーザー同期](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. **[属性マッピング]** セクションで、Azure AD から Tableau Online に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Tableau Online のユーザー アカウントとの照合に使用されます。 すべての変更を保存するために、 **[保存]** を選択します。

    ![Tableau Online の一致するユーザー属性](./media/tableau-online-provisioning-tutorial/attribute.png)

12. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to Tableau]\(Azure Active Directory グループを Tableau に同期する\)** を選択します。

    ![Tableau Online のグループ同期](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. **[属性マッピング]** セクションで、Azure AD から Tableau Online に同期されるグループ属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Tableau Online のユーザー アカウントとの照合に使用されます。 すべての変更を保存するために、 **[保存]** を選択します。

    ![Tableau Online の一致するグループ属性](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の手順を参照してください。

15. Tableau Online に対する Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![Tableau Online のプロビジョニング状態](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Tableau Online にプロビジョニングするユーザーまたはグループを定義します。 **[設定]** セクションで、 **[スコープ]** として指定する値を選択します。

    ![Tableau Online の [スコープ]](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. プロビジョニングの準備ができたら、 **[保存]** を選択します。

    ![Tableau Online の [保存]](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

この操作によって、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーまたはグループの初期同期が開始されます。 初期同期は、以降の同期よりも実行に時間がかかります。 それらは、Azure AD プロビジョニング サービスが実行されている限り、約 40 分ごとに発生します。 

**[同期の詳細]** セクションを使用して進行状況を監視し、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。 このレポートには、Azure AD プロビジョニング サービスによって Tableau Online で実行されたすべてのアクションが記述されます。

Azure AD プロビジョニング ログの見方について詳しくは、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」を参照してください。

## <a name="change-log"></a>ログの変更
* 09/30/2020 - ユーザー用の "authSetting" 属性のサポートを追加。

## <a name="additional-resources"></a>その他の技術情報

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png