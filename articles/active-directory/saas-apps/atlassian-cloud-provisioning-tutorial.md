---
title: チュートリアル:Atlassian Cloud を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Atlassian Cloud に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2019
ms.author: jeedes
ms.openlocfilehash: 691d35267c255c933a8098b99301fbb795a3cd0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181069"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>チュートリアル:Atlassian Cloud を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure Active Directory (Azure AD) が自動的にユーザーまたはグループを [Atlassian Cloud](https://www.atlassian.com/licensing/cloud) にプロビジョニングまたは Atlassian Cloud からプロビジョニング解除するように構成するために、Atlassian Cloud と Azure AD で実行される手順を示すことです。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。 


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * Atlassian Cloud でユーザーを作成する
> * アクセスが不要になった場合に Atlassian Cloud のユーザーを削除する
> * Azure AD と Atlassian Cloud の間でユーザー属性の同期を維持する
> * Atlassian Cloud でグループとグループ メンバーシップをプロビジョニングする
> * Atlassian Cloud への[シングル サインオン](./atlassian-cloud-tutorial.md) (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md)。
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。
* [Atlassian Cloud テナント](https://www.atlassian.com/licensing/cloud)
* Admin アクセス許可がある Atlassian Cloud のユーザー アカウント。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
2. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
3. [Azure AD と Atlassian Cloud の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。

## <a name="step-2-configure-atlassian-cloud-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD でのプロビジョニングをサポートするように Atlassian Cloud を構成する

1. [[Atlassian Organization Manager]\(Atlassian Organization Manager\)](https://admin.atlassian.com) **> [select the org]\(組織の選択\) > [directory]\(ディレクトリ\)** に移動します。

    ![[ディレクトリ] オプションが強調表示された [管理] ページのスクリーンショット。](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

2. **[ユーザーのプロビジョニング]** をクリックし、 **[ディレクトリを作成する]** をクリックします。 **[Directory base URL] (ディレクトリ ベース URL)** と **[Bearer Token] (ベアラー トークン)** をコピーします。これは、Azure AD ポータルの Atlassian Cloud アプリケーションで、[プロビジョニング] タブの **[テナントの URL]** フィールドと **[シークレット トークン]** フィールドに入力します。

    ![[ユーザー プロビジョニング] オプションが強調表示された [管理] ページのスクリーンショット。](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png)![[トークンの作成] ページのスクリーンショット。](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png)
    ![デモの時間ディレクトリ トークン ページのスクリーンショット。](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)


## <a name="step-3-add-atlassian-cloud-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから Atlassian Cloud を追加する

Azure AD アプリケーション ギャラリーから Atlassian Cloud を追加して、Atlassian Cloud へのプロビジョニングの管理を開始します。 SSO のために Atlassian Cloud を以前に設定している場合は、その同じアプリケーションを使用することができます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](../manage-apps/add-application-portal.md)を参照してください。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)で説明されているスコープ フィルターを使用できます。 

* Atlassian Cloud にユーザーとグループを割り当てるときは、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 


## <a name="step-5-configuring-automatic-user-provisioning-to-atlassian-cloud"></a>手順 5. Atlassian Cloud への自動ユーザー プロビジョニングの構成 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Atlassian Cloud のユーザーやグループを作成、更新、無効化する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Azure AD で Atlassian Cloud の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインし、 **[エンタープライズ アプリケーション]** 、 **[すべてのアプリケーション]** 、 **[Atlassian Cloud]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Atlassian Cloud]** を選択します。

    ![[アプリケーション] リストの [Atlassian Cloud] リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションで、Atlassian Cloud のアカウントから先ほど取得した **[テナントの URL]** と **[シークレット トークン]** を入力します。 **[テスト接続]** をクリックして、Azure AD から Atlassian Cloud への接続を確保します。 接続できない場合は、使用中の Atlassian Cloud アカウントに管理者アクセス許可があることを確認してから、もう一度お試しください。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Atlassian Cloud]** を選択します。

9. **[属性マッピング]** セクションで、Azure AD から Atlassian Cloud に同期されるユーザー属性を確認します。 **[Matching]** プロパティとして選択されている属性は、更新処理で Atlassian Cloud のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|
   |---|---|
   |userName|String|
   |active|Boolean|
   |name.familyName|String|
   |name.givenName|String|
   |emails[type eq "work"].value|String|   

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to Atlassian Cloud]** を選択します。

11. **[属性マッピング]** セクションで、Azure AD から Atlassian Cloud に同期されるグループ属性を確認します。 **[Matching]** プロパティとして選択されている属性は、更新処理で Atlassian Cloud のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

      |属性|Type|
      |---|---|
      |displayName|String|
      |externalId|String|
      |members|リファレンス|

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Atlassian Cloud に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Atlassian Cloud にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

16. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

1. [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
2. [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
3. プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](../app-provisioning/application-provisioning-quarantine-status.md)を参照してください。  

## <a name="connector-limitations"></a>コネクタの制限事項

* Atlassian Cloud では、[検証済みドメイン](https://confluence.atlassian.com/cloud/organization-administration-938859734.html)からのみユーザーのプロビジョニングが可能です。
* Atlassian Cloud では、現在のところグループ名の変更はサポートされていません。 つまり、Azure AD でグループの displayName を変更しても、Atlassian Cloud には更新および反映されません。
* Azure AD の **mail** ユーザー属性の値は、ユーザーの Microsoft Exchange メールボックスが存在する場合にのみ設定されます。 ユーザーのメールボックスが存在しない場合は、別の適切な属性を Atlassian Cloud の **emails** 属性にマップすることをお勧めします。

## <a name="change-log"></a>ログの変更

* 2020 年 6 月 15 日 - グループ向けのバッチ PATCH のサポートが追加されました。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png