---
title: チュートリアル:RFPIO を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: ユーザー アカウントを RFPIO に対して自動的にプロビジョニングおよびプロビジョニング解除するように Azure Active Directory を構成する方法について説明します。
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
ms.openlocfilehash: ff859e7d77fd19cd006cf45a6faa737297fdb9a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96349641"
---
# <a name="tutorial-configure-rfpio-for-automatic-user-provisioning"></a>チュートリアル:RFPIO を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure Active Directory (Azure AD) が、ユーザー、グループ、またはその両方を RFPIO に対して自動的にプロビジョニングおよびプロビジョニング解除するよう構成するために、RFPIO と Azure AD で実行する手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント。
* [RFPIO テナント](https://www.rfpio.com/product/)。
* 管理者アクセス許可がある RFPIO のユーザー アカウント。

## <a name="assigning-users-to-rfpio"></a>RFPIO へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成し、有効にする前に、RFPIO へのアクセスが必要な Azure AD のユーザー、グループ、またはその両方を特定する必要があります。 特定した後、次の手順に従い、これらのユーザー、グループ、またはその両方を RFPIO に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-rfpio"></a>ユーザーを RFPIO に割り当てる際の重要なヒント

* 1 名の Azure AD ユーザーを RFPIO に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* RFPIO にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="setup-rfpio-for-provisioning"></a>プロビジョニングのために RFPIO を設定する

Azure AD での自動ユーザー プロビジョニング用に RFPIO を構成する前に、RFPIO で SCIM プロビジョニングを有効にする必要があります。

1.  RFPIO 管理コンソールにサインインします。 管理コンソールの左下にある **[Tenant]\(テナント\)** をクリックします。

    ![RFPIO 管理コンソール](media/rfpio-provisioning-tutorial/aadtest0.png)

2.  **[Organization Settings]\(組織の設定\)** をクリックします。
    
    ![RFPIO 管理者](media/rfpio-provisioning-tutorial/aadtest.png)

3.  **[USER MANAGEMENT]\(ユーザー管理\)**  >  **[SECURITY]\(セキュリティ\)**  >  **[SCIM]** に移動します。

    ![RFPIO での SCIM の追加](media/rfpio-provisioning-tutorial/scim.png)

4.  **[Auto User Provisioning]\(自動ユーザー プロビジョニング\)** がオンになっていることを確認します。 **[GENERATE SCIM API TOKEN]\(SCIM API トークンの生成\)** をクリックします。

    ![[GENERATE SCIM API TOKEN]\(SCIM API トークンの生成\) オプションが呼び出されている [SCIM] セクションのスクリーンショット。](media/rfpio-provisioning-tutorial/generate.png)

5.  **SCIM API トークン** を保存します。このトークンは、セキュリティ上の目的で再び表示されることはありません。 この値を、Azure portal で RFPIO アプリケーションの [プロビジョニング] タブ内の **[シークレット トークン]** フィールドに入力します。

    ![[送信] を選択した後に表示される警告ダイアログ ボックスが表示された [SCIM] セクションのスクリーンショット。](media/rfpio-provisioning-tutorial/auth.png)

## <a name="add-rfpio-from-the-gallery"></a>ギャラリーからの RFPIO の追加

Azure AD を使用した自動ユーザー プロビジョニング用に RFPIO を構成するには、Azure AD アプリケーション ギャラリーから管理対象の SaaS アプリケーションの一覧に RFPIO を追加する必要があります。

**Azure AD アプリケーション ギャラリーから RFPIO を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**RFPIO**」と入力し、結果パネルで **[RFPIO]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の RFPIO](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-rfpio"></a>RFPIO への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザー、グループ、またはその両方の割り当てに基づいて、RFPIO でユーザー、グループ、またはその両方が作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> RFPIO で SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[RFPIO シングル サインオンのチュートリアル](rfpio-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-rfpio-in-azure-ad"></a>Azure AD で RFPIO の自動ユーザー プロビジョニングを構成するには、次の操作を行います。

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[RFPIO]** を選択します。

    ![アプリケーションの一覧の RFPIO のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://<RFPIO tenant instance>.rfpio.com/rfpserver/scim/v2 `」と入力します。 値の例は `https://Azure-test1.rfpio.com/rfpserver/scim/v2` です。 **[シークレット トークン]** に先ほど取得した **SCIM API トークン** の値を入力します。 **[テスト接続]** をクリックして、Azure AD から RFPIO への接続を確保します。 接続できない場合は、使用中の RFPIO アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to RFPIO]\(Azure Active Directory ユーザーを RFPIO に同期する\)** を選択します。

    ![RFPIO ユーザーのマッピング](media/rfpio-provisioning-tutorial/usermapping.png)

9. **[属性マッピング]** セクションで、Azure AD から RFPIO に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で RFPIO のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![RFPIO ユーザーの属性](media/rfpio-provisioning-tutorial/userattributes.png)

10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

11. RFPIO に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

12. **[設定]** セクションの **[スコープ]** で目的の値を選択して、RFPIO にプロビジョニングするユーザー、グループ、またはこれらの両方を定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

13. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって RFPIO に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="connector-limitations"></a>コネクタの制限事項

* 現在、RFPIO ではグループのプロビジョニングはサポートされていません。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
