---
title: チュートリアル:SpaceIQ を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを SpaceIQ に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: a5bd3c39c293880e31ebe63f85b3814725f6027a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96347295"
---
# <a name="tutorial-configure-spaceiq-for-automatic-user-provisioning"></a>チュートリアル:SpaceIQ を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure Active Directory (Azure AD) が、ユーザー、グループ、またはその両方を SpaceIQ に対して自動的にプロビジョニングおよびプロビジョニング解除するよう構成するために、SpaceIQ と Azure AD で実行する手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [SpaceIQ テナント](https://spaceiq.com/)
* 管理者アクセス許可がある SpaceIQ のユーザー アカウント。

## <a name="assigning-users-to-spaceiq"></a>SpaceIQ へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、SpaceIQ へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを SpaceIQ に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-spaceiq"></a>ユーザーを SpaceIQ に割り当てる際の重要なヒント

* 1 名の Azure AD ユーザーを SpaceIQ に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* SpaceIQ にユーザーを割り当てるときは、割り当てダイアログで、有効なアプリケーション固有ロール (使用可能な場合) を選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-spaceiq-for-provisioning"></a>プロビジョニングのために SpaceIQ を設定する

1. [SpaceIQ 管理コンソール](https://main.spaceiq.com/login/)にサインインします。 画面の右上隅にあるドロップダウン メニューからそれを選択して、 **[設定]** に移動します。

    ![SpaceIQ 管理コンソール](media/spaceiq-provisioning-tutorial/admin.png)

2.  **[設定]** ページで、 **[Third Party Integrations]\(サードパーティの統合\)** を選択します。

    ![SpaceIQ での SCIM の追加](media/spaceiq-provisioning-tutorial/thirdparty.png)

3.  **[Provisioning and SSO]\(プロビジョニングと SSO\)** タブに移動します。**Azure** タイルを検索します。 **[Activate]\(アクティブ化\)** をクリックします。

    ![SpaceIQ プロビジョニングと SSO](media/spaceiq-provisioning-tutorial/provisioning.png)

    ![SpaceIQ による Azure のアクティブ化 ](media/spaceiq-provisioning-tutorial/azure.png)

3.  **SCIM ベアラー トークン** をコピーします。 この値を、Azure portal で SpaceIQ アプリケーションの [プロビジョニング] タブ内の [シークレット トークン] フィールドに入力します。 **[アクティブ化]** をクリックします。

    ![SpaceIQ でのトークンの作成](media/spaceiq-provisioning-tutorial/token.png)

## <a name="add-spaceiq-from-the-gallery"></a>ギャラリーから SpaceIQ を追加する

Azure AD での自動ユーザー プロビジョニング用に SpaceIQ を構成する前に、Azure AD アプリケーション ギャラリーから SpaceIQ をマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから SpaceIQ を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**SpaceIQ**」と入力し、結果パネルで **[SpaceIQ]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の SpaceIQ](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-spaceiq"></a>SpaceIQ への自動ユーザー プロビジョニングの構成 

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて SpaceIQ のユーザーやグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> SpaceIQ で SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[SpaceIQ シングル サインオンのチュートリアル](./spaceiq-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-spaceiq-in-azure-ad"></a>Azure AD で SpaceIQ の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[SpaceIQ]** を選択します。

    ![アプリケーションの一覧の SpaceIQ のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://api.spaceiq.com/scim`」と入力します。 **[シークレット トークン]** に先ほど取得した **SCIM 認証トークン** の値を入力します。 **[接続テスト]** をクリックして、Azure AD から SpaceIQ に接続できることを確認します。 接続できない場合は、使用中の SpaceIQ アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to SpaceIQ]\(Azure Active Directory ユーザーを SpaceIQ に同期する\)** を選択します。

    ![SpaceIQ のユーザー マッピング](media/spaceiq-provisioning-tutorial/usermapping.png)

9. **[属性マッピング]** セクションで、Azure AD から SpaceIQ に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で SpaceIQ のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![SpaceIQ ユーザーの属性](media/spaceiq-provisioning-tutorial/userattributes.png)

11. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

12. SpaceIQ に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

13. **[設定]** セクションの **[スコープ]** で目的の値を選択して、SpaceIQ にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

14. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって SpaceIQ に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)