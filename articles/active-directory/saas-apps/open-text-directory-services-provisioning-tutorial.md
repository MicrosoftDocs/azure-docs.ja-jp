---
title: チュートリアル:Azure Active Directory を使用した自動ユーザー プロビジョニング用に OpenText Directory Services を構成する | Microsoft Docs
description: Azure AD から OpenText Directory Services に対してユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除する方法について説明します。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: ad55ba5f-c56c-4ed0-bdfd-163d2883ed80
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: Zhchia
ms.openlocfilehash: 2f31eddab1070d073d3fd5a4761dad597e42a2e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181885"
---
# <a name="tutorial-configure-opentext-directory-services-for-automatic-user-provisioning"></a>チュートリアル:自動ユーザー プロビジョニング用に OpenText Directory Services を構成する

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために OpenText Directory Services と Azure Active Directory (Azure AD) の両方で実行する必要がある手順について説明します。 構成すると、Azure AD では、ユーザーとグループの OpenText Directory Services へのプロビジョニングおよびプロビジョニング解除が Azure AD プロビジョニング サービスを使って自動的に行われるようになります。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。 


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * OpenText Directory Services でユーザーを作成する
> * アクセスが不要になったユーザーを OpenText Directory Services から削除する
> * Azure AD と OpenText Directory Services の間でユーザー属性の同期を維持する
> * OpenText Directory Services でグループとグループ メンバーシップをプロビジョニングする
> * OpenText Directory Services に[シングル サインオン](./opentext-directory-services-tutorial.md)する (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md) 
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。 
* Azure AD によってアクセス可能な OTDS インストール。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
2. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
3. [Azure AD と OpenText Directory Services の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。 

## <a name="step-2-configure-opentext-directory-services-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD によるプロビジョニングをサポートするように OpenText Directory Services を構成する

> [!NOTE]
> 以下の手順は、OpenText Directory Services のインストールに適用されます。 OpenText CoreShare または OpenText OT2 のテナントには適用されません。

1. 専用の機密 **OAuth クライアント** を作成します。
2. 長い **アクセス トークンの有効期間** を設定します。

      ![アクセストークンの有効期間](media/open-text-directory-services-provisioning-tutorial/token-life.png)

3. リダイレクト URL は指定しないでください。 それらは必要ありません。 
4. OTDS により、**クライアント シークレット** が生成されて表示されます。 **クライアント ID** と **クライアント シークレット** を安全な場所に保存します。

      ![クライアント シークレット](media/open-text-directory-services-provisioning-tutorial/client-secret.png)

5. Azure AD から同期するユーザーとグループのパーティションを作成します。

      ![パーティション ページ](media/open-text-directory-services-provisioning-tutorial/partition.png)

6. 同期する Azure AD のユーザーとグループに使用するパーティションで作成した OAuth クライアントに管理者権限を付与します。    
      * [パーティション] -> [アクション] -> [管理者の編集]

      ![管理者ページ](media/open-text-directory-services-provisioning-tutorial/administrator.png)

5. シークレット トークンを取得し、Azure AD で構成する必要があります。 これには、任意の HTTP クライアント アプリケーションを使用できます。 OTDS に含まれる Swagger API アプリケーションを使用して取得する手順を下に示します。
      * Web ブラウザーで、{OTDS URL}/otdsws/oauth2 にアクセスします。
      * /token にアクセスし、右上にあるロック アイコンをクリックします。 前に取得した OAuth クライアント ID とシークレットを、それぞれユーザー名とパスワードとして入力します。 [承認] をクリックします。

      ![承認ボタン](media/open-text-directory-services-provisioning-tutorial/authorization.png)

6. grant_type として **client_credentials** を選択し、 **[実行]** をクリックします。

      ![実行ボタン](media/open-text-directory-services-provisioning-tutorial/execute.png)

7. 応答内のアクセス トークンは、Azure AD の **[シークレット トークン]** フィールドで使用する必要があります。

      ![Access Token](media/open-text-directory-services-provisioning-tutorial/access-token.png)

## <a name="step-3-add-opentext-directory-services-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから OpenText Directory Services を追加する

Azure AD アプリケーション ギャラリーから OpenText Directory Services を追加して、OpenText Directory Services へのプロビジョニングの管理を開始します。 SSO 用に OpenText Directory Services を以前に設定している場合は、同じアプリケーションを使用できます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](../manage-apps/add-application-portal.md)を参照してください。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)で説明されているスコープ フィルターを使用できます。 

* ユーザーとグループを OpenText Directory Services に割り当てるときは、 **[既定のアクセス]** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 


## <a name="step-5-configure-automatic-user-provisioning-to-opentext-directory-services"></a>手順 5. OpenText Directory Services への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザー、グループ、またはその両方の割り当てに基づいて、TestApp でユーザー、グループ、またはその両方が作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-opentext-directory-services-in-azure-ad"></a>Azure AD で OpenText Directory Services の自動ユーザー プロビジョニングを構成するには、次のようにします。

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーション一覧で **[OpenText Directory Services]** を選択します。

    ![アプリケーション一覧の OpenText Directory Services のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブの [自動]](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションで、OpenText Directory Services のテナント URL を入力します。
   * 特定でないテナント URL : {OTDS URL}/scim/{partitionName}
   * 特定のテナント URL :  {OTDS URL}/otdstenant/{tenantID}/scim/{partitionName}

6. 手順 2 で取得したシークレット トークンを入力します。 **[接続テスト]** をクリックして、Azure AD から OpenText Directory Services に接続できることを確認します。 接続できない場合は、OpenText Directory Services アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

      ![トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** を選択します。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to OpenText Directory Services]\(Azure Active Directory ユーザーを OpenText Directory Services に同期する\)** を選択します。

9. **[属性マッピング]** セクションで、Azure AD から OpenText Directory Services に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で OpenText Directory Services のユーザー アカウントの照合に使用されます。 [照合する対象の属性](../app-provisioning/customize-application-attributes.md)を変更する場合は、OpenText Directory Services API で、その属性に基づいたユーザーのフィルター処理がサポートされていることを確認する必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|
   |---|---|
   |userName|String|
   |active|Boolean|
   |displayName|String|
   |title|String|
   |emails[type eq "work"].value|String|
   |preferredLanguage|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |addresses[type eq "work"].formatted|String|
   |addresses[type eq "work"].streetAddress|String|
   |addresses[type eq "work"].locality|String|
   |addresses[type eq "work"].region|String|
   |addresses[type eq "work"].postalCode|String|
   |addresses[type eq "work"].country|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|リファレンス| 

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to OpenText Directory Services]\(Azure Active Directory グループを OpenText Directory Services に同期する\)** を選択します。

11. **[属性マッピング]** セクションで、Azure AD から OpenText Directory Services に同期されるグループ属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で OpenText Directory Services のグループの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

      |属性|Type|
      |---|---|
      |displayName|String|
      |externalId|String|
      |members|リファレンス|

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. OpenText Directory Services に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、OpenText Directory Services にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

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