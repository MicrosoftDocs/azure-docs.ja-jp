---
title: チュートリアル:Smartsheet を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Smartsheet に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: e9ee994564e175d3c41cfd5ce415ead8c67df353
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100103551"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>チュートリアル:自動ユーザー プロビジョニング用に Smartsheet を構成する

このチュートリアルの目的は、Azure AD が自動的にユーザーまたはグループを Smartsheet にプロビジョニングまたは Smartsheet からプロビジョニング解除するように構成するために、[Smartsheet](https://www.smartsheet.com/pricing) と Azure Active Directory (Azure AD) で実行される手順を示すことです。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。 


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * Smartsheet でユーザーを作成する
> * アクセスが不要になった場合に Smartsheet のユーザーを削除する
> * Azure AD と Smartsheet の間でユーザー属性の同期を維持する
> * Smartsheet へのシングル サインオン (推奨)

> [!NOTE]
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md)。
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。
* [Smartsheet テナント](https://www.smartsheet.com/pricing)。
* システム管理者アクセス許可を持つ Smartsheet Enterprise または Enterprise Premier プランのユーザー アカウント。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
2. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
3. [Azure AD と Smartsheet の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。 

## <a name="step-2-configure-smartsheet-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD でのプロビジョニングをサポートするように Smartsheet を構成する

Azure AD での自動ユーザー プロビジョニング用に Smartsheet を構成する前に、Smartsheet 上で SCIM プロビジョニングを有効にする必要があります。

1. **[Smartsheet ポータル](https://app.smartsheet.com/b/home)** で **SysAdmin** としてサインインし、 **[Account Admin]\(アカウント管理\)** に移動します。

    ![Smartsheet のアカウント管理](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. **[Security Controls]\(セキュリティ制御\) >[User Auto Provisioning]\(ユーザーの自動プロビジョニング\) > [Edit]\(編集\)** に移動します。

    ![Smartsheet のセキュリティ制御](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Azure AD から Smartsheet にプロビジョニングする予定のユーザーのメール ドメインを追加し、検証します。 **[Not Enabled]\(無効\)** を選択して、すべてのプロビジョニング操作が Azure AD からのみ行われ、Smartsheet ユーザー一覧が Azure AD の割り当てと同期されるようにします。

    ![Smartsheet のユーザー プロビジョニング](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. 検証が完了したら、ドメインをアクティブにする必要があります。 

    ![Smartsheet のドメインのアクティブ化](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. **[Apps and Integrations]\(アプリと統合\)** に移動して、Azure AD で自動ユーザー プロビジョニングを構成するために必要な **[Secret Token]\(シークレット トークン\)** を生成します。

    ![Smartsheet の管理者ページのスクリーンショット。ユーザーのアバターと [Apps and Integrations]\(アプリと統合\) オプションが選択されています。](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. **[API Access]\(API アクセス\)** を選択します。 **[Generate new access token]\(新しいアクセス トークンの生成\)** をクリックします。

    ![[個人設定] ダイアログ ボックスのスクリーンショット。[API Access]\(API アクセス\) と [Generate new access token]\(新しいアクセス トークンの生成\) オプションが選択されています。](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. API アクセス トークンの名前を定義します。 **[OK]** をクリックします。

    ![ステップ 1 (全部で 2) のスクリーンショット。[OK] オプションが選択されている [Generate API Access Token]\(API アクセス トークンの生成\)。](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. これが API アクセス トークンを表示できる唯一のタイミングなので、コピーして保存します。 これは、Azure AD の **[Secret Token]\(シークレット トークン\)** フィールドに必要です。

    ![Smartsheet のトークン](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="step-3-add-smartsheet-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから Smartsheet を追加する

Azure AD アプリケーション ギャラリーから Smartsheet を追加して、Smartsheet へのプロビジョニングの管理を開始します。 SSO のために以前 Smartsheet を設定している場合は、同じアプリケーションを使用できます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](../manage-apps/add-application-portal.md)を参照してください。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)で説明されているスコープ フィルターを使用できます。 

* Smartsheet にユーザーとグループを割り当てるときは、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 

* Smartsheet と Azure AD の間でユーザー ロールの割り当てを一致させるために、Smartsheet ユーザー一覧全体に設定されているものと同じロールの割り当てを利用することをお勧めします。 Smartsheet からこのユーザー一覧を取得するには、 **[Account Admin]\(アカウント管理\) > [User Management]\(ユーザー管理\) > [More Actions]\(その他の操作\) > [Download User List (csv)]\(ユーザー一覧のダウンロード (csv)\)** に移動します。

* アプリの一部の機能にアクセスするには、Smartsheet でユーザーが複数のロールを持つ必要があります。 Smartsheet のユーザーの種類とアクセス許可の詳細については、「[User Types and Permissions (ユーザーの種類とアクセス許可)](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions)」を参照してください。

*  Smartsheet でユーザーに複数のロールを割り当てられている場合、ユーザーが Smartsheet オブジェクトへのアクセスを完全に失うことがないように、これらのロールの割り当てを Azure AD で確実に複製する **必要があります**。 Smartsheet 内の一意の各ロールは、Azure AD 内で別のグループに割り当てる **必要があります**。 次に、ユーザーを目的のロールに対応する各グループに追加する **必要があります**。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 

## <a name="step-5-configure-automatic-user-provisioning-to-smartsheet"></a>手順 5. Smartsheet への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Smartsheet のユーザーやグループを作成、更新、無効化する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Azure AD で Smartsheet の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Smartsheet]** を選択します。

    ![アプリケーションの一覧の Smartsheet リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナント URL]** および **[シークレット トークン]** に、先ほど Smartsheet から取得した **SCIM 2.0 ベース URL およびアクセス トークン** の値をそれぞれ入力します。 **[接続テスト]** をクリックして、Azure AD から Smartsheet に接続できることを確認します。 接続できない場合は、使用中の Smartsheet アカウントに SysAdmin アクセス許可があることを確認してから、もう一度試します。

    ![トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Smartsheet]\(Azure Active Directory ユーザーを Smartsheet に同期する\)** を選択します。

9. **[属性マッピング]** セクションで、Azure AD から Smartsheet に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Smartsheet のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|フィルター処理のサポート|
   |---|---|---|
   |userName|String|&check;|
   |active|Boolean|
   |title|String|
   |name.givenName|String|
   |name.familyName|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |emails[type eq "work"].value|String|
   |externalId|String|
   |roles|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|String|


10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

11. Smartsheet に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

12. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Smartsheet にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

13. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

1. [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
2. [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
3. プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](../app-provisioning/application-provisioning-quarantine-status.md)を参照してください。  

## <a name="connector-limitations"></a>コネクタの制限事項

* Smartsheet は論理的な削除をサポートしていません。 ユーザーの **active** 属性が False に設定されていると、Smartsheet ではユーザーが完全に削除されます。

## <a name="change-log"></a>ログの変更

* 2020/06/16 - ユーザー向けにエンタープライズ拡張属性 "Cost Center"、"Division"、"Manager"、および "Department" のサポートが追加されました。
* 2021/02/10 - ユーザー向けにコア属性 "emails[type eq "work"]" のサポートが追加されました。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)