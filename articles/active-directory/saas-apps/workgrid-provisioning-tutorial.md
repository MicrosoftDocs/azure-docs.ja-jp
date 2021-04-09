---
title: チュートリアル:Workgrid を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Workgrid に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/17/2019
ms.author: Zhchia
ms.openlocfilehash: ec39e4796d2a06ad88a8e88ea99b953064eb6d21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96348332"
---
# <a name="tutorial-configure-workgrid--for-automatic-user-provisioning"></a>チュートリアル:Workgrid を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure Active Directory (Azure AD) が、ユーザー、グループ、またはその両方を Workgrid に自動的にプロビジョニングおよびプロビジョニング解除するよう構成するために、Workgrid と Azure AD で実行する手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント。
* [Workgrid テナント](https://www.workgrid.com/)
* 管理者アクセス許可がある Workgrid のユーザー アカウント。

## <a name="assigning-users-to-workgrid"></a>Workgrid へのユーザーの割り当て 

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Workgrid へのアクセスが必要な Azure AD 内のユーザーやグループを決定しておく必要があります。 特定した後、次の手順に従い、これらのユーザー、グループ、またはその両方を Workgrid に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-workgrid"></a>ユーザーを Workgrid に割り当てるときの重要なヒント 

* 1 名の Azure AD ユーザーを Workgrid に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Workgrid にユーザーを割り当てるときは、割り当てダイアログで、有効なアプリケーション固有ロール (使用可能な場合) を選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-workgrid-for-provisioning"></a>プロビジョニング用に Workgrid を設定する

Azure AD での自動ユーザー プロビジョニング用に Workgrid を構成する前に、Workgrid で SCIM プロビジョニングを有効にする必要があります。

1. Workgrid にログインします。 **[Users]\(ユーザー\) > [User Provisioning]\(ユーザー プロビジョニング\)** に移動します。

    ![[Users]\(ユーザー\) および [User Provisioning]\(ユーザー プロビジョニング\) オプションが赤い四角で囲まれている Workgrid UI のスクリーンショット。](media/Workgrid-provisioning-tutorial/user.png)

2. **[Account Management API]\(アカウント管理 API\)** で、 **[Create Credentials]\(資格情報の作成\)** をクリックします。

    ![[Create Credentials]\(資格情報の作成\) オプションが赤い四角で囲まれている [Account Management API]\(アカウント管理 API\) セクションのスクリーンショット。](media/Workgrid-provisioning-tutorial/scim.png)

3. **[SCIM Endpoint]\(SCIM エンドポイント\)** と **[Access Token]\(アクセス トークン\)** の値をコピーします。 これらの値を、Azure portal の Workgrid アプリケーションの [プロビジョニング] タブにある **[テナント URL]** および **[シークレット トークン]** フィールドに入力します。

    ![[SCIM Endpoint]\(SCIM エンドポイント\) と [Access Token]\(アクセス トークン\) が赤い四角で囲まれている [Account Management API]\(アカウント管理 API\) セクションのスクリーンショット。](media/Workgrid-provisioning-tutorial/token.png)


## <a name="add-workgrid--from-the-gallery"></a>ギャラリーからの Workgrid の追加

Azure AD で自動ユーザー プロビジョニング用に Workgrid を構成するには、Azure AD アプリケーション ギャラリーから Workgrid をマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Workgrid を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Workgrid**」と入力し、結果ウィンドウで **[Workgrid]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の Workgrid](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-workgrid"></a>Workgrid への自動ユーザー プロビジョニングの構成  

このセクションでは、Azure AD でのユーザー、グループ、またはその両方の割り当てに基づいて、Workgrid でユーザー、グループ、またはその両方が作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> Workgrid で SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Workgrid シングル サインオンのチュートリアル](Workgrid-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-workgrid--in-azure-ad"></a>Azure AD で Workgrid の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Workgrid]** を選択します。

    ![アプリケーションの一覧の Workgrid のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. [管理者資格情報] セクションの **[テナント URL]** および **[シークレット トークン]** に、先ほど取得した **SCIM エンドポイント** と **アクセス トークン** の値をそれぞれ入力します。 **[テスト接続]** をクリックして、Azure AD から Workgrid に接続できることを確認します。 接続できない場合は、使用中の Workgrid アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Workgrid]\(Azure Active Directory ユーザーを Workgrid に同期する\)** を選択します。

    ![[Synchronize Azure Active Directory Users to Workgrid]\(Azure Active Directory ユーザーを Workgrid に同期する\) オプションが赤い四角で囲まれている [マッピング] セクションのスクリーンショット。](media/Workgrid-provisioning-tutorial/usermapping.png)

9. **[属性マッピング]** セクションで、Azure AD から Workgrid に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Workgrid のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Workgrid ユーザーの属性](media/Workgrid-provisioning-tutorial/userattribute.png)

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to Workgrid]\(Azure Active Directory グループを Workgrid に同期する\)** を選択します。

    ![[Synchronize Azure Active Directory Groups to Workgrid]\(Azure Active Directory グループを Workgrid に同期する\) オプションが赤い四角で囲まれている [マッピング] セクションのスクリーンショット。](media/Workgrid-provisioning-tutorial/groupmapping.png)

12. **[属性マッピング]** セクションで、Azure AD から Workgrid に同期されるグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Workgrid のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![3 つのマッピングが表示されている [属性マッピング] セクションのスクリーンショット。](media/Workgrid-provisioning-tutorial/groupattribute.png)

13. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

14. Workgrid に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

15. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Workgrid にプロビジョニングするユーザー、グループ、またはその両方を定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

16. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は、後続の同期よりも実行に時間がかかります。 ユーザーやグループのプロビジョニングにかかる時間の詳細については、「[ユーザーをプロビジョニングするにはどのくらいの時間がかかりますか](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)」を参照してください。

**[現在の状態]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Workgrid に対して実行されたすべてのアクションが記載されています。 詳細については、「[ユーザー プロビジョニングの状態を確認する](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)」を参照してください。 Azure AD プロビジョニング ログを読むには、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
