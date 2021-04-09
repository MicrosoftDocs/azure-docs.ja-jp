---
title: チュートリアル:Meta Networks Connector を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Meta Networks Connector に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: b6a8f192cd26639431cc9fcb6b43e1bc5e8e2843
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96353631"
---
# <a name="tutorial-configure-meta-networks-connector-for-automatic-user-provisioning"></a>チュートリアル:Meta Networks Connector を構成して自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure AD が自動的にユーザーまたはグループを Meta Networks Connector にプロビジョニングまたはプロビジョニング解除するように構成するために、Meta Networks Connector と Azure Active Directory (Azure AD) で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [Meta Networks Connector テナント](https://www.metanetworks.com/)
* 管理者のアクセス許可を持つ Meta Networks Connector のユーザー アカウント。

## <a name="assigning-users-to-meta-networks-connector"></a>Meta Networks Connector へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Meta Networks Connector へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Meta Networks Connector に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-meta-networks-connector"></a>ユーザーを Meta Networks Connector に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを Meta Networks Connector に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Meta Networks Connector にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="setup-meta-networks-connector-for-provisioning"></a>プロビジョニングに対応させるための Meta Networks Connector のセットアップ

1. ご自身の組織名を使用して [Meta Networks Connector 管理コンソール](https://login.metanetworks.com/login/)にサインインします。 **[認証] > [API キー]** に移動します。

    ![Meta Networks Connector 管理コンソール](media/meta-networks-connector-provisioning-tutorial/apikey.png)

2.  画面の右上にあるプラス記号をクリックして、新しい **API キー** を作成します。

    ![Meta Networks Connector のプラス記号アイコン](media/meta-networks-connector-provisioning-tutorial/plusicon.png)

3.  **[API キー名]** と **[API キーの説明]** を設定します。

    :::image type="content" source="media/meta-networks-connector-provisioning-tutorial/keyname.png" alt-text="Meta Networks Connector 管理コンソールのスクリーンショット。Azure AD と API キーの [API キー名] 値と [API キーの説明] 値が強調表示されています。" border="false":::

4.  **[グループ]** と **[ユーザー]** の **[書き込み]** をオンにします。

    ![Meta Networks Connector の権限](media/meta-networks-connector-provisioning-tutorial/privileges.png)

5.  **[追加]** をクリックします。 **[シークレット]** をコピーして保存します (それを表示できるのはこれが唯一のタイミングであるため)。 この値を、Azure portal で Meta Networks Connector アプリケーションの [プロビジョニング] タブにある [シークレット トークン] フィールドに入力します。

    :::image type="content" source="media/meta-networks-connector-provisioning-tutorial/token.png" alt-text="API キーが追加されたことをユーザーに伝えるウィンドウのスクリーンショット。[シークレット] ボックスには解読できない値が含まれており、強調表示されています。" border="false":::

6.  **[管理] > [設定] > [IdP] > [新規作成]** に移動して IdP を追加します。

    ![Meta Networks Connector の IdP の追加](media/meta-networks-connector-provisioning-tutorial/newidp.png)

7.  **[IdP Configuration]\(IdP 構成\)** ページで、IdP 構成の **名前** を指定し、**アイコン** を選択できます。

    ![Meta Networks Connector の IdP 名](media/meta-networks-connector-provisioning-tutorial/idpname.png)

    ![Meta Networks Connector の IdP アイコン](media/meta-networks-connector-provisioning-tutorial/icon.png)

8.  **[Configure SCIM]\(SCIM の構成\)** で、前述のステップで作成した API キー名を選択します。 **[Save]** をクリックします。

    ![Meta Networks Connector の SCIM の構成](media/meta-networks-connector-provisioning-tutorial/configure.png)

9.  **[管理] > [設定] > [IdP] タブ** に移動します。前述のステップで作成した IdP 構成の名前をクリックして、**IdP ID** を表示します。 この **ID** は、Azure portal で Meta Networks Connector アプリケーションの [プロビジョニング] タブにある **[テナント URL]** フィールドに値を入力するときに、**テナント URL** の末尾に追加されます。

    ![Meta Networks Connector の IdP ID](media/meta-networks-connector-provisioning-tutorial/idpid.png)

## <a name="add-meta-networks-connector-from-the-gallery"></a>ギャラリーからの Meta Networks Connector の追加

Azure AD を使用して自動的にユーザー プロビジョニングを行うように Meta Networks Connector を構成する前に、Azure AD アプリケーション ギャラリーからご利用のマネージド SaaS アプリケーションの一覧に Meta Networks Connector を追加する必要があります。

**Azure AD アプリケーション ギャラリーから Meta Networks Connector を追加するには、次の手順を行います。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Meta Networks Connector**」と入力して、結果パネルから **Meta Networks Connector** を選択し、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の Meta Networks Connector](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-meta-networks-connector"></a>Meta Networks Connector への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Meta Networks Connector のユーザーやグループを作成、更新、無効化する手順について説明します。

> [!TIP]
> Meta Networks Connector では、SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Meta Networks Connector シングル サインオンのチュートリアル](./metanetworksconnector-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-meta-networks-connector-in-azure-ad"></a>Azure AD で Meta Networks Connector の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Meta Networks Connector]** を選択します。

    ![アプリケーションの一覧の Meta Networks Connector リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://api.metanetworks.com/v1/scim/<IdP ID>`」と入力します。 **[シークレット トークン]** に先ほど取得した **SCIM 認証トークン** の値を入力します。 **[テスト接続]** をクリックして、Azure AD から Meta Networks Connector に接続できることを確認します。 接続できない場合は、使用中の Meta Networks Connector アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Meta Networks Connector]\(Azure Active Directory ユーザーを Meta Networks Connector に同期する\)** を選択します。

    ![Meta Networks Connector のユーザー マッピング](media/meta-networks-connector-provisioning-tutorial/usermappings.png)

9. **[属性マッピング]** セクションで、Azure AD から Meta Networks Connector に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Meta Networks Connector のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Meta Networks Connector のユーザー属性](media/meta-networks-connector-provisioning-tutorial/userattributes.png)

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to Meta Networks Connector]\(Azure Active Directory グループを Meta Networks Connector に同期する\)** を選択します。

    ![Meta Networks Connector のグループ マッピング](media/meta-networks-connector-provisioning-tutorial/groupmappings.png)

11. **[属性マッピング]** セクションで、Azure AD から Meta Networks Connector に同期されるグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新操作で Meta Networks Connector のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Meta Networks Connector のグループ属性](media/meta-networks-connector-provisioning-tutorial/groupattributes.png)

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Meta Networks Connector に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Meta Networks Connector にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Meta Networks Connector に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)