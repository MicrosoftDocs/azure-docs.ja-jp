---
title: チュートリアル:Dialpad を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Dialpad に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
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
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 32e634bc089417aaa8080b30a5f77f663a3d8b33
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611774"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>チュートリアル:Dialpad を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure Active Directory (Azure AD) が、ユーザー、グループ、またはその両方を Dialpad に自動的にプロビジョニングおよびプロビジョニング解除するよう構成するために、Dialpad と Azure AD で実行する手順を示すことです。

> [!NOTE]
>  このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。

> このコネクタは、現在プレビューの段階です。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント。
* [Dialpad テナント](https://www.dialpad.com/pricing/)。
* 管理者アクセス許可がある Dialpad のユーザー アカウント。

## <a name="assign-users-to-dialpad"></a>ユーザーを Dialpad に割り当てる
Azure Active Directory では、選択されたアプリへのアクセス権を受け取る必要があるユーザーを決定するために、割り当てという概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成し、有効にする前に、Dialpad へのアクセスが必要な Azure AD のユーザー、グループ、またはその両方を特定する必要があります。 特定した後、次の手順に従い、これらのユーザー、グループ、またはその両方を Dialpad に割り当てることができます。
 
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>ユーザーを Dialpad に割り当てる際の重要なヒント

 * 1 名の Azure AD ユーザーを Dialpad に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Dialpad にユーザーを割り当てるときは、割り当てダイアログで、有効なアプリケーション固有ロール (使用可能な場合) を選択する必要があります。 既定のアクセス ロールのユーザーは、プロビジョニングから除外されます。

## <a name="setup-dialpad-for-provisioning"></a>プロビジョニングのために Dialpad を設定する

Azure AD での自動ユーザー プロビジョニング用に Dialpad を構成する前に、Dialpad からプロビジョニング情報を取得する必要があります。

1. [Dialpad 管理コンソール](https://dialpadbeta.com/login)にサインインし、 **[Admin settings]\(管理者設定\)** を選択します。 ドロップダウンから **[My Company]\(自分の会社\)** が選択されていることを確認します。 **[Authentication]\(認証\) > [API Keys]\(API キー\)** に移動します。

    ![Dialpad で SCIM を追加する](media/dialpad-provisioning-tutorial/dialpad01.png)

2. **[Add a key]\(キーの追加\)** をクリックし、シークレット トークンのプロパティを構成することで、新しいキーを生成します。

    ![Dialpad で SCIM を追加する](media/dialpad-provisioning-tutorial/dialpad02.png)

    ![Dialpad で SCIM を追加する](media/dialpad-provisioning-tutorial/dialpad03.png)

3. 直近で作成した API キーの **[Click to show value]\(クリックして値を表示\)** ボタンをクリックし、表示された値をコピーします。 この値を、Azure portal の Dialpad アプリケーションの [プロビジョニング] タブにある **[シークレット トークン]** フィールドに入力します。 

    ![Dialpad でトークンを作成する](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>ギャラリーから Dialpad を追加する

Azure AD を使用した自動ユーザー プロビジョニング用に Dialpad を構成するには、Azure AD アプリケーション ギャラリーから管理対象の SaaS アプリケーションの一覧に Dialpad を追加する必要があります。

**Azure AD アプリケーション ギャラリーから Dialpad を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Dialpad**」と入力し、結果パネルで **[Dialpad]** を選択します。
    ![結果一覧の Dialpad](common/search-new-app.png)

5. 個別のブラウザーで、以下で強調表示されている **URL** に移動します。 

    ![Dialpad で SCIM を追加する](media/dialpad-provisioning-tutorial/dialpad05.png)

6. 右上隅で、 **[Log In]\(ログイン)\ > [Use Dialpad online]\(Dialpad オンラインを使用する\)** を選択します。

    ![Dialpad で SCIM を追加する](media/dialpad-provisioning-tutorial/dialpad06.png)

7. Dialpad は OpenIDConnect アプリであるため、Microsoft の職場アカウントを使用して Dialpad にログインすることを選択します。

    ![Dialpad で SCIM を追加する](media/dialpad-provisioning-tutorial/loginpage.png)

8. 認証に成功した後、同意ページの同意プロンプトを受け入れます。 その後、ご使用のテナントにアプリケーションが自動的に追加され、Dialpad アカウントにリダイレクトされます。

    ![Dialpad で SCIM を追加する](media/dialpad-provisioning-tutorial/redirect.png)

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Dialpad への自動ユーザー プロビジョニングを構成する

このセクションでは、Azure AD でのユーザー、グループ、またはその両方の割り当てに基づいて、Dialpad でユーザー、グループ、またはその両方が作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Azure AD で Dialpad の自動ユーザー プロビジョニングを構成するには:

1. [Azure Portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Dialpad]** を選択します。

    ![アプリケーションの一覧の Dialpad のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://dialpad.com/scim`」と入力します。 前の手順で Dialpad から取得して保存した値を **[シークレット トークン]** に入力します。 **[接続テスト]** をクリックして、Azure AD から Dialpad に接続できることを確認します。 接続できない場合は、使用中の Dialpad アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[Save]** をクリックします。

8. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Users to Dialpad]\(Azure Active Directory ユーザーを Dialpad に同期する\)** を選択します。

    ![Dialpad ユーザーのマッピング](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. **[属性マッピング]** セクションで、Azure AD から Dialpad に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Dialpad のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Dialpad ユーザーの属性](media/dialpad-provisioning-tutorial/dialpad07.png)

10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

11. Dialpad に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

12. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Dialpad にプロビジョニングするユーザー、グループ、またはその両方を定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

13. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Dialpad に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み方について詳しくは、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。
##  <a name="connector-limitations"></a>コネクタの制限事項
* Dialpad では、グループ名の変更は現時点ではサポートされていません。 したがって、Azure AD でグループの **displayName** を変更しても、Dialpad ではその名前は更新されず、変更は反映されません。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)
