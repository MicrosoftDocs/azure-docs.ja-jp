---
title: チュートリアル:BlueJeans を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを BlueJeans に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84870ef03e07cdabe994da7c04a103bead48d34f
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672994"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>チュートリアル:BlueJeans を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure AD が自動的にユーザーまたはグループを BlueJeans にプロビジョニングまたは BlueJeans からプロビジョニング解除するように構成するために、BlueJeans と Azure Active Directory (Azure AD) で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* Azure AD テナント
* [My Company](https://www.BlueJeans.com/pricing) プラン (有効になっているのが望ましい) を使用した BlueJeans テナント
* Admin アクセス許可がある BlueJeans のユーザー アカウント

> [!NOTE]
> Azure AD プロビジョニング統合では、Standard プラン以上の BlueJeans チームで使用できる [BlueJeans API](https://BlueJeans.github.io/developer) が必要です。

## <a name="adding-bluejeans-from-the-gallery"></a>ギャラリーからの BlueJeans の追加

Azure AD で自動ユーザー プロビジョニング用に BlueJeans を構成する前に、Azure AD アプリケーション ギャラリーから BlueJeans を管理対象の SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから BlueJeans を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**BlueJeans**」と入力し、結果パネルで **[BlueJeans]** を選択し、 **[追加]** ボタンをクリックして、アプリケーションを追加します。

    ![結果一覧の BlueJeans](common/search-new-app.png)

## <a name="assigning-users-to-bluejeans"></a>BlueJeans へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、BlueJeans へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを BlueJeans に割り当てることができます。

* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bluejeans"></a>ユーザーを BlueJeans に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを BlueJeans に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* BlueJeans にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="configuring-automatic-user-provisioning-to-bluejeans"></a>BlueJeans への自動ユーザー プロビジョニングの構成

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて BlueJeans のユーザーやグループを作成、更新、削除する手順について説明します。

> [!TIP]
> BlueJeans では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[BlueJeans シングル サインオンのチュートリアル](bluejeans-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Azure AD で BlueJeans の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインし、 **[エンタープライズ アプリケーション]** 、 **[すべてのアプリケーション]** 、 **[BlueJeans]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[BlueJeans]** を選択します。

    ![アプリケーション一覧の [BlueJeans] リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![BlueJeans のプロビジョニング](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningTab.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![BlueJeans のプロビジョニング](./media/bluejeans-provisioning-tutorial/Bluejeans1.png)

5. **[管理者資格情報]** セクションの **[管理ユーザー名]** と **[管理パスワード]** に、BlueJeans アカウントの情報を入力します。 これらの値の例を次に示します。

   * **[管理ユーザー名]** フィールドに、BlueJeans テナントの管理者アカウントのユーザー名を入力します。 例: admin@contoso.com.

   * **[管理パスワード]** フィールドに、管理ユーザー名に対応するパスワードを入力します。

6. 手順 5 のフィールドに入力したら、 **[テスト接続]** をクリックして Azure AD が BlueJeans に接続できることを確認します。 接続できない場合は、使用中の BlueJeans アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![BlueJeans のプロビジョニング](./media/bluejeans-provisioning-tutorial/BluejeansTestConnection.png)

7. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![BlueJeans のプロビジョニング](./media/bluejeans-provisioning-tutorial/BluejeansNotificationEmail.png)

8. **[Save]** をクリックします。

9. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to BlueJeans]\(Azure Active Directory ユーザーを BlueJeans に同期する\)** を選択します。

    ![BlueJeans のプロビジョニング](./media/bluejeans-provisioning-tutorial/BluejeansMapping.png)

10. **[属性マッピング]** セクションで、Azure AD から BlueJeans に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で BlueJeans のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![BlueJeans のプロビジョニング](./media/bluejeans-provisioning-tutorial/BluejeansUserMappingAtrributes.png)

11. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

12. BlueJeans に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![BlueJeans のプロビジョニング](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningStatus.png)

13. **[設定]** セクションの **[スコープ]** で目的の値を選択して、BlueJeans にプロビジョニングするユーザーやグループを定義します。

    ![BlueJeans のプロビジョニング](./media/bluejeans-provisioning-tutorial/UserGroupSelection.png)

14. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![BlueJeans のプロビジョニング](./media/bluejeans-provisioning-tutorial/SaveProvisioning.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって BlueJeans に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="connector-limitations"></a>コネクタの制限事項

* Bluejeans では、30 文字を超えるユーザー名は許可されません。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->

[1]: ./media/bluejeans-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
