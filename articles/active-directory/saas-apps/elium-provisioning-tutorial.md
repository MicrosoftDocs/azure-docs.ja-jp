---
title: チュートリアル:Elium を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Elium に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
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
ms.date: 08/19/2019
ms.author: Zhchia
ms.openlocfilehash: 30f6f81234ce34e40a29a04f6b795aab991a8824
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802658"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>チュートリアル:Elium を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Elium に対するユーザーまたはグループのプロビジョニングまたはプロビジョニング解除を自動的に実行するように Azure Active Directory (Azure AD) を構成するために、Elium と Azure AD で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント。
* [Elium テナント](https://www.elium.com/pricing/)
* Admin アクセス許可がある Elium のユーザー アカウント。

## <a name="assigning-users-to-elium"></a>Elium へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Elium へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Elium に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-elium"></a>ユーザーを Elium に割り当てる際の重要なヒント 

* 単一の Azure AD ユーザーを Elium に割り当て、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Elium にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-elium-for-provisioning"></a>Elium をプロビジョニング用に設定する

Azure AD での自動ユーザー プロビジョニング用に Elium を構成する前に、Elium で SCIM プロビジョニングを有効にする必要があります。

1. Elium にログインします。 **[マイ プロファイル]**  >  **[設定]** に移動します。

    ![Elium](media/Elium-provisioning-tutorial/setting.png)

2. 左下隅にある [ADVANCED]\(詳細設定\) の **[セキュリティ]** を選択します。

    ![Elium](media/Elium-provisioning-tutorial/security.png)

3. **シークレット トークン**をコピーします。 この値を、Azure portal で Elium アプリケーションの [プロビジョニング] タブ内の **[シークレット トークン]** フィールドに入力します。

    ![Elium](media/Elium-provisioning-tutorial/token.png)


## <a name="add-elium--from-the-gallery"></a>ギャラリーから Elium を追加する

Azure AD を使用した自動ユーザー プロビジョニング用に Elium を構成するには、Azure AD アプリケーション ギャラリーから管理対象の SaaS アプリケーションの一覧に Elium を追加する必要があります。

**Azure AD アプリケーション ギャラリーから Elium を追加するには、次の手順を実行します**。

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Elium**」と入力し、結果パネルで **[Elium]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果リスト内の Elium](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-elium"></a>Elium への自動ユーザー プロビジョニングの構成  

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて Elium のユーザーやグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> Elium では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Elium シングル サインオンのチュートリアル](Elium-tutorial.md)に関するページで説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-elium--in-azure-ad"></a>Azure AD で Elium の自動ユーザー プロビジョニングを構成するには、次の操作を実行します。

1. [Azure Portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Elium]** を選択します。

    ![アプリケーションの一覧の [Elium] リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. [管理者資格情報] セクションに、`<tenantURL>/scim/v2` と入力します。ここで、 **{TeanantURL}** は、Elium 管理コンソールから以前に取得した値です。 **[シークレット トークン]** に**シークレット トークン**の値を入力し、 **[テスト接続]** をクリックして、Azure AD が Elium に接続できることを確認します。 接続できない場合は、Elium に管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[Save]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Elium]\(Azure Active Directory ユーザーを Elium に同期する\)** を選択します。

    ![Elium のユーザー マッピング](media/Elium-provisioning-tutorial/usermapping.png)

9. **[属性マッピング]** セクションで、Azure AD から Elium に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Elium のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Elium のユーザー属性](media/Elium-provisioning-tutorial/userattribute.png)


11. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

12. Elium に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

13. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Elium にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

14. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は、後続の同期よりも実行に時間がかかります。 ユーザーやグループのプロビジョニングにかかる時間の詳細については、「[ユーザーをプロビジョニングするにはどのくらいの時間がかかりますか](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)」を参照してください。

**[現在の状態]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Elium に対して実行されたすべてのアクションが記載されています。 詳細については、「[ユーザー プロビジョニングの状態を確認する](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md)」を参照してください。 Azure AD プロビジョニング ログを読むには、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」を参照してください。


## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)。
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)
