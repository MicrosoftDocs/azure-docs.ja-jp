---
title: チュートリアル:15Five を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: 15Five に対してユーザー アカウントの自動的なプロビジョニングとプロビジョニング解除を実行するように Azure Active Directory を構成する方法について説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 00f713e8d319d7ad8dcea014429c57d7fba40541
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181222"
---
# <a name="tutorial-configure-15five-for-automatic-user-provisioning"></a>チュートリアル:15Five を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure AD が自動的にユーザーまたはグループを 15Five にプロビジョニングまたは [15Five](https://www.15five.com/pricing/) からプロビジョニング解除するように構成するために、15Five と Azure Active Directory (Azure AD) で実行される手順を示すことです。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化」を参照してください。

> [!NOTE]
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * 15Five でユーザーを作成する
> * アクセスが不要になった場合に 15Five のユーザーを削除する
> * Azure AD と 15Five の間でユーザー属性の同期を維持する
> * 15Five でグループとグループ メンバーシップをプロビジョニングする
> * 15Five への[シングル サインオン](./15five-tutorial.md) (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md)。
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。
* [15Five テナント](https://www.15five.com/pricing/)。
* Admin アクセス許可がある 15Five のユーザー アカウント

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
2. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
3. [Azure AD と 15Five の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。 

## <a name="step-2-configure-15five-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD でのプロビジョニングをサポートするように 15Five を構成する

Azure AD での自動ユーザー プロビジョニング用に 15Five を構成する前に、15Five 上で SCIM プロビジョニングを有効にする必要があります。

1. [15Five 管理コンソール](https://my.15five.com/)にサインインします。 **[機能] > [統合]** の順に移動します。

    :::image type="content" source="media/15five-provisioning-tutorial/integration.png" alt-text="15Five 管理コンソールのスクリーンショット。メニューの [機能] に [統合] が表示されており、[機能] と [統合] の両方が強調表示されています。" border="false":::

2.  **[SCIM 2.0]** をクリックします。

    :::image type="content" source="media/15five-provisioning-tutorial/image00.png" alt-text="15Five 管理コンソールの [統合] ページのスクリーンショット。[ツール] の [S C I M 2.0] が強調表示されています。" border="false":::

3.  **[SCIM integration]\(SCIM 統合\) > [Generate OAuth token]\(OAuth トークンの生成\)** の順に移動します。

    :::image type="content" source="media/15five-provisioning-tutorial/image02.png" alt-text="15Five 管理コンソールの [S C I M integration]\(S C I M 統合\) ページのスクリーンショット。[Generate OAuth token]\(OAuth トークンの生成\) が強調表示されています。" border="false":::

4.  **[SCIM 2.0 base URL]\(SCIM 2.0 ベース URL\)** と **[アクセス トークン]** の値をコピーします。 この値を、Azure portal の 15Five アプリケーションの [プロビジョニング] タブにある **[テナント URL]** および **[シークレット トークン]** フィールドに入力します。
    
    :::image type="content" source="media/15five-provisioning-tutorial/image03.png" alt-text="[S C I M integration]\(S C I M 統合\) ページのスクリーンショット。[Token]\(トークン\) テーブルの [S C I M 2.0 base U R L]\(S C I M 2.0 ベース U R L\) と [アクセス トークン] の横にある値が強調表示されています。" border="false":::

## <a name="step-3-add-15five-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから 15Five を追加する

Azure AD アプリケーション ギャラリーから 15Five を追加して、15Five へのプロビジョニングの管理を開始します。 SSO のために 15Five を以前に設定している場合は、その同じアプリケーションを使用することができます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](../manage-apps/add-application-portal.md)を参照してください。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)で説明されているスコープ フィルターを使用できます。 

* 15Five にユーザーとグループを割り当てるときは、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。

## <a name="step-5-configure-automatic-user-provisioning-to-15five"></a>手順 5. 15Five への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて 15Five のユーザーやグループを作成、更新、無効化する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-15five-in-azure-ad"></a>Azure AD で 15Five の自動ユーザー プロビジョニングを構成するには、次の操作を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[15Five]** を選択します。

    ![アプリケーションの一覧の 15Five のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5.  [管理者資格情報] セクションの **[テナント URL]** フィールドと **[シークレット トークン]** フィールドに、先ほど取得した **SCIM 2.0 ベース URL と [アクセス トークン]** の値をそれぞれ入力します。 **[Test Connection]\(テスト接続\)** をクリックして、Azure AD から 15Five に接続できることを確認します。 接続できない場合は、その 15Five アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to 15Five]\(Azure Active Directory ユーザーを 15Five に同期する\)** を選択します。

9. **[属性マッピング]** セクションで、Azure AD から 15Five に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で 15Five のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。


   |属性|Type|
   |---|---|
   |active|Boolean|
   |title|String|
   |emails[type eq "work"].value|String|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|リファレンス|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:15Five:2.0:User:location|String|
   |urn:ietf:params:scim:schemas:extension:15Five:2.0:User:startDate|String|

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to 15Five]\(Azure Active Directory グループを 15Five に同期する\)** を選択します。

11. **[属性マッピング]** セクションで、Azure AD から 15Five に同期されるグループ属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で 15Five のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

      |属性|Type|
      |---|---|
      |externalId|String|
      |displayName|String|
      |members|リファレンス|

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. 15Five に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、15Five にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

    これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

1. [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
2. [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
3. プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](../app-provisioning/application-provisioning-quarantine-status.md)を参照してください。  
    
## <a name="connector-limitations"></a>コネクタの制限事項

* 15Five では、ユーザー向けに論理的な削除はサポートされていません。

## <a name="change-log"></a>ログの変更

* 2020/06/16 - ユーザー向けにエンタープライズ拡張属性 "Manager" とカスタム属性 "Location" と"Start Date" のサポートが追加されました。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)。
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)