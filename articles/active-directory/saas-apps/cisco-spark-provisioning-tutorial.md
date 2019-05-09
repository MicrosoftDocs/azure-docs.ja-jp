---
title: チュートリアル:Azure Active Directory を使用して、自動ユーザー プロビジョニングを行うように Cisco Spark を構成する | Microsoft Docs
description: Cisco Spark に対してユーザー アカウントを自動的にプロビジョニングおよびプロビジョニング解除するように Azure Active Directory を設定する方法を説明します。
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-wingf
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77dab6ad0480bc1565c219766d17211995dcfc20
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59278414"
---
# <a name="tutorial-configure-cisco-spark-for-automatic-user-provisioning"></a>チュートリアル:自動的にユーザーをプロビジョニングするように Cisco Spark を構成する

このチュートリアルでは、Azure AD を構成し、Cisco Spark に対してユーザーを自動的にプロビジョニングおよびプロビジョニング解除する手順を説明します。これらの手順は、Cisco Spark および Azure Active Directory (Azure AD) で実行します。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* Cisco Spark テナント
* Admin アクセス許可がある Cisco Spark のユーザー アカウント

> [!NOTE]
> Azure AD プロビジョニング統合では、Cisco Spark チームが使用可能な [Cisco Spark Webservice](https://developer.webex.com/getting-started.html) が利用されます。

## <a name="adding-cisco-spark-from-the-gallery"></a>ギャラリーからの Cisco Spark の追加

Azure AD を使用して自動的にユーザー プロビジョニングを行うように Cisco Spark を構成する前に、Azure AD アプリケーション ギャラリーから管理対象 SaaS アプリケーションの一覧に Cisco Spark を追加する必要があります。

**Azure AD アプリケーション ギャラリーからの Cisco Spark を追加するには、以下の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Cisco Spark**」と入力して、結果パネルから **[Cisco Spark]** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![結果リスト内の Cisco Spark](common/search-new-app.png)

## <a name="assigning-users-to-cisco-spark"></a>Cisco Spark へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Cisco Spark へのアクセスが必要な Azure AD のユーザーを決定しておく必要があります。 決定し終えたら、次の手順でこれらのユーザーを Cisco Spark に割り当てることができます。

* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-spark"></a>ユーザーを Cisco Spark に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを Cisco Spark に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後で追加のユーザーを割り当てられます。

* Cisco Spark にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="configuring-automatic-user-provisioning-to-cisco-spark"></a>Cisco Spark への自動ユーザー プロビジョニングの構成

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーの割り当てに基づいて Cisco Spark のユーザーを作成、更新、無効化する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-cisco-spark-in-azure-ad"></a>Azure AD で Cisco Spark の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインし、**[エンタープライズ アプリケーション]**、**[すべてのアプリケーション]**、**[Cisco Spark]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Cisco Spark]** を選択します。

    ![アプリケーションの一覧の Cisco Spark リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![Cisco Spark プロビジョニング](./media/cisco-spark-provisioning-tutorial/ProvisioningTab.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![Cisco Spark プロビジョニング](./media/cisco-spark-provisioning-tutorial/ProvisioningCredentials.png)

5. **[管理者資格情報]** セクションで、Cisco Spark のアカウントの **[テナントの URL]** と **[シークレット トークン]** を入力します。

    ![Cisco Spark プロビジョニング](./media/cisco-spark-provisioning-tutorial/secrettoken1.png)

    * **[テナントの URL]** フィールドに、テナントの Cisco Spark SCIM API URL を入力します。形式は、`https://api.ciscospark.com/v1/scim/[Tenant ID]/` です。ここで、`[Tenant ID]` は、手順 6 で説明する英数字文字列です。

    * **[シークレット トークン]** フィールドに、手順 6 で説明するシークレット トークンを設定します。

6. Admin アカウントで [Cisco Spark 開発者サイト](https://developer.webex.com/)にログインし、 Cisco Spark アカウントの**テナントの URL** と **シークレット トークン**を確認します。 ログインしたら、次の操作を実行します。

   * [[Getting Started]\(はじめに\) ページ](https://developer.webex.com/getting-started.html)に移動します。

   * [[認証] セクション](https://developer.webex.com/getting-started.html#authentication)まで下にスクロールします。
  
    ![Cisco Spark 認証トークン](./media/cisco-spark-provisioning-tutorial/SecretToken.png)

   * ボックス内の英数字文字列が、**シークレット トークン**です。 このトークンをクリップボードにコピーします。

   * [[Get My Own Details]\([詳細を表示]\) ページ](https://developer.webex.com/endpoint-people-me-get.html)に移動します。
       * [Test Mode]\(テスト モード\) が [オン] であることを確認します。
       * [Authorization]\(許可\) フィールドに "Bearer" という語とスペースを入力し、その後にシークレット トークンを貼り付けます。![Cisco Spark 認証トークン](./media/cisco-spark-provisioning-tutorial/GetMyDetails.png)
       * [Run]\(実行\) をクリックします。

   * 右側の応答テキストで、**テナント ID** が "orgId" として表示されます。

     ```json
     {
       "id": "(...)",
       "emails": [
           "admin.user@contoso.com"
       ],
       "displayName": "John Smith",
       "nickName": "John",
       "orgId": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
       (...)
     }
     ```

7. 手順 5 のフィールドに入力したら、**[テスト接続]** をクリックして Azure AD が Cisco Spark に接続できることを確認します。 接続できない場合は、使用中の Cisco Spark アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![Cisco Spark プロビジョニング](./media/cisco-spark-provisioning-tutorial/TestConnection.png)

8. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、**[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![Cisco Spark プロビジョニング](./media/cisco-spark-provisioning-tutorial/EmailNotification.png)

9. **[Save]** をクリックします。

10. **[マッピング]** セクションで **[Synchronize Azure Active Directory Users to Cisco Spark]\(Azure Active Directory ユーザーを Cisco Spark に同期する\)** を選択します。

    ![Cisco Spark プロビジョニング](./media/cisco-spark-provisioning-tutorial/UserMapping.png)

11. **[属性マッピング]** セクションで、Azure AD から Cisco Spark に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Cisco Spark のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Cisco Spark プロビジョニング](./media/cisco-spark-provisioning-tutorial/UserMappingAttributes.png)

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Cisco Spark に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![Cisco Spark プロビジョニング](./media/cisco-spark-provisioning-tutorial/ProvisioningStatus.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Cisco Spark にプロビジョニングするユーザーやグループを定義します。

    ![Cisco Spark プロビジョニング](./media/cisco-spark-provisioning-tutorial/SyncScope.png)

15. プロビジョニングの準備ができたら、**[保存]** をクリックします。

    ![Cisco Spark プロビジョニング](./media/cisco-spark-provisioning-tutorial/Save.png)

これにより、**[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Cisco Spark に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="connector-limitations"></a>コネクタの制限事項

* 現在、Cisco Spark は Cisco において初期フィールド テスト (EFT) が行われています。 詳しくは、[Cisco サポート チーム](https://www.webex.co.in/support/support-overview.html)にお問い合わせください。 

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_03.png
