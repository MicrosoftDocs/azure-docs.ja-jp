---
title: チュートリアル:Azure Active Directory を使用した自動ユーザー プロビジョニング用に Federated Directory を構成する | Microsoft Docs
description: Federated Directory に対するユーザー アカウントのプロビジョニングとプロビジョニング解除を自動実行するように Azure Active Directory を構成する方法について説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 8ca7654d930247f70d85cbc20fbbeb961223f05f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95998364"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>チュートリアル:自動ユーザー プロビジョニング用に Federated Directory を構成する

このチュートリアルの目的は、Federated Directory に対するユーザーやグループのプロビジョニングまたはプロビジョニング解除を自動実行するように Azure Active Directory (Azure AD) を構成するために、Federated Directory と Azure AD で実行する手順を示すことです。

> [!NOTE]
>  このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント。
* [Federated Directory](https://www.federated.directory/pricing)。
* 管理者アクセス許可を持つ Federated Directory 内のユーザー アカウント。

## <a name="assign-users-to-federated-directory"></a>ユーザーを Federated Directory に割り当てる
Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Federated Directory へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Federated Directory に割り当てることができます。

 * [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>ユーザーを Federated Directory に割り当てる際の重要なヒント
 * 単一の Azure AD ユーザーを Federated Directory に割り当て、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Federated Directory にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 既定のアクセス ロールのユーザーは、プロビジョニングから除外されます。
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>プロビジョニング用に Federated Directory を設定する

Azure AD を使用した自動ユーザー プロビジョニング用に Federated Directory を構成する前に、Federated Directory で SCIM プロビジョニングを有効にする必要があります。

1. [Federated Directory Admin Console](https://federated.directory/of) にサインインします

    :::image type="content" source="media/federated-directory-provisioning-tutorial/companyname.png" alt-text="会社名を入力するためのフィールドを示す Federated Directory 管理コンソールのスクリーンショット。サインイン ボタンも表示されています。" border="false":::

2. **[Directories]\(ディレクトリ\) > [User directories]\(ユーザー ディレクトリ\)** に移動して、テナントを選択します。 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/ad-user-directories.png" alt-text="[Directories]\(ディレクトリ\) と [Federated Directory Azure AD Test]\(Federated Directory Azure AD テスト\) が強調表示されている、Federated Directory 管理コンソールのスクリーンショット。" border="false":::

3.  永続的なベアラー トークンを生成するには、 **[Directory Keys]\(ディレクトリ キー\) > [Create New Key]\(新しいキーの作成\)** に移動します。 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated01.png" alt-text="Federated Directory 管理コンソールの [Directory Keys]\(ディレクトリ キー\) ページのスクリーンショット。[Create New Key]\(新しいキーの作成\) ボタンが強調表示されます。" border="false":::

4. ディレクトリ キーを作成します。 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated02.png" alt-text="[名前] と [説明] フィールドと [Create key]\(キーの作成\) ボタンがある Federated Directory 管理コンソールの [Create directory key]\(ディレクトリ キーの作成\) ページのスクリーンショット。" border="false":::
    

5. **[アクセス トークン]** 値をコピーします。 この値を、Azure portal で Federated Directory アプリケーションの [プロビジョニング] タブ内の **[シークレット トークン]** フィールドに入力します。 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated03.png" alt-text="Federated Directory 管理コンソールのページのスクリーンショット。アクセス トークンのプレースホルダーとキー名、説明、発行者が表示されています。" border="false":::
    
## <a name="add-federated-directory-from-the-gallery"></a>ギャラリーから Federated Directory を追加する

Azure AD で自動ユーザー プロビジョニング用に Federated Directory を構成するには、Azure AD アプリケーション ギャラリーから Federated Directory をマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Federated Directory を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Federated Directory**」と入力し、結果パネルで **[Federated Directory]** を選択します。

    ![結果リストの Federated Directory](common/search-new-app.png)

5. 個別のブラウザーで、以下で強調表示されている **URL** に移動します。 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/loginpage1.png" alt-text="Federated Directory に関する情報を表示する Azure portal のページのスクリーンショット。URL 値が強調表示されています。" border="false":::

6. **[LOG IN]\(ログイン\)** をクリックします。

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated04.png" alt-text="Federated Directory サイトのメイン メニューのスクリーンショット。[Log in]\(ログイン\) ボタンが強調表示されています。" border="false":::

7.  Federated Directory は OpenIDConnect アプリなので、Microsoft 職場アカウントを使用して Federated Directory にログインすることを選択します。
    
    :::image type="content" source="media/federated-directory-provisioning-tutorial/loginpage3.png" alt-text="Federated Directory サイトの SCIM AD テスト ページのスクリーンショット。[Log in with your Microsoft account]\(Microsoft アカウントでログインする\) が強調表示されています。" border="false":::
 
8. 認証に成功した後、同意ページの同意プロンプトを受け入れます。 その後、ご使用のテナントにアプリケーションが自動的に追加され、Federated Directory アカウントにリダイレクトされます。

    ![Federated Directory の SCIM の追加](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>Federated Directory への自動ユーザー プロビジョニングの構成 

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて Federated Directory のユーザーやグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>Azure AD で Federated Directory の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーション一覧で **[Federated Directory]** を選択します。

    ![アプリケーション一覧の [Federated Directory] リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの [テナントの URL] に「`https://api.federated.directory/v2/`」と入力します。 前述の手順で Federated Directory から取得して保存した値を **[シークレット トークン]** に入力します。 **[接続テスト]** をクリックして、Azure AD から Federated Directory に接続できることを確認します。 接続できない場合は、使用中の Federated Directory アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

8. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

9. **[保存]** をクリックします。

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Federated Directory]\(Azure Active Directory ユーザーを Federated Directory に同期する\)** を選択します。

    :::image type="content" source="media/federated-directory-provisioning-tutorial/user-mappings.png" alt-text="[マッピング] セクションのスクリーンショット。[名前] の下で、[Synchronize Azure Active Directory Users to Federated Directory]\(Azure Active Directory ユーザーを Federated Directory に同期する\) が強調表示されています。" border="false":::
    
    
11. **[属性マッピング]** セクションで、Azure AD から Federated Directory に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Federated Directory のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    :::image type="content" source="media/federated-directory-provisioning-tutorial/user-attributes.png" alt-text="[属性マッピング] ページのスクリーンショット。テーブルに、Azure Active Directory と Federated Directory の属性および照合状態が一覧表示されています。" border="false":::
    

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Federated Directory に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Federated Directory にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Federated Directory に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み方について詳しくは、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。
## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
