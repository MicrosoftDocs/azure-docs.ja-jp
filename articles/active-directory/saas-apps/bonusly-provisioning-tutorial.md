---
title: チュートリアル:Azure Active Directory による自動ユーザー プロビジョニングに対応するように Bonusly を構成する | Microsoft Docs
description: Bonusly に対するユーザー アカウントのプロビジョニングとプロビジョニング解除を自動的に実行するように Azure Active Directory を構成する方法を説明します。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 879b0ee9-042a-441b-90a7-8c364d62426a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 692eff26dbbc2289ec589a327dbe58958b414a56
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842729"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>チュートリアル:自動ユーザー プロビジョニングに対応するように Bonusly を構成する

このチュートリアルの目的は、Bonusly に対するユーザーまたはグループのプロビジョニングまたはプロビジョニング解除を自動的に実行するように Azure Active Directory (Azure AD) を構成するために、Bonusly と Azure AD で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* Azure AD テナント
* [Bonusly テナント](https://bonus.ly/pricing)
* 管理者アクセス許可がある Bonusly のユーザー アカウント

> [!NOTE]
> Azure AD プロビジョニング統合は、[Bonusly Rest API](https://konghq.com/solutions/gateway/) に依存しています。この API は Bonusly 開発者から入手できます。

## <a name="adding-bonusly-from-the-gallery"></a>ギャラリーからの Bonusly の追加

Azure AD での自動ユーザー プロビジョニング用に Bonusly を構成する前に、Azure AD アプリケーション ギャラリーから Bonusly をマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Bonusly を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Bonusly**」と入力し、結果ウィンドウで **Bonusly** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Bonusly](common/search-new-app.png)

## <a name="assigning-users-to-bonusly"></a>Bonusly へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。 

自動ユーザー プロビジョニングを構成して有効にする前に、Bonusly へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Bonusly に割り当てることができます。

* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>ユーザーを Bonusly に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを Bonusly に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Bonusly にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>Bonusly への自動ユーザー プロビジョニングの構成

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて Bonusly のユーザーやグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> Bonusly では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Bonusly シングル サインオンのチュートリアル](bonus-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>Azure AD で Bonusly の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインし、 **[エンタープライズ アプリケーション]** 、 **[すべてのアプリケーション]** 、 **[Bonusly]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Bonusly]** を選択します。

    ![アプリケーションの一覧の Bonusly のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![Bonusly のプロビジョニング](./media/bonusly-provisioning-tutorial/ProvisioningTab.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![Bonusly のプロビジョニング](./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png)

5. **[管理者資格情報]** セクションで、手順 6 で説明する Bonusly アカウントの **[シークレット トークン]** を入力します。

    ![Bonusly のプロビジョニング](./media/bonusly-provisioning-tutorial/secrettoken.png)

6. Bonusly アカウントの**シークレット トークン** は、 **[Admin]\(管理者\) > [Company]\(会社\) > [Integrations]\(統合\)** に配置されています。 **[If you want to code]\(コーディングするもの\)** セクションで、 **[API] > [Create New API Access Token]\(新しい API アクセストークンの作成\)** をクリックして、新しいシークレット トークンを作成します。

    ![Bonusly のプロビジョニング](./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png)

    ![Bonusly のプロビジョニング](./media/bonusly-provisioning-tutorial/BonsulyRestApi.png)

    ![Bonusly のプロビジョニング](./media/bonusly-provisioning-tutorial/CreateToken.png)

7. 次の画面で、用意されたテキスト ボックスに、アクセス トークンの名前を入力し、 **[Create Api Key]\(API キーの作成\)**  をクリックします。 数秒後にポップアップ内に新しいアクセス トークンが表示されます。

    ![Bonusly のプロビジョニング](./media/bonusly-provisioning-tutorial/Token01.png)

    ![Bonusly のプロビジョニング](./media/bonusly-provisioning-tutorial/Token02.png)

8. 手順 5 に示されたフィールドに値を入力したら、 **[テスト接続]** をクリックして、Azure AD が Bonusly に接続できることを確認します。 接続できない場合は、使用中の Bonusly アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![Bonusly のプロビジョニング](./media/bonusly-provisioning-tutorial/TestConnection.png)

9. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![Bonusly のプロビジョニング](./media/bonusly-provisioning-tutorial/EmailNotification.png)

10. **[Save]** をクリックします。

11. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Slack]\(Azure Active Directory ユーザーを Bonusly に同期する\)** を選択します。

    ![Bonusly のプロビジョニング](./media/bonusly-provisioning-tutorial/UserMappings.png)

12. **[属性マッピング]** セクションで、Azure AD から Bonusly に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Bonusly のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Bonusly のプロビジョニング](./media/bonusly-provisioning-tutorial/UserAttributeMapping.png)

13. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

14. Bonusly に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![Bonusly のプロビジョニング](./media/bonusly-provisioning-tutorial/ProvisioningStatus.png)

15. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Bonusly にプロビジョニングするユーザーやグループを定義します。

    ![Bonusly のプロビジョニング](./media/bonusly-provisioning-tutorial/ScopeSync.png)

16. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![Bonusly のプロビジョニング](./media/bonusly-provisioning-tutorial/SaveProvisioning.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Bonusly に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png
