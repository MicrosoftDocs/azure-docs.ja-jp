---
title: チュートリアル:Azure Active Directory での自動ユーザー プロビジョニングに対応するように Oracle Cloud Infrastructure Console を構成する | Microsoft Docs
description: Azure AD から Oracle Cloud Infrastructure Console に対してユーザー アカウントを自動的にプロビジョニングまたはプロビジョニング解除する方法を説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/16/2020
ms.author: Zhchia
ms.openlocfilehash: 0c3d68698621fe963074c7216e4636208d93deca
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543872"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>チュートリアル:自動ユーザー プロビジョニングに対応するように Oracle Cloud Infrastructure Console を構成する

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために Oracle Cloud Infrastructure Console と Azure Active Directory (Azure AD) の両方で実行する必要がある手順について説明します。 構成すると、Azure AD では、ユーザーとグループの [Oracle Cloud Infrastructure Console](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton) へのプロビジョニングおよびプロビジョニング解除が、Azure AD プロビジョニング サービスを使用して自動的に行われるようになります。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。 


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * Oracle Cloud Infrastructure Console でのユーザーの作成
> * アクセスが不要になったユーザーの Oracle Cloud Infrastructure Console での削除
> * Azure AD と Oracle Cloud Infrastructure Console の間でのユーザー属性の同期の維持
> * Oracle Cloud Infrastructure Console でのグループとグループ メンバーシップのプロビジョニング
> * Oracle Cloud Infrastructure Console への[シングル サインオン](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial) (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* プロビジョニングを構成するための[アクセス許可](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。 
* Oracle Cloud Infrastructure Console の[テナント](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton)。
* Oracle Cloud Infrastructure Console での管理者権限を持つユーザー アカウント。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)を確認します。
2. [プロビジョニングの対象](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)となるユーザーを決定します。
3. [Azure AD と Oracle Cloud Infrastructure Console の間でマップする](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)データを決定します。 

## <a name="step-2-configure-oracle-cloud-infrastructure-console-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD でのプロビジョニングをサポートするように Oracle Cloud Infrastructure Console を構成する

1. Oracle Cloud Infrastructure Console の管理ポータルにログインします。 画面の左上隅で **[Identity]\(ID\) > [Federation]\(フェデレーション\)** に移動します。

    ![Oracle 管理者](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/identity.png)

2. Oracle Identity Cloud Service Console の横のページに表示される URL をクリックします。

    ![Oracle の URL](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/url.png)

3. **[Add Identity Provider]\(ID プロバイダーの追加\)** をクリックして、新しい ID プロバイダーを作成します。 テナントの URL の一部として使用する IdP の ID を保存します。 **[Applications]\(アプリケーション\)** タブの横にあるプラス記号アイコンをクリックして、OAuth クライアントを作成し、IDCS Identity Domain Administrator AppRole を付与します。

    ![Oracle のクラウド アイコン](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/add.png)

4. 次のスクリーンショットに従って、アプリケーションを構成します。 構成が終わったら、 **[Save]\(保存\)** をクリックします。

    ![Oracle の構成](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/configuration.png)

    ![Oracle のトークン発行ポリシー](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/token-issuance.png)

5. アプリケーションの構成タブで **[General Information]\(全般情報\)** オプションを展開して、クライアント ID とクライアント シークレットを取得します。

    ![Oracle でのトークン生成](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/general-information.png)

6. シークレット トークンを生成するには、クライアント ID とクライアント シークレットを **<クライアント ID>:<クライアント シークレット>** の形式で Base64 にエンコードします。 シークレット トークンを保存します。 この値を、Azure portal で Oracle Cloud Infrastructure Console アプリケーションのプロビジョニング タブの **[Secret Token]\(シークレット トークン\)** フィールドに入力します。

## <a name="step-3-add-oracle-cloud-infrastructure-console-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから Oracle Cloud Infrastructure Console を追加する

Azure AD アプリケーション ギャラリーから Oracle Cloud Infrastructure Console を追加して、Oracle Cloud Infrastructure Console へのプロビジョニングの管理を開始します。 前に SSO 用に Oracle Cloud Infrastructure Console を設定している場合は、同じアプリケーションを使用できます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)を参照してください。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)で説明されているスコープ フィルターを使用できます。 

* Oracle Cloud Infrastructure Console にユーザーとグループを割り当てるときは、 **[Default Access]\(既定のアクセス\)** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)を指定できます。 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>手順 5. Oracle Cloud Infrastructure Console への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザー、グループ、またはその両方の割り当てに基づいて、TestApp でユーザー、グループ、またはその両方が作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>Azure AD で Oracle Cloud Infrastructure Console に対する自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Oracle Cloud Infrastructure Console]** を選択します。

    ![アプリケーション一覧での Oracle Cloud Infrastructure Console のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナント URL]** を `https://<IdP ID>.identity.oraclecloud.com/admin/v1` の形式で入力します。 たとえば、「 `https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1` 」のように指定します。 前に取得したシークレット トークンの値を、 **[シークレット トークン]** に入力します。 **[接続テスト]** をクリックして、Azure AD が Oracle Cloud Infrastructure Console に接続できることを確認します。 接続できない場合は、お使いの Oracle Cloud Infrastructure Console アカウントに管理者アクセス許可があることを確認してから、再試行します。

    ![プロビジョニング](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** を選択します。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Oracle Cloud Infrastructure Console]\(Azure Active Directory のユーザーを Oracle Cloud Infrastructure Console に同期する\)** を選択します。

9. **[属性マッピング]** セクションで、Azure AD から Oracle Cloud Infrastructure Console に同期されるユーザーの属性を確認します。 **[Matching]\(一致する\)** プロパティとして選択されている属性は、更新処理で Oracle Cloud Infrastructure Console のユーザー アカウントとの照合に使用されます。 [一致する対象の属性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)を変更する場合は、その属性に基づいたユーザーのフィルター処理が確実に Oracle Cloud Infrastructure Console API でサポートされているようにする必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

      |属性|Type|
      |---|---|
      |displayName|String|
      |userName|String|
      |active|Boolean|
      |title|String|
      |emails[type eq "work"].value|String|
      |preferredLanguage|String|
      |name.givenName|String|
      |name.familyName|String|
      |addresses[type eq "work"].formatted|String|
      |addresses[type eq "work"].locality|String|
      |addresses[type eq "work"].region|String|
      |addresses[type eq "work"].postalCode|String|
      |addresses[type eq "work"].country|String|
      |addresses[type eq "work"].streetAddress|String|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|リファレンス|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|
      |urn:ietf:params:scim:schemas:oracle:idcs:extension:user:User:bypassNotification|Boolean|
      |urn:ietf:params:scim:schemas:oracle:idcs:extension:user:User:isFederatedUser|Boolean|

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to Oracle Cloud Infrastructure Console]\(Azure Active Directory のグループを Oracle Cloud Infrastructure Console に同期する\)** を選択します。

11. **[属性マッピング]** セクションで、Azure AD から Oracle Cloud Infrastructure Console に同期されるグループの属性を確認します。 **[Matching]\(一致する\)** プロパティとして選択されている属性は、更新処理で Oracle Cloud Infrastructure Console のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

      |属性|Type|
      |---|---|
      |displayName|String|
      |externalId|String|
      |members|リファレンス|

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Oracle Cloud Infrastructure Console に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Oracle Cloud Infrastructure Console にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 初期サイクルは後続の同期よりも実行に時間がかかります。後続のサイクルは、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

* [プロビジョニング ログ](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
* [進行状況バー](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
* プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)
