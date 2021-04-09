---
title: チュートリアル:ThousandEyes のユーザー プロビジョニング - Azure AD
description: Azure Active Directory を構成して、ユーザー アカウントを ThousandEyes に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: arvinh
ms.openlocfilehash: fcb53dc22cfb4bf7308b92ee76e5aaaf3bb0dead
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735100"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>チュートリアル:ThousandEyes を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Azure AD から ThousandEyes にユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除するうえで ThousandEyes と Azure AD で実行する必要がある手順について説明します。 

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* Azure Active Directory テナント
* [Standard プラン](https://www.thousandeyes.com/pricing)以上が有効な ThousandEyes テナント 
* Admin アクセス許可がある ThousandEyes のユーザー アカウント 

> [!NOTE]
> Azure AD プロビジョニング統合では、Standard プラン以上の ThousandEyes チームで使用できる [ThousandEyes SCIM API](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK) が必要です。

## <a name="assigning-users-to-thousandeyes"></a>ThousandEyes へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー アカウント プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。 

プロビジョニング サービスを構成して有効にする前に、ThousandEyes アプリへのアクセスが必要なユーザーを表す Azure AD 内のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順でこれらのユーザーを ThousandEyes アプリに割り当てることができます。

[エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>ユーザーを ThousandEyes に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを ThousandEyes に割り当てて、プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* ThousandEyes にユーザーを割り当てるときは、**ユーザー** ロールまたは別の有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選ぶ必要があります。 **[既定のアクセス]** ロールはプロビジョニングでは使うことができず、このロールのユーザーはスキップされます。

## <a name="configuring-user-provisioning-to-thousandeyes"></a>ThousandEyes へのユーザー プロビジョニングの構成 

このセクションでは、Azure AD を ThousandEyes のユーザー アカウント プロビジョニング API に接続する手順のほか、プロビジョニング サービスを構成して、Azure AD のユーザーとグループの割り当てに基づいて割り当て済みのユーザー アカウントを ThousandEyes で作成、更新、無効化する手順を説明します。

> [!TIP]
> ThousandEyes では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Azure Portal](https://portal.azure.com) で説明されている手順に従ってください。 シングル サインオンは自動プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>Azure AD で ThousandEyes への自動ユーザー アカウント プロビジョニングを構成する

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. シングル サインオンのために ThousandEyes を既に構成している場合は、検索フィールドで ThousandEyes のインスタンスを検索します。 構成していない場合は、 **[追加]** を選択してアプリケーション ギャラリーで **ThousandEyes** を検索します。 検索結果から ThousandEyes を選択してアプリケーションの一覧に追加します。

    ![アプリケーションの一覧の ThousandEyes のリンク](common/all-applications.png)
    
3. ThousandEyes のインスタンスを選択してから、 **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

![プロビジョニング モードで自動選択された ThousandEyes の [プロビジョニング] タブを示すスクリーンショット。](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)
    

5. **[管理者資格情報]** セクションで、ThousandEyes のアカウントによって生成された **[OAuth ベアラー トークン]** を入力します (トークンは、ThousandEyes アカウントの **[プロファイル]** で確認できます)。

    ![現在のアカウント グループの [アカウント設定] リンクが表示されている場所を示すスクリーンショット。](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. Azure Portal で、 **[テスト接続]** をクリックして Azure AD が ThousandEyes アプリに接続できることを確認します。 接続できない場合は、使用中の ThousandEyes アカウントに管理者アクセス許可があることを確認してから、手順 5 をもう一度試します。

7. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

8. **[保存]** をクリックします。

9. [マッピング] セクションの **[Synchronize Azure Active Directory Users to ThousandEyes]\(Azure Active Directory ユーザーを ThousandEyes に同期する\)** を選びます。

10. **[Attribute-Mapping]\(属性マッピング\)** セクションで、Azure AD から ThousandEyes に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Parsable のユーザー アカウントとの照合に使用されます。 [照合する対象の属性](../app-provisioning/customize-application-attributes.md)を変更する場合は、その属性に基づいたユーザーのフィルター処理が Parsable API で確実にサポートされている必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

     |属性|Type|フィルター処理のサポート|
     |---|---|---|
     |externalId|String|&check;|
     |userName|String|&check;|
     |active|Boolean|
     |displayName|String|
     |emails[type eq "work"].value|String|
     |name.formatted|String|


11. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

12. ThousandEyes に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

13. **[設定]** セクションの **[スコープ]** で目的の値を選択して、ThousandEyes にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

14. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

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