---
title: 'チュートリアル: Azure Active Directory で自動ユーザー プロビジョニング用に Keeper Password Manager & Digital Vault を構成する | Microsoft Docs'
description: ユーザー アカウントを Keeper Password Manager & Digital Vault に自動的にプロビジョニングおよびプロビジョニング解除するよう Azure Active Directory を構成する方法について説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 2670dc0cb56805a2afa966bee1d2aa52b6c8e46a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94358985"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>チュートリアル: 自動ユーザー プロビジョニング用に Keeper Password Manager & Digital Vault を構成する

このチュートリアルの目的は、ユーザーまたはグループを Keeper Password Manager & Digital Vault に自動的にプロビジョニングおよびプロビジョニング解除するよう Azure AD を構成するために、Keeper Password Manager & Digital Vault と Azure Active Directory (Azure AD) で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [Keeper Password Manager & Digital Vault テナント](https://keepersecurity.com/pricing.html?t=e)
* 管理者アクセス許可がある Keeper Password Manager & Digital Vault のユーザー アカウント

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>ギャラリーから Keeper Password Manager & Digital Vault を追加する

Azure AD で自動ユーザー プロビジョニング用に Keeper Password Manager & Digital Vault を構成する前に、Keeper Password Manager & Digital Vault を Azure AD アプリケーション ギャラリーからマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Keeper Password Manager & Digital Vault を追加するには、次の手順に従います。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Keeper Password Manager & Digital Vault**」と入力し、結果パネルで **[Keeper Password Manager & Digital Vault]** を選択してから、**[追加]** をクリックしてアプリケーションを追加します。

    ![結果一覧の Keeper Password Manager & Digital Vault](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Keeper Password Manager & Digital Vault へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Keeper Password Manager & Digital Vault へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定したら、次の手順に従って、これらのユーザーやグループを Keeper Password Manager & Digital Vault に割り当てることができます。

* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Keeper Password Manager & Digital Vault にユーザーを割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを Keeper Password Manager & Digital Vault に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Keeper Password Manager & Digital Vault にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Keeper Password Manager & Digital Vault への自動ユーザー プロビジョニングの構成 

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて Keeper Password Manager & Digital Vault のユーザーやグループを作成、更新、無効化するよう、Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> Keeper Password Manager & Digital Vault では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Keeper Password Manager & Digital Vault シングル サインオンのチュートリアル](keeperpasswordmanager-tutorial.md)に関するページで説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>Azure AD で Keeper Password Manager & Digital Vault 用に自動ユーザー プロビジョニングを構成するには

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Keeper Password Manager & Digital Vault]** を選択します。

    ![アプリケーションの一覧での Keeper Password Manager & Digital Vault リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションで、手順 6 で説明するように、Keeper Password Manager & Digital Vault アカウントの **[テナント URL]** と **[シークレット トークン]** を入力します。

6. [Keeper 管理コンソール](https://keepersecurity.com/console/#login)にサインインします。 **[Admin]\(管理者\)** をクリックし、既存のノードを選択するか、または新しく作成します。 **[Provisioning]\(プロビジョニング\)** タブに移動し、**[Add Method]\(メソッドの追加\)** を選択します。

    ![Keeper 管理コンソール](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    **[SCIM (System for Cross-domain Identity Management)]** を選択します。

    ![Keeper での SCIM の追加](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    **[Create Provisioning Token]\(プロビジョニング トークンの作成/)** をクリックします。

    ![Keeper でのエンドポイントの作成](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    **[URL]** と **[Token]\(トークン\)** の値をコピーし、Azure AD の **[テナント URL]** と **[シークレット トークン]** に貼り付けます。 **[保存]** をクリックして、Keeper でのプロビジョニング設定を完了します。

    ![Keeper でのトークンの作成](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. 手順 5 の各フィールドに値を入力したら、**[テスト接続]** をクリックして、Azure AD が Keeper Password Manager & Digital Vault に接続できることを確認します。 接続できない場合は、使用中の Keeper Password Manager & Digital Vault アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

8. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

9. **[保存]** をクリックします。

10. **[マッピング]** セクションで、**[Synchronize Azure Active Directory Users to Keeper Password Manager & Digital Vault]\(Azure Active Directory ユーザーを Keeper Password Manager & Digital Vault に同期する\)** を選択します。

    ![Keeper ユーザー マッピング](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. **[属性マッピング]** セクションで、Azure AD から Keeper Password Manager & Digital Vault に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新操作で Keeper Password Manager & Digital Vault のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Keeper ユーザーの属性](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. **[マッピング]** セクションで、**[Synchronize Azure Active Directory Groups to Keeper Password Manager & Digital Vault]\(Azure Active Directory グループを Keeper Password Manager & Digital Vault に同期する\)** を選択します。

    ![Keeper グループ マッピング](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. **[属性マッピング]** セクションで、Azure AD から Keeper Password Manager & Digital Vault に同期されるグループ属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新操作で Keeper Password Manager & Digital Vault のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Keeper グループ属性](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

15. Keeper Password Manager & Digital Vault に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

16. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Keeper Password Manager & Digital Vault にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

17. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Keeper Password Manager & Digital Vault に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="connector-limitations"></a>コネクタの制限事項

* Keeper Password Manager & Digital Vault では、**電子メール** と **ユーザー名** のソース値が同じである必要があります。これは、どちらかの属性が更新されると、もう一方の値が変更されるためです。
* Keeper Password Manager & Digital Vault では、ユーザーの削除はサポートされておらず、無効化のみ可能です。 無効化されたユーザーは、Keeper 管理コンソール UI でロック済みとして表示されます。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)

