---
title: チュートリアル:Flock を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Flock に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 01c3f6429d2a5c8443ac128d763033dc8c53cbc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94359410"
---
# <a name="tutorial-configure-flock-for-automatic-user-provisioning"></a>チュートリアル:Flock を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure AD が自動的にユーザーまたはグループを Flock にプロビジョニングまたは Flock からプロビジョニング解除するように構成するために、Flock と Azure Active Directory (Azure AD) で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント。
* [Flock テナント](https://flock.com/pricing/)
* Admin アクセス許可がある Flock のユーザー アカウント。

## <a name="assigning-users-to-flock"></a>Flock へのユーザーの割り当て 

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Flock へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Flock に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-flock"></a>ユーザーを Flock に割り当てる際の重要なヒント 

* 単一の Azure AD ユーザーを Flock に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Flock にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="setup-flock--for-provisioning"></a>プロビジョニングのために Flock を設定する

Azure AD での自動ユーザー プロビジョニング用に Flock を構成する前に、Flock 上で SCIM プロビジョニングを有効にする必要があります。

1. [Flock](https://web.flock.com/?) にログインします。 **[Settings]\(設定\) アイコン** >  **[Manage your team]\(チームの管理\)** をクリックします。

    :::image type="content" source="media/flock-provisioning-tutorial/icon.png" alt-text="Flock Web サイトのスクリーンショット。設定アイコンが強調表示されていて、そのショートカット メニューが表示されています。そのメニューで、[チームを管理する] が強調表示されています。" border="false":::

2. **[Auth and Provisioning]\(認証とプロビジョニング\)** を選択します。

    :::image type="content" source="media/Flock-provisioning-tutorial/auth.png" alt-text="Flock Web サイトのメニューのスクリーンショット。[認証とプロビジョニング] 項目が強調表示されています。" border="false":::

3. **[API Token]\(API トークン\)** をコピーします。 これらの値は、Azure portal で Flock アプリケーションの [プロビジョニング] タブにある **[シークレット トークン]** フィールドに入力します。

    :::image type="content" source="media/Flock-provisioning-tutorial/provisioning.png" alt-text="Flock Web サイトの [プロビジョニング] タブのスクリーンショット。[API トークン] の下に、値が強調表示されています。トークンの横に [トークンのコピー] ボタンがあります。" border="false":::


## <a name="add-flock--from-the-gallery"></a>ギャラリーから Flock を追加する

Azure AD で自動ユーザー プロビジョニング用に Flock を構成するには、Azure AD アプリケーション ギャラリーから Flock をマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Flock を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Flock**」と入力し、結果ウィンドウで **Flock** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の Flock](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-flock"></a>Flock への自動ユーザー プロビジョニングの構成  

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Flock のユーザーやグループを作成、更新、無効化する手順について説明します。

> [!TIP]
> Flock では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Flock シングル サインオンのチュートリアル](Flock-tutorial.md)に関するページで説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-flock--in-azure-ad"></a>Azure AD で Flock の自動ユーザー プロビジョニングを構成するには、次の操作を行います。

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Flock]** を選択します。

    ![アプリケーションの一覧の Flock のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. [管理者資格情報] セクションの **[テナント URL]** および **[シークレット トークン]** に、先ほど取得した `https://api.flock-staging.com/v2/scim` と **API トークン** の値をそれぞれ入力します。 **[テスト接続]** をクリックして、Azure AD から Flock への接続を確保します。 接続できない場合は、使用中の Flock アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Flock]\(Azure Active Directory ユーザーを Flock に同期する\)** を選択します。

    ![Flock のユーザー マッピング](media/flock-provisioning-tutorial/usermapping.png)

9. **[属性マッピング]** セクションで、Azure AD から Flock に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Flock のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Flock ユーザーの属性](media/flock-provisioning-tutorial/userattribute.png)

11. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

12. Flock に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

13. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Flock にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

14. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は、後続の同期よりも実行に時間がかかります。 ユーザーやグループのプロビジョニングにかかる時間の詳細については、「[ユーザーをプロビジョニングするにはどのくらいの時間がかかりますか](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)」を参照してください。

**[現在の状態]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Flock に対して実行されたすべてのアクションが記載されています。 詳細については、「[ユーザー プロビジョニングの状態を確認する](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)」を参照してください。 Azure AD プロビジョニング ログを読むには、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」を参照してください。



## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)