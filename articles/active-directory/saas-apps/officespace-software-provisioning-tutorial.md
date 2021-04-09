---
title: 'チュートリアル: Azure Active Directory で自動ユーザー プロビジョニング用に OfficeSpace Software を構成する | Microsoft Docs'
description: Azure Active Directory を構成して、ユーザー アカウントを OfficeSpace Software に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/02/2019
ms.author: Zhchia
ms.openlocfilehash: fc67d649e3a7cd094eb2c3d633244077abcab308
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94359927"
---
# <a name="tutorial-configure-officespace-software-for-automatic-user-provisioning"></a>チュートリアル: 自動ユーザー プロビジョニング用に OfficeSpace Software を構成する

このチュートリアルの目的は、ユーザーまたはグループを OfficeSpace Software に自動的にプロビジョニングおよびプロビジョニング解除するよう Azure AD を構成するために、OfficeSpace Software と Azure Active Directory (Azure AD) で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [OfficeSpace Software テナント](https://www.officespacesoftware.com/)
* 管理者アクセス許可がある OfficeSpace Software のユーザー アカウント。

## <a name="assigning-users-to-officespace-software"></a>OfficeSpace Software へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成し、有効にする前に、OfficeSpace Software へのアクセスが必要な Azure AD のユーザー、グループ、またはその両方を特定する必要があります。 決定したら、次の手順に従って、これらのユーザーやグループを OfficeSpace Software に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-officespace-software"></a>ユーザーを OfficeSpace Software に割り当てるときの重要なヒント

* 単一の Azure AD ユーザーを OfficeSpace Software に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* OfficeSpace Software にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-officespace-software-for-provisioning"></a>プロビジョニング用に OfficeSpace Software を設定する

1. [OfficeSpace Software 管理コンソール](https://support.officespacesoftware.com/hc)にサインインします。 **[設定] > [コネクタ]** に移動します。

    ![OfficeSpace Software 管理コンソール](media/officespace-software-provisioning-tutorial/settings.png)

2.  **[ディレクトリ同期] > [SCIM]** に移動します。

    ![OfficeSpace Software による SCIM の追加](media/officespace-software-provisioning-tutorial/scim.png)

3.  **SCIM 認証トークン** をコピーします。 この値を、Azure portal で OfficeSpace Software アプリケーションの [プロビジョニング] タブ内の [シークレット トークン] フィールドに入力します。

    ![OfficeSpace Software でのトークンの作成](media/officespace-software-provisioning-tutorial/token.png)

## <a name="add-officespace-software-from-the-gallery"></a>ギャラリーから OfficeSpace Software を追加する

Azure AD で自動ユーザー プロビジョニング用に OfficeSpace Software を構成する前に、OfficeSpace Software を Azure AD アプリケーション ギャラリーからマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから OfficeSpace Software を追加するには、次の手順に従います。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**OfficeSpace Software**」と入力し、結果ウィンドウで **[OfficeSpace Software]** を選択してから、**[追加]** をクリックしてアプリケーションを追加します。

    ![結果一覧の OfficeSpace Software](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-officespace-software"></a>OfficeSpace Software に自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて OfficeSpace Software のユーザーやグループを作成、更新、無効化するよう、Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> OfficeSpace Software では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[OfficeSpace Software シングル サインオンのチュートリアル](./officespace-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-officespace-software-in-azure-ad"></a>Azure AD で OfficeSpace Software 用に自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[OfficeSpace Software]** を選択します。

    ![アプリケーションの一覧の OfficeSpace Software リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナント URL]** に `https://<subdomain>.officespacesoftware.com/api/scim/v2/` の URL 形式を入力します。 たとえば、「 `https://contoso.officespacesoftware.com/api/scim/v2/` 」のように指定します。 **[シークレット トークン]** に先ほど取得した **SCIM 認証トークン** の値を入力します。 **[テスト接続]** をクリックして、Azure AD から OfficeSpace Software への接続を確保します。 接続できない場合は、使用中の OfficeSpace Software アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to OfficeSpace Software]\(Azure Active Directory ユーザーを OfficeSpace Software に同期する\)** を選択します。

    ![OfficeSpace Software ユーザー マッピング](media/officespace-software-provisioning-tutorial/usermappings.png)

9. **[属性マッピング]** セクションで、Azure AD から OfficeSpace Software に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新操作で OfficeSpace Software のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![OfficeSpace Software ユーザー属性](media/officespace-software-provisioning-tutorial/userattributes.png)

11. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

12. OfficeSpace Software に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

13. **[設定]** セクションの **[スコープ]** で目的の値を選択して、OfficeSpace Software にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

14. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって OfficeSpace Software に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)