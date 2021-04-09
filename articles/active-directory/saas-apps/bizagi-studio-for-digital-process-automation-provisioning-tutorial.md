---
title: チュートリアル:Azure Active Directory で自動ユーザー プロビジョニング用に Bizagi Studio for Digital Process Automation を構成する | Microsoft Docs
description: Azure AD から Bizagi Studio for Digital Process Automation へのユーザー アカウントのプロビジョニングとプロビジョニング解除を自動的に実行する方法について説明します。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 2fbff65a-5345-4c08-a6c7-60b80d867a3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2020
ms.author: Zhchia
ms.openlocfilehash: 72e021f47bb8db4dedf0e434d0d94bb2118a4c00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98728163"
---
# <a name="tutorial-configure-bizagi-studio-for-digital-process-automation-for-automatic-user-provisioning"></a>チュートリアル:自動ユーザー プロビジョニング用に Bizagi Studio for Digital Process Automation を構成する

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために Bizagi Studio for Digital Process Automation と Azure Active Directory (Azure AD) の両方で行う必要がある手順について説明します。 これを行うように構成すると、Azure AD で Azure AD プロビジョニング サービスを使用して、[Bizagi Studio for Digital Process Automation](https://www.bizagi.com/) に対するユーザーとグループのプロビジョニングおよびプロビジョニング解除が自動的に行われます。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。 


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * Bizagi Studio for Digital Process Automation でユーザーを作成する
> * ユーザーがアクセスを必要としなくなった場合に Bizagi Studio for Digital Process Automation でユーザーを削除する
> * Azure AD と Bizagi Studio for Digital Process Automation の間でユーザー属性の同期を維持する
> * Bizagi Studio for Digital Process Automation に[シングル サインオンする](./bizagi-studio-for-digital-process-automation-tutorial.md) (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md)。 
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)が割り当てられた Azure AD のユーザー アカウント。 たとえば、アプリケーション管理者、クラウド アプリケーション管理者、アプリケーションの所有者、全体管理者です。 
* Bizagi Studio for Digital Process Automation バージョン 11.2.4.2X 以降。

## <a name="plan-your-provisioning-deployment"></a>プロビジョニングのデプロイを計画する
計画のために次の手順に従います。

1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
2. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
3. [Azure AD と Bizagi Studio for Digital Process Automation の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。 

## <a name="configure-to-support-provisioning-with-azure-ad"></a>Azure AD でのプロビジョニングをサポートするように構成する
Azure AD でのプロビジョニングをサポートするように Bizagi Studio for Digital Process Automation を構成するには、次の手順に従います。

1. **管理者のアクセス許可** を持つユーザーとして、作業ポータルにサインインします。

2. **[Admin]\(管理者\)**  >  **[Security]\(セキュリティ\)**  >  **[OAuth 2 Applications]\(OAuth 2 アプリケーション\)** にアクセスします。

   ![[OAuth 2 Applications]\(OAuth 2 アプリケーション\) が強調表示されている Bizagi のスクリーンショット。](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/admin.png)

3. **[追加]** を選択します。
4. **[Grant Type]\(付与タイプ\)** で **[Bearer token]\(ベアラー トークン\)** を選択します。 **[Allowed scope]\(許可されるスコープ\)** で **[API]** と **[USER SYNC]\(ユーザー同期\)** を選択します。 次に、 **[保存]** を選択します。

   ![付与タイプと許可されるスコープが強調表示されている [Register Application]\(アプリケーションの登録\) のスクリーンショット。](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/token.png)

5. **クライアント シークレット** をコピーして保存します。 このクライアント シークレット値を、Azure portal で Bizagi Studio for Digital Process Automation アプリケーションの **[プロビジョニング]** タブの **[シークレット トークン]** フィールドに入力します。

   ![クライアント シークレットが強調表示されている Oauth のスクリーンショット。](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/secret.png)

## <a name="add-the-application-from-the-azure-ad-gallery"></a>Azure AD ギャラリーからアプリケーションを追加する

Bizagi Studio for Digital Process Automation へのプロビジョニングの管理を開始するには、Azure AD アプリケーション ギャラリーからアプリを追加します。 シングル サインオンのために Bizagi Studio for Digital Process Automation を以前に設定している場合は、その同じアプリケーションを使用できます。 ただし、最初に統合をテストする場合は、別のアプリを作成してください。 詳細については、「[クイック スタート: Azure Active Directory (Azure AD) テナントにアプリケーションを追加する](../manage-apps/add-application-portal.md)」を参照してください。 

## <a name="define-who-is-in-scope-for-provisioning"></a>プロビジョニングの対象ユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当てに基づいて、ユーザーおよびグループの属性に基づいて、またはその両方でプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてスコープを設定する場合は、「[Graph API を使用してアプリに対してユーザーとグループの割り当ておよび割り当て解除を行う](../manage-apps/assign-user-or-group-access-portal.md)」の手順を参照して、ユーザーとグループをアプリケーションに割り当てます。 ユーザーまたはグループの属性のみに基づいてスコープを設定する場合は、スコープ フィルターを使用できます。 詳細については、「[スコープ フィルターを使用した属性ベースのアプリケーション プロビジョニング](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)」を参照してください。 

スコープに関して次の点に注意してください。

* Bizagi Studio for Digital Process Automation にユーザーとグループを割り当てるときは、 **[既定のアクセス]** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定されている場合、これを制御するには、アプリにユーザーまたはグループを 1 つか、2 つ割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 


## <a name="configure-automatic-user-provisioning"></a>自動ユーザー プロビジョニングの構成 

このセクションでは、ユーザーとグループが作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。 これは、Azure AD でのユーザーおよびグループの割り当てに基づいて、テスト アプリで行います。

### <a name="configure-automatic-user-provisioning-for-bizagi-studio-for-digital-process-automation-in-azure-ad"></a>Azure AD で Bizagi Studio for Digital Process Automation に対する自動ユーザー プロビジョニングを構成する

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。

    ![[エンタープライズ アプリケーション] と [すべてのアプリケーション] が強調表示されている Azure portal のスクリーンショット。](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[Bizagi Studio for Digital Process Automation]** を選択します。

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] が強調表示されている [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] が強調表示されている [プロビジョニング モード] コントロールのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションで、Bizagi Studio for Digital Process Automation のテナントの URL とシークレット トークンを入力します。 

      * **テナント URL:** Bizagi SCIM エンドポイントを `<Your_Bizagi_Project>/scim/v2/` の構造で入力します。
         (例: `https://my-company.bizagi.com/scim/v2/`)。

      * **シークレット トークン:** この値は、この記事の前半で説明した手順で取得します。

      Azure AD が Bizagi Studio for Digital Process Automation に接続できることを確認するには、 **[テスト接続]** を選択します。 接続できない場合は、ご使用の Bizagi Studio for Digital Process Automation アカウントに管理者のアクセス許可があることを確認し、もう一度試します。

   ![[テスト接続] が強調表示されている [管理者資格情報] のスクリーンショット。](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** に、プロビジョニングのエラー通知を受け取るユーザーまたはグループのメール アドレスを入力します。 **[エラーが発生したときにメール通知を送信します]** オプションを選択します。

    ![[通知用メール] オプションのスクリーンショット。](common/provisioning-notification-email.png)

7. **[保存]** を選択します。

8. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Users to Bizagi Studio for Digital Process Automation]\(Azure Active Directory ユーザーを Bizagi Studio for Digital Process Automation に同期する\)** を選択します。

9. **[Attribute-Mapping]\(属性マッピング\)** セクションで、Azure AD から Bizagi Studio for Digital Process Automation に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新操作で Bizagi Studio for Digital Process Automation のユーザー アカウントとの照合に使用されます。 [一致するターゲット属性](../app-provisioning/customize-application-attributes.md)を変更する場合は、その属性に基づいたユーザーのフィルター処理を Bizagi Studio for Digital Process Automation API がサポートしているか確認する必要があります。 すべての変更をコミットするには、 **[保存]** を選択します。

   |属性|Type|フィルター処理のサポート|
   |---|---|---|
   |userName|String|&check;|
   |active|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |phoneNumbers[type eq "mobile"].value|String|

   カスタム拡張属性は、 **[詳細オプションの表示] > [Bizagi の属性リストを編集します]** で追加できます。 カスタム拡張属性には、**urn:ietf:params:scim:schemas:extension:bizagi:2.0:UserProperties:** というプレフィックスを付ける必要があります。 たとえば、カスタム拡張属性が **IdentificationNumber** の場合、属性は **urn:ietf:params:scim:schemas:extension:bizagi:2.0:UserProperties:IdentificationNumber** として追加する必要があります。 すべての変更をコミットするには、 **[保存]** を選択します。
   
    ![属性リストを編集します。](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/edit.png)  

   カスタム属性の追加方法の詳細については、[アプリケーション属性のカスタマイズ](../app-provisioning/customize-application-attributes.md)に関するページを参照してください。

> [!NOTE]
> サポートされるのは、基本的な型のプロパティのみです (String、Integer、Boolean、DateTime など)。 パラメーター テーブルまたは複数の型にリンクされているプロパティは、まだサポートされていません。

10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を参照してください。

11. Bizagi Studio for Digital Process Automation に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![[プロビジョニング状態] トグルのスクリーンショット。](common/provisioning-toggle-on.png)

12. Bizagi Studio for Digital Process Automation に対してプロビジョニングするユーザーとグループを定義します。 **[設定]** セクションの **[スコープ]** で、目的の値を選択します。

    ![[スコープ] オプションのスクリーンショット。](common/provisioning-scope.png)

13. プロビジョニングの準備ができたら、 **[保存]** を選択します。

    ![[保存] コントロールのスクリーンショット。](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 初期サイクルは後続の同期よりも実行に時間がかかります。後続のサイクルは、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 

## <a name="monitor-your-deployment"></a>デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

- [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
- [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
- プロビジョニング構成が異常な状態である場合、アプリケーションは検疫されます。 詳細については、「[検疫状態のアプリケーションのプロビジョニング](../app-provisioning/application-provisioning-quarantine-status.md)」を参照してください。  

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)