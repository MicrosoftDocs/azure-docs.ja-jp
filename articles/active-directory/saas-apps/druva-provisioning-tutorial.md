---
title: チュートリアル:Druva を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: ユーザー アカウントを Druva に対して自動的にプロビジョニングおよびプロビジョニング解除するように Azure Active Directory を構成する方法について説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2ef564ae61aab10e1d100ccaf2547248e4a6a8e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96352169"
---
# <a name="tutorial-configure-druva-for-automatic-user-provisioning"></a>チュートリアル:Druva を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure Active Directory (Azure AD) が、ユーザー、グループ、またはその両方を Druva に対して自動的にプロビジョニングおよびプロビジョニング解除するよう構成するために、Druva と Azure AD で実行する手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント。
* [Druva テナント](https://www.druva.com/products/pricing-plans/)。
* Admin アクセス許可がある Druva のユーザー アカウント。

## <a name="assigning-users-to-druva"></a>Druva へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成し、有効にする前に、Druva へのアクセスが必要な Azure AD のユーザー、グループ、またはその両方を特定する必要があります。 特定した後、次の手順に従い、これらのユーザー、グループ、またはその両方を Druva に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-druva"></a>ユーザーを Druva に割り当てる際の重要なヒント

* 1 名の Azure AD ユーザーを Druva に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Druva にユーザーを割り当てるときは、割り当てダイアログで、有効なアプリケーション固有ロール (使用可能な場合) を選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="setup-druva-for-provisioning"></a>プロビジョニングのために Druva を設定する

Azure AD での自動ユーザー プロビジョニング用に Druva を構成する前に、Druva で SCIM プロビジョニングを有効にする必要があります。

1. [Druva 管理コンソール](https://console.druva.com)にサインインします。 **[Druva] > [inSync]** に移動します。

    ![Druva 管理コンソール](media/druva-provisioning-tutorial/menubar.png)

2. **[管理]**  >  **[デプロイ]**  >  **[ユーザー]** に移動します。

    :::image type="content" source="media/druva-provisioning-tutorial/manage.png" alt-text="Druva 管理コンソールのスクリーンショット。[Manage]\(管理\) が強調表示され、[Manage]\(管理\) メニューが表示されています。そのメニューの [Deployments]\(デプロイ\) の下にある [Users]\(ユーザー\) が強調表示されています。" border="false":::

3.  **[設定]** に移動します。 **[トークンの生成]** をクリックします。

    :::image type="content" source="media/druva-provisioning-tutorial/settings.png" alt-text="Druva 管理コンソールのページのスクリーンショット。[Settings]\(設定\) が強調表示され、[Settings]\(設定\) タブが開いています。[Generate token]\(トークンの生成\) ボタンが強調表示されています。" border="false":::

4.  **[認証トークン]** 値をコピーします。 この値を、Azure portal で Druva アプリケーションの [プロビジョニング] タブ内の **[シークレット トークン]** フィールドに入力します。
    
    :::image type="content" source="media/druva-provisioning-tutorial/auth.png" alt-text="Druva 管理コンソールの [Create token]\(トークンの作成\) ページのスクリーンショット。&quot;トークンのコピー&quot; というラベルが付いたリンクは、認証トークンの値をコピーするためにあります。" border="false":::

## <a name="add-druva-from-the-gallery"></a>ギャラリーからの Druva の追加

Azure AD を使用した自動ユーザー プロビジョニング用に Druva を構成するには、Azure AD アプリケーション ギャラリーから管理対象の SaaS アプリケーションの一覧に Druva を追加する必要があります。

**Azure AD アプリケーション ギャラリーから Druva を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Druva**」と入力し、結果パネルで **[Druva]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の Druva](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-druva"></a>Druva への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザー、グループ、またはその両方の割り当てに基づいて、Druva でユーザー、グループ、またはその両方が作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> Druva では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Druva シングル サインオンのチュートリアル](druva-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-druva-in-azure-ad"></a>Azure AD で Druva の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Druva]** を選択します。

    ![アプリケーションの一覧の Druva のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5.  [管理者資格情報] セクションの **[テナントの URL]** に「`https://apis.druva.com/insync/scim`」と入力します。 **[認証トークン]** 値を **[シークレット トークン]** に入力します。 **[テスト接続]** をクリックして、Azure AD から Druva に接続できることを確認します。 接続できない場合は、使用中の Druva アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力し、 **[エラーが発生したときにメール通知を送信します]** を選択します。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Druva]\(Azure Active Directory ユーザーを Druva に同期する\)** を選択します。

    ![Druva ユーザーのマッピング](media/druva-provisioning-tutorial/usermapping.png)

9. **[属性マッピング]** セクションで、Azure AD から Druva に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Druva のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Druva ユーザーの属性](media/druva-provisioning-tutorial/userattribute.png)


10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

11. Druva に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

12. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Druva にプロビジョニングするユーザー、グループ、またはこれらの両方を定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

13. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

    これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Druva に対して実行されたすべてのアクションが記載されています。

    Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。
    
## <a name="connector-limitations"></a>コネクタの制限事項

* Druva では、必須の属性として **電子メール** が必要です。 

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)。
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
