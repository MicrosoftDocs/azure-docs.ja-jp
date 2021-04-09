---
title: チュートリアル:PureCloud by Genesys を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure AD から PureCloud by Genesys に対してユーザー アカウントを自動的にプロビジョニング、またはプロビジョニング解除する方法を説明します。
services: active-directory
author: Zhchia
writer: Zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2020
ms.author: Zhchia
ms.openlocfilehash: bbb9b47e42ce195a98801ee08d177efd409c597e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181664"
---
# <a name="tutorial-configure-purecloud-by-genesys-for-automatic-user-provisioning"></a>チュートリアル:PureCloud by Genesys を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために PureCloud by Genesys と Azure Active Directory (Azure AD) の両方で実行する必要がある手順について説明します。 構成すると、Azure AD では、ユーザーとグループの [PureCloud by Genesys](https://www.genesys.com) へのプロビジョニングおよびプロビジョニング解除が Azure AD プロビジョニング サービスを使って自動的に行われるようになります。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。 


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * PureCloud by Genesys にユーザーを作成する
> * アクセスが不要になった場合に PureCloud by Genesys のユーザーを削除する
> * Azure AD と PureCloud by Genesys の間でユーザー属性の同期を維持する
> * PureCloud by Genesys でグループとグループ メンバーシップをプロビジョニングする
> * PureCloud by Genesys への[シングル サインオン](./purecloud-by-genesys-tutorial.md) (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md) 
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。 
* PureCloud [組織](https://help.mypurecloud.com/?p=81984)。
* OAuth クライアントを作成する[アクセス許可](https://help.mypurecloud.com/?p=24360)があるユーザー。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
2. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
3. [Azure AD と PureCloud by Genesys の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。 

## <a name="step-2-configure-purecloud-by-genesys-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD でのプロビジョニングをサポートするように PureCloud by Genesys を構成する

1. PureCloud 組織内に構成される [OAuth クライアント](https://help.mypurecloud.com/?p=188023)を作成します。
2. [OAuth クライアント](https://developer.mypurecloud.com/api/rest/authorization/use-client-credentials.html)を使用してトークンを生成します。
3. PureCloud 内のグループ メンバーシップを自動的にプロビジョニングしたい場合は、PureCloud に、Azure AD 内のグループとまったく同じ名前の[グループを作成](https://help.mypurecloud.com/?p=52397)する必要があります。

## <a name="step-3-add-purecloud-by-genesys-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから PureCloud by Genesys を追加する

Azure AD アプリケーション ギャラリーから PureCloud by Genesys を追加して、PureCloud by Genesys へのプロビジョニングの管理を開始します。 SSO のために PureCloud by Genesys を以前に設定している場合は、その同じアプリケーションを使用することができます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](../manage-apps/add-application-portal.md)を参照してください。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)で説明されているスコープ フィルターを使用できます。 

* PureCloud by Genesys にユーザーとグループを割り当てるときは、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 


## <a name="step-5-configure-automatic-user-provisioning-to-purecloud-by-genesys"></a>手順 5. PureCloud by Genesys への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザー、グループ、またはその両方の割り当てに基づいて、TestApp でユーザー、グループ、またはその両方が作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-purecloud-by-genesys-in-azure-ad"></a>Azure AD で PureCloud by Genesys の自動ユーザー プロビジョニングを構成するには

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[PureCloud by Genesys]** を選択します。

    ![アプリケーションの一覧の PureCloud by Genesys リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** フィールドと **[シークレット トークン]** フィールドに、それぞれ PureCloud by Genesys API の URL と OAuth トークンを入力します。 API URL は、[PureCloud Developer Center](https://developer.mypurecloud.com/api/rest/index.html) の PureCloud リージョンの API URL を使用して、`{{API Url}}/api/v2/scim/v2` の構造になります。 **[テスト接続]** をクリックして、Azure AD から PureCloud by Genesys への接続を確保します。 接続できない場合は、使用中の PureCloud by Genesys アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL とシークレット トークンを入力できる場所である [管理者資格情報] ダイアログ ボックスを示すスクリーンショット。](./media/purecloud-by-genesys-provisioning-tutorial/provisioning.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** を選択します。

8. **[Mappings]\(マッピング\)** セクションの **[Synchronize Azure Active Directory Users to PureCloud by Genesys]\(Azure Active Directory ユーザーを PureCloud by Genesys に同期する\)** を選びます。

9. **[属性マッピング]** セクションで、Azure AD から PureCloud by Genesys に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性を使用して、更新処理で PureCloud by Genesys のユーザー アカウントとの照合が行われます。 [一致する対象の属性](../app-provisioning/customize-application-attributes.md)を変更する場合は、その属性に基づいたユーザーのフィルター処理が確実に PureCloud by Genesys API でサポートされているようにする必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

     |属性|Type|
     |---|---|
     |userName|String|
     |active|Boolean|
     |displayName|String|
     |emails[type eq "work"].value|String|
     |title|String|
     |phoneNumbers[type eq "mobile"].value|String|
     |phoneNumbers[type eq "work"].value|String|
     |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
     |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|リファレンス|
     |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
     

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to PureCloud by Genesys]\(Azure Active Directory グループを PureCloud by Genesys に同期する\)** を選択します。

11. **[属性マッピング]** セクションで、Azure AD から PureCloud by Genesys に同期されるグループ属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新操作で PureCloud by Genesys のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。 PureCloud by Genesys では、グループの作成と削除はサポートされません。サポートされるのはグループの更新のみです。

      |属性|Type|
      |---|---|
      |displayName|String|
      |externalId|String|
      |members|リファレンス|

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. PureCloud by Genesys に対して Azure AD プロビジョニング サービスを有効にするには、**[Settings]\(設定\)** セクションで **[Provisioning Status]\(プロビジョニング状態\)** を **[On]\(オン\)** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、PureCloud by Genesys にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 初期サイクルは後続の同期よりも実行に時間がかかります。後続のサイクルは、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

* [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
* [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
* プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](../app-provisioning/application-provisioning-quarantine-status.md)を参照してください。

## <a name="change-log"></a>ログの変更

09/10 - エンタープライズ属性 "employeeNumber" のサポートが追加されました。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)