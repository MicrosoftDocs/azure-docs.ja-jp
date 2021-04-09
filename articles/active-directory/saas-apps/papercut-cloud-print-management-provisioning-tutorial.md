---
title: チュートリアル:Azure Active Directory を使用した自動ユーザー プロビジョニングのために PaperCut Cloud Print Management を構成する | Microsoft Docs
description: Azure AD から PaperCut Cloud Print Management に対してユーザー アカウントを自動的にプロビジョニングおよびプロビジョニング解除する方法を説明します。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 7e65d727-2951-4aec-a7a3-7bde49ed09e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: c0738e7033f0aa39ca5f12a44c06d086175eb8fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98731468"
---
# <a name="tutorial-configure-papercut-cloud-print-management-for-automatic-user-provisioning"></a>チュートリアル:自動ユーザー プロビジョニングのために PaperCut Cloud Print Management を構成する

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために PaperCut Cloud Print Management と Azure Active Directory (Azure AD) の両方で行う必要がある手順について説明します。 構成すると、Azure AD プロビジョニング サービスを使用した、[PaperCut Cloud Print Management](https://www.papercut.com/products/papercut-pocket/) に対するユーザーとグループのプロビジョニングおよびプロビジョニング解除が、Azure AD によって自動的に行われます。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。

## <a name="capabilities-supported"></a>サポートされる機能

> [!div class="checklist"]
> * PaperCut Cloud Print Management でユーザーを作成する
> * ユーザーがアクセスを必要としなくなったときに PaperCut Cloud Print Management から削除する
> * Azure AD と PaperCut Cloud Print Management の間でのユーザー属性の同期を維持する

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md)。 
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。 
* PaperCut Cloud Print Management 管理者アカウント。


## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する

1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
2. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
3. [Azure AD と PaperCut Cloud Print Management の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。

## <a name="step-2-configure-papercut-cloud-print-management-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD を使用したプロビジョニングをサポートするように PaperCut Cloud Print Management を構成する

1. [PaperCut Pocket 管理コンソール](https://pocket.papercut.com/)または [PaperCut Hive 管理コンソール](https://hive.papercut.com/)にサインインします。

2. **[Add-ons]**  >  **[All Add-ons]** に移動し、 **[Microsoft Azure AD User Sync Addon]** を見つけます。

3. **[詳細情報]** をクリックし、 **[追加]** をクリックしてインストールします。



4. インストールが完了するとアドオンの詳細ページが開き、**テナント URL** と **シークレット トークン** が表示されます。 これらの値を、Azure portal の PaperCut Cloud Print Management アプリケーションの [プロビジョニング] タブの "テナント URL" \* フィールドと "シークレット トークン" \* フィールドに入力します。



## <a name="step-3-add-papercut-cloud-print-management-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから PaperCut Cloud Print Management を追加する

Azure AD アプリケーション ギャラリーから PaperCut Cloud Print Management を追加して、PaperCut Cloud Print Management へのプロビジョニングの管理を開始します。 前に SSO 用に PaperCut Cloud Print Management を設定している場合は、同じアプリケーションを使用できます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](../manage-apps/add-application-portal.md)を参照してください。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)で説明されているスコープ フィルターを使用できます。

* PaperCut Cloud Print Management にユーザーとグループを割り当てるときは、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 

## <a name="step-5-configure-automatic-user-provisioning-to-papercut-cloud-print-management"></a>手順 5. PaperCut Cloud Print Management に自動ユーザー プロビジョニングを構成する

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて PaperCut Cloud Print Management のユーザーやグループを作成、更新、無効化する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-papercut-cloud-print-management-in-azure-ad"></a>Azure AD で PaperCut Cloud Print Management への自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

   ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーション一覧で、**PaperCut Cloud Print Management** を選択します。

   ![アプリケーション一覧での、PaperCut Cloud Print Management のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

   ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

   ![[プロビジョニング] タブの [自動]](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションで、PaperCut Cloud Print Management のテナント URL とシークレット トークンを入力します。 **[接続テスト]** をクリックして、Azure AD が PaperCut Cloud Print Management に接続できることを確認します。 接続できない場合は、お使いの PaperCut Cloud Print Management アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

   ![トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

   ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** を選択します。

8. **[マッピング]** セクションの **[Provision Azure Active Directory Users]\(Azure Active Directory ユーザーをプロビジョニングする\)** を選択します。
   ![AAD マッピング](media/papercut-cloud-print-management-provisioning-tutorial/mapping.png)

9. **[属性マッピング]** セクションで、Azure AD から PaperCut Cloud Print Management に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で PaperCut Cloud Print Management のユーザー アカウントとの照合に使用されます。 [一致する対象の属性](../app-provisioning/customize-application-attributes.md)を変更する場合は、その属性に基づいたユーザーのフィルター処理が確実に PaperCut Cloud Print Management API でサポートされているようにする必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|フィルター処理のサポート|
   |---|---|---|
   |userName|String|&check;|
   |displayName|String|
   |emails[type eq "work"].value|String|

10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

11. PaperCut Cloud Print Management に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

12. **[設定]** セクションの **[スコープ]** で目的の値を選択することによって、PaperCut Cloud Print Management にプロビジョニングするユーザーまたはグループ、あるいはその両方を定義します。

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

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)