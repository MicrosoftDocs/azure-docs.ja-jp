---
title: チュートリアル:Azure Active Directory での自動ユーザー プロビジョニングに対応するように Templafy OpenID Connect を構成する | Microsoft Docs
description: Templafy OpenID Connect に対して、ユーザー アカウントのプロビジョニングとプロビジョニング解除を自動的に実行するように Azure Active Directory を構成する方法について説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.assetid: 8cbb387a-e3fb-4588-bb87-bf4f88144361
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: zhchia
ms.openlocfilehash: 6b1894d832c3a74916418aa06885c2917267f4b1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101652591"
---
# <a name="tutorial-configure-templafy-openid-connect-for-automatic-user-provisioning"></a>チュートリアル:自動ユーザー プロビジョニングに対応するように Templafy OpenID Connect を構成する

このチュートリアルの目的は、Templafy OpenID Connect に対して、ユーザーやグループのプロビジョニングとプロビジョニング解除を自動的に実行するように Azure Active Directory (Azure AD) を構成するために、Templafy OpenID Connect と Azure AD で実行する手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント。
* [Templafy テナント](https://www.templafy.com/pricing/)。
* 管理者アクセス許可がある Templafy のユーザー アカウント。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
2. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
3. [Azure AD と Templafy OpenID Connect の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。 

## <a name="assigning-users-to-templafy-openid-connect"></a>Templafy OpenID Connect へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Templafy OpenID Connect へのアクセスが必要な Azure AD 内のユーザーやグループを決定しておく必要があります。 決定したら、次の手順に従って、これらのユーザーやグループを Templafy OpenID Connect に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-templafy-openid-connect"></a>ユーザーを Templafy OpenID Connect に割り当てる際の重要なヒント

* 1 人の Azure AD ユーザーを Templafy OpenID Connect に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 さらに多くのユーザーやグループは、後で割り当てることができます。

* Templafy OpenID Connect にユーザーを割り当てるときは、割り当てダイアログでアプリケーション固有の有効なロール (使用可能な場合) を選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="step-2-configure-templafy-openid-connect-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD でのプロビジョニングをサポートするように Templafy OpenID Connect を構成する

Azure AD での自動ユーザー プロビジョニングに対応するように Templafy OpenID Connect を構成する前に、Templafy OpenID Connect で SCIM プロビジョニングを有効にする必要があります。

1. Templafy 管理コンソールにサインインします。 **[管理]** をクリックします。

    ![Templafy 管理コンソール](media/templafy-openid-connect-provisioning-tutorial/templafy-admin.png)

2. **[認証方法]** をクリックします。

    ![Templafy 管理セクションのスクリーンショット。[認証方法] オプションが選択されています。](media/templafy-openid-connect-provisioning-tutorial/templafy-auth.png)

3. **SCIM Api キー** の値をコピーします。 この値は、Azure portal で Templafy OpenID Connect アプリケーションの [プロビジョニング] タブの **[シークレット トークン]** フィールドに入力します。

    ![SCIM API キーのスクリーンショット。](media/templafy-openid-connect-provisioning-tutorial/templafy-token.png)

## <a name="step-3-add-templafy-openid-connect-from-the-gallery"></a>手順 3. ギャラリーから Templafy OpenID Connect を追加する

Azure AD での自動ユーザー プロビジョニングに対応するように Templafy OpenID Connect を構成するには、Azure AD アプリケーション ギャラリーからマネージド SaaS アプリケーションの一覧に Templafy OpenID Connect を追加する必要があります。

**Azure AD アプリケーション ギャラリーから Templafy OpenID Connect を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Templafy OpenID Connect**」と入力し、結果パネルで **[Templafy OpenID Connect]** を選択します。次に、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果リストの Templafy OpenID Connect](common/search-new-app.png)

## <a name="step-4-configure-automatic-user-provisioning-to-templafy-openid-connect"></a>手順 4. Templafy OpenID Connect への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて、Templafy OpenID Connect のユーザーやグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> Templafy で OpenID Connect ベースのシングル サインオンを有効にすることもできます。これを行うには、[Templafy シングル サインオンのチュートリアル](templafy-tutorial.md)に記載されている手順に従います。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-templafy-openid-connect-in-azure-ad"></a>Azure AD で Templafy OpenID Connect の自動ユーザー プロビジョニングを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Templafy OpenID Connect]** を選択します。

    ![アプリケーションの一覧の Templafy OpenID Connect のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://scim.templafy.com/scim`」と入力します。 **[シークレット トークン]** に、前に取得した **SCIM API キー** の値を入力します。 **[テスト接続]** をクリックして、Azure AD から Templafy への接続を確保します。 接続できない場合は、使用中の Templafy アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Users to Templafy OpenID Connect]\(Azure Active Directory ユーザーを Templafy OpenID Connect に同期する\)** を選択します。

    ![Templafy OpenID Connect のユーザー マッピング](media/templafy-openid-connect-provisioning-tutorial/user-mapping.png)

9. **[属性マッピング]** セクションで、Azure AD から Templafy OpenID Connect に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新操作で Templafy OpenID Connect のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|フィルター処理のサポート|
   |---|---|---|
   |userName|String|&check;|
   |active|Boolean|
   |displayName|String|
   |title|String|
   |preferredLanguage|String|
   |name.givenName|String|
   |name.familyName|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |externalId|String|
   |addresses[type eq "work"].locality|String|
   |addresses[type eq "work"].postalCode|String|
   |addresses[type eq "work"].region|String|
   |addresses[type eq "work"].streetAddress|String|
   |addresses[type eq "work"].country|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to Templafy]\(Azure Active Directory グループを Templafy に同期する\)** を選択します。

    ![Templafy OpenID Connect のグループ マッピング](media/templafy-openid-connect-provisioning-tutorial/group-mapping.png)

11. **[属性マッピング]** セクションで、Azure AD から Templafy OpenID Connect に同期されるグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新操作で Templafy OpenID Connect のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

      |属性|Type|フィルター処理のサポート|
      |---|---|---|
      |displayName|String|&check;|
      |members|リファレンス|
      |externalId|String|      

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Templafy OpenID Connect に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で必要な値を選択して、Templafy OpenID Connect にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

    これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視し、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Templafy OpenID Connect に対して実行されたすべてのアクションが記載されています。

## <a name="step-5-monitor-your-deployment"></a>手順 5. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

* [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
* [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
* プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](../app-provisioning/application-provisioning-quarantine-status.md)を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)