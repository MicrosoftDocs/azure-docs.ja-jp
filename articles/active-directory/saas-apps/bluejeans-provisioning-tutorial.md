---
title: 'チュートリアル: BlueJeans を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs'
description: Azure Active Directory を構成して、ユーザー アカウントを BlueJeans に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: d029f033a3c452587dbeeadf69c46cc99f604031
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053857"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>チュートリアル: BlueJeans を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために BlueJeans と Azure Active Directory (Azure AD) の両方で実行する必要がある手順について説明します。 構成すると、Azure AD では、Azure AD プロビジョニング サービスを使用して、[BlueJeans](https://www.bluejeans.com/pricing) に対するユーザーのプロビジョニングおよびプロビジョニング解除が自動的に行われます。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。 

## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * BlueJeans でユーザーを作成する
> * アクセスが不要になった BlueJeans のユーザーを削除する
> * Azure AD と BlueJeans の間でのユーザー属性の同期を維持する
> * BlueJeans に[シングル サインオン](https://docs.microsoft.com/azure/active-directory/saas-apps/bluejeans-tutorial)する (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md)。
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。 
* [My Company](https://www.bluejeans.com/pricing) プラン (有効になっているのが望ましい) を使用した BlueJeans テナント。
* Admin アクセス許可がある BlueJeans のユーザー アカウント。
* BlueJeans Enterprise で SCIM プロビジョニングが有効になっていること。

> [!NOTE]
> Azure AD プロビジョニング統合では、Standard プラン以上の BlueJeans チームで使用できる [BlueJeans API](https://BlueJeans.github.io/developer) が必要です。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
2. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
3. [Azure AD と BlueJeans の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。

## <a name="step-2-configure-bluejeans-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD によるプロビジョニングをサポートするように BlueJeans を構成する

1. BlueJeans 管理コンソールにログインします。 [Group Settings]\(グループ設定\) の [Security]\(セキュリティ\) に移動します。
2. **[Single Sign On]\(シングル サインオン\)** と **[Configure Now]\(今すぐ構成\)** を選択します。

    ![now](./media/bluejeans-provisioning-tutorial/configure.png)

3. **[Provision & Integration]\(プロビジョニングと統合\)** ウィンドウで **[Create and manage user accounts through IDP]\(ユーザー アカウントの作成と管理を IDP で行う\)** を選択し、 **[GENERATE TOKEN]\(トークンの生成\)** をクリックします。

    ![generate](./media/bluejeans-provisioning-tutorial/token.png)
    
4. トークンをコピーして保存します。 
5. BlueJeans テナントの URL は `https://api.bluejeans.com/v2/scim` です。 前の手順で取得した **テナント URL** と **シークレット トークン** が、Azure portal の BlueJeans アプリケーションの [プロビジョニング] タブに入力されます。

## <a name="step-3-add-bluejeans-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから BlueJeans を追加する

Azure AD アプリケーション ギャラリーから BlueJeans を追加して、BlueJeans へのプロビジョニングの管理を開始します。 SSO のために BlueJeans を以前に設定している場合は、同じアプリケーションを使用できます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](../manage-apps/add-application-portal.md)を参照してください。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーをアプリケーションに割り当てることができます。 ユーザーの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)で説明されているスコープ フィルターを使用できます。 

* BlueJeans にユーザーを割り当てるときは、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーでテストします。 プロビジョニングのスコープが割り当て済みユーザーに設定される場合、これを制御するには、1 人または 2 人のユーザーをアプリに割り当てます。 スコープがすべてのユーザーに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 

## <a name="step-5-configure-automatic-user-provisioning-to-bluejeans"></a>手順 5. BlueJeans への自動ユーザー プロビジョニングを構成する

このセクションでは、Azure AD でのユーザーの割り当てに基づいて、TestApp でユーザーが作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Azure AD で BlueJeans の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[BlueJeans]** を選択します。

    ![アプリケーション一覧の [BlueJeans] リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブの [自動]](common/provisioning-automatic.png)

5. 手順 2. で取得した BlueJeans の [テナントの URL] と [シークレット トークン] を **[管理者資格情報]** セクションに入力します。 **[テスト接続]** をクリックして、Azure AD から BlueJeans への接続を確保します。 接続できない場合は、使用中の BlueJeans アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![トークン](common/provisioning-testconnection-tenanturltoken.png)


6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーのメール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** を選択します。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to BlueJeans]\(Azure Active Directory ユーザーを BlueJeans に同期する\)** を選択します。

9. **[属性マッピング]** セクションで、Azure AD から BlueJeans に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で BlueJeans のユーザー アカウントとの照合に使用されます。 [照合する対象の属性](../app-provisioning/customize-application-attributes.md)を変更する場合は、その属性に基づいたユーザーのフィルター処理が BlueJeans API で確実にサポートされている必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

|属性|Type|フィルター処理のサポート|
|---|---|---|
|userName|String|&check;|
|active|Boolean|
|title|String|
|emails[type eq "work"].value|String|
|name.givenName|String|
|name.familyName|String|
|phoneNumbers[type eq "work"].value|String|
|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|String|

10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

11. BlueJeans に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

12. **[設定]** セクションの **[スコープ]** で目的の値を選択して、BlueJeans にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

13. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

この操作によって、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

1. [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
2. [進行状況バー](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
3. プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](../app-provisioning/application-provisioning-quarantine-status.md)を参照してください。  

## <a name="connector-limitations"></a>コネクタの制限事項

* Bluejeans では、30 文字を超える userNames は許可されません。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)