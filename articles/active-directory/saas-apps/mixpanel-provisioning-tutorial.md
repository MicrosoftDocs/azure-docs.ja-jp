---
title: チュートリアル:Mixpanel を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure AD から Mixpanel に対してユーザー アカウントを自動的にプロビジョニング、またはプロビジョニング解除する方法を説明します。
services: active-directory
author: Zhchia
writer: Zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/24/2020
ms.author: Zhchia
ms.openlocfilehash: c17d0d927fab86774f25a3ada1fbb3e675957af8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552511"
---
# <a name="tutorial-configure-mixpanel-for-automatic-user-provisioning"></a>チュートリアル:Mixpanel を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために Mixpanel と Azure Active Directory (Azure AD) の両方で実行する必要がある手順について説明します。 構成すると、Azure AD では、ユーザーとグループの [Mixpanel](https://mixpanel.com/pricing/) へのプロビジョニングおよびプロビジョニング解除が Azure AD プロビジョニング サービスを使って自動的に行われるようになります。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。 


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * Mixpanel でユーザーを作成する
> * アクセスが不要になった場合に Mixpanel のユーザーを削除する
> * Azure AD と Mixpanel の間でユーザー属性の同期を維持する
> * Mixpanel でグループとグループ メンバーシップをプロビジョニングする
> * Mixpanel への[シングル サインオン](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-tutorial) (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。
* [Azure AD テナント](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* プロビジョニングを構成するための[アクセス許可](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。 
* エンタープライズ レベルの Mixpanel 組織
* この組織に対する管理者特権を持つ Mixpanel アカウント
* ドメインが要求されている Mixpanel での有効な SSO

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)を確認します。
2. [プロビジョニングの対象](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)となるユーザーを決定します。
3. [Azure AD と Mixpanel の間でマップする](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)データを決定します。 

## <a name="step-2-configure-mixpanel-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD でのプロビジョニングをサポートするように Mixpanel を構成する
1. SSO の設定およびドメインの要求については、[こちら](https://help.mixpanel.com/hc/articles/360036428871-Single-Sign-On)を参照してください。
2. その後、組織の設定のアクセス セキュリティ セクションの [SCIM] タブで、SCIM トークンを生成する必要があります。
![Mixpanel トークン](./media/mixpanel-provisioning-tutorial/mixpanelscim.png)


## <a name="step-3-add-mixpanel-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから Mixpanel を追加する

Azure AD アプリケーション ギャラリーから Mixpanel を追加して、Mixpanel へのプロビジョニングの管理を開始します。 SSO で Mixpanel を以前に設定している場合は、その同じアプリケーションを使用できます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)を参照してください。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)で説明されているスコープ フィルターを使用できます。 

* Mixpanel にユーザーとグループを割り当てるときは、**既定のアクセス**以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)を指定できます。 


## <a name="step-5-configure-automatic-user-provisioning-to-mixpanel"></a>手順 5. Mixpanel への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザー、グループ、またはその両方の割り当てに基づいて、TestApp でユーザー、グループ、またはその両方が作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-mixpanel-in-azure-ad"></a>Azure AD で Mixpanel の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Mixpanel]** を選択します。

    ![アプリケーションの一覧の [Mixpanel] リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションで、Mixpanel の **[テナントの URL]** と **[シークレット トークン]** を入力します。 **[テスト接続]** をクリックして、Azure AD から Mixpanel への接続を確保します。 接続できない場合は、使用中の Mixpanel アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![プロビジョニング](./media/mixpanel-provisioning-tutorial/provisioning.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** を選択します。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Mixpanel]\(Azure Active Directory ユーザーを Mixpanel に同期する\)** を選択します。

9. **[属性マッピング]** セクションで、Azure AD から Mixpanel に同期されるユーザー属性を確認します。 **[Matching]** プロパティとして選択されている属性は、更新処理で Mixpanel のユーザー アカウントとの照合に使用されます。 [一致する対象の属性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)を変更する場合は、その属性に基づいたユーザーのフィルター処理が確実に Mixpanel API でサポートされているようにする必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|
   |---|---|
   |userName|String|
   |displayName|String|

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to Mixpanel]\(Azure Active Directory グループを Mixpanel に同期する\)** を選択します。

11. **[属性マッピング]** セクションで、Azure AD から Mixpanel に同期されるグループ属性を確認します。 **[Matching]** プロパティとして選択されている属性は、更新処理で Mixpanel のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

      |属性|Type|
      |---|---|
      |displayName|String|
      |members|リファレンス|

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Mixpanel に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Mixpanel にプロビジョニングするユーザー、グループ、またはこれらの両方を定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 初期サイクルは後続の同期よりも実行に時間がかかります。後続のサイクルは、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

1. [プロビジョニング ログ](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
2. [進行状況バー](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
3. プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)
