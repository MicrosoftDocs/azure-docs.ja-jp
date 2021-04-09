---
title: チュートリアル:SmartFile を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを SmartFile に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
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
ms.openlocfilehash: 03c7efd390d4714680ed2ccd54d297b8137aaafc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96348672"
---
# <a name="tutorial-configure-smartfile-for-automatic-user-provisioning"></a>チュートリアル:SmartFile を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、ユーザーとグループ、またはそのいずれかを SmartFile に自動的にプロビジョニングまたはプロビジョニング解除するように Azure AD を構成するために、SmartFile と Azure Active Directory (Azure AD) で実行する手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント。
* [SmartFile テナント](https://www.SmartFile.com/pricing/)。
* 管理者アクセス許可がある SmartFile のユーザー アカウント。

## <a name="assigning-users-to-smartfile"></a>SmartFile へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、SmartFile へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定したら、次の手順に従って、これらのユーザーやグループを SmartFile に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-smartfile"></a>ユーザーを SmartFile に割り当てるときの重要なヒント

* 単一の Azure AD ユーザーを SmartFile に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* SmartFile にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="setup-smartfile-for-provisioning"></a>SmartFile をプロビジョニング用に設定する

Azure AD での自動ユーザー プロビジョニング用に SmartFile を構成する前に、SmartFile 上で SCIM プロビジョニングを有効にして、必要な追加の詳細情報を収集する必要があります。

1. SmartFile 管理コンソールにサインインします。 SmartFile 管理コンソールの右上隅に移動します。 **[プロダクト キー]** を選択します。

    ![SmartFile 管理コンソール](media/smartfile-provisioning-tutorial/login.png)

2. ベアラー トークンを生成するには、**プロダクト キー** と **プロダクト パスワード** をコピーします。 それらをメモ帳に貼り付け、間にコロンを入れます。
    
     ![[プロダクト キー] セクションのスクリーンショット。[プロダクト キー] と [プロダクト パスワード] のテキスト ボックスが選択されています。](media/smartfile-provisioning-tutorial/auth.png)

    ![プレーンテキストのスクリーンショット。[プロダクト キー] と [プロダクト パスワード] がコロンで区切られています。](media/smartfile-provisioning-tutorial/key.png)

## <a name="add-smartfile-from-the-gallery"></a>ギャラリーから SmartFile を追加する

Azure AD で自動ユーザー プロビジョニング用に SmartFile を構成するには、Azure AD アプリケーション ギャラリーから管理対象 SaaS アプリケーションの一覧に SmartFile を追加する必要があります。

**Azure AD アプリケーション ギャラリーから SmartFile を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**SmartFile**」と入力し、結果ウィンドウで **[SmartFile]** を選択してから、 **[追加]** をクリックしてアプリケーションを追加します。

    ![結果一覧の SmartFile](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-smartfile"></a>SmartFile への自動ユーザー プロビジョニングの構成 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて SmartFile のユーザーやグループを作成、更新、無効化する手順について説明します。

> [!TIP]
> SmartFile で SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[SmartFile シングル サインオンのチュートリアル](SmartFile-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-smartfile-in-azure-ad"></a>Azure AD で SmartFile の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[SmartFile]** を選択します。

    ![アプリケーションの一覧の [SmartFile] リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5.  **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://<SmartFile sitename>.smartfile.com/ftp/scim`」と入力します。 たとえば、`https://demo1test.smartfile.com/ftp/scim` のようになります。 先ほど取得した **ベアラー トークン** の値 (ProductKey:ProductPassword) を **[シークレット トークン]** に入力します。 **[テスト接続]** をクリックして、Azure AD から SmartFile に接続できることを確認します。 接続できない場合は、使用している SmartFile アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to SmartFile]\(Azure Active Directory ユーザーを SmartFile に同期する\)** を選択します。

    ![SmartFile のユーザー マッピング](media/smartfile-provisioning-tutorial/usermapping.png)

9. **[属性マッピング]** セクションで、Azure AD から SmartFile に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で SmartFile のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![SmartFile のユーザー属性](media/smartfile-provisioning-tutorial/userattribute.png)

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to SmartFile]\(Azure Active Directory グループを SmartFile に同期する\)** を選択します。

    ![SmartFile のグループ マッピング](media/smartfile-provisioning-tutorial/groupmapping.png)

11. **[属性マッピング]** セクションで、Azure AD から SmartFile に同期されるグループ属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で SmartFile のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![SmartFile のグループ属性](media/smartfile-provisioning-tutorial/groupattribute.png)

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. SmartFile に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、SmartFile にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

    これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって SmartFile に対して実行されたすべてのアクションが記載されています。

    Azure AD プロビジョニング ログの読み方について詳しくは、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。
    
## <a name="connector-limitations"></a>コネクタの制限事項

* SmartFile では、物理的な削除のみがサポートされます。 

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

 [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
