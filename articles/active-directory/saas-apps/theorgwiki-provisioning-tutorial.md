---
title: チュートリアル:TheOrgWiki を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを TheOrgWiki に自動的にプロビジョニングまたはプロビジョニング解除する方法を説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 8238b9902aafcabc079c551a0eabc7170042209a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94357625"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>チュートリアル:TheOrgWiki を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure Active Directory (Azure AD) が、ユーザー、グループ、またはその両方を TheOrgWiki に対して自動的にプロビジョニングおよびプロビジョニング解除するよう構成するために、TheOrgWiki と Azure AD で実行する手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント。
* [OrgWiki テナント](https://www.theorgwiki.com/welcome/)。
* Admin アクセス許可がある TheOrgWiki のユーザー アカウント。

## <a name="assign-users-to-theorgwiki"></a>ユーザーを TheOrgWiki に割り当てる

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、TheOrgWiki へのアクセスが必要な Azure AD 内のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを TheOrgWiki に割り当てることができます。

* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>ユーザーを TheOrgWiki に割り当てる際の重要なヒント

* 1 名の Azure AD ユーザーを TheOrgWiki に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* TheOrgWiki にユーザーを割り当てるときは、割り当てダイアログで、有効なアプリケーション固有ロール (使用可能な場合) を選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-theorgwiki-for-provisioning"></a>プロビジョニングのために TheOrgWiki をセットアップする

Azure AD での自動ユーザー プロビジョニング用に TheOrgWiki を構成する前に、TheOrgWiki で SCIM プロビジョニングを有効にする必要があります。

1. [TheOrgWiki 管理コンソール](https://www.theorgwiki.com/login/)にサインインします。 **[管理コンソール]** をクリックします。

    ![The Org Wiki のスクリーンショット。ユーザーのアバターと [管理コンソール] が選択されています。](media/theorgwiki-provisioning-tutorial/login.png)

2. 管理コンソールで、 **[設定] タブ** をクリックします。 

    ![The Org Wiki の [管理コンソール] のスクリーンショット。[設定] タブが選択されています。](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. **[サービス アカウント]** に移動します。

    ![TheOrgWiki の [管理コンソール] の [サービス アカウント] ページのスクリーンショット。](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. **[+サービス アカウント]** をクリックします。 **[Service Account Type]\(サービス アカウントの種類\)** で、 **[Token Based]\(トークン ベース\)** を選択します。 **[保存]** をクリックします。

    ![[新しいサービス アカウント] ダイアログ ボックスのスクリーンショット。[Service Account Type]\(サービス アカウントの種類\)、[Token Based]\(トークン ベース\)、[保存] が選択されています。](media/theorgwiki-provisioning-tutorial/auth.png)

5.  **[Active Tokens]\(アクティブ トークン\)** をコピーします。 この値を、Azure portal 上で TheOrgWiki アプリケーションの [プロビジョニング] タブ内の [シークレット トークン] フィールドに入力します。
     
    ![SCIM プロビジョニング ダイアログ ボックスのトークン管理のスクリーンショット。](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>ギャラリーから TheOrgWiki を追加する

Azure AD で自動ユーザー プロビジョニング用に TheOrgWiki を構成するには、Azure AD アプリケーション ギャラリーから TheOrgWiki をマネージド SaaS アプリケーションの一覧に追加する必要があります。

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**TheOrgWiki**」と入力し、結果パネルで **[TheOrgWiki]** を選択します。 

    ![結果一覧の TheOrgWiki](common/search-new-app.png)

5. **[Sign-up for TheOrgWiki]\(TheOrgWiki にサインアップ\)** ボタンを選択します。TheOrgWiki のログイン ページにリダイレクトされます。 

    ![The Org Wiki ログイン ページのスクリーンショット。URL が選択されています](media/theorgwiki-provisioning-tutorial/image00.png)

6.  右上隅にある **[ログイン]** を選択します。

    ![ログイン ページの右上隅のスクリーンショット。[ログイン] オプションが選択されています。](media/theorgwiki-provisioning-tutorial/image02.png)

7. TheOrgWiki は OpenIDConnect アプリであるため、Microsoft の職場アカウントを使用して TheOrgWiki にログインすることを選択します。

    ![The Org Wiki のサインイン ページのスクリーンショット。[Microsoft アカウントでサインイン] オプションが選択されています。](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. 認証に成功すると、ご使用のテナントにアプリケーションが自動的に追加され、TheOrgWiki アカウントにリダイレクトされます。

    ![OrgWiki: SCIM の追加](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>TheOrgWiki への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて TheOrgWiki のユーザーやグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>Azure AD 内で TheOrgWiki の自動ユーザー プロビジョニングを構成するには、次の操作を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[TheOrgWiki]** を選択します。

    ![アプリケーションの一覧の OrgWiki のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/`」と入力します。 

    例: `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> **[Subdomain Value]\(サブドメイン値\)** は、TheOrgWiki の最初のサインアップ プロセスの間にしか設定できません。
 
6. **[シークレット トークン]** フィールドには、以前に TheOrgWiki から取得したトークンの値を入力します。 **[テスト接続]** をクリックして、Azure AD から TheOrgWiki への接続を確保します。 接続できない場合は、使用中の TheOrgWiki アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

7. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

8. **[保存]** をクリックします。

9. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Users to TheOrgWiki]\(Azure Active Directory ユーザーを TheOrgWiki に同期する\)** を選択します。

    ![TheOrgWiki のユーザー マッピング](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. **[属性マッピング]** セクションで、Azure AD から TheOrgWiki に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で TheOrgWiki のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![TheOrgWiki のユーザー属性](media/theorgwiki-provisioning-tutorial/userattribute.png).

11. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

12. TheOrgWiki に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

13. **[設定]** セクションの **[スコープ]** で目的の値を選択して、OrgWiki にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

14. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は、後続の同期よりも実行に時間がかかります。 ユーザーやグループのプロビジョニングにかかる時間の詳細については、「[ユーザーをプロビジョニングするにはどのくらいの時間がかかりますか](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)」を参照してください。

**[現在の状態]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって TheOrgWiki に対して実行されたすべてのアクションが記載されています。 詳細については、「[ユーザー プロビジョニングの状態を確認する](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)」を参照してください。 Azure AD プロビジョニング ログを読むには、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)。
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)