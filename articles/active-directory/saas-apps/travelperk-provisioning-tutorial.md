---
title: 'チュートリアル: Azure Active Directory を使用して、自動ユーザー プロビジョニングを行うように TravelPerk を構成する | Microsoft Docs'
description: Azure AD から TravelPerk へのユーザー アカウントのプロビジョニングおよびプロビジョニング解除を自動的に実行する方法について説明します。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 3e40f87d-8624-4b14-b098-80ff916103c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2021
ms.author: Zhchia
ms.openlocfilehash: 19436b7faef081757e4500c76e7537ee78081bfa
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950382"
---
# <a name="tutorial-configure-travelperk-for-automatic-user-provisioning"></a>チュートリアル: 自動的にユーザーをプロビジョニングするように TravelPerk を構成する

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために TravelPerk と Azure Active Directory (Azure AD) の両方で行う必要がある手順について説明します。 構成すると、Azure AD によって、[TravelPerk](https://www.travelperk.com/) に対するユーザーとグループのプロビジョニングおよびプロビジョニング解除が Azure AD プロビジョニング サービスを使用して自動的に行われます。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。

## <a name="capabilities-supported"></a>サポートされる機能

> [!div class="checklist"]
>
> - TravelPerk でユーザーを作成する
> - アクセスが不要になった場合に TravelPerk のユーザーを削除する
> - Azure AD と TravelPerk の間でユーザー属性の同期を維持する
> - TravelPerk への[シングル サインオン](./travelperk-tutorial.md) (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

- [Azure AD テナント](../develop/quickstart-create-new-tenant.md)。
- プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。
- アクティブな [TravelPerk](https://app.travelperk.com/signup) 管理者アカウント。
- Premium または Pro [プラン](https://www.travelperk.com/pricing/)。


## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する

1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
2. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
3. [Azure AD と TravelPerk の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。

## <a name="step-2-configure-travelperk-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD でのプロビジョニングをサポートするように TravelPerk を構成する

1. ご利用の管理者アカウントを使用して [TravelPerk](https://app.travelperk.com/company/integrations/scim) アプリケーションにログインします。

2. **[Company Settings]\(会社の設定\)**  >  **[Integrations]\(統合\)**  >  **[SCIM]** の順に移動します。

3. **[Enable SCIM API]\(SCIM API を有効にする\)** をクリックします。

   ![有効にする](./media/travelperk-provisioning-tutorial/configuration.png)

4. SCIM を介して承認を有効にすることもできます。 承認を使用すると、指定した承認者によって旅行が最初に確実に承認されるようにすることで、追加のガバナンスを容易に設定できます。 これの詳細については、[こちら](https://support.travelperk.com/hc/en-us/articles/360044168971-How-do-approval-processes-work-)を参照してください。

5. どの人のマネージャーも旅行の承認を担当するユーザーに自動的になるようにするかどうかを指定できます。 そのため、承認者は、対応する自動承認プロセスで割り当てられます。 TravelPerk によって、Azure の **マネージャー** の値が、ユーザーの希望する承認者に割り当てられます。 プロビジョニングされたユーザー承認者になるには、ユーザーは事前にプラットフォーム上に存在している必要があります。
TravelPerk 上で適切に構成されていない場合、承認者は作成されません。

6. [Integrations]\(統合\) ページで SCIM を有効にしたら、 **[SCIM Settings]\(SCIM の設定\)** 内で自動承認プロセスの作成を指定できるようになります。 それをオンにするには、 **[Through an Identity Provider]\(ID プロバイダー経由\)** を選択し、 **[Enable automatic approval process creation]\(自動承認プロセスの作成を有効にする\)** のトグルを切り替えます。

7. 必要な承認プロセスが構成されたら、 **[変更の保存]** をクリックします。

   ![自動化](./media/travelperk-provisioning-tutorial/approval.png)

## <a name="step-3-add-travelperk-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから TravelPerk を追加する

Azure AD アプリケーション ギャラリーから TravelPerk を追加して、TravelPerk へのプロビジョニングの管理を開始します。 以前に SSO のために Slack をセットアップしている場合は、同じアプリケーションを使用できます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](../manage-apps/add-application-portal.md)を参照してください。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)で説明されているスコープ フィルターを使用できます。

- TravelPerk にユーザーを割り当てるときは、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。

- 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。

## <a name="step-5-configure-automatic-user-provisioning-to-travelperk"></a>手順 5. TravelPerk への自動ユーザー プロビジョニングを構成する

このセクションでは、Azure AD でのユーザー、グループ、またはその両方の割り当てに基づいて、TestApp でユーザー、グループ、またはその両方が作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-travelperk-in-azure-ad"></a>Azure AD で TravelPerk の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

   ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[TravelPerk]** を選択します。

   ![アプリケーションの一覧の [TravelPerk] リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

   ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

   ![[プロビジョニング] タブの [自動]](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクション下にある **[承認する]** をクリックします。 **TravelPerk** のログイン ページにリダイレクトされます。 自分の **[ユーザー名]** と **[パスワード]** を入力し、 **[サインイン]** ボタンをクリックします。 [承認] ページで **[アプリの承認]** をクリックします。 **[テスト接続]** をクリックして、Azure AD から TravelPerk への接続を確保します。 接続できない場合は、使用中の SecureLogin アカウントで管理者アクセス許可を確保してから、もう一度試します。

   ![[Admin Credentials (管理者の資格情報)]](./media/travelperk-provisioning-tutorial/authorize.png)

   ![ようこそ](./media/travelperk-provisioning-tutorial/login.png)

   ![アクセス](./media/travelperk-provisioning-tutorial/authorization.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

   ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** を選択します。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to TravelPerk]\(Azure Active Directory ユーザーを TravelPerk に同期する\)** を選択します。

9. **[属性マッピング]** セクションで、Azure AD から TravelPerk に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で TravelPerk のユーザー アカウントとの照合に使用されます。 [一致する対象の属性](../app-provisioning/customize-application-attributes.md)を変更する場合は、その属性に基づいたユーザーのフィルター処理が確実に TravelPerk API でサポートされているようにする必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

   | 属性                                                                         | Type      | フィルター処理のサポート |
   | --------------------------------------------------------------------------------- | --------- | ----------------------- |
   | userName                                                                          | String    | &check;                 |
   | externalId                                                                        | String    |
   | active                                                                            | Boolean   |
   | name.honorificPrefix                                                              | String    |
   | name.familyName                                                                   | String    |
   | name.givenName                                                                    | String    |
   | name.middleName                                                                   | String    |
   | preferredLanguage                                                                 | String    |
   | locale                                                                            | String    |
   | phoneNumbers[type eq "work"].value                                                | String    |
   | externalId                                                                        | String    |
   | title                                                                             | String    |
   | urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter             | String    |
   | urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager                | リファレンス |
   | urn:ietf:params:scim:schemas:extension:travelperk:2.0:User:gender                 | String    |
   | urn:ietf:params:scim:schemas:extension:travelperk:2.0:User:dateOfBirth            | String    |
   | urn:ietf:params:scim:schemas:extension:travelperk:2.0:User:invoiceProfiles        | Array     |
   | urn:ietf:params:scim:schemas:extension:travelperk:2.0:User:emergencyContact.name  | String    |
   | urn:ietf:params:scim:schemas:extension:travelperk:2.0:User:emergencyContact.phone | String    |
   | urn:ietf:params:scim:schemas:extension:travelperk:2.0:User:travelPolicy           | String    |

10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

11. TravelPerk に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

12. **[設定]** セクションの **[スコープ]** で目的の値を選択して、TravelPerk にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

13. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 初期サイクルは後続の同期よりも実行に時間がかかります。後続のサイクルは、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する

プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

1. [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
2. [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
3. プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](../app-provisioning/application-provisioning-quarantine-status.md)を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

- [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)