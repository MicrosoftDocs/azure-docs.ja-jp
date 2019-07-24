---
title: チュートリアル:Zendesk を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Zendesk に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 01d5e4d5-d856-42c4-a504-96fa554baf66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf747fb75ea663d2c64038d73f48adb19d9fb804
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59278583"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>チュートリアル:Zendesk を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure AD が自動的にユーザーまたはグループを Zendesk にプロビジョニングまたは Zendesk からプロビジョニング解除するように構成するために、Replicon と Azure Active Directory (Azure AD) で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [Enterprise プラン](https://www.zendesk.com/product/pricing/)以上の有効な Zendesk テナント
* Admin アクセス許可がある Zendesk のユーザー アカウント

> [!NOTE]
> Azure AD プロビジョニング統合では、Enterprise プラン以上の Zendesk チームで使用できる [Zendesk Rest API](https://developer.zendesk.com/rest_api/docs/core/introduction) が必要です。

## <a name="adding-zendesk-from-the-gallery"></a>ギャラリーからの Zendesk の追加

Azure AD で自動ユーザー プロビジョニング用に Zendesk を構成する前に、Azure AD アプリケーション ギャラリーから Zendesk を管理対象の SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Zendesk を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Zendesk**」と入力し、結果ウィンドウで **[Zendesk]** を選び、**[追加]** をクリックしてアプリケーションを追加します。

    ![結果一覧の Zendesk](common/search-new-app.png)

## <a name="assigning-users-to-zendesk"></a>Zendesk へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Zendesk へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Zendesk に割り当てることができます。

* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zendesk"></a>ユーザーを Zendesk に割り当てる際の重要なヒント

* Zendesk のロールは、Azure portal UI で今すぐ、自動的かつ動的に設定されます。 Zendesk のロールをユーザーに割り当てる前に、必ず Zendesk との初期同期を完了して、お使いの Zendesk テナントの最新ロールを取得してください。

* 単一の Azure AD ユーザーを Zendesk に割り当てて、初期自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 テストが成功すれば、後でユーザーやグループを追加で割り当てられます。
  
* 単一の Azure AD ユーザーを Zendesk に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Zendesk にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="configuring-automatic-user-provisioning-to-zendesk"></a>Zendesk への自動ユーザー プロビジョニングの構成 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Zendesk のユーザーやグループを作成、更新、無効化する手順について説明します。

> [!TIP]
> Zendesk では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Zendesk シングル サインオンのチュートリアル](zendesk-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Azure AD で Zendesk の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインし、**[エンタープライズ アプリケーション]**、**[すべてのアプリケーション]**、**[Zendesk]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Zendesk]** を選択します。

    ![アプリケーションの一覧の Zendesk のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![Zendesk のプロビジョニング](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![Zendesk のプロビジョニング](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. **[管理者資格情報]** セクションに、Zendesk アカウントの **[管理ユーザー名]**、**[シークレット トークン]**、**[ドメイン]** を入力します。 これらの値の例を次に示します。

   * **[管理ユーザー名]** フィールドには、Zendesk テナントの管理者アカウントのユーザー名を入力します。 例: admin@contoso.com.

   * **[シークレット トークン]** フィールドに、手順 6 で説明されているシークレット トークンを設定します。

   * **[ドメイン]** フィールドに、Zendesk テナントのサブドメインを設定します。
     例: [https://my-tenant.zendesk.com](`https://my-tenant.zendesk.com`) のテナント URL があるアカウントの場合、サブドメインは **my-tenant** になります。

6. Zendesk アカウントの**シークレット トークン** は、**[管理者] > [API] > [設定]** に配置されています。
   **[Token Access]\(トークン アクセス\)** が **[有効]** に設定されていることを確認します。

    ![Zendesk のプロビジョニング](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Zendesk のプロビジョニング](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. 手順 5 の各フィールドに値を入力したら、**[テスト接続]** をクリックして、Azure AD が Zendesk に接続できることを確認します。 接続できない場合は、使用中の Zendesk アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![Zendesk のプロビジョニング](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、**[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![Zendesk のプロビジョニング](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. **[Save]** をクリックします。

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Zendesk]\(Azure Active Directory ユーザーを Zenesk に同期する\)** を選びます。

    ![Zendesk のプロビジョニング](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. **[属性マッピング]** セクションで、Azure AD から Zendesk に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Zendesk のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Zendesk のプロビジョニング](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to ZenDesk]\(Azure Active Directory グループを ZenDesk に同期する\)** を選択します。

    ![Zendesk のプロビジョニング](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. **[属性マッピング]** セクションで、Azure AD から Zendesk に同期されるグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新操作で Zendesk のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Zendesk のプロビジョニング](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

15. Zendesk に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![Zendesk のプロビジョニング](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Zendesk にプロビジョニングするユーザーやグループを定義します。

    ![Zendesk のプロビジョニング](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. プロビジョニングの準備ができたら、**[保存]** をクリックします。

    ![Zendesk のプロビジョニング](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

これにより、**[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Zendesk に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="connector-limitations"></a>コネクタの制限事項

* Zendesk は、エージェントの役割のみを持つユーザーのグループの使用をサポートしています。 詳しくは、[Zendesk のドキュメント](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups)をご覧ください。

* カスタム ロールがユーザーやグループに割り当てられると、Azure AD の自動ユーザー プロビジョニング サービスも既定のロールを**エージェント**に割り当てます。 **エージェント**のみにカスタム ロールを割り当てることができます。 詳細については、この [Zendesk API ドキュメント](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests)を参照してください。  

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
