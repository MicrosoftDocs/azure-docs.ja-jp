---
title: 'チュートリアル: Storegate を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs'
description: Azure Active Directory を構成して、ユーザー アカウントを Storegate に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/15/2019
ms.author: Zhchia
ms.openlocfilehash: c984beff630ef90ea33a13e2fef1bca0189c2314
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94357943"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>チュートリアル: Storegate を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure AD によって自動的にユーザーまたはグループが Storegate にプロビジョニングされる、または Storegate からプロビジョニング解除されるように構成するため、Storegate と Azure Active Directory (Azure AD) で実行する手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [Storegate テナント](https://www.storegate.com)
* 管理者アクセス許可を持つ Storegate のユーザー アカウント。

## <a name="assign-users-to-storegate"></a>ユーザーを Storegate に割り当てる

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Storegate へのアクセスが必要な Azure AD 内のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Storegate に割り当てることができます。

* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>ユーザーを Storegate に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを Storegate に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Storegate にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-storegate-for-provisioning"></a>プロビジョニングのために Storegate を設定する

Azure AD での自動ユーザー プロビジョニング用に Storegate を構成する前に、Storegate からプロビジョニング情報を取得する必要があります。

1. [Storegate 管理コンソール](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367)にサインインし、右上隅にあるユーザー アイコンをクリックして設定に移動して、**[Account Settings]\(アカウントの設定\)** を選択します。

    ![Storegate: SCIM の追加](media/storegate-provisioning-tutorial/admin.png)

2. 設定で **[Team]\(チーム\) > [Settings]\(設定\)** に移動し、**[Single sign-on]\(シングル サインオン\)** セクションでトグル スイッチがオンになっていることを確認します。

    ![Storegate: 設定](media/storegate-provisioning-tutorial/team.png)

    ![Storegate: トグル ボタン](media/storegate-provisioning-tutorial/sso.png)

3. **[Tenant URL]\(テナント URL\)** と **[Token]\(トークン\)** をコピーします。 これらの値を、Azure portal の Storegate アプリケーションの [プロビジョニング] タブの **[テナント URL]** および **[シークレット トークン]** フィールドにそれぞれ入力します。 

    ![Storegate: トークンを作成する](media/storegate-provisioning-tutorial/token.png)

## <a name="add-storegate-from-the-gallery"></a>ギャラリーから Storegate を追加する

Azure AD で自動ユーザー プロビジョニング用に Storegate を構成するには、Azure AD アプリケーション ギャラリーから管理対象 SaaS アプリケーションの一覧に Storegate を追加する必要があります。

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Storegate**」と入力し、結果パネルで **Storegate** を選択します。 

    ![結果一覧の Storegate](common/search-new-app.png)

5. **[Storegate にサインアップ]** ボタンを選択します。Storegate のログイン ページにリダイレクトされます。 

    ![Storegate: OIDC の追加](media/storegate-provisioning-tutorial/signup.png)

6.  [Storegate 管理コンソール](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367)にサインインし、右上隅にあるユーザー アイコンをクリックして設定に移動して、**[Account Settings]\(アカウントの設定\)** を選択します。

    ![Storegate: ログイン](media/storegate-provisioning-tutorial/admin.png)

7. 設定で **[Team]\(チーム\) > [Settings]\(設定\)** に移動し、[Single sign-on]\(シングル サインオン\) セクションのトグル スイッチをクリックします。これにより、同意フローが開始されます。 **[Activate]\(アクティブ化\)** をクリックします。

    ![Storegate: チーム](media/storegate-provisioning-tutorial/team.png)

    ![Storegate: SSO](media/storegate-provisioning-tutorial/sso.png)

    ![Storegate: アクティブ化](media/storegate-provisioning-tutorial/activate.png)

8. Storegate は OpenIDConnect アプリであるため、Microsoft の職場アカウントを使用して Storegate にログインすることを選択します。

    ![Storegate: OIDC ログイン](media/storegate-provisioning-tutorial/login.png)

9. 認証に成功した後、同意ページの同意プロンプトを受け入れます。 その後、ご使用のテナントにアプリケーションが自動的に追加され、Storegate アカウントにリダイレクトされます。

    ![Storegate: OIDC の同意](media/storegate-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-storegate"></a>Storegate への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Storegate のユーザーやグループを作成、更新、無効化する手順について説明します。

> [!NOTE]
> Storegate の SCIM エンドポイントの詳細については、[こちら](https://en-support.storegate.com/article/step-by-step-instruction-how-to-enable-azure-provisioning-to-your-storegate-team-account/)を参照してください。

### <a name="to-configure-automatic-user-provisioning-for-storegate-in-azure-ad"></a>Azure AD で Storegate の自動ユーザー プロビジョニングを構成するには

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Storegate]** を選択します。

    ![アプリケーションの一覧の Storegate のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://dialpad.com/scim`」と入力します。 前の手順で Storegate から取得して保存した値を **[シークレット トークン]** に入力します。 **[接続テスト]** をクリックして、Azure AD から確実に Storegate に接続できるようにします。 接続できない場合は、使用中の Storegate アカウントに管理者アクセス許可があること確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Storegate]\(Azure Active Directory ユーザーを Storegate に同期する\)** を選択します。

    ![Storegate のユーザー マッピング](media/storegate-provisioning-tutorial/usermappings.png)

9. **[属性マッピング]** セクションで、Azure AD から Storegate に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Storegate のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Storegate: ユーザー属性](media/storegate-provisioning-tutorial/userattributes.png)

10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

11. Storegate に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

12. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Storegate にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

13. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Storegate に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
