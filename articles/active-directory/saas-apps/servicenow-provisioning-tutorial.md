---
title: チュートリアル:ServiceNow を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure AD から ServiceNow へのユーザー アカウントのプロビジョニングおよびプロビジョニング解除を自動的に実行する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: b3b62e7c16106fd9d94d4a3438331dab4ce8b6e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99539045"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>チュートリアル:自動ユーザー プロビジョニングのために ServiceNow を構成する

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために、ServiceNow と Azure Active Directory (Azure AD) の両方で実行する手順について説明します。 Azure AD を構成すると、Azure AD プロビジョニング サービスを使用して、[ServiceNow](https://www.servicenow.com/) に対するユーザーとグループのプロビジョニングおよびプロビジョニング解除が自動的に実行されます。 

このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。 


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * ServiceNow でユーザーを作成する
> * アクセスが不要になったユーザーを ServiceNow で削除する
> * Azure AD と ServiceNow の間でユーザー属性の同期を維持する
> * ServiceNow でグループとグループ メンバーシップをプロビジョニングする
> * ServiceNow への[シングル サインオン](servicenow-tutorial.md)を許可する (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md) 
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、またはグローバル管理者)
* Calgary 以降の [ServiceNow インスタンス](https://www.servicenow.com/)
* Helsinki 以降の [ServiceNow Express インスタンス](https://www.servicenow.com/)
* 管理者ロールを持つ ServiceNow のユーザー アカウント

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1:プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
2. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
3. [Azure AD と ServiceNow の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>手順 2:Azure AD でのプロビジョニングをサポートするように ServiceNow を構成する

1. ServiceNow インスタンス名を指定します。 インスタンス名は、ServiceNow にアクセスするために使用する URL で確認できます。 次の例では、インスタンス名は **dev35214** です。

   ![ServiceNow インスタンスを示すスクリーンショット。](media/servicenow-provisioning-tutorial/servicenow-instance.png)

2. ServiceNow で管理者の資格情報を取得します。 ServiceNow でユーザー プロファイルに移動し、ユーザーに管理者ロールが割り当てられていることを確認します。 

   ![ServiceNow 管理者ロールを示すスクリーンショット。](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)


## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>手順 3: Azure AD アプリケーション ギャラリーから ServiceNow を追加する

Azure AD アプリケーション ギャラリーから ServiceNow を追加して、ServiceNow へのプロビジョニングの管理を開始します。 シングル サインオン (SSO) のために ServiceNow を以前に設定している場合は、同じアプリケーションを使用できます。 ただし、統合をテストするときは、別のアプリを作成することをお勧めします。 [ギャラリーからアプリケーションを追加する方法の詳細をご覧ください](../manage-apps/add-application-portal.md)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4:プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、あるいはユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合は、[こちらの手順を使用してユーザーとグループをアプリケーションに割り当てる](../manage-apps/assign-user-or-group-access-portal.md)ことができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合は、[スコープ フィルターを使用](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)できます。 

次のヒントに留意してください。

* ServiceNow にユーザーとグループを割り当てるときは、既定のアクセス以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定されている場合、これを制御するには、アプリにユーザーまたはグループを 1 つか、2 つ割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>手順 5:ServiceNow への自動ユーザー プロビジョニングを構成する 

このセクションでは、TestApp でユーザーとグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。 Azure AD では、ユーザーとグループの割り当てに基づいて構成を行うことができます。

Azure AD で ServiceNow の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。

    ![[エンタープライズ アプリケーション] ペインのスクリーンショット。](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[ServiceNow]** を選択します。

    ![アプリケーションの一覧を示すスクリーンショット。](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションで、ServiceNow 管理者の資格情報とユーザー名を入力します。 **[テスト接続]** を選択して、Azure AD から ServiceNow に接続できることを確認します。 接続に失敗した場合は、お使いの ServiceNow アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![管理者資格情報を入力できる [サービス プロビジョニング] ページを示すスクリーンショット。](./media/servicenow-provisioning-tutorial/servicenow-provisioning.png)

6. プロビジョニングのエラー通知を受け取るユーザーまたはグループのメール アドレスを **[通知用メール]** フィールドに入力し、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メールのボックスを示すスクリーンショット。](common/provisioning-notification-email.png)

7. **[保存]** を選択します。

8. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Users to ServiceNow]\(Azure Active Directory ユーザーを ServiceNow に同期する\)** を選択します。

9. **[属性マッピング]** セクションで、Azure AD から ServiceNow に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で ServiceNow のユーザー アカウントとの照合に使用されます。 

   [一致する対象の属性](../app-provisioning/customize-application-attributes.md)を変更する場合は、その属性に基づいたユーザーのフィルター処理が ServiceNow API でサポートされていることを確認する必要があります。 
   
   **[保存]** ボタンをクリックして変更をコミットします。

10. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Groups to ServiceNow]\(Azure Active Directory グループを ServiceNow に同期する\)** を選択します。

11. **[属性マッピング]** セクションで、Azure AD から ServiceNow に同期されるグループ属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で ServiceNow のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の手順を参照してください。

13. ServiceNow に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![[プロビジョニングの状態] が [オン] に設定されていることを示すスクリーンショット。](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、ServiceNow にプロビジョニングするユーザーとグループを定義します。

    ![プロビジョニングのスコープの選択肢を示すスクリーンショット。](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** を選択します。

    ![プロビジョニング構成を保存するためのボタンのスクリーンショット。](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 初期サイクルは、後続のサイクルよりも実行に時間がかかります。 Azure AD プロビジョニング サービスが実行されている限り、後続のサイクルは約 40 分ごとに発生します。 

## <a name="step-6-monitor-your-deployment"></a>手順 6:デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

- [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
- [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
- プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 [検疫状態の詳細をご覧ください](../app-provisioning/application-provisioning-quarantine-status.md)。  

## <a name="troubleshooting-tips"></a>トラブルシューティングのヒント
* ServiceNow の特定の属性 (**Department** や **Location** など) をプロビジョニングする場合は、ServiceNow の参照テーブルに値が既に存在する必要があります。 存在しない場合、**InvalidLookupReference** エラーが発生します。 

   たとえば、ServiceNow の特定のテーブルに 2 つの場所 (Seattle、Los Angeles) と 3 つの部門 (Sales、Finance、Marketing) があるとします。 部門が "Sales" で場所が "Seattle" のユーザーをプロビジョニングすると、そのユーザーは正常にプロビジョニングされます。 部門が "Sales" で場所が "LA" のユーザーをプロビジョニングしようとすると、そのユーザーはプロビジョニングされません。 ServiceNow の参照テーブルに場所 "LA" を追加するか、ServiceNow の形式に合わせて Azure AD のユーザー属性を更新する必要があります。 
* **EntryJoiningPropertyValueIsMissing** エラーが発生した場合は、[属性マッピング](../app-provisioning/customize-application-attributes.md)を確認して、一致する属性を特定します。 プロビジョニングしようとしているユーザーまたはグループに、この値が存在する必要があります。 
* 要件や制限事項 (ユーザーの国番号を指定する際の形式など) については、[ServiceNow SOAP API](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) を確認してください。
* プロビジョニング要求は、既定では https://{your-instance-name}.service-now.com/{table-name} に送信されます。 カスタム テナント URL が必要な場合は、URL 全体をインスタンス名として指定できます。
* **ServiceNowInstanceInvalid** エラーは、ServiceNow インスタンスとの通信に問題があることを示しています。 このエラーのテキストを次に示します。
  
  `Details: Your ServiceNow instance name appears to be invalid.  Please provide a current ServiceNow administrative user name and          password along with the name of a valid ServiceNow instance.`                                                              

  テスト接続の問題が発生している場合は、ServiceNow の次の設定で **[No]\(いいえ\)** を選択してみてください。
   
  - **[System Security]\(システム セキュリティ\)**  >  **[High security settings]\(高セキュリティ設定\)**  >  **[Require basic authentication for incoming SCHEMA requests]\(受信 SCHEMA 要求に対して基本認証を要求する\)**
  - **[System Properties]\(システム プロパティ\)**  >  **[Web Services]\(Web サービス\)**  >  **[Require basic authorization for incoming SOAP requests]\(受信 SOAP 要求に対して基本認証を要求する\)**

     ![SOAP 要求を承認するためのオプションを示すスクリーンショット。](media/servicenow-provisioning-tutorial/servicenow-webservice.png)

  引き続き問題が解決しない場合は、ServiceNow サポートに連絡し、トラブルシューティングに役立てるために SOAP デバッグを有効にするように依頼してください。 

* 現在、Azure AD プロビジョニング サービスは特定の [IP 範囲](../app-provisioning/use-scim-to-provision-users-and-groups.md#ip-ranges)で動作します。 必要に応じて、他の IP 範囲を制限し、これらの特定の IP 範囲をアプリケーションの許可リストに追加できます。 この手法により、Azure AD プロビジョニング サービスからアプリケーションへのトラフィック フローが可能になります。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
