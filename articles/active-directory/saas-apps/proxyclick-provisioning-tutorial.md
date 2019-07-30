---
title: チュートリアル:Azure Active Directory を使用した自動ユーザー プロビジョニング Proxyclick を構成する | Microsoft Docs
description: ユーザー アカウントを Proxyclick に自動的にプロビジョニング/プロビジョニング解除するように Azure Active Directory を構成する方法について説明します。
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
ms.openlocfilehash: c1656e6cc0c690e5a2bccfd2efab02aa843875b8
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672899"
---
# <a name="tutorial-configure-proxyclick-for-automatic-user-provisioning"></a>チュートリアル:自動ユーザー プロビジョニング用に Proxyclick を構成する

このチュートリアルの目的は、ユーザーまたはグループの Proxyclick へのプロビジョニングとプロビジョニング解除を自動的に行うように Azure AD を構成するために、Proxyclick と Azure Active Directory (Azure AD) で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [Proxyclick テナント](https://www.proxyclick.com/pricing)
* 管理者アクセス許可がある Proxyclick のユーザー アカウント

## <a name="add-proxyclick-from-the-gallery"></a>ギャラリーから Proxyclick を追加する

Azure AD での自動ユーザー プロビジョニング用に Proxyclick を構成する前に、Azure AD アプリケーション ギャラリーから Proxyclick を管理対象の SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Proxyclick を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Proxyclick**」と入力し、結果ウィンドウで **[Proxyclick]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果リストの Proxyclick](common/search-new-app.png)

## <a name="assigning-users-to-proxyclick"></a>Proxyclick へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Proxyclickr へのアクセスが必要な Azure AD 内のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Proxyclick に割り当てることができます。

* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-proxyclick"></a>ユーザーを Proxyclick に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを Proxyclick に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Proxyclick にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="configuring-automatic-user-provisioning-to-proxyclick"></a>Proxyclick への自動ユーザー プロビジョニングの構成 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Proxyclick のユーザーやグループを作成、更新、無効化する手順について説明します。

> [!TIP]
> Proxyclick では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Proxyclick シングル サインオンのチュートリアル](proxyclick-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-proxyclick-in-azure-ad"></a>Azure AD で Proxyclick の自動ユーザー プロビジョニングを構成するには:

1. [Azure Portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Proxyclick]** を選択します。

    ![アプリケーションの一覧の Proxyclick のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. お使いの Proxyclick アカウントの**テナント URL** と**シークレット トークン**を取得するために、手順 6 で説明されている操作を行います。

6. [Proxyclick 管理コンソール](https://app.proxyclick.com/login//?destination=%2Fdefault)にサインインします。 **[Settings]\(設定\)**  >  **[Integrations]\(統合\)**  >  **[Browse Marketplace]\(マーケットプレースの参照\)** に移動します。

    ![Proxyclick の [Settings]\(設定\)](media/proxyclick-provisioning-tutorial/proxyclick09.png)

    ![Proxyclick の [Integrations]\(統合\)](media/proxyclick-provisioning-tutorial/proxyclick01.png)

    ![Proxyclick の [Marketplace]\(マーケットプレース\)](media/proxyclick-provisioning-tutorial/proxyclick02.png)

    **[Azure AD ]** を選びます。 **[Install now]\(今すぐインストール\)** をクリックします。

    ![Proxyclick の [Azure AD]](media/proxyclick-provisioning-tutorial/proxyclick03.png)

    ![Proxyclick の [Install]\(インストール\)](media/proxyclick-provisioning-tutorial/proxyclick04.png)

    **[User Provisioning]\(ユーザー プロビジョニング\)** を選択し、 **[Start integration]\(統合の開始\)** をクリックします。 

    ![Proxyclick の [User Provisioning]\(ユーザー プロビジョニング\)](media/proxyclick-provisioning-tutorial/proxyclick05.png)

    **[Settings]\(設定\)**  >  **[Integrations]\(統合\)** で、適切な設定構成 UI が表示されるはずです。 **[Azure AD (User Provisioning)]\(Azure AD (ユーザー プロビジョニング)\)** で **[Settings]\(設定\)** を選択します。

    ![Proxyclick の作成](media/proxyclick-provisioning-tutorial/proxyclick06.png)

    ここで、**テナント URL** と**シークレット トークン**を見つけることができます。

    ![Proxyclick の作成トークン](media/proxyclick-provisioning-tutorial/proxyclick07.png)

7. 手順 5 の各フィールドに値を入力したら、 **[Test Connection]\(テスト接続\)** をクリックして、Azure AD で Proxyclick に接続できることを確認します。 接続できない場合は、使用中の Proxyclick アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![トークン](common/provisioning-testconnection-tenanturltoken.png)

8. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

9. **[Save]** をクリックします。

10. **[Mappings]\(マッピング\)** セクションの **[Synchronize Azure Active Directory Users to Pingboard]\(Azure Active Directory ユーザーを Proxyclick に同期する\)** を選びます。

    ![Proxyclick のユーザー マッピング](media/proxyclick-provisioning-tutorial/Proxyclick-user-mappings.png)

11. **[Attribute Mapping]\(属性のマッピング\)** セクションで、Azure AD から Proxyclick に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性を使用して、更新処理で Proxyclick のユーザー アカウントとの照合が行われます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Proxyclick のユーザー属性](media/proxyclick-provisioning-tutorial/Proxyclick-user-attribute.png)

13. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

14. Proxyclick に対して Azure AD プロビジョニング サービスを有効にするには、 **[Settings]\(設定\)** セクションで **[Provisioning Status]\(プロビジョニング状態\)** を **[On]\(オン\)** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

15. **[Settings]\(設定\)** セクションの **[Scope]\(スコープ\)** で目的の値を選択して、Proxyclick にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

16. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[Synchronization Details]\(同期の詳細\)** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Proxyclick に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="connector-limitations"></a>コネクタの制限事項

* Proxyclick では、 **[emails]** と **[userName]** に同じソース値を設定する必要があります。 片方の属性が更新されると、他方の値が変更されます。
* Proxyclick では、グループのプロビジョニングはサポートされていません。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)

