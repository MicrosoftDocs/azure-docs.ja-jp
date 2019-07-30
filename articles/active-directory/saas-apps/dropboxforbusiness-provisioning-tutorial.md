---
title: チュートリアル:Dropbox for Business を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: ユーザー アカウントを Dropbox for Business に自動的にプロビジョニングおよびプロビジョニング解除するよう Azure Active Directory を構成する方法について説明します。
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
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: d7a7a76c86100041b544916c7d10e43bf3aaa44d
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672910"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>チュートリアル:Dropbox for Business を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、ユーザーまたはグループを Dropbox for Business に自動的にプロビジョニングおよびプロビジョニング解除するよう Azure AD を構成するために、Dropbox for Business と Azure Active Directory (Azure AD) で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [Dropbox for Business テナント](https://www.dropbox.com/business/pricing)
* Admin アクセス許可がある Dropbox for Business のユーザー アカウント。

## <a name="add-dropbox-for-business-from-the-gallery"></a>ギャラリーから Dropbox for Business を追加する

Azure AD で自動ユーザー プロビジョニング用に Dropbox for Business を構成する前に、Dropbox for Business を Azure AD アプリケーション ギャラリーからマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Dropbox for Business を追加するには、次の手順に従います。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Dropbox for Business**」と入力し、結果パネルで **[Dropbox for Business]** を選択し、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果リストの Dropbox for Business](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>Dropbox for Business へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Dropbox for Business へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定したら、次の手順に従って、これらのユーザーやグループを Dropbox for Business に割り当てることができます。

* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>ユーザーを Dropbox for Business に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを Dropbox for Business に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Dropbox for Business にユーザーを割り当てるときは、割り当てダイアログで有効なアプリケーション固有ロール (使用可能な場合) を選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>Dropbox for Business への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて Dropbox for Business のユーザーやグループを作成、更新、無効化するよう、Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> Dropbox for Business では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Dropbox for Business シングル サインオンのチュートリアル](dropboxforbusiness-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>Azure AD で Dropbox for Business の自動ユーザー プロビジョニングを構成するには:

1. [Azure Portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Dropbox for Business]** を選択します。

    ![アプリケーション一覧の Dropbox for Business リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションにある **[承認する]** をクリックします。 新しいブラウザー ウィンドウで、Dropbox for Business のログイン ダイアログが開きます。

    ![プロビジョニング ](common/provisioning-oauth.png)

6. **[Sign-in to Dropbox for Business to link with Azure AD]\(Dropbox for Business にサインインして Azure AD とリンク\)** ダイアログで、Dropbox for Business テナントにサインインし、ID を検証します。

    ![Dropbox for Business サインイン](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. 手順 5 と 6 を完了したら、 **[テスト接続]** をクリックして、Azure AD が Dropbox for Business に接続できることを確認します。 接続できない場合は、使用中の Dropbox for Business アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![トークン](common/provisioning-testconnection-oauth.png)

8. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

9. **[Save]** をクリックします。

10. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Users to Dropbox]\(Azure Active Directory ユーザーを Dropbox に同期する\)** を選択します。

    ![Dropbox ユーザー マッピング](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. **[属性マッピング]** セクションで、Azure AD から Dropbox に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Dropbox のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Dropbox ユーザー属性](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Groups to Dropbox]\(Azure Active Directory グループを Dropbox に同期する\)** を選択します。

    ![Dropbox グループ マッピング](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. **[属性マッピング]** セクションで、Azure AD から Dropbox に同期されるグループ属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Dropbox のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Dropbox グループ属性](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

15. Dropbox に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

16. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Dropbox にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

17. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Dropbox に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="connector-limitations"></a>コネクタの制限事項
 
* Dropbox では、保留中の招待ユーザーはサポートされていません。 招待されたユーザーが保留中の場合、そのユーザーは削除されます。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)

