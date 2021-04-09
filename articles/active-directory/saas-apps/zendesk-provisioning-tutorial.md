---
title: 'チュートリアル: Zendesk を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs'
description: Azure Active Directory を構成して、ユーザー アカウントを Zendesk に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.openlocfilehash: 620dd8fd586352ebeaf097a8f870a606f8e06c01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94359716"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>チュートリアル: Zendesk を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Zendesk に対するユーザーとグループのプロビジョニングとプロビジョニング解除を自動的に実行するように Azure AD を構成するために、Zendesk と Azure Active Directory (Azure AD) で実行する手順を示します。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービス上に構築されるコネクタについて説明します。 このサービスで実行されること、しくみ、およびよく寄せられるについては、「[Azure Active Directory によるサービスとしてのソフトウェア (SaaS) アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、以下を所有していることを前提としています。

* Azure AD テナント。
* Professional プラン以上が有効な Zendesk テナント。
* 管理者アクセス許可がある Zendesk のユーザー アカウント。

## <a name="add-zendesk-from-the-azure-marketplace"></a>Azure Marketplace からの Zendesk の追加

Azure AD での自動ユーザー プロビジョニング用に Zendesk を構成する前に、Zendesk を Azure Marketplace から管理対象の SaaS アプリケーションの一覧に追加する必要があります。

Marketplace から Zendesk を追加するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のアイコン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部の **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Zendesk**」と入力し、結果パネルで **[Zendesk]** を選択します。 アプリケーションを追加するには、 **[追加]** を選択します。

    ![結果一覧の Zendesk](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>ユーザーを Zendesk に割り当てる

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD でアプリケーションに割り当てられているユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Zendesk にアクセスする必要がある Azure AD のユーザーまたはグループを決定しておく必要があります。 これらのユーザーまたはグループを Zendesk に割り当てるには、「[エンタープライズ アプリにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)」の指示に従います。

### <a name="important-tips-for-assigning-users-to-zendesk"></a>ユーザーを Zendesk に割り当てる際の重要なヒント

* 現在、Zendesk のロールは、Azure portal UI で自動的かつ動的に設定されます。 Zendesk のロールをユーザーに割り当てる前に、必ず Zendesk との初期同期を完了して、お使いの Zendesk テナントの最新ロールを取得してください。

* 単一の Azure AD ユーザーを Zendesk に割り当てて、初期の自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 テストが成功した後で、追加のユーザーまたはグループを割り当てることができます。

* Zendesk にユーザーを割り当てるとき、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログ ボックスで選択します。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Zendesk への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD プロビジョニング サービスを構成する手順を説明します。 これを使用して、Azure AD でのユーザーまたはグループの割り当てに基づいて、Zendesk でのユーザーまたはグループの作成、更新、および無効化を行います。

> [!TIP]
> Zendesk に対する SAML ベースのシングル サインオンを有効にすることもできます。 [Zendesk のシングル サインオンに関するチュートリアル](zendesk-tutorial.md)の手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Azure AD で Zendesk の自動ユーザー プロビジョニングを構成する

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]**  >  **[Zendesk]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Zendesk]** を選択します。

    ![アプリケーションの一覧の Zendesk のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![Zendesk のプロビジョニング](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![Zendesk のプロビジョニング モード](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. **[管理者資格情報]** セクションに、Zendesk アカウントの管理ユーザー名、シークレット トークン、ドメインを入力します。 これらの値の例を次に示します。

   * **[管理ユーザー名]** ボックスに、Zendesk テナントの管理者アカウントのユーザー名を入力します。 たとえば admin@contoso.com です。

   * **[シークレット トークン]** ボックスに、手順 6 で説明されているシークレット トークンを入力します。

   * **[ドメイン]** ボックスに、Zendesk テナントのサブドメインを入力します。 たとえば、テナント URL が `https://my-tenant.zendesk.com` のアカウントの場合、サブドメインは **my-tenant** になります。

6. Zendesk アカウントのシークレット トークンは、 **[管理者]**  >  **[API]**  >  **[設定]** に配置されています。 **[Token Access]\(トークン アクセス\)** が **[有効]** に設定されていることを確認します。

    ![Zendesk の管理者設定](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Zendesk のシークレット トークン](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. 手順 5 に示されているボックスに入力したら、**[テスト接続]** を選択して、Azure AD が Zendesk に接続できることを確認します。 接続できない場合は、使用中の Zendesk アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![Zendesk のテスト接続](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループのメール アドレスを入力します。 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![Zendesk の通知用メール](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. **[保存]** を選択します。

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Zendesk]\(Azure Active Directory ユーザーを Zenesk に同期する\)** を選びます。

    ![Zendesk のユーザー同期](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. **[属性マッピング]** セクションで、Azure AD から Zendesk に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Zendesk のユーザー アカウントとの照合に使用されます。 すべての変更を保存するために、 **[保存]** を選択します。

    ![Zendesk の一致するユーザー属性](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to Zendesk]\(Azure Active Directory グループを Zendesk に同期する\)** を選択します。

    ![Zendesk のグループの同期](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. **[属性マッピング]** セクションで、Azure AD から Zendesk に同期されるグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新操作で Zendesk のグループとの照合に使用されます。 すべての変更を保存するために、 **[保存]** を選択します。

    ![Zendesk の一致するグループ属性](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の手順を参照してください。

15. Zendesk に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![Zendesk のプロビジョニング状態](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Zendesk にプロビジョニングするユーザーまたはグループを定義します。 **[設定]** セクションで、 **[スコープ]** として指定する値を選択します。

    ![Zendesk の [スコープ]](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. プロビジョニングの準備ができたら、 **[保存]** を選択します。

    ![Zendesk の [保存]](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

この操作によって、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーまたはグループの初期同期が開始されます。 初期同期は、以降の同期よりも実行に時間がかかります。 それらは、Azure AD プロビジョニング サービスが実行されている限り、約 40 分ごとに発生します。 

**[同期の詳細]** セクションを使用して進行状況を監視し、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。 このレポートには、Azure AD プロビジョニング サービスによって Zendesk で実行されたすべてのアクションが記述されます。

Azure AD プロビジョニング ログの見方について詳しくは、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」を参照してください。

## <a name="connector-limitations"></a>コネクタの制限事項

* Zendesk は、**エージェント** の役割のみを持つユーザーのグループの使用をサポートしています。 詳細については、[Zendesk のドキュメント](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups)を参照してください。

* カスタム ロールがユーザーやグループに割り当てられると、Azure AD の自動ユーザー プロビジョニング サービスも既定のロールを **エージェント** に割り当てます。 エージェントのみにカスタム ロールを割り当てることができます。 詳細については、[Zendesk API のドキュメント](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests)を参照してください。 

## <a name="additional-resources"></a>その他の技術情報

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
