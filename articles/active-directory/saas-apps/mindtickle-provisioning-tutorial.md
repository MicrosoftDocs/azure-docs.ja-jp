---
title: チュートリアル:MindTickle を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを MindTickle に自動的にプロビジョニングまたはプロビジョニング解除する方法を説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: c158358b1c4fcd72d9189d7a991645cb65a4dc83
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96348128"
---
# <a name="tutorial-configure-mindtickle-for-automatic-user-provisioning"></a>チュートリアル:MindTickle を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、MindTickle に対するユーザーまたはグループのプロビジョニングまたはプロビジョニング解除を自動的に実行するように Azure Active Directory (Azure AD) を構成するために、MindTickle と Azure AD で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [MindTickle テナント](https://www.mindtickle.com/)
* Admin アクセス許可がある MindTickle のユーザー アカウント。

## <a name="assigning-users-to-mindtickle"></a>MindTickle へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、MindTickle へのアクセスが必要な Azure AD 内のユーザーやグループを決定しておく必要があります。 決定したら、次の手順に従って、これらのユーザーやグループを MindTickle に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-mindtickle"></a>ユーザーを MindTickle に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを MindTickle に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* MindTickle にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="setup-mindtickle-for-provisioning"></a>プロビジョニングのための MindTickle のセットアップ

Azure AD での自動ユーザー プロビジョニング用に MindTickle を構成する前に、MindTickle 上で SCIM プロビジョニングを有効にする必要があります。


1.  [MindTickle のサポート チーム](mailto:help@mindtickle.com)に連絡して、SCIM プロビジョニングを構成するために必要な JWT トークンを入手します。


## <a name="add-mindtickle-from-the-gallery"></a>ギャラリーから MindTickle を追加する

Azure AD で自動ユーザー プロビジョニング用に MindTickle を構成するには、Azure AD アプリケーション ギャラリーから管理対象 SaaS アプリケーションの一覧に MindTickle を追加する必要があります。

**Azure AD アプリケーション ギャラリーから MindTickle を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**MindTickle**」と入力し、結果ウィンドウで **[MindTickle]** を選択してから、 **[追加]** をクリックしてアプリケーションを追加します。

    ![結果一覧の MindTickle](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-mindtickle"></a>MindTickle への自動ユーザー プロビジョニングの構成 

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて MindTickle でのユーザーやグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> MindTickle で SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[MindTickle シングル サインオンのチュートリアル](mindtickle-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-mindtickle-in-azure-ad"></a>Azure AD で MindTickle の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[MindTickle]** を選択します。

    ![アプリケーションの一覧の MindTickle のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://admin.mindtickle.com/scim`」と入力します。 以前に [シークレット トークン] テキスト ボックスで取得した **JWT トークン** の値を入力し、MindTickle サポート チームから教えられた **JWT トークン** の値を入力します。 **[Test Connection]\(テスト接続\)** をクリックして、Azure AD から myPolicies に接続できることを確認します。 接続できない場合は、使用中の MindTickle アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to MindTickle]\(Azure Active Directory ユーザーを MindTickle に同期する\)** を選択します。

    :::image type="content" source="media/mindtickle-provisioning-tutorial/usermapping.png" alt-text="[マッピング] セクションのスクリーンショット。[名前] の下に、[Azure Active Directory ユーザーを MindTickle に同期する] が表示されています。" border="false":::

9. **[属性マッピング]** セクションで、Azure AD から MindTickle に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で MindTickle のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    :::image type="content" source="media/mindtickle-provisioning-tutorial/userattribute.png" alt-text="[属性マッピング] ページのスクリーンショット。テーブルに、Azure Active Directory 属性と MindTickle 属性、および照合の優先順位が一覧表示されています。" border="false":::

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. MindTickle に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、MindTickle にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は、後続の同期よりも実行に時間がかかります。 ユーザーやグループのプロビジョニングにかかる時間の詳細については、「[ユーザーをプロビジョニングするにはどのくらいの時間がかかりますか](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)」を参照してください。 

**[現在の状態]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって MindTickle に対して実行されたすべてのアクションが記載されています。 詳細については、「[ユーザー プロビジョニングの状態を確認する](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)」を参照してください。 Azure AD プロビジョニング ログを読むには、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
