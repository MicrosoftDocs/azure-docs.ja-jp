---
title: チュートリアル:Foodee を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Foodee に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 171a1141670e55814474390c59ae8d514491edbd
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088088"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>チュートリアル:Foodee を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure Active Directory (Azure AD) が自動的にユーザーまたはグループを Foodee にプロビジョニングまたは Foodee からプロビジョニング解除するように構成するために、Foodee と Azure AD で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント。
* [Foodee テナント](https://www.food.ee/about/)
* Admin アクセス許可がある Foodee のユーザー アカウント。

## <a name="assigning-users-to-foodee"></a>Foodee へのユーザーの割り当て 

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Foodee へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Foodee に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-foodee"></a>ユーザーを Foodee に割り当てる際の重要なヒント 

* 単一の Azure AD ユーザーを Foodee に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Foodee にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="setup-foodee-for-provisioning"></a>プロビジョニングのために Foodee を設定する

Azure AD での自動ユーザー プロビジョニング用に Foodee を構成する前に、Foodee 上で SCIM プロビジョニングを有効にする必要があります。

1. [Foodee](https://www.food.ee/login/) にログインします。 **テナント ID** をクリックします。

    ![Foodee](media/Foodee-provisioning-tutorial/tenant.png)

2. [エンタープライズ ポータル]で、 **[シングル サインオン]** を選択します。

    ![Foodee](media/Foodee-provisioning-tutorial/scim.png)

3. **API トークン**をコピーします。 これらの値は、Azure portal で Foodee アプリケーションの [プロビジョニング] タブにある **[シークレット トークン]** フィールドに入力します。

    ![Foodee](media/Foodee-provisioning-tutorial/token.png)


## <a name="add-foodee-from-the-gallery"></a>ギャラリーからの Foodee の追加

Azure AD で自動ユーザー プロビジョニング用に Foodee を構成するには、Azure AD アプリケーション ギャラリーから Foodee をマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Foodee を追加するには、次の手順を実行します**。

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Foodee**」と入力し、結果パネルで **[Foodee]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の Foodee](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-foodee"></a>Foodee への自動ユーザー プロビジョニングの構成  

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Foodee のユーザーやグループを作成、更新、無効化する手順について説明します。

> [!TIP]
> Foodee では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Foodee シングル サインオンのチュートリアル](Foodee-tutorial.md)に関するページで説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-foodee-in-azure-ad"></a>Azure AD で Foodee の自動ユーザー プロビジョニングを構成するには、次の操作を行います。

1. [Azure Portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Foodee]** を選択します。

    ![アプリケーションの一覧の Foodee のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. [管理者資格情報] セクションの **[テナント URL]** および **[シークレット トークン]** に、先ほど取得した ` https://concierge.food.ee/scim/v2` と **API トークン**の値をそれぞれ入力します。 **[テスト接続]** をクリックして、Azure AD から Foodee への接続を確認します。 接続できない場合は、使用中の Foodee アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[Save]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Foodee]\(Azure Active Directory ユーザーを Foodee に同期する\)** を選択します。

    ![Foodee のユーザー マッピング](media/Foodee-provisioning-tutorial/usermapping.png)

9. **[属性マッピング]** セクションで、Azure AD から Foodee に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Foodee のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Foodee のユーザー属性](media/Foodee-provisioning-tutorial/userattribute.png)

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to Foodee]\(Azure Active Directory グループを Foodee に同期する\)** を選択します。

    ![Foodee のユーザー属性](media/Foodee-provisioning-tutorial/groupmapping.png)

11. **[属性マッピング]** セクションで、Azure AD から Foodee に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Foodee のグループ アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Foodee のユーザー属性](media/Foodee-provisioning-tutorial/groupattribute.png)

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Foodee に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Foodee にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は、後続の同期よりも実行に時間がかかります。 ユーザーやグループのプロビジョニングにかかる時間の詳細については、「[ユーザーをプロビジョニングするにはどのくらいの時間がかかりますか](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)」を参照してください。

**[現在の状態]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Foodee に対して実行されたすべてのアクションが記載されています。 詳細については、「[ユーザー プロビジョニングの状態を確認する](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md)」を参照してください。 Azure AD プロビジョニング ログを読むには、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」を参照してください。



## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)
