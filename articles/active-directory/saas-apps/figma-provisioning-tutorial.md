---
title: チュートリアル:Figma を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Figma に対してユーザー アカウントの自動的なプロビジョニングとプロビジョニング解除を実行するように Azure Active Directory を構成する方法について説明します。
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
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 38ebba8803e584e9b5d1179281fcff3a3f98d5a4
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67848161"
---
# <a name="tutorial-configure-figma-for-automatic-user-provisioning"></a>チュートリアル:Figma を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、ユーザーやグループを自動的に Figma に対してプロビジョニングまたはプロビジョニング解除するように Azure AD で構成するために、Figma と Azure Active Directory (Azure AD) で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント。
* [Figma テナント](https://www.figma.com/pricing/)。
* 管理者アクセス許可がある Figma のユーザー アカウント。

## <a name="assign-users-to-figma"></a>ユーザーをアプリに割り当てる
Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Figma へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Figma に割り当てることができます。
 
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)
## <a name="important-tips-for-assigning-users-to-figma"></a>ユーザーを Figma に割り当てる際の重要なヒント

 * 単一の Azure AD ユーザーを Figma に割り当て、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Figma にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 既定のアクセス ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-figma-for-provisioning"></a>Figma をプロビジョニング用に設定する

Azure AD での自動ユーザー プロビジョニング用に Figma からプロビジョニング情報を取得する必要があります。

1. [Figma 管理コンソール](https://www.Figma.com/)にサインインします。 お使いのテナントの横にある歯車アイコンをクリックします。

    ![FigmaFigma-employee-provision](media/Figma-provisioning-tutorial/image0.png)

2. **[General]\(全般\) > [Update Log in Settings]\(ログイン設定の更新\)** に移動します。

    ![FigmaFigma-employee-provision](media/Figma-provisioning-tutorial/figma03.png)

3. **テナント ID** をコピーします。 この値を使用して、Azure portal で Figma アプリケーションの [プロビジョニング] タブ内の **[テナント URL]** フィールドに入力される SCIM エンドポイント URL を構築します。

    ![Figma でのトークンの作成](media/Figma-provisioning-tutorial/figma-tenantid.png)

4. 下にスクロールし、 **[Generate API Token]\(API トークンの生成\)** をクリックします。

    ![Figma でのトークンの作成](media/Figma-provisioning-tutorial/token.png)

5. **[API Token]\(API トークン\)** 値をコピーします。 この値を、Azure portal で Figma アプリケーションの [プロビジョニング] タブ内の **[シークレット トークン]** フィールドに入力します。 

    ![Figma でのトークンの作成](media/Figma-provisioning-tutorial/figma04.png)

## <a name="add-figma-from-the-gallery"></a>ギャラリーから Figma を追加する

Azure AD で自動ユーザー プロビジョニング用に Figma を構成するには、Azure AD アプリケーション ギャラリーから Figma をマネージド SaaS アプリケーションの一覧に追加する必要があります。

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Figma**」と入力し、結果ウィンドウで **[Figma]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の Figma](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-figma"></a>Figma への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて Figma のユーザーやグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> Figma では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Figma シングル サインオンのチュートリアル](figma-tutorial.md)に関するページで説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-figma--in-azure-ad"></a>Azure AD で Figma の自動ユーザー プロビジョニングを構成するには、次の操作を実行します。

1. [Azure Portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Figma]** を選択します。

    ![アプリケーションの一覧の Figma のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. **[管理者の資格情報]** セクションで、`https://www.figma.com/scim/v2/<TenantID>` を **[テナント URL]** に入力します。**TenantID** は先ほど Figma から取得した値です。 **[API Token]\(API トークン\)** 値を **[シークレット トークン]** に入力します。 **[Test Connection]\(テスト接続\)** をクリックして、Azure AD から Figma に接続できることを確認します。 接続できない場合は、使用中の Figma アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

8. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

9. **[Save]** をクリックします。

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Mappings]\(Azure Active Directory ユーザーを Mappings に同期する\)** を選択します。

    ![Figma のユーザー マッピング](media/Figma-provisioning-tutorial/figma05.png)

11. **[属性マッピング]** セクションで、Azure AD から Figma に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Figma のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Figma のユーザー属性](media/Figma-provisioning-tutorial/figma06.png)

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Figma に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Figma にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Figma に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)