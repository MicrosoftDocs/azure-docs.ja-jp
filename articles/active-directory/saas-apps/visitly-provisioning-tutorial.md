---
title: チュートリアル:Visitly を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: ユーザー アカウントを Visitly に自動的にプロビジョニングしたりプロビジョニング解除したりするように Azure Active Directory を構成する方法について説明します。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 381f9cb2cb9ef196149144d40332a1de3b90f188
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802626"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>チュートリアル:Visitly を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Visitly に対するユーザーまたはグループのプロビジョニングまたはプロビジョニング解除が自動的に実行されるように Azure Active Directory (Azure AD) を構成するための、Visitly と Azure AD で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント。
* [Visitly テナント](https://www.visitly.io/pricing/)
* 管理者アクセス許可がある Visitly のユーザー アカウント。

## <a name="assigning-users-to-visitly"></a>Visitly へのユーザーの割り当て 

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Visitly へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Visitly に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>ユーザーを Visitly に割り当てる際の重要なヒント 

* 単一の Azure AD ユーザーを Visitly に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Visitly にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="setup-visitly-for-provisioning"></a>プロビジョニングのために Visitly を設定する

Azure AD での自動ユーザー プロビジョニング用に Visitly を構成する前に、Visitly で SCIM プロビジョニングを有効にする必要があります。

1. [Visitly](https://app.visitly.io/login) にログインします。 Click on **[Integrations]\(統合\)**  >  **[Host Synchronization]\(ホストの同期\)** の順にクリックします。

    ![Visitly](media/Visitly-provisioning-tutorial/login.png)

2. 下へスクロールして、 **[Azure AD]** セクションを選択します

    ![Visitly](media/Visitly-provisioning-tutorial/integration.png)

3. **API キー**をコピーします。 これらの値は、Azure portal で Visitly アプリケーションの [プロビジョニング] タブにある **[シークレット トークン]** フィールドに入力します。

    ![Visitly](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>ギャラリーからの Visitly の追加

Azure AD で自動ユーザー プロビジョニング用に Visitly を構成するには、Azure AD アプリケーション ギャラリーから Visitly をマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Visitly を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Visitly**」と入力し、結果ウィンドウで **[Visitly]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の Visitly](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-visitly"></a>Visitly への自動ユーザー プロビジョニングの構成  

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて Visitly のユーザーやグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> Visitly では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Visitly シングル サインオンのチュートリアル](Visitly-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-visitly--in-azure-ad"></a>Azure AD で Visitly の自動ユーザー プロビジョニングを構成するには:

1. [Azure Portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Visitly]** を選択します。

    ![アプリケーションの一覧の Visitly のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. [管理者資格情報] セクションの **[テナント URL]** および **[シークレット トークン]** に、先ほど取得した ` https://api.visitly.io/v1/usersync/SCIM` と **API キー**の値をそれぞれ入力します。 **[テスト接続]** をクリックして、Azure AD から Visitly への接続を確認します。 接続できない場合は、使用中の Visitly アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[Save]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Visitly]\(Azure Active Directory ユーザーを Visitly に同期する\)** を選択します。

    ![Visitly のユーザー マッピング](media/visitly-provisioning-tutorial/usermapping.png)

9. **[属性マッピング]** セクションで、Azure AD から Visitly に同期されるユーザー属性を確認します。 **照合**用プロパティとして選択されている属性は、更新処理で Visitly のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Visitly のユーザー属性](media/visitly-provisioning-tutorial/userattribute.png)



10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

11. Visitly に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

12. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Visitly にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

13. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は、後続の同期よりも実行に時間がかかります。 ユーザーやグループのプロビジョニングにかかる時間の詳細については、「[ユーザーをプロビジョニングするにはどのくらいの時間がかかりますか](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)」を参照してください。

**[現在の状態]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Visitly に対して実行されたすべてのアクションが記載されています。 詳細については、「[ユーザー プロビジョニングの状態を確認する](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md)」を参照してください。 Azure AD プロビジョニング ログを読むには、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」を参照してください。

## <a name="connector-limitations"></a>コネクタの制限事項

Visitly では、物理的な削除はサポートされていません。すべてが論理的な削除のみです。  

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)
