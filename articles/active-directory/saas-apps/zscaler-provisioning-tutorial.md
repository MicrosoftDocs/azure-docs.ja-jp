---
title: チュートリアル:Zscaler を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Zscaler に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 9f368a4aebc4d5de38ebbab800241366650633e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97936585"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>チュートリアル:Zscaler を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure AD が自動的にユーザーまたはグループを Zscaler にプロビジョニングまたは Zscaler からプロビジョニング解除するように構成するために、Zscaler と Azure Active Directory (Azure AD) で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* Azure AD テナント。
* Zscaler テナント。
* 管理者アクセス許可がある Zscaler のユーザー アカウント。

> [!NOTE]
> Azure AD プロビジョニング統合では、Enterprise パッケージを含むアカウントについて Zscaler 開発者が使用できる Zscaler SCIM API が必要です。

## <a name="adding-zscaler-from-the-gallery"></a>ギャラリーからの Zscaler の追加

Azure AD で自動ユーザー プロビジョニング用に Zscaler を構成する前に、Zscaler を Azure AD アプリケーション ギャラリーから管理対象の SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Zscaler を追加するには、次の手順を行います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Zscaler**」と入力し、結果パネルで **Zscaler** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Zscaler](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>Zscaler にユーザーを割り当てる

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Zscaler へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Zscaler に割り当てることができます。

* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>ユーザーを Zscaler に割り当てるときの重要なヒント

* 単一の Azure AD ユーザーを Zscaler に割り当て、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Zscaler にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Zscaler への自動ユーザー プロビジョニングの構成

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Zscaler のユーザーやグループを作成、更新、無効化する手順について説明します。

> [!TIP]
> Zscaler では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Zscaler シングル サインオンのチュートリアル](zscaler-tutorial.md)に関するページで説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

> [!NOTE]
> ユーザーとグループをプロビジョニングしたりプロビジョニング解除したりする際は、グループ メンバーシップが適切に更新されるよう、定期的にプロビジョニングをやり直すことをお勧めします。 そうすることによって、サービスによって強制的にすべてのグループが再評価され、メンバーシップが更新されます。 

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>Azure AD で Zscaler の自動ユーザー プロビジョニングを構成するには

1. [Azure portal](https://portal.azure.com) にサインインし、 **[エンタープライズ アプリケーション]** 、 **[すべてのアプリケーション]** 、 **[Zscaler]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[Zscaler]** を選択します。

    ![アプリケーションの一覧の Zscaler のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[Zscaler - プロビジョニング エンタープライズ アプリケーション] サイドバーのスクリーンショット。[プロビジョニング] オプションが強調表示されています。](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] ページのスクリーンショット。[プロビジョニング モード] が [自動] に設定されています。](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. **[管理者資格情報]** セクションで、手順 6 で説明する Zscaler アカウントの **[テナント URL]** と **[シークレット トークン]** を入力します。

6. **[テナント URL]** と **[シークレット トークン]** を取得するには、Zscaler ポータル ユーザー インターフェイスで **[管理]、[認証の設定]** の順に移動し、 **[認証の種類]** で **[SAML]** をクリックします。

    ![[認証設定] ページのスクリーンショット。](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    **[Configure SAML]\(SAML の構成\)** をクリックし、 **[Configuration SAML]\(構成 SAML\)** オプションを開きます。

    ![[SAML の構成] ダイアログ ボックスのスクリーンショット。[ベース URL] と [ベアラー トークン] のテキスト ボックスが選択されています。](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    **[Enable SCIM-Based Provisioning]\(SCIM ベースのプロビジョニングを有効にする\)** を選択して、**ベース URL** と **ベアラー トークン** を取得し、設定を保存します。 Azure portal で **ベース URL** を **テナント URL** にコピーし、**ベアラー トークン** を **シークレット トークン** にコピーします。

7. 手順 5 の各フィールドに値を入力したら、 **[テスト接続]** をクリックして、Azure AD が Zscaler に接続できることを確認します。 接続できない場合は、使用中の Zscaler アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![[管理者資格情報] セクションのスクリーンショット。[テスト接続] オプションが選択されています。](./media/zscaler-provisioning-tutorial/test-connection.png)

8. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![[通知用メール] テキスト ボックスのスクリーンショット。](./media/zscaler-provisioning-tutorial/notification.png)

9. **[保存]** をクリックします。

10. **[マッピング]** セクションで **[Synchronize Azure Active Directory Users to Zscaler]\(Azure Active Directory ユーザーを Zscaler に同期する\)** を選択します。

    ![[マッピング] セクションのスクリーンショット。[Synchronize Azure Active Directory Users to Zscaler]\(Azure Active Directory ユーザーを Zscaler に同期する\) オプションが強調表示されています。](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. **[属性マッピング]** セクションで、Azure AD から Zscaler に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Zscaler のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![7 つのマッピングが表示されている [属性マッピング] セクションのスクリーンショット。](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. **[マッピング]** セクションで **[Synchronize Azure Active Directory Groups to Zscaler]\(Azure Active Directory グループを Zscaler に同期する\)** を選択します。

    ![[マッピング] セクションのスクリーンショット。[Synchronize Azure Active Directory Groups to Zscaler]\(Azure Active Directory グループを Zscaler に同期する\) オプションが強調表示されています。](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. **[属性マッピング]** セクションで、Azure AD から Zscaler に同期されるグループ属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Zscaler のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![3 つのマッピングが表示されている [属性マッピング] セクションのスクリーンショット。](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

15. Zscaler に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![[プロビジョニングの状態] オプションのスクリーンショット。[オン] に設定されています。](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Zscaler にプロビジョニングするユーザーやグループを定義します。

    ![[スコープ] 設定のスクリーンショット。[割り当てられたユーザーとグループのみを同期する] オプションが強調表示されています。](./media/zscaler-provisioning-tutorial/scoping.png)

17. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![[Zscaler - プロビジョニング エンタープライズ アプリケーション] サイドバーのスクリーンショット。[保存] オプションが選択されています。](./media/zscaler-provisioning-tutorial/save-provisioning.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Zscaler に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png