---
title: チュートリアル:Reward Gateway を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Reward Gateway に自動的にプロビジョニングしたりプロビジョニングを解除したりする方法を説明します。
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
ms.openlocfilehash: 2d51903aff6f3fd1cd53d85a980f1b5dc2a893e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94354286"
---
# <a name="tutorial-configure-reward-gateway-for-automatic-user-provisioning"></a>チュートリアル:Reward Gateway を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Reward Gateway に対するユーザーまたはグループのプロビジョニングまたはプロビジョニング解除を自動的に実行するように Azure Active Directory (Azure AD) を構成するために、Reward Gateway と Azure AD で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント。
* [Reward Gateway テナント](https://www.rewardgateway.com/)。
* Admin アクセス許可がある Reward Gateway のユーザー アカウント。

## <a name="assigning-users-to-reward-gateway"></a>Reward Gateway へのユーザーの割り当て 

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Reward Gateway へのアクセスが必要な Azure AD 内のユーザーやグループを決定しておく必要があります。 それが決まれば、[エンタープライズ アプリへのユーザーまたはグループの割り当て](../manage-apps/assign-user-or-group-access-portal.md)に関するページの手順に従って、これらのユーザーやグループを Reward Gateway に割り当てることができます。


## <a name="important-tips-for-assigning-users-to-reward-gateway"></a>ユーザーを Reward Gateway に割り当てる際の重要なヒント 

* 単一の Azure AD ユーザーを Reward Gateway に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Reward Gateway にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="setup-reward-gateway--for-provisioning"></a>プロビジョニングのための Reward Gateway の設定
Azure AD での自動ユーザー プロビジョニング用に Reward Gateway を構成する前に、Reward Gateway 上で SCIM プロビジョニングを有効にする必要があります。

1. [Reward Gateway 管理コンソール](https://rewardgateway.photoshelter.com/login/)にサインインします。 **[Integrations (統合)]** をクリックします。

    ![Reward Gateway 管理コンソールのスクリーンショット。[統合] オプションが選択されています。](media/reward-gateway-provisioning-tutorial/image00.png)

2.  **[My Integration]\(自分の統合\)** を選択します。

    ![2 つの [統合] オプションのスクリーンショット。[My Integration]\(自分の統合\) が選択されています。](media/reward-gateway-provisioning-tutorial/image001.png)

3.  **[SCIM URL (v2)]** および **[OAuth Bearer Token]\(OAuth ベアラー トークン\)** の値をコピーします。 これらの値を、Azure portal の Reward Gateway アプリケーションの [プロビジョニング] タブの [テナント URL] および [シークレット トークン] フィールドに入力します。

    ![[My Integration]\(自分の統合\) パネルのスクリーンショット。[OAuth Bearer Token]\(OAuth ベアラー トークン\) テキスト ボックスが選択されています。](media/reward-gateway-provisioning-tutorial/image03.png)

## <a name="add-reward-gateway-from-the-gallery"></a>ギャラリーからの Reward Gateway の追加

Azure AD で自動ユーザー プロビジョニング用に Reward Gateway を構成するには、Azure AD アプリケーション ギャラリーから Reward Gateway をマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Reward Gateway を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Reward Gateway**」と入力し、結果ウィンドウで **[Reward Gateway]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の Reward Gateway](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-reward-gateway"></a>Reward Gateway への自動ユーザー プロビジョニングの構成  

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて Reward Gateway のユーザーやグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> Reward Gateway では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Reward Gateway シングル サインオンのチュートリアル](reward-gateway-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-reward-gateway-in-azure-ad"></a>Azure AD で Reward Gateway の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Reward Gateway]** を選択します。

    ![アプリケーションの一覧の Reward Gateway のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナント URL]** および **[シークレット トークン]** に、先ほど取得した **[SCIM URL (v2)]** および **[OAuth Bearer Token]\(OAuth ベアラー トークン\)** の値をそれぞれ入力します。 **[テスト接続]** をクリックして、Azure AD から Reward Gateway に接続できることを確認します。 接続できない場合は、使用中の Reward Gateway アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Reward Gateway]\(Azure Active Directory ユーザーを Reward Gateway に同期する\)** を選択します。

    ![[マッピング] セクションのスクリーンショット。[Synchronize Azure Active Directory Users to Reward Gateway]\(Azure Active Directory ユーザーを Reward Gateway に同期する\) オプションが選択されています。](media/reward-gateway-provisioning-tutorial/user-mappings.png)

9. **[属性マッピング]** セクションで、Azure AD から Reward Gateway に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Reward Gateway のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![6 つのマッピングが表示されている [属性マッピング] セクションのスクリーンショット。](media/reward-gateway-provisioning-tutorial/user-attributes.png)

10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

11. Reward Gateway に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

12. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Reward Gateway にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

13. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Reward Gateway に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="connector-limitations"></a>コネクタの制限事項

現在、Reward Gateway ではグループのプロビジョニングはサポートされていません。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

[プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)