---
title: チュートリアル:Harness を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Harness に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 13ae960f5d259314f00f8f09b2999a36c0919bc5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94353716"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>チュートリアル:Harness を構成し、自動ユーザー プロビジョニングに対応させる

この記事では、Azure Active Directory (Azure AD) を構成して、ユーザーまたはグループを Harness に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。

> [!NOTE]
> この記事では、Azure AD ユーザー プロビジョニング サービス上に構築されるコネクタについて説明します。 このサービスについての重要な情報とよく寄せられる質問への回答については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> このコネクタは、現在プレビューの段階です。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

この記事で説明するシナリオでは、次の前提条件があることを前提としています。

* Azure AD テナント
* [Harness テナント](https://harness.io/pricing/)
* *管理者* アクセス許可がある Harness のユーザー アカウント

## <a name="assign-users-to-harness"></a>ユーザーを Harness に割り当てる

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーまたはグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Harness にアクセスする必要がある Azure AD のユーザーまたはグループを決定しておく必要があります。 その後、「[エンタープライズ アプリにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)」の手順に従って、これらのユーザーまたはグループを Harness に割り当てることができます。

## <a name="important-tips-for-assigning-users-to-harness"></a>ユーザーを Harness に割り当てるときの重要なヒント

* 単一の Azure AD ユーザーを Harness に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後で追加のユーザーまたはグループを割り当てることができます。

* Harness にユーザーを割り当てるときに、有効なアプリケーション固有ロール (ある場合) を **[割り当て]** ダイアログ ボックスで選択する必要があります。 *既定のアクセス* ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-harness-for-provisioning"></a>プロビジョニングのための Harness の設定

1. [Harness 管理コンソール](https://app.harness.io/#/login)にサインインし、 **[Continuous Security]\(継続的セキュリティ\)**  >  **[Access Management]\(アクセス管理\)** に移動します。

    ![Harness 管理コンソール](media/harness-provisioning-tutorial/admin.png)

1. **[API Keys]\(API キー\)** を選択します。

    ![Harness の [API Keys]\(API キー\) リンク](media/harness-provisioning-tutorial/apikeys.png)

1. **[Add API Key]\(API キーの追加\)** を選択します。 

    ![Harness の [Add API Key]\(API キーの追加\) リンク](media/harness-provisioning-tutorial/addkey.png)

1. **[Add Api Key]\(API キーの追加\)** ウィンドウで、次の操作を行います。

    ![Harness の [Add Api Key]\(API キーの追加\) ウィンドウ](media/harness-provisioning-tutorial/title.png)
   
   a. **[Name]\(名前\)** ボックスにキーの名前を入力します。  
   b. **[Permissions Inherited from]\(アクセス許可の継承元\)** ドロップダウン リストで、オプションを選択します。 
   
1. **[Submit]\(送信\)** をクリックします。

1. このチュートリアルで後から使用するので、**キー** をコピーしておきます。

    ![Harness でのトークンの作成](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>ギャラリーからの Harness の追加

Azure AD での自動ユーザー プロビジョニング用に Harness を構成する前に、Azure AD アプリケーション ギャラリーから Harness をマネージド SaaS アプリケーションの一覧に追加する必要があります。

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

1. **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。

    ![[すべてのアプリケーション] リンク](common/enterprise-applications.png)

1. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

1. 検索ボックスに「**Harness**」と入力し、結果一覧から **[Harness]** を選択してから、 **[追加]** ボタンを選択してアプリケーションを追加します。

    ![結果一覧の Harness](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-harness"></a>Harness への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて Harness のユーザーやグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> Harness では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Harness シングル サインオンのチュートリアル](./harness-tutorial.md)の手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

> [!NOTE]
> Harness の SCIM エンドポイントの詳細については、Harness の [API キー](https://docs.harness.io/article/smloyragsm-api-keys)の記事ご覧ください。

Azure AD で Harness の自動ユーザー プロビジョニングを構成するには、以下を行います。

1. [Azure portal](https://portal.azure.com) で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

1. アプリケーションの一覧で **[Harness]** を選択します。

    ![アプリケーションの一覧の Harness のリンク](common/all-applications.png)

1. **[プロビジョニング]** を選択します。

    ![[プロビジョニング] ボタン](common/provisioning.png)

1. **[プロビジョニング モード]** ドロップダウン リストで、 **[自動]** を選択します。

    ![[プロビジョニング モード] ドロップダウン リスト](common/provisioning-automatic.png)

1. **[管理者資格情報]** で、次の操作を行います。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)
 
   a. **[テナント URL]** ボックスに、「 **`https://app.harness.io/gateway/api/scim/account/<your_harness_account_ID>`** 」と入力します。 Harness にログインしているとき、お使いのブラウザーで URL から Harness アカウント ID を取得できます。
   b. **[シークレット トークン]** ボックスに、「プロビジョニングのためのハーネスの設定」セクションの手順 6 で保存した SCIM 認証トークンの値を入力します。  
   c. Azure AD から Harness に接続できることを確認するために、**[テスト接続]** を選択します。 接続できない場合は、使用中の Harness アカウントに "*管理者*" アクセス許可があることを確認してから、もう一度試します。

1. **[通知用メール]** ボックスに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力し、**[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![[通知用メール] ボックス](common/provisioning-notification-email.png)

1. **[保存]** を選択します。

1. **[マッピング]** の下で、**[Synchronize Azure Active Directory Users to Harness]\(Azure Active Directory ユーザーを Harness に同期する\)** を選択します。

    ![Harness の [Synchronize Azure Active Directory Users to Harness]\(Azure Active Directory ユーザーを Harness に同期する\) リンク](media/harness-provisioning-tutorial/usermappings.png)

1. **[属性マッピング]** の下で、Azure AD から Harness に同期されるユーザーの属性を確認します。 *[Matching]\(照合\)* として選択されている属性を使用して、更新操作で Harness のユーザー アカウントとの照合が行われます。 すべての変更をコミットするには、 **[保存]** を選択します。

    ![Harness ユーザーの [属性マッピング] ウィンドウ](media/harness-provisioning-tutorial/userattributes.png)

1. **[マッピング]** の下で、**[Synchronize Azure Active Directory Groups to Harness]\(Azure Active Directory グループを Harness に同期する\)** を選択します。

    ![Harness の [Synchronize Azure Active Directory Groups to Harness]\(Azure Active Directory グループを Harness に同期する\) リンク](media/harness-provisioning-tutorial/groupmappings.png)

1. **[属性マッピング]** の下で、Azure AD から Harness に同期されるグループの属性を確認します。 *[Matching]\(照合\)* プロパティとして選択されている属性は、更新操作で Harness のグループとの照合に使用されます。 すべての変更をコミットするには、 **[保存]** を選択します。

    ![Harness のグループの [属性マッピング] ウィンドウ](media/harness-provisioning-tutorial/groupattributes.png)

1. スコープ フィルターを構成するには、「[スコープ フィルターを使用した属性ベースのアプリケーション プロビジョニング](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)」を参照します。

1. **[設定]** セクションで、Harness に対する Azure AD プロビジョニング サービスを有効にするために、**[プロビジョニング状態]** スイッチを **[オン]** に切り替えます。

    ![[オン] に切り替えられた [プロビジョニング状態] スイッチ](common/provisioning-toggle-on.png)

1. **[設定]** の下の **[スコープ]** ドロップダウン リストで、Harness にプロビジョニングするユーザーまたはグループの同期方法を選択します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

1. プロビジョニングの準備ができたら、 **[保存]** を選択します。

    ![プロビジョニングの [保存] ボタン](common/provisioning-configuration-save.png)

この操作により、プロビジョニングするユーザーまたはグループの初期同期が開始されます。 初期同期は、以降の同期よりも実行に時間がかかります。 同期は、Azure AD プロビジョニング サービスが実行されている限り、約 40 分ごとに発生します。 進行状況を監視するには、**[同期の詳細]** セクションに移動します。 また、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Harness に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他の技術情報

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)