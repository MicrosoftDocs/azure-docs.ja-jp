---
title: チュートリアル:8x8 を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure AD から 8x8 に対してユーザー アカウントを自動的にプロビジョニングおよびプロビジョニング解除する方法を学習します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/15/2020
ms.author: Zhchia
ms.openlocfilehash: a6aa4ad009d037e6ea0d1ade3cc9735351bd634a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96558863"
---
# <a name="tutorial-configure-8x8-for-automatic-user-provisioning"></a>チュートリアル:8x8 を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために 8x8 Admin Console と Azure Active Directory (Azure AD) の両方で実行する必要がある手順について説明します。 構成すると、Azure AD では、Azure AD プロビジョニング サービスを使用して、[8x8](https://www.8x8.com) に対するユーザーとグループのプロビジョニングおよびプロビジョニング解除が自動的に行われます。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。 

## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * 8x8 でユーザーを作成する
> * アクセスが不要になった場合に 8x8 のユーザーを削除する
> * Azure AD と 8x8 の間でユーザー属性の同期を維持する
> * 8x8 への[シングル サインオン](./8x8virtualoffice-tutorial.md) (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md) 
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。
* 任意のレベルの 8x8 X シリーズ サブスクリプション。
* [Admin Console](https://vo-cm.8x8.com) の管理者権限を備えた 8x8 ユーザー アカウント。
* [Azure AD でのシングル サインオン](./8x8virtualoffice-tutorial.md)は既に構成されています。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
2. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
3. [Azure AD と 8x8 の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。

## <a name="step-2-configure-8x8-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD でのプロビジョニングをサポートするように 8x8 を構成する

このセクションでは、Azure AD でのプロビジョニングをサポートするように 8x8 を構成する手順を説明します。

### <a name="to-configure-a-user-provisioning-access-token-in-8x8-admin-console"></a>8x8 Admin Console でユーザー プロビジョニング アクセス トークンを構成するには、次の操作を実行します。

1. [管理コンソール](https://admin.8x8.com)にサインインします。 **[ID 管理]** を選択します。

   ![[Admin]](./media/8x8-provisioning-tutorial/8x8-identity-management.png)

2. **[Show user provisioning information]\(ユーザー プロビジョニング情報を表示する\)** リンクをクリックして、トークンを生成します。

   ![表示](./media/8x8-provisioning-tutorial/8x8-show-user-provisioning.png)

3. **8x8 URL** 値と **8x8 API トークン** 値をコピーします。 これらの値は、Azure portal の 8x8 アプリケーションの [プロビジョニング] タブにある **[テナントの URL]** フィールドと **[シークレット トークン]** フィールドにそれぞれ入力します。

   ![トークン](./media/8x8-provisioning-tutorial/8x8-copy-url-token.png)

## <a name="step-3-add-8x8-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから 8x8 を追加する

Azure AD アプリケーション ギャラリーから 8x8 を追加して、8x8 へのプロビジョニングの管理を開始します。 SSO のために 8x8 を以前に設定している場合は、その同じアプリケーションを使用することができます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](../manage-apps/add-application-portal.md)を参照してください。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 これは、より簡単なオプションであり、ほとんどの人々に使用されています。

ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)で説明されているスコープ フィルターを使用できます。 

* 8x8 にユーザーとグループを割り当てるときは、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 

## <a name="step-5-configure-automatic-user-provisioning-to-8x8"></a>手順 5. 8x8 への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて 8x8 のユーザーやグループを作成、更新、無効化する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-8x8-in-azure-ad"></a>Azure AD で 8x8 の自動ユーザー プロビジョニングを構成するには、次の操作を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](./media/8x8-provisioning-tutorial/enterprise-applications.png)

    ![[すべてのアプリケーション] ブレード](./media/8x8-provisioning-tutorial/all-applications.png)

2. アプリケーションの一覧で **[8x8]** を選択します。

    ![アプリケーションの一覧の 8x8 リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。 **[開始]** をクリックします。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

   ![[開始] ブレード](./media/8x8-provisioning-tutorial/get-started.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[Admin Credentials]\(管理者の資格情報\)** セクションで、**8x8 URL** を Admin Console から **[テナントの URL]** にコピーします。 **8x8 API トークン** を Admin Console から **[シークレット トークン]** にコピーします。 **[テスト接続]** をクリックして、Azure AD から 8x8 への接続を確認します。 接続できない場合は、使用中の 8x8 アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL とシークレット トークンを入力できる場所である [管理者資格情報] ダイアログ ボックスを示すスクリーンショット。](./media/8x8-provisioning-tutorial/provisioning.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** を選択します。

8. **[マッピング]** セクションの **[Provision Azure Active Directory Users]\(Azure Active Directory ユーザーをプロビジョニングする\)** を選択します。

9. **[属性マッピング]** セクションで、Azure AD から 8x8 に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で 8x8 のユーザー アカウントとの照合に使用されます。 [一致する対象の属性](../app-provisioning/customize-application-attributes.md)を変更する場合は、その属性に基づいたユーザーのフィルター処理が確実に 8x8 API でサポートされているようにする必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|Notes|
   |---|---|---|
   |userName|String|ユーザー名とフェデレーション ID の両方を設定します|
   |externalId|String||
   |active|Boolean||
   |title|String||
   |emails[type eq "work"].value|String||
   |name.givenName|String||
   |name.familyName|String||
   |phoneNumbers[type eq "mobile"].value|String|個人の連絡先番号|
   |phoneNumbers[type eq "work"].value|String|個人の連絡先番号|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String||
   |urn:ietf:params:scim:schemas:extension:8x8:1.1:User:site|String|ユーザーの作成後は更新できません|
   |locale|String|既定ではマップされません|
   |timezone|String|既定ではマップされません|

10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

11. 8x8 に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

12. **[設定]** セクションの **[スコープ]** で目的の値を選択して、8x8 にプロビジョニングするユーザーやグループを定義します。

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