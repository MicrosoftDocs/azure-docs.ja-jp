---
title: チュートリアル:Smartsheet を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Smartsheet に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: f0ca2dfa90e1312db664962e7ffbe6b3f4dd96e1
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670933"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>チュートリアル:自動ユーザー プロビジョニング用に Smartsheet を構成する

このチュートリアルの目的は、Azure AD が自動的にユーザーまたはグループを Smartsheet にプロビジョニングまたは Smartsheet からプロビジョニング解除するように構成するために、Smartsheet と Azure Active Directory (Azure AD) で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [Smartsheet テナント](https://www.smartsheet.com/pricing)
* システム管理者アクセス許可を持つ Smartsheet Enterprise または Enterprise Premier プランのユーザー アカウント。

## <a name="assign-users-to-smartsheet"></a>ユーザーを Smartsheet に割り当てる

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Smartsheet へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Smartsheet に割り当てることができます。

* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>ユーザーを Smartsheet に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを Smartsheet に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Smartsheet にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

* Smartsheet と Azure AD の間でユーザー ロールの割り当てを一致させるために、Smartsheet ユーザー一覧全体に設定されているものと同じロールの割り当てを利用することをお勧めします。 Smartsheet からこのユーザー一覧を取得するには、 **[Account Admin]\(アカウント管理\) > [User Management]\(ユーザー管理\) > [More Actions]\(その他の操作\) > [Download User List (csv)]\(ユーザー一覧のダウンロード (csv)\)** に移動します。

* アプリの一部の機能にアクセスするには、Smartsheet でユーザーが複数のロールを持つ必要があります。 Smartsheet のユーザーの種類とアクセス許可の詳細については、「[User Types and Permissions (ユーザーの種類とアクセス許可)](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions)」を参照してください。

*  Smartsheet でユーザーに複数のロールを割り当てられている場合、ユーザーが Smartsheet オブジェクトへのアクセスを完全に失うことがないように、これらのロールの割り当てを Azure AD で確実に複製する**必要があります**。 Smartsheet 内の一意の各ロールは、Azure AD 内で別のグループに割り当てる**必要があります**。 次に、ユーザーを目的のロールに対応する各グループに追加する**必要があります**。 

## <a name="set-up-smartsheet-for-provisioning"></a>プロビジョニング用に Smartsheet を設定する

Azure AD での自動ユーザー プロビジョニング用に Smartsheet を構成する前に、Smartsheet 上で SCIM プロビジョニングを有効にする必要があります。

1. **[Smartsheet ポータル](https://app.smartsheet.com/b/home)** で **SysAdmin** としてサインインし、 **[Account Admin]\(アカウント管理\)** に移動します。

    ![Smartsheet のアカウント管理](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. **[Security Controls]\(セキュリティ制御\) >[User Auto Provisioning]\(ユーザーの自動プロビジョニング\) > [Edit]\(編集\)** に移動します。

    ![Smartsheet のセキュリティ制御](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Azure AD から Smartsheet にプロビジョニングする予定のユーザーのメール ドメインを追加し、検証します。 **[Not Enabled]\(無効\)** を選択して、すべてのプロビジョニング操作が Azure AD からのみ行われ、Smartsheet ユーザー一覧が Azure AD の割り当てと同期されるようにします。

    ![Smartsheet のユーザー プロビジョニング](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. 検証が完了したら、ドメインをアクティブにする必要があります。 

    ![Smartsheet のドメインのアクティブ化](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. **[Apps and Integrations]\(アプリと統合\)** に移動して、Azure AD で自動ユーザー プロビジョニングを構成するために必要な **[Secret Token]\(シークレット トークン\)** を生成します。

    ![Smartsheet のインストール](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. **[API Access]\(API アクセス\)** を選択します。 **[Generate new access token]\(新しいアクセス トークンの生成\)** をクリックします。

    ![Smartsheet のインストール](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. API アクセス トークンの名前を定義します。 Click **OK**.

    ![Smartsheet のインストール](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. これが API アクセス トークンを表示できる唯一のタイミングなので、コピーして保存します。 これは、Azure AD の **[Secret Token]\(シークレット トークン\)** フィールドに必要です。

    ![Smartsheet のトークン](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>ギャラリーから Smartsheet を追加する

Azure AD で自動ユーザー プロビジョニング用に Smartsheet を構成するには、Azure AD アプリケーション ギャラリーから Smartsheet をマネージド SaaS アプリケーションの一覧に追加する必要があります。

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Smartsheet**」と入力し、結果パネルで **[Smartsheet]** を選択します。 

    ![結果一覧の Smartsheet](common/search-new-app.png)

5. **[Sign-up for Smartsheet]\(Smartsheet へのサインアップ\)** ボタンを選択します。Smartsheet のログイン ページにリダイレクトされます。 

    ![Smartsheet OIDC の追加](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Smartsheet は OpenIDConnect アプリなので、Microsoft の職場アカウントを使用して Smartsheet にログインすることを選択します。

    ![Smartsheet OIDC のログイン](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. 認証に成功したら、同意ページの同意プロンプトを受け入れます。 アプリケーションはお客様のテナントに自動的に追加され、Smartsheet アカウントにリダイレクトされます。

    ![Smartsheet OIDC の同意](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>Smartsheet への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Smartsheet のユーザーやグループを作成、更新、無効化する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Azure AD で Smartsheet の自動ユーザー プロビジョニングを構成するには:

1. [Azure Portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Smartsheet]** を選択します。

    ![アプリケーションの一覧の Smartsheet リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://scim.smartsheet.com/v2/`」と入力します。 前の手順で Smartsheet から取得して保存した値を **[シークレット トークン]** に入力します。 **[接続テスト]** をクリックして、Azure AD から Smartsheet に接続できることを確認します。 接続できない場合は、使用中の Smartsheet アカウントに SysAdmin アクセス許可があることを確認してから、もう一度試します。

    ![トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[Save]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Smartsheet]\(Azure Active Directory ユーザーを Smartsheet に同期する\)** を選択します。

    ![Smartsheet のユーザー マッピング](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. **[属性マッピング]** セクションで、Azure AD から Smartsheet に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Smartsheet のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Smartsheet のユーザー属性](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

11. Smartsheet に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

12. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Smartsheet にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

13. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Smartsheet に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="connector-limitations"></a>コネクタの制限事項

* Smartsheet は論理的な削除をサポートしていません。 ユーザーの **active** 属性が False に設定されていると、Smartsheet ではユーザーが完全に削除されます。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)
