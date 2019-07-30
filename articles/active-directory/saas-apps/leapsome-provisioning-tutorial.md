---
title: チュートリアル:Leapsome を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Leapsome に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
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
ms.date: 06/28/2019
ms.author: jeedes
ms.openlocfilehash: e02deaa29b40e64b63d9afb471717feef78b3cee
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672694"
---
# <a name="tutorial-configure-leapsome-for-automatic-user-provisioning"></a>チュートリアル:Leapsome を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure AD が自動的にユーザーまたはグループを Leapsome にプロビジョニングまたは Leapsome からプロビジョニング解除するように構成するために、Leapsome と Azure Active Directory (Azure AD) で実行される手順を示すことです。

> [!NOTE]
>  このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。
>
> このコネクタは、現在プレビューの段階です。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント。
* [Leapsome](https://www.Leapsome.com/en/pricing) テナント。
* Admin アクセス許可がある Leapsome のユーザー アカウント。

## <a name="assigning-users-to-leapsome"></a>Leapsome へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Leapsome へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Leapsome に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)


## <a name="important-tips-for-assigning-users-to-leapsome"></a>ユーザーを Leapsome に割り当てるときの重要なヒント

* 単一の Azure AD ユーザーを Leapsome に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Leapsome にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。


## <a name="setup-leapsome-for-provisioning"></a>プロビジョニング用に Leapsome を設定する

1. [Leapsome 管理コンソール](https://www.Leapsome.com/app/#/login)にサインインします。 **[Settings] (設定) > [Admin Settings] (管理者設定)** に移動します。

    ![Leapsome 管理コンソール](media/Leapsome-provisioning-tutorial/leapsome-admin-console.png)

2.  **[Integrations] (統合) > [SCIM User provisioning] (SCIM ユーザー プロビジョニング)** に移動します。

    ![Leapsome で SCIM を追加する](media/Leapsome-provisioning-tutorial/leapsome-add-scim.png)

3.  **SCIM 認証トークン**をコピーします。 この値を、Azure portal の Leapsome アプリケーションの [プロビジョニング] タブの [シークレット トークン] フィールドに入力します。

    ![Leapsome でトークンを作成する](media/Leapsome-provisioning-tutorial/leapsome-create-token.png)

## <a name="add-leapsome-from-the-gallery"></a>ギャラリーから Leapsome を追加する

Azure AD で自動ユーザー プロビジョニング用に Leapsome を構成する前に、Azure AD アプリケーション ギャラリーから Leapsome を管理対象の SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Leapsome を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Leapsome**」と入力し、結果ウィンドウで **[Leapsome]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の Leapsome](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-leapsome"></a>Leapsome への自動ユーザー プロビジョニングの構成 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Leapsome のユーザーやグループを作成、更新、無効化する手順について説明します。

> [!TIP]
> Leapsome では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Leapsome シングル サインオンのチュートリアル](Leapsome-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-leapsome-in-azure-ad"></a>Azure AD で Leapsome の自動ユーザー プロビジョニングを構成するには:

1. [Azure Portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Leapsome]** を選択します。

    ![アプリケーションの一覧の Leapsome のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://www.leapsome.com/api/scim`」と入力します。 **[シークレット トークン]** に先ほど取得した**SCIM 認証トークン**の値を入力します。 **[接続テスト]** をクリックして、Azure AD から Leapsome に接続できることを確認します。 接続できない場合は、使用中の Leapsome アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[Save]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Leapsome] (Azure Active Directory ユーザーを Leapsome に同期する)** を選択します。

    ![Leapsome のユーザー マッピング](media/Leapsome-provisioning-tutorial/Leapsome-user-mappings.png)

9. **[属性マッピング]** セクションで、Azure AD から Leapsome に同期されるユーザー属性を確認します。 **[Matching] (照合)** プロパティとして選択されている属性は、更新処理で Leapsome のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Leapsome のユーザー属性](media/Leapsome-provisioning-tutorial/Leapsome-user-attributes.png)

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to Leapsome] (Azure Active Directory グループを Leapsome に同期する)** を選択します。

    ![Leapsome グループ マッピング](media/Leapsome-provisioning-tutorial/Leapsome-group-mappings.png)

11. **[属性マッピング]** セクションで、Azure AD から Leapsome に同期されるグループ属性を確認します。 **[Matching] (照合)** プロパティとして選択されている属性は、更新処理で Leapsome のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Leapsome のグループ属性](media/Leapsome-provisioning-tutorial/Leapsome-group-attributes.png)

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Leapsome に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Leapsome にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Leapsome に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="connector-limitations"></a>コネクタの制限事項

* Leapsome には、一意な **userName** が必要です。
* Leapsome では、勤務先電子メール アドレスのみ保存できます。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)
