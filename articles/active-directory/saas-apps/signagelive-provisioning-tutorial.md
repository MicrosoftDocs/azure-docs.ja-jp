---
title: チュートリアル:Azure Active Directory を使用した自動ユーザー プロビジョニングに対応するように Signagelive を構成する | Microsoft Docs
description: Signagelive に対するユーザー アカウントのプロビジョニングおよびプロビジョニング解除を自動的に行えるように Azure Active Directory を構成する方法について説明します。
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
ms.openlocfilehash: 10ad06041e8136b5661b1b1ff487cd4d3b0f5153
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358407"
---
# <a name="tutorial-configure-signagelive--for-automatic-user-provisioning"></a>チュートリアル:自動ユーザー プロビジョニングに対応するように Signagelive を構成する

このチュートリアルの目的は、Azure AD が自動的にユーザーまたはグループを Signagelive にプロビジョニングまたは Signagelive からプロビジョニング解除するように構成するために、Signagelive と Azure Active Directory (Azure AD) で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント。
* [Signagelive テナント](https://signagelive.com/pricing/)
* 管理者アクセス許可がある Signagelive のユーザー アカウント。

## <a name="assigning-users-to-signagelive"></a>Signagelive へのユーザーの割り当て   

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Signagelive へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Signagelive に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-signagelive"></a>ユーザーを Signagelive に割り当てる際の重要なヒント   

* 単一の Azure AD ユーザーを Signagelive に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Signagelive にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="setup-signagelive--for-provisioning"></a>プロビジョニングのための Signagelive の設定

Azure AD での自動ユーザー プロビジョニング用に Signagelive を構成する前に、Signagelive 上で SCIM プロビジョニングを有効にする必要があります。

  [Signagelive](mailto:development@signagelive.com) に連絡して、SCIM のプロビジョニングを構成するために必要なシークレット トークンを入手します。

## <a name="add-signagelive-from-the-gallery"></a>ギャラリーからの Signagelive の追加

Azure AD での自動ユーザー プロビジョニングのために Signagelive を構成するには、Azure AD アプリケーション ギャラリーから Signagelive をマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Signagelive を追加するには、次の手順を行います。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Signagelive**」と入力し、結果パネルで **[Signagelive]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果リストの Signagelive](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-signagelive"></a>Signagelive への自動ユーザー プロビジョニングの構成    

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて Signagelive のユーザーやグループの作成、更新、無効化を行えるように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
>  Signagelive では、[Signagelive シングル サインオンのチュートリアル](Signagelive-tutorial.md)で説明されている手順に従って、SAML ベースのシングル サインオンを有効にすることもできます。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-signagelive--in-azure-ad"></a>Azure AD で Signagelive の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Signagelive]** を選択します。

    ![アプリケーションの一覧の Signagelive リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. [管理者資格情報] セクションの **[テナント URL]** に「` https://samlapi.signagelive.com/scim/v2`」と入力します。 **[シークレット トークン]** フィールドに、エンジニアリング開発チームから提供された **ベアラー トークン** 値を入力します。 **[接続テスト]** をクリックして、Azure AD から Signagelive に接続できることを確認します。 接続できない場合は、使用中の Signagelive アカウントに管理者アクセス許可があることを確認してから、もう一度試します。
    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションの **[Azure Active Directory ユーザーを Signagelive に同期する]** を選びます。

    ![[マッピング] セクションのスクリーンショット。[Azure Active Directory ユーザーを Signagelive に同期する] オプションが選択されています。](media/signagelive-provisioning-tutorial/usermapping.png)

9. **[属性マッピング]** セクションで、Azure AD から Signagelive に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Signagelive のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![7 つのマッピングが表示されている [属性マッピング] セクションのスクリーンショット。](media/signagelive-provisioning-tutorial/userattribute.png)

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Group to Signagelive]\(Azure Active Directory グループを Signagelive に同期する\)** を選択します。

    ![[マッピング] セクションのスクリーンショット。[Synchronize Azure Active Directory Group to Signagelive]\(Azure Active Directory グループを Signagelive に同期する\) オプションが選択されています。](media/signagelive-provisioning-tutorial/groupmapping.png)

11. **[属性マッピング]** セクションで、Azure AD から Signagelive に同期されるグループ属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Signagelive のグループ アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![3 つのマッピングが表示されている [属性マッピング] セクションのスクリーンショット。](media/signagelive-provisioning-tutorial/groupattribute.png)

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Signagelive に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Signagelive にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は、後続の同期よりも実行に時間がかかります。 ユーザーやグループのプロビジョニングにかかる時間の詳細については、「[ユーザーをプロビジョニングするにはどのくらいの時間がかかりますか](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)」を参照してください。 

**[現在の状態]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Signagelive に対して実行されたすべてのアクションが記載されています。 詳細については、「[ユーザー プロビジョニングの状態を確認する](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)」を参照してください。 Azure AD プロビジョニング ログを読むには、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
