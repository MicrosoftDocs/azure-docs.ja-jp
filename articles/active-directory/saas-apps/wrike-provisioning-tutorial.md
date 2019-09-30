---
title: チュートリアル:Wrike を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Wrike に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2019
ms.author: Zhchia
ms.openlocfilehash: f94c12ed355b25e6a10c8d71e176d9bf3bf78758
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70847003"
---
# <a name="tutorial-configure-wrike-for-automatic-user-provisioning"></a>チュートリアル:Wrike を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure AD が自動的にユーザーまたはグループを Wrike にプロビジョニングまたは Wrike からプロビジョニング解除するように構成するために、Wrike と Azure Active Directory (Azure AD) で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [Wrike テナント](https://www.wrike.com/price/)。
* Admin アクセス許可がある Wrike のユーザー アカウント。

## <a name="assign-users-to-wrike"></a>ユーザーを Wrike に割り当てる
Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成し、有効にする前に、Wrike へのアクセスが必要な Azure AD のユーザー、グループ、またはその両方を特定する必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Wrike に割り当てることができます。

* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-wrike"></a>ユーザーを Wrike に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを Wrike に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Wrike にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-wrike-for-provisioning"></a>プロビジョニングする Wrike を設定する

Azure AD での自動ユーザー プロビジョニング用に Wrike を構成する前に、Wrike 上で SCIM プロビジョニングを有効にする必要があります。

1. [Wrike 管理コンソール](https://www.Wrike.com/login/)にサインインします。 お使いのテナント ID に移動します。 **[アプリおよび統合]** を選択します。

    ![Wrike 管理コンソール](media/Wrike-provisioning-tutorial/admin.png)

2.  **[Azure AD]** に移動します それを選択します。

    ![Wrike 管理コンソール](media/Wrike-provisioning-tutorial/Capture01.png)

3.  SCIM を選択します。 **[ベース URL]** をコピーします。

    ![Wrike 管理コンソール](media/Wrike-provisioning-tutorial/Wrike-tenanturl.png)

4. **[API]** をクリックし、 **[Azure SCIM]** を選択します。

    ![Wrike での SCIM の追加](media/Wrike-provisioning-tutorial/Wrike-add-scim.png)

5.  ポップアップが開きます。 前にアカウントの作成で作成したものと同じパスワードを入力します。 

    ![Wrike のトークン作成](media/Wrike-provisioning-tutorial/password.png)

6.  **[シークレット トークン]** をコピーし、Azure AD に貼り付けます。 **[保存]** をクリックし、Wrike でのプロビジョニング設定を完了します。

    ![Wrike のトークン作成](media/Wrike-provisioning-tutorial/Wrike-create-token.png)


## <a name="add-wrike-from-the-gallery"></a>ギャラリーからの Wrike の追加

Azure AD での自動ユーザー プロビジョニング用に Wrike を構成する前に、Azure AD アプリケーション ギャラリーから Wrike をマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Wrike を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Wrike**」と入力し、結果ウィンドウで **[Wrike]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の Wrike](common/search-new-app.png)


## <a name="configuring-automatic-user-provisioning-to-wrike"></a>Wrike への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Wrike のユーザーやグループを作成、更新、無効化する手順について説明します。

> [!TIP]
> Wrike では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Wrike シングル サインオンのチュートリアル](wrike-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-wrike-in-azure-ad"></a>Azure AD で Wrike の自動ユーザー プロビジョニングを構成するには、次の操作を行います。

1. [Azure Portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Wrike]** を選択します。

    ![アプリケーションの一覧の Wrike のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. [管理者資格情報] セクションの **[テナント URL]** と **[シークレット トークン]** に、先ほど取得した **[ベース URL]** と **[Permanent access Token]\(固定アクセス トークン\)** の値をそれぞれ入力します。 **[テスト接続]** をクリックして、Azure AD から Wrike への接続を確保します。 接続できない場合は、使用中の Wrike アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

7. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

8. **[Save]** をクリックします。

9. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Wrike]\(Azure Active Directory ユーザーを Wrike に同期する\)** を選択します。

    ![Wrike のユーザー マッピング](media/Wrike-provisioning-tutorial/Wrike-user-mappings.png)

10. **[属性マッピング]** セクションで、Azure AD から Wrike に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Wrike のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Wrike のユーザー属性](media/Wrike-provisioning-tutorial/Wrike-user-attributes.png)

11. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

12. Wrike に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

13. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Wrike にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

14. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は、後続の同期よりも実行に時間がかかります。 ユーザーやグループのプロビジョニングにかかる時間の詳細については、「[ユーザーをプロビジョニングするにはどのくらいの時間がかかりますか](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)」を参照してください。

**[現在の状態]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Wrike に対して実行されたすべてのアクションが記載されています。 詳細については、「[ユーザー プロビジョニングの状態を確認する](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md)」を参照してください。 Azure AD プロビジョニング ログを読むには、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)
