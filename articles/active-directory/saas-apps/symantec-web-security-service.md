---
title: チュートリアル:Symantec Web Security Service (WSS) を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Symantec Web Security Service (WSS) に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
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
ms.openlocfilehash: d7e0db1b0bc1e7aef68ee06f3bdd5e5e0f83b73e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94354718"
---
# <a name="tutorial-configure-symantec-web-security-service-wss-for-automatic-user-provisioning"></a>チュートリアル:Symantec Web Security Service (WSS) を構成して自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure Active Directory (Azure AD) が、ユーザー、グループ、またはその両方を Symantec Web Security Service (WSS) に対して自動的にプロビジョニングおよびプロビジョニング解除するよう構成するために、Symantec Web Security Service (WSS) と Azure AD で実行する手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [Symantec Web Security Service (WSS) テナント](https://www.websecurity.symantec.com/buy-renew?inid=brmenu_nav_brhome)
* 管理者のアクセス許可を持つ Symantec Web Security Service (WSS) のユーザー アカウント。

## <a name="assigning-users-to-symantec-web-security-service-wss"></a>Symantec Web Security Service (WSS) へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Symantec Web Security Service (WSS) へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定したら、次の手順に従って、これらのユーザーやグループを Symantec Web Security Service (WSS) に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

##  <a name="important-tips-for-assigning-users-to-symantec-web-security-service-wss"></a>ユーザーを Symantec Web Security Service (WSS) に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを Symantec Web Security Service (WSS) に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Symantec Web Security Service (WSS) にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="setup-symantec-web-security-service-wss-for-provisioning"></a>プロビジョニングのための Symantec Web Security Service (WSS) のセットアップ

Azure AD を使用した自動ユーザー プロビジョニングのために Symantec Web Security Service (WSS) を構成する前に、Symantec Web Security Service (WSS) 上で SCIM プロビジョニングを有効にする必要があります。

1. [Symantec Web Security Service 管理コンソール](https://portal.threatpulse.com/login.jsp)にサインインします。 **[ソリューション]**  >  **[サービス]** に移動します。

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/service.png)

2. **[Account Maintenance]\(アカウントのメンテナンス\)**  >  **[Integrations]\(統合\)**  >  **[New Integration]\(新しい統合\)** に移動します。

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/acount.png)

3.  **[Third-Party Users & Groups Sync]\(サードパーティのユーザーとグループの同期\)** を選択します。 

    ![[Third-Party Users & Groups Sync]\(サードパーティのユーザーとグループの同期\) オプションのスクリーンショット。](media/symantec-web-security-service/third-party-users.png)

4.  **[SCIM URL]** と **[トークン]** をコピーします。 これらの値を、Azure portal の Symantec Web Security Service (WSS) アプリケーションの [プロビジョニング] タブの **[テナント URL]** および **[シークレット トークン]** フィールドに入力します。

    ![[New Integration]\(新しい統合\) ダイアログ ボックスのスクリーンショット。[SCIM URL] と [トークン] のテキスト ボックスが選択されています。](media/symantec-web-security-service/scim.png)

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>ギャラリーから Symantec Web Security Service (WSS) を追加する

Azure AD で自動ユーザー プロビジョニング用に Symantec Web Security Service (WSS) を構成するには、Azure AD アプリケーション ギャラリーから管理対象 SaaS アプリケーションの一覧に Symantec Web Security Service (WSS) を追加する必要があります。

**Azure AD アプリケーション ギャラリーから Symantec Web Security Service (WSS) を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Symantec Web Security Service**」と入力し、結果ウィンドウで **[Symantec Web Security Service]** を選択し、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の Symantec Web Security Service (WSS)](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-symantec-web-security-service-wss"></a>Symantec Web Security Service (WSS) への自動ユーザー プロビジョニングを構成する

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて Symantec Web Security Service (WSS) のユーザーやグループを作成、更新、無効化するよう、Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> [Symantec Web Security Service (WSS) シングル サインオン チュートリアル](symantec-tutorial.md)で説明している手順に従って、Symantec Web Security Service (WSS) に対する SAML ベースのシングル サインオンを有効にすることもできます。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-symantec-web-security-service-wss-in-azure-ad"></a>Azure AD で Symantec Web Security Service (WSS) に対する自動ユーザー プロビジョニングを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[Symantec Web Security Service]** を選択します。

    ![アプリケーションの一覧の Symantec Web Security Service (WSS) リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. [管理者資格情報] セクションの **[テナント URL]** および **[シークレット トークン]** に、先ほど取得した **[SCIM URL]** と **[トークン]** の値をそれぞれ入力します。 **[接続テスト]** をクリックして、Azure AD から Symantec Web Security Service に接続できることを確認します。 接続できない場合は、使用中の Symantec Web Security Service (WSS) アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Users to Symantec Web Security Service (WSS)]\(Azure Active Directory ユーザーを Symantec Web Security Service (WSS) に同期する\)** を選択します。

    ![[マッピング] セクションのスクリーンショット。[Synchronize Azure Active Directory Users to Symantec Web Security Service (WSS)]\(Azure Active Directory ユーザーを Symantec Web Security Service (WSS) に同期する\) オプションが選択されています。](media/symantec-web-security-service/usermapping.png)

9. **[属性マッピング]** セクションで、Azure AD から Symantec Web Security Service (WSS) に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Symantec Web Security Service (WSS) のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![[属性マッピング] セクションのスクリーンショット。16 件のプロパティが一致しています。](media/symantec-web-security-service/userattribute.png)

10. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Groups to Symantec Web Security Service]\(Azure Active Directory グループを Symantec Web Security Service に同期する\)** を選択します。

    ![[マッピング] セクションのスクリーンショット。[Synchronize Azure Active Directory Groups to Symantec Web Security Service]\(Azure Active Directory グループを Symantec Web Security Service に同期する\) オプションが選択されています。](media/symantec-web-security-service/groupmapping.png)

11. **[属性マッピング]** セクションで、Azure AD から Symantec Web Security Service (WSS) に同期されるグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Symantec Web Security Service (WSS) のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![[属性マッピング] セクションのスクリーンショット。3 件のプロパティが一致しています。](media/symantec-web-security-service/groupattribute.png)

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Symantec Web Security Service に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Symantec Web Security Service (WSS) にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は、後続の同期よりも実行に時間がかかります。 ユーザーやグループのプロビジョニングにかかる時間の詳細については、「[ユーザーをプロビジョニングするにはどのくらいの時間がかかりますか](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)」を参照してください。

**[現在の状態]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Symantec Web Security Service (WSS) に対して実行されたすべてのアクションが記載されています。 詳細については、「[ユーザー プロビジョニングの状態を確認する](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)」を参照してください。 Azure AD プロビジョニング ログを読むには、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
