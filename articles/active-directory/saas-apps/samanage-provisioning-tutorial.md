---
title: チュートリアル:Samanage を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure AD から Samanage に対してユーザー アカウントを自動的にプロビジョニングおよびプロビジョニング解除する方法を学習します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/13/2020
ms.author: Zhchia
ms.openlocfilehash: 21a3c81d9a24cc63d3fc77c95c94df1e9113d292
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543450"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>チュートリアル:Samanage を構成し、自動ユーザー プロビジョニングに対応させる
このチュートリアルでは、自動ユーザー プロビジョニングを構成するために Samanage と Azure Active Directory (Azure AD) の両方で行う必要がある手順について説明します。 構成すると、Azure AD では、Azure AD プロビジョニング サービスを使用して、[Samanage](https://www.samanage.com/pricing/) に対するユーザーとグループのプロビジョニングおよびプロビジョニング解除が自動的に行われます。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。

## <a name="migrate-to-the-new-samange-application"></a>新しい Samange アプリケーション への移行

Samanage との統合が既にある場合は、今後の変更について以下のセクションを参照してください。 Samanage を初めて設定する場合は、このセクションをスキップして、「**サポートされる機能**」に進むことができます。

#### <a name="whats-changing"></a>変更点
* Azure AD 側での変更点: Samanage でユーザーをプロビジョニングするための承認方法は、従来は **Basic 認証**でした。間もなく、承認方法が**有効期間の長いシークレット トークン**に変更されます。


#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>既存のカスタム統合を新しいアプリケーションに移行するには、どうすればよいですか?
既存の Samanage と有効な管理者資格情報が統合されている場合は、**対応は不要です**。 新しいアプリケーションに顧客が自動的に移行されます。 この処理は、完全にバックグラウンドで実行されます。 既存の資格情報の有効期限が切れている場合、またはアプリケーションへのアクセスを再度承認する必要がある場合は、有効期間の長いシークレット トークンを生成する必要があります。 新しいトークンを生成するには、この記事の手順 2 を参照してください。


#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>アプリケーションが移行されたかどうかを確認するにはどうすればよいですか? 
アプリケーションを移行すると、 **[管理者資格情報]** セクションで、 **[管理ユーザー名]** と **[管理パスワード]** フィールドが、1 つの **[シークレット トークン]** フィールドに置き換えられます。

## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * Samanage でユーザーを作成する
> * アクセスが不要になった場合に Samanage のユーザーを削除する
> * Azure AD と Samanage の間でユーザー属性の同期を維持する
> * Samanage でグループとグループ メンバーシップをプロビジョニングする
> * Samanage への[シングル サインオン](https://docs.microsoft.com/azure/active-directory/saas-apps/samanage-tutorial) (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* プロビジョニングを構成するための[アクセス許可](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。 
* Professional パッケージを含む [Samanage テナント](https://www.samanage.com/pricing/)。
* 管理者アクセス許可がある Samanage のユーザー アカウント

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)を確認します。
2. [プロビジョニングの対象](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)となるユーザーを決定します。
3. [Azure AD と Samanage の間でマップする](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)データを決定します。 

## <a name="step-2-configure-samanage-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD でのプロビジョニングをサポートするように Samanage を構成する

認証用のシークレット トークンを生成するには、[こちら](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657)参照してください。

## <a name="step-3-add-samanage-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから Samanage を追加する

Azure AD アプリケーション ギャラリーから Samanage を追加して、Samanage へのプロビジョニングの管理を開始します。 SSO のために Samanage を以前に設定している場合は、その同じアプリケーションを使用することができます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)を参照してください。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)で説明されているスコープ フィルターを使用できます。 

* Samanage にユーザーとグループを割り当てるときは、**既定のアクセス**以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)を指定できます。 


## <a name="step-5-configure-automatic-user-provisioning-to-samanage"></a>手順 5. Samanage への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザー、グループ、またはその両方の割り当てに基づいて、TestApp でユーザー、グループ、またはその両方が作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Azure AD で Samanage の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Samanage]** を選択します。

    ![アプリケーションの一覧の Samanage のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://api.samanage.com`」と入力します。  前に取得したシークレット トークンの値を、 **[シークレット トークン]** に入力します。 **[接続テスト]** をクリックして、Azure AD から Samanage に接続できることを確認します。 接続できない場合は、使用中の Samanage アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![プロビジョニング](./media/samanage-provisioning-tutorial/provisioning.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** を選択します。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Samanage]\(Azure Active Directory ユーザーを Samanage に同期する\)** を選びます。

9. **[属性マッピング]** セクションで、Azure AD から Samanage に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Samanage のユーザー アカウントとの照合に使用されます。 [一致する対象の属性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)を変更する場合は、その属性に基づいたユーザーのフィルター処理が確実に Samanage API でサポートされているようにする必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

      ![Samanage のユーザー マッピング](./media/samanage-provisioning-tutorial/user-attributes.png)

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to Samanage]\(Azure Active Directory グループを Samanage に同期する\)** を選択します。

11. **[属性マッピング]** セクションで、Azure AD から Samanage に同期されるグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新操作で Samanage のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

      ![Samange のグループ マッピング](./media/samanage-provisioning-tutorial/group-attributes.png)

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Samanage に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Samanage にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 初期サイクルは後続の同期よりも実行に時間がかかります。後続のサイクルは、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

1. [プロビジョニング ログ](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
2. [進行状況バー](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
3. プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)を参照してください。

## <a name="connector-limitations"></a>コネクタの制限事項

**[すべてのユーザーとグループを同期する]** オプションを選択し、Samanage の**ロール**属性に値を構成した場合、 **[null の場合の既定値 (オプション)]** ボックスの値は次の形式で表現する必要があります。

- {"displayName":"role"}。この role は使用する既定値です。

## <a name="change-log"></a>ログの変更

* 2020 年 4 月 22 日 - 基本認証から有効期間の長いシークレット トークンへ承認方法を更新しました。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)
