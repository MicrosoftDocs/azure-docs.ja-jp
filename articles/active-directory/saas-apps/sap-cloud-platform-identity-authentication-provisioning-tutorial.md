---
title: チュートリアル:SAP Cloud Platform Identity Authentication を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを SAP Cloud Platform Identity Authentication に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: 419f25ee3df471bc2fc4526254f5677b8bd71856
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96342741"
---
# <a name="tutorial-configure-sap-cloud-platform-identity-authentication-for-automatic-user-provisioning"></a>チュートリアル:SAP Cloud Platform Identity Authentication を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure Active Directory (Azure AD) がユーザーまたはグループを SAP Cloud Platform Identity Authentication に自動的にプロビジョニングまたは SAP Cloud Platform Identity Authentication からプロビジョニング解除するように構成するために、SAP Cloud Platform Identity Authentication と Azure AD で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [SAP Cloud Platform Identity Authentication テナント](https://cloudplatform.sap.com/pricing.html)
* 管理者のアクセス許可がある SAP Cloud Platform Identity Authentication のユーザー アカウント。

## <a name="assigning-users-to-sap-cloud-platform-identity-authentication"></a>SAP Cloud Platform Identity Authentication にユーザーを割り当てる

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、SAP Cloud Platform Identity Authentication へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを SAP Cloud Platform Identity Authentication に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-sap-cloud-platform-identity-authentication"></a>SAP Cloud Platform Identity Authentication にユーザーを割り当てる際の重要なヒント

* 1 名の Azure AD ユーザーを SAP Cloud Platform Identity Authentication に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* SAP Cloud Platform Identity Authentication にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="setup-sap-cloud-platform-identity-authentication-for-provisioning"></a>プロビジョニングのための SAP Cloud Platform Identity Authentication のセットアップ

1. [SAP Cloud Platform Identity Authentication 管理コンソール](https://sapmsftintegration.accounts.ondemand.com/admin) にサインインします。 **[Users & Authorizations]\(ユーザー&認可\) > [Administrators]\(管理者\)** に移動します。

    ![SAP Cloud Platform Identity Authentication 管理コンソール](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/adminconsole.png)

2.  新しい管理者を一覧に追加するには、左側パネルの **[+ 追加]** ボタンを押します。 **[システムの追加]** を選択し、システムの名前を入力します。   

> [!NOTE]
> SAP Cloud Platform Identity Authentication の管理者ユーザーの種類は **System** にする必要があります。 通常の管理者ユーザーを作成すると、プロビジョニング中、"*無許可*" エラーが発生することがあります。   

3.  [Configure Authorizations]\(承認の構成\) で、 **[Manage Users]\(ユーザーの管理\)**  および **[Manage Groups]\(グループの管理\)** のトグルボタンをオンにします。

    ![SAP Cloud Platform Identity Authentication の SCIM の追加](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/configurationauth.png)

4. アカウントをアクティブ化して **SAP Cloud Platform Identity Authentication サービス** のパスワードを設定するための電子メールを受け取ります。

4.  **[User ID]\(ユーザー ID\)** および **[Password]\(パスワード\)** をコピーします。 これらの値は、Azure portal の SAP Cloud Platform Identity Authentication アプリケーションの [プロビジョニング] タブにある [管理ユーザー名] フィールドと [管理パスワード] フィールドにそれぞれ入力されます。

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>ギャラリーからの SAP Cloud Platform Identity Authentication の追加

Azure AD で自動ユーザー プロビジョニング用に SAP Cloud Platform Identity Authentication を構成する前に、SAP Cloud Platform Identity Authentication を Azure AD アプリケーション ギャラリーからマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから SAP Cloud Platform Identity Authentication を追加するには、次の手順に従います。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**SAP Cloud Platform Identity Authentication**」と入力して、結果パネルで **[SAP Cloud Platform Identity Authentication]** を選択し、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果リストの SAP Cloud Platform Identity Authentication](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-sap-cloud-platform-identity-authentication"></a>SAP Cloud Platform Identity Authentication に対する自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて SAP Cloud Platform Identity Authentication のユーザーやグループを作成、更新、無効化する手順について説明します。

> [!TIP]
> [SAP Cloud Platform Identity Authentication シングル サインオン チュートリアル](./sap-hana-cloud-platform-identity-authentication-tutorial.md)に関する記事にある説明に従い、SAP Cloud Platform Identity Authentication の SAML ベースのシングル サインオンを有効にすることもできます。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-sap-cloud-platform-identity-authentication-in-azure-ad"></a>Azure AD で SAP Cloud Platform Identity Authentication の自動ユーザー プロビジョニングを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[SAP Cloud Platform Identity Authentication]** を選択します。

    ![アプリケーションの一覧の [SAP Cloud Platform Identity Authentication] リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://<tenantID>.accounts.ondemand.com/service/scim `」と入力します。 以前に取得した **[ユーザー ID]** および **[パスワード]** の値を、 **[管理ユーザー名]** および **[管理パスワード]** にそれぞれ入力します。 **[テスト接続]** をクリックして、Azure AD が SAP Cloud Platform Identity Authentication に接続できることを確認します。 接続できない場合は、使用中の SAP Cloud Platform Identity Authentication アカウントに管理者アクセス許可があることを確認してから、もう一度お試しください。

    ![テナント URL + トークン](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/testconnection.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to SAP Cloud Platform Identity Authentication]\(Azure Active Directory ユーザーを SAP Cloud Platform Identity Authentication に同期する\)** を選びます。

    ![SAP Cloud Platform Identity Authentication ユーザー マッピング](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/mapping.png)

9. **[属性マッピング]** セクションで、Azure AD から SAP Cloud Platform Identity Authentication に同期されるユーザー属性を確認します。 **[Matching]** プロパティとして選択されている属性は、更新処理で SAP Cloud Platform Identity Authentication のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![SAP Cloud Platform Identity Authentication ユーザー属性](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/userattributes.png)

10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

11. SAP Cloud Platform Identity Authentication に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

12. **[設定]** セクションの **[スコープ]** で目的の値を選択して、SAP Cloud Platform Identity Authentication にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

13. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって SAP Cloud Platform Identity Authentication に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="connector-limitations"></a>コネクタの制限事項

* SAP Cloud Platform Identity Authentication の SCIM エンドポイントでは、いくつかの属性を特定の形式にする必要があります。 これらの属性とその特定の形式の詳細については、[ここ](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/b10fc6a9a37c488a82ce7489b1fab64c.html#)を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)