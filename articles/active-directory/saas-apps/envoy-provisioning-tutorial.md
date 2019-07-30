---
title: チュートリアル:Envoy を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Envoy に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
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
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: df4c5895e15e7e9e63ad1f3d273af1c3fdab2e90
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672726"
---
# <a name="tutorial-configure-envoy-for-automatic-user-provisioning"></a>チュートリアル:Envoy を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Envoy に対するユーザーまたはグループのプロビジョニングまたはプロビジョニング解除を自動的に実行するように Azure Active Directory (Azure AD) を構成するために、Envoy と Azure AD で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [Envoy テナント](https://envoy.com/pricing/)
* Admin アクセス許可がある Envoy のユーザー アカウント。

## <a name="add-envoy-from-the-gallery"></a>ギャラリーから Envoy を追加する

Azure AD での自動ユーザー プロビジョニング用に Envoy を構成する前に、Azure AD アプリケーション ギャラリーから Envoy をマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Envoy を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Envoy**」と入力し、結果ウィンドウで **[Envoy]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果リストの Envoy](common/search-new-app.png)

## <a name="assigning-users-to-envoy"></a>Envoy へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Envoy へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Envoy に割り当てることができます。

* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-envoy"></a>ユーザーを Envoy に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを Envoy に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Envoy にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="configuring-automatic-user-provisioning-to-envoy"></a>Envoy への自動ユーザー プロビジョニングの構成 

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて Envoy のユーザーやグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> Envoy では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Envoy シングル サインオンのチュートリアル](envoy-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-envoy-in-azure-ad"></a>Azure AD で Envoy の自動ユーザー プロビジョニングを構成するには、次の操作を実行します。

1. [Azure Portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Envoy]** を選択します。

    ![アプリケーションの一覧の Envoy のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://app.envoy.com/scim/v2`」と入力します。 お使いの Envoy アカウントの**シークレット トークン**を取得するために、手順 6 で説明されている操作を行います。

6. [Envoy 管理コンソール](https://dashboard.envoy.com/login)にサインインします。 **[統合]** をクリックします。

    ![Envoy の統合](media/envoy-provisioning-tutorial/envoy01.png)

    **[Microsoft Azure SCIM integration]\(Microsoft Azure SCIM の統合\)** で **[インストール]** をクリックします。

    ![Envoy のインストール](media/envoy-provisioning-tutorial/envoy02.png)

    **[Sync all users]\(すべてのユーザーを同期\)** で **[保存]** をクリックします。 

    ![Envoy の保存](media/envoy-provisioning-tutorial/envoy03.png)

    設定されているシークレット トークンを取得します。
    
    ![Envoy の OAUTH](media/envoy-provisioning-tutorial/envoy04.png)

7. 手順 5 の各フィールドに値を入力したら、 **[テスト接続]** をクリックして、Azure AD が Envoy に接続できることを確認します。 接続できない場合は、使用中の Envoy アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![トークン](common/provisioning-testconnection-tenanturltoken.png)

8. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

9. **[Save]** をクリックします。

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Envoy]\(Azure Active Directory ユーザーを Envoy に同期する\)** を選択します。
    
    ![Envoy のユーザー属性](media/envoy-provisioning-tutorial/envoy-user-mappings.png)
    
11. **[属性マッピング]** セクションで、Azure AD から Envoy に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Envoy のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Envoy のユーザー属性](media/envoy-provisioning-tutorial/envoy-user-attribute.png)

12. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to Envoy]\(Azure Active Directory グループを Envoy に同期する\)** を選択します。

    ![Envoy のユーザー マッピング](media/envoy-provisioning-tutorial/envoy-group-mapping.png)

13. **[属性マッピング]** セクションで、Azure AD から Envoy に同期されるグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Envoy のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Envoy のユーザー マッピング](media/envoy-provisioning-tutorial/envoy-group-attributes.png)
    
14. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

15. Envoy に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

16. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Envoy にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

17. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Envoy に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)

