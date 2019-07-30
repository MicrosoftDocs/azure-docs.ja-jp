---
title: チュートリアル:Zoom を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Zoom に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
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
ms.openlocfilehash: 0a21a35e6d4a938d41e8bc11cebc1be5738d893e
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671003"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>チュートリアル:Zoom を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure AD が自動的にユーザーまたはグループを Zoom にプロビジョニングまたは Zoom からプロビジョニング解除するように構成するために、Zoom と Azure Active Directory (Azure AD) で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [Zoom のテナント](https://zoom.us/pricing)
* Admin アクセス許可がある Zoom のユーザー アカウント

## <a name="add-zoom-from-the-gallery"></a>ギャラリーから Zoom を追加する

Azure AD での自動ユーザー プロビジョニング用に Zoom を構成する前に、Azure AD アプリケーション ギャラリーから Zoom をマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Zoom を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Zoom**」と入力し、結果ウィンドウで **[Zoom]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の Zoom](common/search-new-app.png)

## <a name="assign-users-to-zoom"></a>ユーザーを Zoom に割り当てる

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Zoom へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Zoom に割り当てることができます。

* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zoom"></a>ユーザーを Zoom に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを Zoom に割り当て、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Zoom にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="configure-automatic-user-provisioning-to-zoom"></a>Zoom への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Zoom のユーザーやグループを作成、更新、無効化する手順について説明します。

> [!TIP]
> Zoom では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Zoom シングル サインオンのチュートリアル](zoom-tutorial.md)に関するページで説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Azure AD で Zoom の自動ユーザー プロビジョニングを構成する

1. [Azure Portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Zoom]** を選択します。

    ![アプリケーションの一覧の [Zoom] リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://api.zoom.us/scim`」と入力します。 お使いの Zoom アカウントの**シークレット トークン**を取得するために、手順 6 で説明されている操作を行います。

6. [Zoom 管理コンソール](https://zoom.us/signin)にサインインします。 左側のナビゲーション ウィンドウで **[詳細設定] > [Zoom for Developers]\(開発者向け Zoom\)** に移動します。

    ![Zoom の統合](media/zoom-provisioning-tutorial/zoom01.png)

    ページの右上隅にある **[管理]** に移動します。 

    ![Zoom のインストール](media/zoom-provisioning-tutorial/zoom02.png)

    作成した Azure AD アプリに移動します。 
    
    ![Zoom アプリ](media/zoom-provisioning-tutorial/zoom03.png)

    左側のナビゲーション ウィンドウで、 **[アプリの資格情報]** を選択します。

    ![Zoom アプリ](media/zoom-provisioning-tutorial/zoom04.png)

    下に示す JWT トークンの値を取得し、これを Azure AD の **[シークレット トークン]** フィールドに入力します。 新しい無期限のトークンが必要な場合は、新しいトークンを自動生成する有効期限を再構成する必要があります。 

    ![Zoom のインストール](media/zoom-provisioning-tutorial/zoom05.png)

7. 手順 5 の各フィールドに値を入力したら、 **[テスト接続]** をクリックして、Azure AD が Zoom に接続できることを確認します。 接続できない場合は、使用中の Zoom アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![トークン](common/provisioning-testconnection-tenanturltoken.png)

8. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

9. **[Save]** をクリックします。

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Zoom]\(Azure Active Directory ユーザーを Zoom に同期する\)** を選択します。

    ![Zoom のユーザー マッピング](media/zoom-provisioning-tutorial/zoom-user-mapping.png)

11. **[属性マッピング]** セクションで、Azure AD から Zoom に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Zoom のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。
    
     ![Zoom のユーザー マッピング](media/zoom-provisioning-tutorial/zoom-user-attributes.png)

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Zoom に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。
    
    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Zoom にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Zoom に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="connector-limitations"></a>コネクタの制限事項

* Zoom では、グループのプロビジョニングはサポートされていません。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)
