---
title: チュートリアル:Snowflake を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Snowflake に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 7fbf7743eb1c037a364e1004fc3be30db6777bb0
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546644"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>チュートリアル:Snowflake を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure Active Directory (Azure AD) によって自動的にユーザーまたはグループが [Snowflake](https://www.Snowflake.com/pricing/) にプロビジョニングされる、または Snowflake からプロビジョニング解除されるように構成するため、Snowflake と Azure AD で実行する手順を示すことです。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。 


> [!NOTE]
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * Snowflake でユーザーを作成する
> * アクセスが不要になった場合に Snowflake のユーザーを削除する
> * Azure AD と Snowflake の間でユーザー属性の同期を維持する
> * Snowflake でグループとグループ メンバーシップをプロビジョニングする
> * Snowflake への[シングル サインオン](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial) (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)。
* プロビジョニングを構成するための[アクセス許可](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。
* [Snowflake テナント](https://www.Snowflake.com/pricing/)。
* Admin アクセス許可がある Snowflake のユーザー アカウント。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)を確認します。
2. [プロビジョニングの対象](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)となるユーザーを決定します。
3. [Azure AD と Snowflake の間でマップする](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)データを決定します。 

## <a name="step-2-configure-snowflake-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD でのプロビジョニングをサポートするように Snowflake を構成する

Azure AD での自動ユーザー プロビジョニング用に Snowflake を構成する前に、Snowflake 上で SCIM プロビジョニングを有効にする必要があります。

1. Snowflake の管理コンソールにサインインします。 下に示されているクエリを、強調表示されているワークシートに入力し、 **[実行]** をクリックします。

    ![Snowflake 管理コンソール](media/Snowflake-provisioning-tutorial/image00.png)

2.  SCIM アクセス トークンが、Snowflake テナントに対して生成されます。 それを取得するには、下の強調表示されているリンクをクリックします。

    ![Snowflake での SCIM の追加](media/Snowflake-provisioning-tutorial/image01.png)

3. 生成されたトークンの値をコピーし、 **[完了]** をクリックします。 この値を、Azure portal の Snowflake アプリケーションの [プロビジョニング] タブの **[シークレット トークン]** フィールドに入力します。

    ![Snowflake での SCIM の追加](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="step-3-add-snowflake-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから Snowflake を追加する

Azure AD アプリケーション ギャラリーから Snowflake を追加して、Snowflake へのプロビジョニングの管理を開始します。 SSO のために Snowflake を以前に設定している場合は、その同じアプリケーションを使用することができます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)を参照してください。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)で説明されているスコープ フィルターを使用できます。 

* Snowflake にユーザーとグループを割り当てるときは、**既定のアクセス**以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)を指定できます。 


## <a name="step-5-configure-automatic-user-provisioning-to-snowflake"></a>手順 5. Snowflake への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Snowflake のユーザーやグループを作成、更新、無効化する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>Azure AD で Snowflake の自動ユーザー プロビジョニングを構成するには、次の操作を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Snowflake]** を選択します。

    ![アプリケーションの一覧の Snowflake のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. [管理者資格情報] セクションの **[テナント URL]** フィールドおよび **[シークレット トークン]** フィールドに、先ほど取得した **SCIM 2.0 ベース URL および認証トークン**の値をそれぞれ入力します。 **[接続テスト]** をクリックして、Azure AD から Snowflake に接続できることを確認します。 接続できない場合は、使用中の Snowflake アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

7. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

8. **[保存]** をクリックします。

9. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Snowflake]\(Azure Active Directory ユーザーを Snowflake に同期する\)** を選択します。

10. **[属性マッピング]** セクションで、Azure AD から Snowflake に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Snowflake のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|
   |---|---|
   |active|Boolean|
   |displayName|String|
   |emails[type eq "work"].value|String|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:defaultRole|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:defaultWarehouse|String|

11. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to Snowflake]\(Azure Active Directory グループを Snowflake に同期する\)** を選択します。

12. **[属性マッピング]** セクションで、Azure AD から Snowflake に同期されるグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Snowflake のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

      |属性|Type|
      |---|---|
      |displayName|String|
      |members|リファレンス|

13. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

14. Snowflake に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

15. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Snowflake にプロビジョニングするユーザーやグループを定義します。 このオプションが使用できない場合は、[Admin Credentials]\(管理者の資格情報\) の下の必須フィールドを構成し、 **[保存]** をクリックして、ページを更新してください。 

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

16. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

    これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

1. [プロビジョニング ログ](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
2. [進行状況バー](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
3. プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)を参照してください。  

## <a name="connector-limitations"></a>コネクタの制限事項

* Snowflake で生成される SCIM トークンの有効期間は 6 か月です。 プロビジョニングの同期を続行するには、有効期限が切れる前にこれらを更新する必要があることに注意してください。 

## <a name="change-log"></a>変更履歴

* 2020 年 7 月 21 日 - すべてのユーザーに対して (アクティブな属性を介して) 論理的な削除を有効化。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)。
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ
* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
