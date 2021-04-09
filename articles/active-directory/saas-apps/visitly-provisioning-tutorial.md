---
title: 'チュートリアル: Visitly を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs'
description: Azure Active Directory を構成して、ユーザー アカウントを Visitly に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: ff3f3ab65df2d801b7c962de7cce645e9fc00b30
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94358611"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>チュートリアル: Visitly を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure AD が自動的にユーザーまたはグループを Visitly にプロビジョニングまたは Wrike からプロビジョニング解除するように構成するために、Visitly と Azure Active Directory (Azure AD) で実行する手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスで実行されること、しくみ、よく寄せられる質問の重要な詳細については、[Azure Active Directory によるサービスとしてのソフトウェア (SaaS) アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)に関する記事を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure の使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [Visitly テナント](https://www.visitly.io/pricing/)
* 管理者アクセス許可がある Visitly のユーザー アカウント

## <a name="assign-users-to-visitly"></a>ユーザーを Visitly に割り当てる 

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD でアプリケーションに割り当てられているユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Visitly にアクセスする必要がある Azure AD のユーザーまたはグループを決定しておく必要があります。 その後、次の手順に従って、これらのユーザーまたはグループを Visitly に割り当てます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>ユーザーを Visitly に割り当てる際の重要なヒント 

* 単一の Azure AD ユーザーを Visitly に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後で追加のユーザーまたはグループを割り当てることができます。

* Visitly にユーザーを割り当てるときに、有効なアプリケーション固有ロール (ある場合) を割り当てダイアログ ボックスで選択する必要があります。 既定のアクセス ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-visitly-for-provisioning"></a>プロビジョニング用に Visitly を設定する

Azure AD を使用した自動ユーザー プロビジョニング用に Visitly を構成する前に、Visitly で System for Cross-domain Identity Management (SCIM) プロビジョニングを有効にする必要があります。

1. [Visitly](https://app.visitly.io/login) にサインインします。 **[Integrations]\(統合\)**  >  **[Host Synchronization]\(ホストの同期\)** の順に選択します。

    ![ホストの同期](media/Visitly-provisioning-tutorial/login.png)

2. **[Azure AD]** セクションを選択します。

    ![Azure AD セクション](media/Visitly-provisioning-tutorial/integration.png)

3. **API キー** をコピーします。 これらの値は、Azure portal で Visitly アプリケーションの **[プロビジョニング]** タブにある **[シークレット トークン]** ボックスに入力します。

    ![API キー](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>ギャラリーからの Visitly の追加

Azure AD で自動ユーザー プロビジョニング用に Visitly を構成するには、Azure AD アプリケーション ギャラリーから Visitly をマネージド SaaS アプリケーションの一覧に追加します。

Azure AD ギャラリーから Visitly を追加するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Visitly**」と入力し、結果ウィンドウで **[Visitly]** を選択してから、**[追加]** を選択してアプリケーションを追加します。

    ![結果一覧の Visitly](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-visitly"></a>Visitly への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて Visitly のユーザーやグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> Visitly で SAML ベースのシングル サインオンを有効にするには、[Visitly シングル サインオンのチュートリアル](Visitly-tutorial.md)の手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="configure-automatic-user-provisioning-for-visitly-in-azure-ad"></a>Azure AD で Visitly の自動ユーザー プロビジョニングを構成する

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。

    ![すべてのアプリケーション](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Visitly]** を選択します。

    ![アプリケーションの一覧の Visitly のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング モード] が [自動] に設定されている](common/provisioning-automatic.png)

5. [管理者資格情報] セクションの **[テナント URL]** および **[シークレット トークン]** に、先ほど取得した `https://api.visitly.io/v1/usersync/SCIM` と **API キー** の値をそれぞれ入力します。 Azure AD から Visitly に接続できることを確認するために、**[テスト接続]** を選択します。 接続できない場合は、使用中の Visitly アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. プロビジョニングのエラー通知を受け取るユーザーまたはグループのメール アドレスを **[通知用メール]** フィールドに入力し、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** を選択します。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Visitly]\(Azure Active Directory ユーザーを Visitly に同期する\)** を選択します。

    ![Visitly のユーザー マッピング](media/visitly-provisioning-tutorial/usermapping.png)

9. **[属性マッピング]** セクションで、Azure AD から Visitly に同期されるユーザー属性を確認します。 **照合** 用プロパティとして選択されている属性は、更新処理で Visitly のユーザー アカウントとの照合に使用されます。 すべての変更をコミットするには、 **[保存]** を選択します。

    ![Visitly のユーザー属性](media/visitly-provisioning-tutorial/userattribute.png)

10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の手順を参照してください。

11. Visitly に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

12. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Visitly にプロビジョニングするユーザーまたはグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

13. プロビジョニングの準備ができたら、 **[保存]** を選択します。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

この操作によって、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーまたはグループの初期同期が開始されます。 初期同期は、後続の同期よりも実行に時間がかかります。 ユーザーやグループのプロビジョニングにかかる時間の詳細については、「[ユーザーをプロビジョニングするにはどのくらいの時間がかかりますか](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)」を参照してください。

**[現在の状態]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Visitly に対して実行されたすべてのアクションが記載されています。 詳細については、「[ユーザー プロビジョニングの状態を確認する](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)」を参照してください。 Azure AD プロビジョニング ログを読むには、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」を参照してください。

## <a name="connector-limitations"></a>コネクタの制限事項

Visitly では、物理的な削除をサポートしていません。 すべて論理的な削除のみが可能です。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
