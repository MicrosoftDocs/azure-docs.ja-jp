---
title: チュートリアル:Looop を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Looop に対してユーザー アカウントの自動的なプロビジョニングとプロビジョニング解除を実行するように Azure Active Directory を構成する方法について説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: 528003ac482da6f254bf437321c70c389d23844b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94835036"
---
# <a name="tutorial-configure-looop-for-automatic-user-provisioning"></a>チュートリアル:Looop を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure Active Directory (Azure AD) が、ユーザー、グループ、またはその両方を Looop に対して自動的にプロビジョニングおよびプロビジョニング解除するよう構成するために、Looop と Azure AD で実行する手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [Looop テナント](https://www.looop.co/pricing/)
* 管理者アクセス許可を持つ Looop のユーザー アカウント。

## <a name="assign-users-to-looop"></a>ユーザーを Looop に割り当てる

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成し、有効にする前に、Looop へのアクセスが必要な Azure AD のユーザー、グループ、またはその両方を特定する必要があります。 特定した後、次の手順に従い、これらのユーザー、グループ、またはその両方を Looop に割り当てることができます。

* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-looop"></a>ユーザーを Looop に割り当てる際の重要なヒント

* 1 名の Azure AD ユーザーを Looop に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Looop にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-looop-for-provisioning"></a>プロビジョニングのために Looop を設定する

Azure AD での自動ユーザー プロビジョニング用に Looop を構成する前に、Looop からプロビジョニング情報を取得する必要があります。

1. [Looop 管理コンソール](https://app.looop.co/#/login) にサインインし、 **[アカウント]** を選択します。 **[アカウントの設定]** で **[認証]** を選択します。

    :::image type="content" source="media/looop-provisioning-tutorial/admin.png" alt-text="Looop 管理コンソールのスクリーンショット。[アカウント] タブが強調表示され、開いています。[アカウント設定] の [認証] が強調表示されています。" border="false":::

2. **[SCIM integration]\(SCIM 統合\)** の下にある **[Reset Token]\(トークンのリセット\)** をクリックして、新しいトークンを生成します。

    :::image type="content" source="media/looop-provisioning-tutorial/resettoken.png" alt-text="Looop 管理コンソールのページの [SCIM 統合] セクションのスクリーンショット。[トークンのリセット] ボタンが強調表示されています。" border="false":::

3. **SCIM エンドポイント** と **トークン** をコピーします。 これらの値は、Azure portal の Looop アプリケーションの [プロビジョニング] タブの **[テナント URL]** および **[シークレット トークン]** フィールドに入力されます。 

    ![Looop でのトークンの作成](media/looop-provisioning-tutorial/token.png)

## <a name="add-looop-from-the-gallery"></a>ギャラリーからの Looop の追加

Azure AD で自動ユーザー プロビジョニング用に Looop を構成するには、Azure AD アプリケーション ギャラリーから Looop をマネージド SaaS アプリケーションの一覧に追加する必要があります。

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Looop**」と入力し、結果パネルで **[Looop]** を選択します。 

    ![結果一覧の Looop](common/search-new-app.png)

5. **[Sign-up for Looop]\(Looop にサインアップ\)** ボタンを選択します。Looop のログイン ページにリダイレクトされます。 

    ![Looop OIDC の追加](media/looop-provisioning-tutorial/signup.png)

6. Looop は OpenIDConnect アプリであるため、Microsoft の職場アカウントを使用して Looop にログインすることを選択します。

    ![Looop OIDC ログイン](media/looop-provisioning-tutorial/msftlogin.png)

7. 認証に成功した後、同意ページの同意プロンプトを受け入れます。 その後、ご使用のテナントにアプリケーションが自動的に追加され、Looop アカウントにリダイレクトされます。

    ![Looop OIDc の同意](media/looop-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-looop"></a>Looop への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザー、グループ、またはその両方の割り当てに基づいて、Looop でユーザー、グループ、またはその両方が作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-looop-in-azure-ad"></a>Azure AD で Looop の自動ユーザー プロビジョニングを構成するには、次の操作を行います。

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Looop]** を選択します。

    ![アプリケーションの一覧の [Looop] リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://<organisation_domain>.looop.co/scim/v2`」と入力します。 たとえば、「 `https://demo.looop.co/scim/v2` 」のように入力します。 前の手順で Looop から取得して保存した値を **[シークレット トークン]** に入力します。 **[テスト接続]** をクリックして、Azure AD から Looop への接続を確保します。 接続できない場合は、使用中の Looop アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションで **[Synchronize Azure Active Directory Users to Looop]\(Azure Active Directory ユーザーを Looop に同期する\)** を選択します。

    ![Looop のユーザー マッピング](media/looop-provisioning-tutorial/usermappings.png)

9. **[属性マッピング]** セクションで、Azure AD から Looop に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Looop のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|フィルター処理のサポート|
   |---|---|---|
   |userName|String|&check;|
   |active|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:area|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:custom_1|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:custom_2|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:custom_3|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:employee_id|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:location|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:position|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:startAt|String|

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to Meta Networks Connector]\(Azure Active Directory グループを Meta Networks Connector に同期する\)** を選択します。

    ![Looop のグループ マッピング](media/looop-provisioning-tutorial/groupmappings.png)

11. **[属性マッピング]** セクションで、Azure AD から Meta Networks Connector に同期されるグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新操作で Meta Networks Connector のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    |属性|Type|フィルター処理のサポート|
    |---|---|---|
    |displayName|String|&check;|
    |members|リファレンス|
    |externalId|String|


10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

11. Looop に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

12. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Looop にプロビジョニングするユーザー、グループ、またはこれらの両方を定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

13. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Looop に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)


