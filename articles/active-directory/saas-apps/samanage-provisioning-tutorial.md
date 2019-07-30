---
title: チュートリアル:Samanage を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Samanage に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 67cfe5a26740837508ea3a3e76295a896c3cc107
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670918"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>チュートリアル:Samanage を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Samanage に対するユーザーとグループのプロビジョニングとプロビジョニング解除を自動的に実行するように Azure AD を構成するために、Samanage と Azure Active Directory (Azure AD) で実行する手順を示します。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービス上に構築されるコネクタについて説明します。 このサービスで実行されること、しくみ、およびよく寄せられるについては、「[Azure Active Directory によるサービスとしてのソフトウェア (SaaS) アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、以下を所有していることを前提としています。

* Azure AD テナント。
* Professional パッケージを含む [Samanage テナント](https://www.samanage.com/pricing/)。
* 管理者アクセス許可がある Samanage のユーザー アカウント

> [!NOTE]
> Azure AD プロビジョニング統合は、[Samanage Rest API](https://www.samanage.com/api/) に依存しています。 この API は、Samanage の開発者が Professional パッケージを含むアカウントで使用できます。

## <a name="add-samanage-from-the-azure-marketplace"></a>Azure Marketplace から Samanage を追加する

Azure AD での自動ユーザー プロビジョニング用に Samanage を構成する前に、Samanage を Azure Marketplace から管理対象の SaaS アプリケーションの一覧に追加する必要があります。

Marketplace から Samanage を追加するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のアイコン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部の **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Samanage**」と入力し、結果パネルで **[Samanage]** を選択します。 アプリケーションを追加するには、 **[追加]** を選択します。

    ![結果一覧の Samanage](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Samanage にユーザーを割り当てる

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD でアプリケーションに割り当てられているユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Samanage にアクセスする必要がある Azure AD のユーザーまたはグループを決定しておく必要があります。 これらのユーザーまたはグループを Samanage に割り当てるには、「[エンタープライズ アプリにユーザーまたはグループを割り当てる](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)」の指示に従います。

### <a name="important-tips-for-assigning-users-to-samanage"></a>ユーザーを Samanage に割り当てる際の重要なヒント

*    現在、Samanage のロールは、Azure portal UI で自動的かつ動的に設定されます。 Samanage のロールをユーザーに割り当てる前に、必ず Samanage との初期同期を完了して、お使いの Samanage テナントの最新ロールを取得してください。

*    単一の Azure AD ユーザーを Samanage に割り当てて、初期の自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 テストが成功した後で、追加のユーザーとグループを割り当てることができます。

*    Samanage にユーザーを割り当てるとき、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログ ボックスで選択します。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Samanage への自動ユーザー プロビジョニングを構成する

このセクションでは、Azure AD プロビジョニング サービスを構成する手順を説明します。 これを使用して、Azure AD でのユーザーまたはグループの割り当てに基づいて、Samanage でのユーザーまたはグループの作成、更新、および無効化を行います。

> [!TIP]
> Samanage に対する SAML ベースのシングル サインオンを有効にすることもできます。 [Samanage のシングル サインオンに関するチュートリアル](samanage-tutorial.md)の手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Azure AD で Samanage の自動ユーザー プロビジョニングを構成する

1. [Azure Portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]**  >  **[Samanage]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Samanage]** を選択します。

    ![アプリケーションの一覧の Samanage のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![Samanage のプロビジョニング](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![Samanage のプロビジョニング モード](./media/samanage-provisioning-tutorial/ProvisioningCredentials.png)

5. **[管理者資格情報]** セクションに、Samanage アカウントの管理ユーザー名と管理パスワードを入力します。 これらの値の例を次に示します。

   * **[管理ユーザー名]** ボックスに、Samanage テナントの管理者アカウントのユーザー名を入力します。 例: admin@contoso.com。

   * **[管理パスワード]** ボックスに、管理者ユーザー名に対応する管理者アカウントのパスワードを入力します。

6. 手順 5 に示されているボックスに入力したら、 **[テスト接続]** を選択して、Azure AD が Samanage に接続できることを確認します。 接続できない場合は、使用中の Samanage アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![Samanage のテスト接続](./media/samanage-provisioning-tutorial/TestConnection.png)

7. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループのメール アドレスを入力します。 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![Samanage の通知メール](./media/samanage-provisioning-tutorial/EmailNotification.png)

8. **[保存]** を選択します。

9. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Samanage]\(Azure Active Directory ユーザーを Samanage に同期する\)** を選びます。

    ![Samanage のユーザー同期](./media/samanage-provisioning-tutorial/UserMappings.png)

10. **[属性マッピング]** セクションで、Azure AD から Samanage に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Samanage のユーザー アカウントとの照合に使用されます。 すべての変更を保存するために、 **[保存]** を選択します。

    ![Samanage の一致するユーザー属性](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

11. グループ マッピングを有効にするには、 **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to Samanage]\(Azure Active Directory グループを Samanage に同期する\)** を選択します。

    ![Samanage のグループ同期](./media/samanage-provisioning-tutorial/GroupMappings.png)

12. **[有効化]** を **[はい]** に設定して、グループを同期します。 **[属性マッピング]** セクションで、Azure AD から Samanage に同期されるグループ属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Samanage のユーザー アカウントとの照合に使用されます。 すべての変更を保存するために、 **[保存]** を選択します。

    ![Samanage の一致するグループ属性](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の手順を参照してください。

14. Samanage に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![Samanage のプロビジョニング状態](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

15. Samanage にプロビジョニングするユーザーまたはグループを定義します。 **[設定]** セクションで、 **[スコープ]** として指定する値を選択します。 **[すべてのユーザーとグループを同期する]** オプションを選択するときは、「コネクタの制限事項」セクションの説明に従って制限事項を検討します。

    ![Samanage の [スコープ]](./media/samanage-provisioning-tutorial/ScopeSync.png)

16. プロビジョニングの準備ができたら、 **[保存]** を選択します。

    ![Samanage の [保存]](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


この操作によって、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーまたはグループの初期同期が開始されます。 初期同期は、以降の同期よりも実行に時間がかかります。 それらは、Azure AD プロビジョニング サービスが実行されている限り、約 40 分ごとに発生します。 

**[同期の詳細]** セクションを使用して進行状況を監視し、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。 このレポートには、Azure AD プロビジョニング サービスによって Samanage で実行されたすべてのアクションが記述されます。

Azure AD プロビジョニング ログの見方について詳しくは、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」を参照してください。

## <a name="connector-limitations"></a>コネクタの制限事項

**[すべてのユーザーとグループを同期する]** オプションを選択し、Samanage の**ロール**属性に値を構成した場合、 **[null の場合の既定値 (オプション)]** ボックスの値は次の形式で表現する必要があります。

- {"displayName":"role"}。この role は使用する既定値です。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
