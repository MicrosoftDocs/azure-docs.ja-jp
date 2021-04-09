---
title: チュートリアル:Robin を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Robin Powered に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/12/2019
ms.author: Zhchia
ms.openlocfilehash: 83af1c3bc323546534613e6ff99c731010b103d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96352135"
---
# <a name="tutorial-configure-robin-for-automatic-user-provisioning"></a>チュートリアル:Robin を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure Active Directory (Azure AD) が、ユーザー、グループ、またはその両方を Robin に対して自動的にプロビジョニングおよびプロビジョニング解除するよう構成するために、Robin と Azure AD で実行する手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [Robin テナント](https://robinpowered.com/pricing/)
* Admin アクセス許可がある Robin のユーザー アカウント

## <a name="assigning-users-to-robin"></a>Robin へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成し、有効にする前に、Robin へのアクセスが必要な Azure AD のユーザー、グループ、またはその両方を特定する必要があります。 特定した後、次の手順に従い、これらのユーザー、グループ、またはその両方を Robin に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-robin"></a>ユーザーを Robin に割り当てる際の重要なヒント

* 1 名の Azure AD ユーザーを Robin に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Robin にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-robin-for-provisioning"></a>プロビジョニングのために Robin を設定する

1. [Robin 管理コンソール](https://dashboard.robinpowered.com/login)にサインインします。 **[Manage]\(管理\) > [Integrations]\(統合\) > [SCIM] > [Manage]\(管理\)** に移動します。

    ![Robin Powered 管理コンソール](media/robin-provisioning-tutorial/robin-admin.png)

2.  新しい組織トークンを生成します。 このトークンを紛失しても、既存のユーザーに影響を与えることなく、いつでも新しいトークンを作成できます。

    ![Robin Powered の SCIM の追加](media/robin-provisioning-tutorial/robin-token.png)

3.  **SCIM 認証トークン** をコピーします。 この値を、Azure portal で Robin アプリケーションの [プロビジョニング] タブ内の [シークレット トークン] フィールドに入力します。



## <a name="add-robin-from-the-gallery"></a>ギャラリーからの Robin の追加

Azure AD での自動ユーザー プロビジョニング用に Robin を構成する前に、Azure AD アプリケーション ギャラリーから Robin をマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Robin を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Robin**」と入力し、結果パネルで **[Robin]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の Robin](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-robin"></a>Robin への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザー、グループ、またはその両方の割り当てに基づいて、Robin でユーザー、グループ、またはその両方が作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> Robin では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Robin シングル サインオンのチュートリアル](./robin-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-robin-in-azure-ad"></a>Azure AD で Robin の自動ユーザー プロビジョニングを構成するには、次の操作を行います。

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Robin]** を選択します。

    ![アプリケーションの一覧の Robin Powered のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://api.robinpowered.com/v1.0/scim-2`」と入力します。 **[シークレット トークン]** に先ほど取得した **SCIM 認証トークン** の値を入力します。 **[テスト接続]** をクリックして、Azure AD から Robin への接続を確保します。 接続できない場合は、使用中の Robin アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Asana]\(Azure Active Directory ユーザーを Robin に同期する\)** を選択します。

    ![Robin Powered のユーザー マッピング](media/robin-provisioning-tutorial/robin-user-mapping.png)

9. **[属性マッピング]** セクションで、Azure AD から Robin に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Robin のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Robin Powered のユーザー属性](media/robin-provisioning-tutorial/robin-user-attribute-mapping.png)

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to 4me]\(Azure Active Directory グループを Robin に同期する\)** を選択します。

    ![Robin Powered のグループ マッピング](media/robin-provisioning-tutorial/robin-group-mapping.png)

11. **[属性マッピング]** セクションで、Azure AD から Robin に同期されるグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Robin のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Robin Powered のグループ属性](media/robin-provisioning-tutorial/robin-group-attribute-mapping.png)

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Robin に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Robin にプロビジョニングするユーザー、グループ、またはこれらの両方を定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Robin に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。



## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)