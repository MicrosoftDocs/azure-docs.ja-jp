---
title: チュートリアル:Snowflake を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Snowflake に対するユーザー アカウントのプロビジョニングおよびプロビジョニング解除を自動的に実行するように Azure Active Directory を構成する方法について説明します。
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
ms.openlocfilehash: 06f11763498e3e8393d688a71e1c37b466be3f6f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99539537"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>チュートリアル:Snowflake を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、[Snowflake](https://www.Snowflake.com/pricing/) に対するユーザーとグループのプロビジョニングおよびプロビジョニング解除を自動的に実行するように Azure Active Directory (Azure AD) を構成するために、Snowflake と Azure AD で実行する手順を示します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure AD での SaaS アプリ ユーザー プロビジョニングの自動化とは](../app-provisioning/user-provisioning.md)」をご覧ください。 

> [!NOTE]
> 現在、このコネクタはパブリック プレビュー段階にあります。 利用規約については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。

## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * Snowflake でユーザーを作成する
> * アクセスが不要になったユーザーを Snowflake で削除する
> * Azure AD と Snowflake の間でユーザー属性の同期を維持する
> * Snowflake でグループとグループ メンバーシップをプロビジョニングする
> * Snowflake への[シングル サインオン](./snowflake-tutorial.md)を許可する (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md)
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、またはグローバル管理者)
* [Snowflake テナント](https://www.Snowflake.com/pricing/)
* 管理者アクセス許可を持つ Snowflake のユーザー アカウント

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1:プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
2. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
3. [Azure AD と Snowflake の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。 

## <a name="step-2-configure-snowflake-to-support-provisioning-with-azure-ad"></a>手順 2:Azure AD でのプロビジョニングをサポートするように Snowflake を構成する

Azure AD を使用した自動ユーザー プロビジョニング用に Snowflake を構成する前に、Snowflake でクロスドメイン ID 管理システム (SCIM) プロビジョニングを有効にする必要があります。

1. Snowflake 管理コンソールにサインインします。 強調表示されているワークシートに次のクエリを入力し、 **[Run]\(実行\)** を選択します。

    ![クエリと [Run]\(実行\) ボタンが表示された Snowflake 管理コンソールのスクリーンショット。](media/Snowflake-provisioning-tutorial/image00.png)

2.  Snowflake テナントに対して SCIM アクセス トークンが生成されます。 これを取得するには、次のスクリーンショットで強調表示されているリンクを選択します。

    ![S C I M アクセス トークンが強調表示された、Snowflake U I のワークシートのスクリーンショット。](media/Snowflake-provisioning-tutorial/image01.png)

3. 生成されたトークンの値をコピーし、 **[Done]\(完了\)** を選択します。 この値は、Azure portal の Snowflake アプリケーションの **[プロビジョニング]** タブにある **[シークレット トークン]** ボックスに入力します。

    ![テキスト フィールドにコピーされたトークンと、強調表示された [Done]\(完了\) オプションを示す [Details]\(詳細\) セクションのスクリーンショット。](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="step-3-add-snowflake-from-the-azure-ad-application-gallery"></a>手順 3: Azure AD アプリケーション ギャラリーから Snowflake を追加する

Azure AD アプリケーション ギャラリーから Snowflake を追加して、Snowflake へのプロビジョニングの管理を開始します。 シングル サインオン (SSO) のために Snowflake を以前に設定している場合は、同じアプリケーションを使用できます。 ただし、最初に統合をテストするときは、別のアプリを作成することをお勧めします。 [ギャラリーからアプリケーションを追加する方法の詳細をご覧ください](../manage-apps/add-application-portal.md)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4:プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、あるいはユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合は、[こちらの手順を使用してユーザーとグループをアプリケーションに割り当てる](../manage-apps/assign-user-or-group-access-portal.md)ことができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合は、[スコープ フィルターを使用](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)できます。 

次のヒントに留意してください。

* Snowflake にユーザーとグループを割り当てるときは、既定のアクセス以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定されている場合、これを制御するには、アプリにユーザーまたはグループを 1 つか、2 つ割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 


## <a name="step-5-configure-automatic-user-provisioning-to-snowflake"></a>手順 5:Snowflake への自動ユーザー プロビジョニングを構成する 

このセクションでは、Snowflake でユーザーとグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。 Azure AD では、ユーザーとグループの割り当てに基づいて構成を行うことができます。

Azure AD で Snowflake の自動ユーザー プロビジョニングを構成するには、次の操作を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。

    ![[エンタープライズ アプリケーション] ペインのスクリーンショット。](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[Snowflake]** を選択します。

    ![アプリケーションの一覧を示すスクリーンショット。](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナント URL]** ボックスと **[シークレット トークン]** ボックスに、先ほど取得した SCIM 2.0 ベース URL と認証トークンをそれぞれ入力します。 

   **[テスト接続]** を選択して、Azure AD から Snowflake に接続できることを確認します。 接続に失敗した場合は、お使いの Snowflake アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント U R L およびシークレット トークンのボックスと、[テスト接続] ボタンを示すスクリーンショット。](common/provisioning-testconnection-tenanturltoken.png)

6. プロビジョニングのエラー通知を受け取るユーザーまたはグループのメール アドレスを **[通知用メール]** フィールドに入力し、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メールのボックスを示すスクリーンショット。](common/provisioning-notification-email.png)

7. **[保存]** を選択します。

8. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Users to Snowflake]\(Azure Active Directory ユーザーを Snowflake に同期する\)** を選択します。

9. **[属性マッピング]** セクションで、Azure AD から Snowflake に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Snowflake のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|
   |---|---|
   |active|Boolean|
   |displayName|String|
   |emails[type eq "work"].value|String|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:defaultRole|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:defaultWarehouse|String|

10. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Groups to Snowflake]\(Azure Active Directory グループを Snowflake に同期する\)** を選択します。

11. **[属性マッピング]** セクションで、Azure AD から Snowflake に同期されるグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Snowflake のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    |属性|Type|
    |---|---|
    |displayName|String|
    |members|リファレンス|

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の手順を参照してください。

13. Snowflake に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

     ![[プロビジョニングの状態] が [オン] に設定されていることを示すスクリーンショット。](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Snowflake にプロビジョニングするユーザーとグループを定義します。 

    このオプションが使用できない場合は、 **[管理者資格情報]** で必要なフィールドを構成して、 **[保存]** を選択し、ページを最新の情報に更新します。 

     ![プロビジョニングのスコープの選択肢を示すスクリーンショット。](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** を選択します。

     ![プロビジョニング構成を保存するためのボタンのスクリーンショット。](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期が開始されます。 初期同期は、後続の同期よりも実行に時間がかかります。 Azure AD プロビジョニング サービスが実行されている限り、後続の同期は約 40 分ごとに実行されます。

## <a name="step-6-monitor-your-deployment"></a>手順 6:デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

- [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
- [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
- プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 [検疫状態の詳細をご覧ください](../app-provisioning/application-provisioning-quarantine-status.md)。  

## <a name="connector-limitations"></a>コネクタの制限事項

Snowflake で生成される SCIM トークンの有効期間は 6 か月です。 プロビジョニングの同期を引き続き機能させるには、有効期限が切れる前にこれらのトークンを更新する必要があることに注意してください。 

## <a name="troubleshooting-tips"></a>トラブルシューティングのヒント

現在、Azure AD プロビジョニング サービスは特定の [IP 範囲](../app-provisioning/use-scim-to-provision-users-and-groups.md#ip-ranges)で動作します。 必要に応じて、他の IP 範囲を制限し、これらの特定の IP 範囲をアプリケーションの許可リストに追加できます。 この手法により、Azure AD プロビジョニング サービスからアプリケーションへのトラフィック フローが可能になります。

## <a name="change-log"></a>ログの変更

* 2020 年 7 月 21 日: (アクティブな属性を使用して) すべてのユーザーに対して論理的な削除を有効化。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ
* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
