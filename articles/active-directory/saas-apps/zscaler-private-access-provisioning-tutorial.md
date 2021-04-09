---
title: チュートリアル:Azure Active Directory での自動ユーザー プロビジョニング用に Zscaler Private Access (ZPA) を構成する | Microsoft Docs
description: ユーザー アカウントを Zscaler Private Access (ZPA) に自動的にプロビジョニング/プロビジョニング解除するように Azure Active Directory を構成する方法について説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: 14708ddcc5c0e06ee58f5e9db5945c4e9f1a1d08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937146"
---
# <a name="tutorial-configure-zscaler-private-access-zpa-for-automatic-user-provisioning"></a>チュートリアル:自動ユーザー プロビジョニング用に Zscaler Private Access (ZPA) を構成する

このチュートリアルの目的は、ユーザーまたはグループを Zscaler Private Access (ZPA) に自動的にプロビジョニング/プロビジョニング解除するように Azure AD を構成するために Zscaler Private Access (ZPA) と Azure Active Directory (Azure AD) で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [Zscaler Private Access (ZPA) テナント](https://www.zscaler.com/pricing-and-plans#contact-us)
* 管理者アクセス許可を持つ Zscaler Private Access (ZPA) のユーザー アカウント。

## <a name="assigning-users-to-zscaler-private-access-zpa"></a>Zscaler Private Access (ZPA) へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Azure AD のどのユーザーまたはグループに Zscaler Private Access (ZPA) へのアクセスが必要かを決定する必要があります。 決定したら、次の手順に従って、これらのユーザーまたはグループを Zscaler Private Access (ZPA) に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-zscaler-private-access-zpa"></a>ユーザーを Zscaler Private Access (ZPA) に割り当てるための重要なヒント

* 単一の Azure AD ユーザーを Zscaler Private Access (ZPA) に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Zscaler Private Access (ZPA) にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-zscaler-private-access-zpa-for-provisioning"></a>プロビジョニング用に Zscaler Private Access (ZPA) を設定する

1. [Zscaler Private Access (ZPA) の管理コンソール](https://admin.private.zscaler.com/)にサインインします。 **[管理] > [IdP Configuration] (IdP 構成)** に移動します。

    ![Zscaler Private Access (ZPA) の管理コンソール](media/zscaler-private-access-provisioning-tutorial/idpconfig.png)

2.  **シングル サインオン** 用の IdP が構成されていることを確認します。 IdP が設定されていない場合は、画面の右上隅にあるプラス アイコンをクリックすることによって追加します。

    ![Zscaler Private Access (ZPA) の SCIM の追加](media/zscaler-private-access-provisioning-tutorial/plusicon.png)

3. **[Add IdP Configuration] (IdP 構成の追加)** ウィザードに従って IdP を追加します。 **[シングル サインオン]** フィールドは **[ユーザー]** に設定されたままにします。 **[名前]** を指定し、ドロップダウン リストから **[ドメイン]** を選択します。 **[次へ]** をクリックして、次のウィンドウに移動します。

    ![Zscaler Private Access (ZPA) の IdP の追加](media/zscaler-private-access-provisioning-tutorial/addidp.png)

4. **サービス プロバイダー証明書** をダウンロードします。 **[次へ]** をクリックして、次のウィンドウに移動します。

    ![Zscaler Private Access (ZPA) の SP 証明書](media/zscaler-private-access-provisioning-tutorial/spcertificate.png)

5. 次のウィンドウで、前にダウンロードされた **サービス プロバイダー証明書** をアップロードします。

    ![Zscaler Private Access (ZPA) の証明書のアップロード](media/zscaler-private-access-provisioning-tutorial/uploadfile.png)

6.  下へスクロールして、 **[シングル サインオン URL]** と **[IdP エンティティ ID]** を指定します。

    ![Zscaler Private Access (ZPA) の IdP ID](media/zscaler-private-access-provisioning-tutorial/idpid.png)

7.  **[Enable SCIM Sync] (SCIM 同期を有効にする)** まで下へスクロールします。 **[新しいトークンの生成]** ボタンをクリックします。 **[Bearer Token] (ベアラー トークン)** をコピーします。 この値は、Azure portal で Zscaler Private Access (ZPA) アプリケーションの [プロビジョニング] タブにある [シークレット トークン] フィールドに入力されます。

    ![Zscaler Private Access (ZPA) のトークンの作成](media/zscaler-private-access-provisioning-tutorial/token.png)

8.  **[テナント URL]** を見つけるには、 **[管理] > [IdP Configuration]\(IdP 構成\)** に移動します。 このページに表示されている新しく追加された IdP 構成の名前をクリックします。

    ![Zscaler Private Access (ZPA) の Idp 名](media/zscaler-private-access-provisioning-tutorial/idpname.png)

9.  下へスクロールして、このページの最後にある **[SCIM Service Provider Endpoint] (SCIM サービス プロバイダーのエンドポイント)** を表示します。 **[SCIM Service Provider Endpoint] (SCIM サービス プロバイダーのエンドポイント)** をコピーします。 この値は、Azure portal で Zscaler Private Access (ZPA) アプリケーションの [プロビジョニング] タブにある [テナント URL] フィールドに入力されます。

    ![Zscaler Private Access (ZPA) の SCIM URL](media/zscaler-private-access-provisioning-tutorial/tenanturl.png)


## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>ギャラリーから Zscaler Private Access (ZPA) を追加する

Azure AD での自動ユーザー プロビジョニング用に Zscaler Private Access (ZPA) を構成する前に、Zscaler Private Access (ZPA) を Azure AD アプリケーション ギャラリーからマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Zscaler Private Access (ZPA) を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Zscaler Private Access (ZPA)** 」と入力します。結果ウィンドウの **[Zscaler Private Access (ZPA)]** を選択し、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の Zscaler Private Access (ZPA)](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-zscaler-private-access-zpa"></a>Zscaler Private Access (ZPA) への自動ユーザー プロビジョニングの構成 

このセクションでは、Azure AD でのユーザーまたはグループの割り当てに基づいて、Zscaler Private Access (ZPA) のユーザーまたはグループを作成、更新、および無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> [Zscaler Private Access (ZPA) のシングル サインオン](./zscalerprivateaccess-tutorial.md)に関するチュートリアルに示されている手順に従って、Zscaler Private Access (ZPA) の SAML ベースのシングル サインオンを有効にすることも選択できます。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

> [!NOTE]
> ユーザーとグループをプロビジョニングしたりプロビジョニング解除したりする際は、グループ メンバーシップが適切に更新されるよう、定期的にプロビジョニングをやり直すことをお勧めします。 そうすることによって、サービスによって強制的にすべてのグループが再評価され、メンバーシップが更新されます。  

> [!NOTE]
> Zscaler Private Access の SCIM エンドポイントの詳細については、[ここ](https://www.zscaler.com/partners/microsoft)を参照してください。

### <a name="to-configure-automatic-user-provisioning-for-zscaler-private-access-zpa-in-azure-ad"></a>Azure AD で Zscaler Private Access (ZPA) の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[Zscaler Private Access (ZPA)]** を選択します。

    ![アプリケーションの一覧の Zscaler Private Access (ZPA) のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者の資格情報]** セクションで、 **[テナント URL]** に、前に取得した **[SCIM Service Provider Endpoint] (SCIM サービス プロバイダーのエンドポイント)** の値を入力します。 **[シークレット トークン]** に、前に取得した **[Bearer Token] (ベアラー トークン)** の値を入力します。 **[接続テスト]** をクリックして、Azure AD が Zscaler Private Access (ZPA) に接続できることを確認します。 接続に失敗する場合は、Zscaler Private Access (ZPA) アカウントに管理者アクセス許可があることを確認し、再試行します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Users to Zscaler Private Access (ZPA)] (Azure Active Directory ユーザーを Zscaler Private Access (ZPA) に同期する)** を選択します。

    ![Zscaler Private Access (ZPA) のユーザー マッピング](media/zscaler-private-access-provisioning-tutorial/usermappings.png)

9. **[属性マッピング]** セクションで、Azure AD から Zscaler Private Access (ZPA) に同期されるユーザー属性を確認します。 **[Matching] (照合)** プロパティとして選択されている属性は、更新操作のために Zscaler Private Access (ZPA) のユーザー アカウントを照合するために使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Zscaler Private Access (ZPA) のユーザー属性](media/zscaler-private-access-provisioning-tutorial/userattributes.png)

10. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Groups to Zscaler Private Access (ZPA)] (Azure Active Directory グループを Zscaler Private Access (ZPA) に同期する)** を選択します。

    ![Zscaler Private Access (ZPA) のグループ マッピング](media/zscaler-private-access-provisioning-tutorial/groupmappings.png)

11. **[属性マッピング]** セクションで、Azure AD から Zscaler Private Access (ZPA) に同期されるグループ属性を確認します。 **[Matching] (照合)** プロパティとして選択されている属性は、更新操作のために Zscaler Private Access (ZPA) のグループを照合するために使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Zscaler Private Access (ZPA) のグループ属性](media/zscaler-private-access-provisioning-tutorial/groupattributes.png)

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Zscaler Private Access (ZPA) の Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択することによって、Zscaler Private Access (ZPA) にプロビジョニングするユーザーまたはグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視したり、プロビジョニング アクティビティ レポートへのリンクに従ったりできます。このレポートには、Zscaler Private Access (ZPA) の Azure AD プロビジョニング サービスによって実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)