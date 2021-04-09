---
title: チュートリアル:Azure Active Directory を使用して、自動ユーザー プロビジョニングを行うように Cisco Webex を構成する | Microsoft Docs
description: Cisco Webex に対してユーザー アカウントを自動的にプロビジョニングおよびプロビジョニング解除するように Azure Active Directory を設定する方法を説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: bc05e83ac6c7f0f7c5e9a571c1fa7397af858f44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96180100"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>チュートリアル:自動的にユーザーをプロビジョニングするための Cisco Webex の構成

このチュートリアルでは、Azure AD を構成し、Cisco Webex に対してユーザーを自動的にプロビジョニングおよびプロビジョニング解除する手順を説明します。これらの手順は、Cisco Webex および Azure Active Directory (Azure AD) で実行します。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> このコネクタは、現在プレビューの段階です。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント。
* [Cisco Webex テナント](https://www.webex.com/pricing/index.html)。
* Admin アクセス許可がある Cisco Webex のユーザー アカウント。

## <a name="adding-cisco-webex-from-the-gallery"></a>ギャラリーからの Cisco Webex の追加

Azure AD を使用して自動的にユーザー プロビジョニングを行うように Cisco Webex を構成する前に、Azure AD アプリケーション ギャラリーから管理対象 SaaS アプリケーションの一覧に Cisco Webex を追加する必要があります。

**Azure AD アプリケーション ギャラリーから Cisco Webex を追加するには、以下の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Cisco Webex**」と入力して、結果パネルから **[Cisco Webex]** を選択し、 **[追加]** をクリックしてアプリケーションを追加します。

    ![結果リストの Cisco Webex](common/search-new-app.png)

## <a name="assigning-users-to-cisco-webex"></a>Cisco Webex へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Cisco Webex へのアクセスが必要な Azure AD のユーザーを決定しておく必要があります。 決定し終えたら、次の手順でこれらのユーザーを Cisco Webex に割り当てることができます。

* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>ユーザーを Cisco Webex に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを Cisco Webex に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後で追加のユーザーを割り当てられます。

* Cisco Webex にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>Cisco Webex への自動ユーザー プロビジョニングの構成

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーの割り当てに基づいて Cisco Webex のユーザーを作成、更新、無効化する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>Azure AD で Cisco Webex の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインし、 **[エンタープライズ アプリケーション]** 、 **[すべてのアプリケーション]** 、 **[Cisco Webex]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Cisco Webex]** を選択します。

    ![アプリケーションの一覧の [Cisco Webex] リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    :::image type="content" source="common/provisioning.png" alt-text="Azure portal のメニューのスクリーンショット。[管理] の下の [プロビジョニング] が強調表示されています。" border="false":::

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    :::image type="content" source="common/provisioning-automatic.png" alt-text="[自動] が強調表示されている、[プロビジョニング モード] リスト ボックスのスクリーンショット。" border="false":::

5. **[管理者資格情報]** セクションで、Cisco Webex のアカウントの **[テナントの URL]** と **[シークレット トークン]** を入力します。

    :::image type="content" source="./media/cisco-webex-provisioning-tutorial/secrettoken1.png" alt-text="[管理者資格情報] セクションのスクリーンショット。[テナントの U R L] および [シークレット トークン] ボックスが強調表示されていますが、空です。" border="false":::

6.  **[テナントの URL]** フィールドに `https://api.ciscospark.com/v1/scim/[OrgId]` という形式で値を入力します。 `[OrgId]` を取得するには、[Cisco Webex Control Hub](https://admin.webex.com/login) にサインインします。 左下にある組織名をクリックし、 **[組織 ID]** の値をコピーします。 

    * **[シークレット トークン]** の値を取得するには、この [[URL]](https://idbroker.webex.com/idb/saml2/jsp/doSSO.jsp?type=login&goto=https%3A%2F%2Fidbroker.webex.com%2Fidb%2Foauth2%2Fv1%2Fauthorize%3Fresponse_type%3Dtoken%26client_id%3DC4ca14fe00b0e51efb414ebd45aa88c1858c3bfb949b2405dba10b0ca4bc37402%26redirect_uri%3Dhttp%253A%2f%2flocalhost%253A3000%2fauth%2fcode%26scope%3Dspark%253Apeople_read%2520spark%253Apeople_write%2520Identity%253ASCIM%26state%3Dthis-should-be-a-random-string-for-security-purpose) に移動します。 表示される webex サインイン ページから、組織の完全な Cisco Webex 管理者アカウントでサインインします。 サイトに到達できないというエラー ページが表示されますが、これは正常です。

        :::image type="content" source="./media/cisco-webex-provisioning-tutorial/test.png" alt-text="エラー メッセージを表示している Web ページのスクリーンショット。メッセージは、サイトに到達できないという内容で、トラブルシューティングのヒントがいくつか示されています。" border="false":::
 
    * 次に示すように、生成されたベアラー トークンの値を URL からコピーします。 このトークンは 365 日間有効です。
        
        :::image type="content" source="./media/cisco-webex-provisioning-tutorial/test1.png" alt-text="長い U R L を示すスクリーンショット。アドレスの一部は解読できませんが、強調表示され、[ベアラー トークン] というラベルが付いています。" border="false":::

7. 手順 5 のフィールドに入力したら、 **[テスト接続]** をクリックして Azure AD が Cisco Webex に接続できることを確認します。 接続できない場合は、使用中の Cisco Webex アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)
   
8. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

9. **[保存]** をクリックします。

10. **[マッピング]** セクションで **[Synchronize Azure Active Directory Users to Cisco Webex]\(Azure Active Directory ユーザーを Cisco Webex に同期する\)** を選択します。

    :::image type="content" source="./media/cisco-webex-provisioning-tutorial/usermapping.png" alt-text="Azure portal の [マッピング] セクションのスクリーンショット。[名前] の下で、[Synchronize Azure Active Directory Users to CiscoSpark]\(Azure Active Directory ユーザーを CiscoSpark に同期する\) が強調表示されています。" border="false":::

11. **[属性マッピング]** セクションで、Azure AD から Cisco Webex に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Cisco Webex のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    :::image type="content" source="./media/cisco-webex-provisioning-tutorial/usermappingattributes.png" alt-text="Azure Active Directory 属性、対応する CiscoSpark 属性、および一致するステータスを示す、[属性マッピング] セクションのスクリーンショット。" border="false":::

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Cisco Webex に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Cisco Webex にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Cisco Webex に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="connector-limitations"></a>コネクタの制限事項

* 現在、Cisco Webex は Cisco において初期フィールド テスト (EFT) が行われています。 詳しくは、[Cisco サポート チーム](https://www.webex.co.in/support/support-overview.html)にお問い合わせください。 
* Cisco Webex の構成の詳細については、Cisco のドキュメントの[こちら](https://help.webex.com/en-us/aumpbz/Synchronize-Azure-Active-Directory-Users-into-cisco-webex-Control-Hub)を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
