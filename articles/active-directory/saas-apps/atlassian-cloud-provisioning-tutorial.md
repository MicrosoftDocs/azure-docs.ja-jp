---
title: チュートリアル:Atlassian Cloud を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Atlassian Cloud に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: f168e2746afa278880ad7ceb21f78666151d5aa1
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672707"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>チュートリアル:Atlassian Cloud を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure Active Directory (Azure AD) が自動的にユーザーまたはグループを Atlassian Cloud にプロビジョニングまたは Atlassian Cloud からプロビジョニング解除するように構成するために、Atlassian Cloud と Azure AD で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [Atlassian Cloud テナント](https://www.atlassian.com/licensing/cloud)
* Admin アクセス許可がある Atlassian Cloud のユーザー アカウント。

> [!NOTE]
> Azure AD プロビジョニング統合では、Atlassian Cloud のチームで使用できる **Atlassian Cloud SCIM API** が必要です。

## <a name="add-atlassian-cloud-from-the-gallery"></a>ギャラリーからの Atlassian Cloud の追加

Azure AD で自動ユーザー プロビジョニング用に Atlassian Cloud を構成する前に、Azure AD アプリケーション ギャラリーから Atlassian Cloud を管理対象の SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Atlassian Cloud を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Atlassian Cloud**」と入力し、結果ウィンドウで **[Atlassian Cloud]** を選択し、 **[追加]** ボタンをクリックして、アプリケーションを追加します。

    ![結果リスト内の Atlassian Cloud](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Atlassian Cloud へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Atlassian Cloud へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Atlassian Cloud に割り当てることができます。

* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>ユーザーを Atlassian Cloud に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを Atlassian Cloud に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Atlassian Cloud にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Atlassian Cloud への自動ユーザー プロビジョニングの構成 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Atlassian Cloud のユーザーやグループを作成、更新、無効化する手順について説明します。

> [!TIP]
> Atlassian Cloud では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Atlassian Cloud シングル サインオンのチュートリアル](atlassian-cloud-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Azure AD で Atlassian Cloud の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインし、 **[エンタープライズ アプリケーション]** 、 **[すべてのアプリケーション]** 、 **[Atlassian Cloud]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Atlassian Cloud]** を選択します。

    ![[アプリケーション] リストの [Atlassian Cloud] リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![Atlassian Cloud のプロビジョニング](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![Atlassian Cloud のプロビジョニング](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. **[管理者資格情報]** セクションで、Atlassian Cloud のアカウントの **[テナントの URL]** と **[シークレット トークン]** を入力します。 これらの値の例を次に示します。

   * **[テナント URL]** フィールドに、手順 6. の説明に従って Atlassian から受信した特定のテナント エンドポイントを入力します。 たとえば、「`https://api.atlassian.com/scim/directory/{directoryId}`」のように入力します。

   * **[シークレット トークン]** フィールドに、手順 6 で説明されているシークレット トークンを設定します。

6. [Atlassian 組織マネージャー](https://admin.atlassian.com)の **[User Provisioning]** に移動し、 **[Create a Token]** をクリックします。 **[Directory base URL]** と **[Bearer Token]** を **[Tenant URL]** フィールドと **[Secret Token]** フィールドにそれぞれコピーします。

    ![Atlassian Cloud のプロビジョニング](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Atlassian Cloud のプロビジョニング](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png)

    ![Atlassian Cloud のプロビジョニング](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. 手順 5 に示されたフィールドに値を入力したら、 **[テスト接続]** をクリックして、Azure AD が Atlassian Cloud に接続できることを確認します。 接続できない場合は、使用中の Atlassian Cloud アカウントに管理者アクセス許可があることを確認してから、もう一度お試しください。

    ![Atlassian Cloud のプロビジョニング](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

8. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![Atlassian Cloud のプロビジョニング](./media/atlassian-cloud-provisioning-tutorial/notification.png)

9. **[Save]** をクリックします。

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Atlassian Cloud]** を選択します。

    ![Atlassian Cloud のプロビジョニング](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

11. **[属性マッピング]** セクションで、Azure AD から Atlassian Cloud に同期されるユーザー属性を確認します。 **[Matching]** プロパティとして選択されている属性は、更新処理で Atlassian Cloud のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Atlassian Cloud のプロビジョニング](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

12. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to Atlassian Cloud]** を選択します。

    ![Atlassian Cloud のプロビジョニング](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

13. **[属性マッピング]** セクションで、Azure AD から Atlassian Cloud に同期されるグループ属性を確認します。 **[Matching]** プロパティとして選択されている属性は、更新処理で Atlassian Cloud のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Atlassian Cloud のプロビジョニング](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

14. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

15. Atlassian Cloud に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![Atlassian Cloud のプロビジョニング](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

16. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Atlassian Cloud にプロビジョニングするユーザーやグループを定義します。

    ![Atlassian Cloud のプロビジョニング](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

17. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![Atlassian Cloud のプロビジョニング](./media/atlassian-cloud-provisioning-tutorial/save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Atlassian Cloud に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="connector-limitations"></a>コネクタの制限事項

* Atlassian Cloud では、[検証済みドメイン](https://confluence.atlassian.com/cloud/organization-administration-938859734.html)からのみユーザーのプロビジョニングが可能です。
* Atlassian Cloud では、現在のところグループ名の変更はサポートされていません。 つまり、Azure AD でグループの displayName を変更しても、Atlassian Cloud には更新および反映されません。
* Azure AD の **mail** ユーザー属性の値は、ユーザーの Microsoft Exchange メールボックスが存在する場合にのみ設定されます。 ユーザーのメールボックスが存在しない場合は、別の適切な属性を Atlassian Cloud の **emails** 属性にマップすることをお勧めします。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png
