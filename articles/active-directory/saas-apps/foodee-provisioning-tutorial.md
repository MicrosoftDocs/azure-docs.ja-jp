---
title: チュートリアル:Azure Active Directory を使用して Foodee を自動ユーザー プロビジョニング用に構成する | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Foodee に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
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
ms.openlocfilehash: 8b4bfa7e9bf457d79c6c4a0b5255bce4fe36dff4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358680"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>チュートリアル:Foodee を構成し、自動ユーザー プロビジョニングに対応させる

この記事では、Foodee での Azure Active Directory (Azure AD) と Azure AD を構成して、ユーザーまたはグループを Foodee に自動的にプロビジョニングしたり、プロビジョニング解除したりする方法を説明します。

> [!NOTE]
> この記事では、Azure AD ユーザー プロビジョニング サービス上に構築されるコネクタについて説明します。 このサービスが実行する内容、しくみについて、よく寄せられる質問への回答については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> このコネクタは、現在プレビューの段階です。 プレビュー機能のための Azure 利用規約機能の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、次の前提条件があることを前提としています。

* Azure AD テナント
* [Foodee テナント](https://www.food.ee/about/)
* Admin アクセス許可がある Foodee のユーザー アカウント

## <a name="assign-users-to-foodee"></a>ユーザーを Foodee に割り当てる 

Azure AD では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に *割り当て* という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーまたはグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Foodee へのアクセスが必要な Azure AD のユーザーまたはグループを決定しておく必要があります。 これを決定したら、[エンタープライズ アプリへのユーザーまたはグループの割り当て](../manage-apps/assign-user-or-group-access-portal.md)に関するページの手順に従って、これらのユーザーまたはグループを Foodee に割り当てることができます。

## <a name="important-tips-for-assigning-users-to-foodee"></a>ユーザーを Foodee に割り当てる際の重要なヒント 

ユーザーを割り当てるときは、次のヒントに留意してください。

* 単一の Azure AD ユーザーのみを Foodee に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後で、追加のユーザーまたはグループを割り当てることができます。

* Foodee にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を **[割り当て]** ウィンドウで選択します。 *既定のアクセス* ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-foodee-for-provisioning"></a>プロビジョニングのために Foodee を設定する

Azure AD を使用して自動ユーザー プロビジョニング用に Foodee を構成する前に、Foodee で System for Cross-domain Identity Management (SCIM) プロビジョニングを有効にする必要があります。

1. [Foodee](https://www.food.ee/login/) にサインインし、ご自分のテナント ID を選択します。

    :::image type="content" source="media/Foodee-provisioning-tutorial/tenant.png" alt-text="Foodee エンタープライズ ポータルのメイン メニューのスクリーンショット。メニューでテナント ID のプレースホルダーが表示されています。" border="false":::

1. **[エンタープライズ ポータル]** で、 **[シングル サインオン]** を選択します。

    ![Foodee エンタープライズ ポータルの左側のウィンドウのメニュー](media/Foodee-provisioning-tutorial/scim.png)

1. 後で使用するために、 **[API Token]\(API トークン\)** の値をコピーします。 この値は、Azure portal で Foodee アプリケーションの **[プロビジョニング]** タブにある **[シークレット トークン]** ボックスに入力します。

    :::image type="content" source="media/Foodee-provisioning-tutorial/token.png" alt-text="Foodee エンタープライズ ポータル内のページのスクリーンショット。A P I トークンの値が強調表示されています。" border="false":::

## <a name="add-foodee-from-the-gallery"></a>ギャラリーからの Foodee の追加

Azure AD を使用して自動ユーザー プロビジョニング用に Foodee を構成するには、Azure AD アプリケーション ギャラリーから Foodee をマネージド SaaS アプリケーションの一覧に追加する必要があります。

Azure AD アプリケーション ギャラリーから Foodee を追加するには、次の操作を行います。

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory コマンド](common/select-azuread.png)

1. **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。

    ![[エンタープライズ アプリケーション] ウィンドウ](common/enterprise-applications.png)

1. 新しいアプリケーションを追加するには、ウィンドウの上部の **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

1. 検索ボックスに「**Foodee**」と入力し、結果パネルで **[Foodee]** を選択してから、 **[追加]** ボタンを選択してアプリケーションを追加します。

    ![結果一覧の Foodee](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>Foodee への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Foodee のユーザーまたはグループを作成、更新、無効化します。

> [!TIP]
> Foodee では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Foodee シングル サインオンのチュートリアル](Foodee-tutorial.md)に関するページの手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

Azure AD で Foodee の自動ユーザー プロビジョニングを構成するには、次の操作を行います。

1. [Azure portal](https://portal.azure.com) で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。

    ![[エンタープライズ アプリケーション] ウィンドウ](common/enterprise-applications.png)

1. **アプリケーション** 一覧で、 **[Foodee]** を選択します。

    ![アプリケーションの一覧の Foodee のリンク](common/all-applications.png)

1. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

1. **[プロビジョニング モード]** ドロップダウン リストで、 **[自動]** を選択します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

1. **[管理者資格情報]** で、次の操作を行います。

   a. **[テナント URL]** ボックスに、前に取得した **https:\//concierge.food.ee/scim/v2** 値を入力します。

   b. **[シークレット トークン]** ボックスに、前に取得した **API トークン** 値を入力します。
   
   c. Azure AD から Foodee へ接続できることを確認するために、 **[テスト接続]** を選択します。 接続できない場合は、使用中の Foodee アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![[テスト接続] リンク](common/provisioning-testconnection-tenanturltoken.png)

1. **[通知用メール]** ボックスに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![[通知用メール] テキスト ボックス](common/provisioning-notification-email.png)

1. **[保存]** を選択します。

1. **[マッピング]** の下で、 **[Synchronize Azure Active Directory Users to Foodee]\(Azure Active Directory ユーザーを Foodee に同期する\)** を選択します。

    :::image type="content" source="media/Foodee-provisioning-tutorial/usermapping.png" alt-text="[マッピング] セクションのスクリーンショット。[名前] の下で、[Synchronize Azure Active Directory Users to Foodee]\(Azure Active Directory ユーザーを Foodee に同期する\) が強調表示されています。" border="false":::

1. **[属性マッピング]** の下で、Azure AD から Foodee に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Foodee の *ユーザー アカウント* との照合に使用されます。 

    :::image type="content" source="media/Foodee-provisioning-tutorial/userattribute.png" alt-text="[属性マッピング] ページのスクリーンショット。テーブルに、Azure Active Directory と Foodee の属性および対応する優先順位が表示されています。" border="false":::

1. 変更をコミットするには、 **[保存]** を選択します。
1. **[マッピング]** の下で、 **[Synchronize Azure Active Directory Groups to Foodee]\(Azure Active Directory グループを Foodee に同期する\)** を選択します。

    :::image type="content" source="media/Foodee-provisioning-tutorial/groupmapping.png" alt-text="[マッピング] セクションのスクリーンショット。[名前] の下で、[Synchronize Azure Active Directory Groups to Foodee]\(Azure Active Directory グループを Foodee に同期する\) が強調表示されています。" border="false":::

1. **[属性マッピング]** の下で、Azure AD から Foodee に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Foodee の *グループ アカウント* との照合に使用されます。

    :::image type="content" source="media/Foodee-provisioning-tutorial/groupattribute.png" alt-text="[属性マッピング] ページのスクリーンショット。テーブルに、Azure Active Directory の属性と Foodee の属性および対応する優先順位が表示されています。" border="false":::

1. 変更をコミットするには、 **[保存]** を選択します。
1. スコープ フィルターを構成します。 方法については、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)に関するページの手順を参照してください。

1. Foodee に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![[プロビジョニング状態] スイッチ](common/provisioning-toggle-on.png)

1. **[設定]** の下の **[スコープ]** ドロップダウン リストで、Foodee にプロビジョニングするユーザーまたはグループを定義します。

    ![プロビジョニングのスコープのドロップダウン リスト](common/provisioning-scope.png)

1. プロビジョニングの準備ができたら、 **[保存]** を選択します。

    ![プロビジョニングの構成の [保存] ボタン](common/provisioning-configuration-save.png)

上記の操作によって、 **[スコープ]** ドロップダウン リストで定義したユーザーまたはグループの初期同期が開始されます。 初期同期は、後続の同期よりも実行に時間がかかります。 詳細については、「[ユーザーをプロビジョニングするにはどのくらいの時間がかかりますか。](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)」を参照してください。

**[現在の状態]** セクションを使用して進行状況を監視し、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。 このレポートには、Azure AD プロビジョニング サービスによって Foodee で実行されたすべてのアクションが記述されます。 詳細については、「[ユーザー プロビジョニングの状態を確認する](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)」を参照してください。 Azure AD プロビジョニング ログを読むには、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
