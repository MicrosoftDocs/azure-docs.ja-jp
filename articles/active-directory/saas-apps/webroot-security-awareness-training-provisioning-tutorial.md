---
title: チュートリアル:Azure Active Directory での自動ユーザー プロビジョニング用に Webroot Security Awareness Training を構成する | Microsoft Docs
description: Azure AD から Webroot Security Awareness Training にユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除する方法について説明します。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 455f4396-930e-4db5-a167-d3ea6a860a17
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: Zhchia
ms.openlocfilehash: f015718c59178b8094f535280edadd641d22a806
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2021
ms.locfileid: "98741895"
---
# <a name="tutorial-configure-webroot-security-awareness-training-for-automatic-user-provisioning"></a>チュートリアル:自動ユーザー プロビジョニング用に Webroot Security Awareness Training を構成する

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために Webroot Security Awareness Training と Azure Active Directory (Azure AD) の両方で実行する必要がある手順について説明します。 構成されると、Azure AD は、Azure AD プロビジョニング サービスを使用してユーザーやグループを [Webroot Security Awareness Training](https://www.webroot.com/) に自動的にプロビジョニング/プロビジョニング解除します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。 


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * Webroot Security Awareness Training のユーザーを作成する
> * アクセスが必要なくなった Webroot Security Awareness Training のユーザーを削除する
> * Azure AD と Webroot Security Awareness Training の間のユーザー属性の同期を維持する
> * Webroot Security Awareness Training のグループとグループ メンバーシップをプロビジョニングする

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md) 
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。
* 少なくとも 1 つのサイトで有効になっている Webroot Security Awareness Training のマネージド サービス プロバイダー コンソール。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
2. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
3. どのようなデータを [Azure AD と Webroot Security Awareness Training の間でマップする](../app-provisioning/customize-application-attributes.md)かを決定します。 

## <a name="step-2-configure-webroot-security-awareness-training-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD でのプロビジョニングをサポートするように Webroot Security Awareness Training を構成する

### <a name="obtain-a-secret-token"></a>シークレット トークンを取得する

サイトを Azure AD に接続するには、Webroot 管理コンソールで、そのサイトの **シークレット トークン** を取得する必要があります。

1. [Webroot 管理コンソール](https://identity.webrootanywhere.com/v1/Account/login#tab_customers)にサインインします。

2. **[サイト]** タブから、Azure AD に接続するサイトの [Security Awareness Training] 列にある歯車アイコンをクリックします。

    ![歯車アイコン](./media/webroot-security-awareness-training-provisioning-tutorial/gear-icon.png)

3. **[Azure AD の統合の構成]** のボタンをクリックします。

    ![[Azure AD の統合の構成]](./media/webroot-security-awareness-training-provisioning-tutorial/configure-azure-ad-integration.png)

4. **[シークレット トークン]** をコピーして保存します。 この値は、Azure portal で Webroot Security Awareness Training アプリケーションの [プロビジョニング] タブにある [シークレット トークン] フィールドに入力されます。

5. **[Done]** をクリックします。

    ![シークレット トークンをコピーする](./media/webroot-security-awareness-training-provisioning-tutorial/copy-secret-token.png)

## <a name="step-3-add-webroot-security-awareness-training-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから Webroot Security Awareness Training を追加する

Webroot Security Awareness Training へのプロビジョニングの管理を開始するには、Azure AD アプリケーション ギャラリーから Webroot Security Awareness Training を追加します。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](../manage-apps/add-application-portal.md)を参照してください。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)で説明されているスコープ フィルターを使用できます。 

* ユーザーやグループを Webroot Security Awareness Training に割り当てるときは、 **[既定のアクセス]** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 


## <a name="step-5-configure-automatic-user-provisioning-to-webroot-security-awareness-training"></a>手順 5. Webroot Security Awareness Training への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザー、グループ、またはその両方の割り当てに基づいて、TestApp でユーザー、グループ、またはその両方が作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-webroot-security-awareness-training-in-azure-ad"></a>Azure AD で Webroot Security Awareness Training の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[Webroot Security Awareness Training]** を選択します。

    ![アプリケーションの一覧の Webroot Security Awareness Training のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://awarenessapi.webrootanywhere.com/api/v2/scim`」と入力します。 前に取得したシークレット トークンの値を、 **[シークレット トークン]** に入力します。 **[接続テスト]** をクリックして、Azure AD が Webroot Security Awareness Training に接続できることを確認します。 接続に失敗する場合は、Webroot Security Awareness Training アカウントに管理者アクセス許可があることを確認し、再試行します。

    ![テナント URL とシークレット トークンを入力できる場所である [管理者資格情報] ダイアログ ボックスを示すスクリーンショット。](./media/webroot-security-awareness-training-provisioning-tutorial/provisioning.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** を選択します。

8. **[マッピング]** セクションの **[Provision Azure Active Directory Users]\(Azure Active Directory ユーザーをプロビジョニングする\)** を選択します。

9. **[属性マッピング]** セクションで、Azure AD から Webroot Security Awareness Training に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新操作のために Webroot Security Awareness Training のユーザー アカウントを照合するために使用されます。 [一致する対象の属性](../app-provisioning/customize-application-attributes.md)を変更することにした場合は、その属性に基づいたユーザーのフィルター処理が Webroot Security Awareness Training API によって確実にサポートされるようにする必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|フィルター処理のサポート|
   |---|---|---|
   |userName|String|&check;|
   |externalId|String|
   |name.givenName|String|
   |name.familyName|String|
   |emails[type eq "work"].value|String|

10. **[マッピング]** セクションの **[Provision Azure Active Directory Groups]\(Azure Active Directory グループをプロビジョニングする\)** を選択します。

11. **[属性マッピング]** セクションで、Azure AD から Webroot Security Awareness Training に同期されるグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新操作のために Webroot Security Awareness Training のグループを照合するために使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

      |属性|Type|フィルター処理のサポート|
      |---|---|---|
      |displayName|String|&check;|
      |members|リファレンス|
      |externalId|String|

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Webroot Security Awareness Training に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択することによって、Webroot Security Awareness Training にプロビジョニングするユーザーまたはグループ、あるいはその両方を定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 初期サイクルは後続の同期よりも実行に時間がかかります。後続のサイクルは、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

1. [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
2. [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
3. プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](../app-provisioning/application-provisioning-quarantine-status.md)を参照してください。

## <a name="change-log"></a>ログの変更
* 2021 年 1 月 21 日 - ユーザーの主要な属性 "userName" のサポートを追加しました。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)