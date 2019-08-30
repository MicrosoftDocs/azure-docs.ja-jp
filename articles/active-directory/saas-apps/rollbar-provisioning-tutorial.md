---
title: チュートリアル:Rollbar を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: ユーザー アカウントを Rollbar に対して自動的にプロビジョニングおよびプロビジョニング解除するように Azure Active Directory を構成する方法について説明します。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: d737aa16-8ab4-4c0c-a68b-2911623b41eb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 99ab045a2b670beb7c74f84119918dccd896f848
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707464"
---
# <a name="tutorial-configure-rollbar-for-automatic-user-provisioning"></a>チュートリアル:Rollbar を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure AD が自動的にユーザーまたはグループを Rollbar にプロビジョニングまたは Rollbar からプロビジョニング解除するように構成するために、Rollbar と Azure Active Directory (Azure AD) で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント。
* Enterprise プランがある [Rollbar テナント](https://rollbar.com/pricing/)。
* Admin アクセス許可がある Rollbar のユーザー アカウント。

## <a name="assigning-users-to-rollbar"></a>Rollbar へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Rollbar へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Rollbar に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-rollbar"></a>ユーザーを Rollbar に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを Rollbar に割り当て、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Rollbar にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="setup-rollbar-for-provisioning"></a>プロビジョニングのために Rollbar をセットアップする

Azure AD での自動ユーザー プロビジョニング用に Rollbar を構成する前に、Rollbar で SCIM プロビジョニングを有効にする必要があります。

1. [Rollbar 管理コンソール](https://rollbar.com/login/)にサインインします。 **[アカウント設定]** をクリックします。

    ![Rollbar 管理コンソール](media/rollbar-provisioning-tutorial/image00.png)

2. **[Rollbar Tenant Name] (Rollbar テナント名) > [Account Access Tokens] (アカウント アクセス トークン)** に移動します。

    ![Rollbar 管理コンソール](media/rollbar-provisioning-tutorial/account.png)

3. **[SCIM]** の値をコピーします。 この値を、Azure portal で Rollbar アプリケーションの [プロビジョニング] タブ内の [シークレット トークン] フィールドに入力します。

    ![Rollbar 管理コンソール](media/rollbar-provisioning-tutorial/scim.png)

## <a name="add-rollbar-from-the-gallery"></a>ギャラリーから Rollbar を追加する

Azure AD を使用した自動ユーザー プロビジョニング用に Rollbar を構成するには、Azure AD アプリケーション ギャラリーから管理対象の SaaS アプリケーションの一覧に Rollbar を追加する必要があります。

**Azure AD アプリケーション ギャラリーから Rollbar を追加するには、次の手順を行います。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Rollbar**」と入力し、結果パネルで **[Rollbar]** を選択してから、 **[追加]** をクリックしてアプリケーションを追加します。

    ![結果一覧の Rollbar](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-rollbar"></a>Rollbar への自動ユーザー プロビジョニングの構成 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Rollbar のユーザーやグループを作成、更新、無効化する手順について説明します。

> [!TIP]
> Rollbar では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Rollbar シングル サインオンのチュートリアル](rollbar-tutorial.md)に関するページで説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-rollbar-in-azure-ad"></a>Azure AD で Rollbar の自動ユーザー プロビジョニングを構成するには

1. [Azure Portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Rollbar]** を選択します。

    ![アプリケーションの一覧の [Rollbar] リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションで、以前に取得した**アカウント アクセス トークン**の値を **[シークレット トークン]** に入力します。 **[テスト接続]** をクリックして、Azure AD から Rollbar への接続を確保します。 接続できない場合は、使用中の Rollbar アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![Rollbar 管理コンソール](media/rollbar-provisioning-tutorial/admin.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[Save]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Rollbar]\(Azure Active Directory ユーザーを Rollbar に同期する\)** を選択します。

    ![Rollbar ユーザー マッピング](media/rollbar-provisioning-tutorial/usermapping.png)

9. **[属性マッピング]** セクションで、Azure AD から Rollbar に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Rollbar のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Rollbar ユーザーの属性](media/rollbar-provisioning-tutorial/userattribute.png)

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to Rollbar]\(Azure Active Directory グループを Rollbar に同期する\)** を選択します。

    ![Rollbar グループ マッピング](media/rollbar-provisioning-tutorial/groupmapping.png)

11. **[属性マッピング]** セクションで、Azure AD から Rollbar に同期されるグループ属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Rollbar のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Rollbar グループ属性](media/rollbar-provisioning-tutorial/groupattribute.png)

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Rollbar に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Rollbar にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

    これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Rollbar に対して実行されたすべてのアクションが記載されています。

    Azure AD プロビジョニング ログの読み方について詳しくは、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。
    
## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)
