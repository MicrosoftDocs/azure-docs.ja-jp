---
title: チュートリアル:Slack のユーザー プロビジョニング - Azure AD
description: Azure Active Directory を構成して、ユーザー アカウントを Slack に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 05/06/2020
ms.author: arvinh
ms.openlocfilehash: 368d75ecffda49f688a7a5ce11b60693650014c6
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88527827"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>チュートリアル:Slack を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Azure AD から Slack にユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除するうえで Slack と Azure AD で実行する必要がある手順について説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。 


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * Slack でユーザーを作成する
> * アクセスが不要になった場合に Slack のユーザーを削除する
> * Azure AD と Slack の間でユーザー属性の同期を維持する
> * Slack でグループとグループ メンバーシップをプロビジョニングする
> * Slack への[シングル サインオン](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-tutorial) (推奨)


## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* [Azure AD テナント](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)。
* プロビジョニングを構成するための[アクセス許可](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。
* [Plus プラン](https://aadsyncfabric.slack.com/pricing)以上の Slack テナントが有効になっていること。
* Team Admin アクセス許可がある Slack のユーザー アカウント。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)を確認します。
2. [プロビジョニングの対象](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)となるユーザーを決定します。
3. [Azure AD と Slack の間でマップする](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)データを決定します。 

## <a name="step-2-add-slack-from-the-azure-ad-application-gallery"></a>手順 2. Azure AD アプリケーション ギャラリーから Slack を追加する

Azure AD アプリケーション ギャラリーから Slack を追加して、Slack へのプロビジョニングの管理を開始します。 SSO のために以前 Slack を設定している場合は、同じアプリケーションを使用できます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)を参照してください。 

## <a name="step-3-define-who-will-be-in-scope-for-provisioning"></a>手順 3. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)で説明されているスコープ フィルターを使用できます。 

* Slack にユーザーとグループを割り当てるときは、**既定のアクセス**以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)を指定できます。

## <a name="step-4-configure-automatic-user-provisioning-to-slack"></a>手順 4. Slack への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD を Slack のユーザー アカウント プロビジョニング API に接続する手順のほか、プロビジョニング サービスを構成して、Azure AD のユーザーとグループの割り当てに基づいて割り当て済みのユーザー アカウントを Slack で作成、更新、無効化する手順を説明します。

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Azure AD で Slack への自動ユーザー アカウント プロビジョニングを構成するには

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Slack]** を選択します。

    ![アプリケーションの一覧の [Slack] リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションにある **[承認する]** をクリックします。 これで、ブラウザーの新しいウィンドウで Slack 承認ダイアログが開きます。

    ![承認](media/slack-provisioning-tutorial/authorization.png)


6. 新しいウィンドウで、Team Admin アカウントを使用して Slack にサインインします。 表示された承認ダイアログで、プロビジョニングを有効にしたい Slack チームを選択して **[承認する]** を選択します。 終わったら Azure Portal に戻り、プロビジョニング構成を完了します。

    ![承認ダイアログ](./media/slack-provisioning-tutorial/slackauthorize.png)

7. Azure Portal で、 **[テスト接続]** をクリックして Azure AD が Slack アプリに接続できることを確認します。 接続が失敗した場合、使用中の Slack アカウントに Team Admin アクセス許可があることを確認して、"承認" の手順をもう一度試してください。

8. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

9. **[保存]** を選択します。

10. [マッピング] セクションの **[Synchronize Azure Active Directory Users to Slack (Azure Active Directory ユーザーを Slack に同期する)]** を選択します。

11. **[属性マッピング]** セクションで、Azure AD から Slack に同期されるユーザー属性を確認します。 **[Matching (照合)]** プロパティとして選択されている属性は、更新処理で Slack のユーザー アカウントとの照合に使用されることに注意してください。 [保存] ボタンをクリックして変更をコミットします。

   |属性|Type|
   |---|---|
   |active|Boolean|
   |externalId|String|
   |displayName|String|
   |name.familyName|String|
   |name.givenName|String|
   |title|String|
   |emails[type eq "work"].value|String|
   |userName|String|
   |nickName|String|
   |addresses[type eq "untyped"].streetAddress|String|
   |addresses[type eq "untyped"].locality|String|
   |addresses[type eq "untyped"].region|String|
   |addresses[type eq "untyped"].postalCode|String|
   |addresses[type eq "untyped"].country|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "work"].value|String|
   |roles[primary eq "True"].value|String|
   |locale|String|
   |name.honorificPrefix|String|
   |photos[type eq "photo"].value|String|
   |profileUrl|String|
   |timezone|String|
   |userType|String|
   |urn:scim:schemas:extension:enterprise:1.0.department|String|
   |urn:scim:schemas:extension:enterprise:1.0.manager|リファレンス|
   |urn:scim:schemas:extension:enterprise:1.0.employeeNumber|String|
   |urn:scim:schemas:extension:enterprise:1.0.costCenter|String|
   |urn:scim:schemas:extension:enterprise:1.0.organization|String|
   |urn:scim:schemas:extension:enterprise:1.0.division|String|

12. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to Slack] (Azure Active Directory グループを Slack に同期する)** を選択します。

13. **[属性マッピング]** セクションで、Azure AD から Slack に同期するグループ属性を確認します。 **[Matching (照合)]** プロパティとして選択されている属性は、更新処理で Slack のグループとの照合に使用されることに注意してください。 [保存] ボタンをクリックして変更をコミットします。

      |属性|Type|
      |---|---|
      |displayName|String|
      |members|リファレンス|

14. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

15. Slack に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

16. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Slack にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

17. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 初期サイクルは後続の同期よりも実行に時間がかかります。後続のサイクルは、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 

## <a name="step-5-monitor-your-deployment"></a>手順 5. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

1. [プロビジョニング ログ](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
2. [進行状況バー](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
3. プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)を参照してください。

## <a name="troubleshooting-tips"></a>トラブルシューティングのヒント

* Slack の **displayName**属性を構成する際、次の動作に注意してください。

  * 値は (例: 2 人のユーザーは同じ表示名を持てる) が完全に一意ではありません

  * 非英語の文字、スペース、大文字と小文字をサポートしています。 
  
  * 許可されている句読点はピリオド、アンダースコア、ハイフン、アポストロフィ、かっこ (例: **( [ { } ] )** ) と区切り記号 (例: **, /;** ) です。
  
  * displayName プロパティに '@' 文字を含めることはできません。 '@' を含めると、プロビジョニング ログに「AttributeValidationFailed」という説明とともにスキップされたイベントが記録される可能性があります。

  * Slack のワークプレイスまたは組織内でこれらの 2 つの設定が構成されている場合のみ、更新を行います。**プロファイル同期が有効**、**ユーザーが表示名を変更できない**。

* Slack の **userName** 属性は 21 文字未満で、一意の値を持つ必要があります。

* Slack では、属性 **userName** と **email** との照合のみが許可されます。  
  
* 一般的なエラー コードについては、Slack の公式ドキュメント (https://api.slack.com/scim#errors ) を参照してください

## <a name="change-log"></a>ログの変更

* 2020 年 6 月 16 日 - 新しいユーザーの作成時にのみ更新されるように DisplayName 属性が変更されました。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)