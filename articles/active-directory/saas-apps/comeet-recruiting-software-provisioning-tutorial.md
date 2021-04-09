---
title: 'チュートリアル: Azure Active Directory で自動ユーザー プロビジョニング用に Comeet Recruiting Software を構成する | Microsoft Docs'
description: ユーザー アカウントを Comeet Recruiting Software に自動的にプロビジョニングおよびプロビジョニング解除するよう Azure Active Directory を構成する方法について説明します。
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
ms.openlocfilehash: 288d1e6cec8ddcf7d4afe5a35f28a022c2a7be10
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96006714"
---
# <a name="tutorial-configure-comeet-recruiting-software-for-automatic-user-provisioning"></a>チュートリアル: 自動ユーザー プロビジョニング用に Comeet Recruiting Software を構成する

このチュートリアルの目的は、ユーザーまたはグループを Comeet Recruiting Software に自動的にプロビジョニングおよびプロビジョニング解除するよう Azure AD を構成するために、Comeet Recruiting Software と Azure Active Directory (Azure AD) で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [Comeet Recruiting Software テナント](https://www.comeet.co/)
* 管理者アクセス許可がある Comeet Recruiting Software のユーザー アカウント

## <a name="add-comeet-recruiting-software-from-the-gallery"></a>ギャラリーから Comeet Recruiting Software を追加する

Azure AD で自動ユーザー プロビジョニング用に Comeet Recruiting Software を構成する前に、Comeet Recruiting Software を Azure AD アプリケーション ギャラリーからマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Comeet Recruiting Software を追加するには、次の手順に従います。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Comeet Recruiting Software**」と入力し、結果ウィンドウで **[Comeet Recruiting Software]** を選択してから、**[追加]** をクリックしてアプリケーションを追加します。

    ![結果一覧の Comeet Recruiting Software](common/search-new-app.png)

## <a name="assigning-users-to-comeet-recruiting-software"></a>Comeet Recruiting Software へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Comeet Recruiting Software へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定したら、次の手順に従って、これらのユーザーやグループを Comeet Recruiting Software に割り当てることができます。

* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-comeet-recruiting-software"></a>Comeet Recruiting Software にユーザーを割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを Comeet Recruiting Software に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Comeet Recruiting Software にユーザーを割り当てるときは、割り当てダイアログで有効なアプリケーション固有ロール (使用可能な場合) を選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="configuring-automatic-user-provisioning-to-comeet-recruiting-software"></a>Comeet Recruiting Software への自動ユーザー プロビジョニングの構成 

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて Comeet Recruiting Software のユーザーやグループを作成、更新、無効化するよう、Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> Comeet Recruiting Software では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Comeet Recruiting Software シングル サインオンのチュートリアル](comeetrecruitingsoftware-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-comeet-recruiting-software-in-azure-ad"></a>Azure AD で Comeet Recruiting Software 用に自動ユーザー プロビジョニングを構成するには

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Comeet Recruiting Software]** を選択します。

    ![アプリケーションの一覧の Comeet Recruiting Software のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションで、手順 6 で説明するように、Comeet Recruiting Software アカウントの **[テナント URL]** と **[シークレット トークン]** を入力します。

6. [Comeet Recruiting Software 管理コンソール](https://app.comeet.co/)で **[Comeet] > [Settings]\(設定\) > [Authentication]\(認証\) > [Microsoft Azure]** に移動し、**[Secret Token for your company]\(会社のシークレット トークン\)** の値を Azure AD の **[シークレット トークン]** フィールドにコピーします。

    ![Comeet Recruiting Software のプロビジョニング](./media/comeet-recruiting-software-provisioning-tutorial/secret-token-1.png)

7. 手順 5 の各フィールドに値を入力したら、**[テスト接続]** をクリックして、Azure AD が Comeet Recruiting Software に接続できることを確認します。 接続できない場合は、使用中の Comeet Recruiting Software アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![トークン](common/provisioning-testconnection-token.png)

8. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

9. **[保存]** をクリックします。

10. **[マッピング]** セクションで、**[Synchronize Azure Active Directory Users to Comeet]\(Azure Active Directory ユーザーを Comeet に同期する\)** を選択します。

    ![Comeet Recruiting Software のユーザー マッピング](media/comeet-recruiting-software-provisioning-tutorial/user-mappings.png)

11. **[属性マッピング]** セクションで、Azure AD から Comeet Recruiting Software に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新操作で Comeet Recruiting Software のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Comeet Recruiting Software のグループ属性](media/comeet-recruiting-software-provisioning-tutorial/user-mapping-attributes.png)

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Comeet Recruiting Software に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Comeet Recruiting Software にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Comeet Recruiting Software に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="connector-limitations"></a>コネクタの制限事項

* 現時点では、Comeet Recruiting Software ではグループはサポートされていません。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)

