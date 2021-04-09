---
title: 'チュートリアル: Cornerstone OnDemand を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs'
description: Azure Active Directory を構成して、ユーザー アカウントを Cornerstone OnDemand に自動的にプロビジョニングおよびプロビジョニング解除する方法を説明します。
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 59c599167089d222324ed880c18e68d763f5e468
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358458"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>チュートリアル: Cornerstone OnDemand を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Cornerstone OnDemand に対するユーザーとグループのプロビジョニングとプロビジョニング解除を自動的に実行するように Azure AD を構成するために、Cornerstone OnDemand と Azure Active Directory (Azure AD) で実行する手順を示します。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービス上に構築されるコネクタについて説明します。 このサービスで実行されること、しくみ、およびよく寄せられるについては、「[Azure Active Directory によるサービスとしてのソフトウェア (SaaS) アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、以下を所有していることを前提としています。

* Azure AD テナント。
* Cornerstone OnDemand テナント。
* 管理者アクセス許可がある Cornerstone OnDemand のユーザー アカウント

> [!NOTE]
> Azure AD プロビジョニング統合は、[Cornerstone OnDemand Web サービス](https://www.cornerstoneondemand.com/)に依存しています。 このサービスは Cornerstone OnDemand チームが使用できます。

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Azure Marketplace から Cornerstone OnDemand を追加する

Azure AD で自動ユーザー プロビジョニング用に Cornerstone OnDemand を構成する前に、Marketplace から Cornerstone OnDemand をマネージド SaaS アプリケーションの一覧に追加します。

Marketplace から Cornerstone OnDemand を追加するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のアイコン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部の **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに、「**Cornerstone OnDemand**」と入力し、結果パネルから **[Cornerstone OnDemand]** を選択します。 アプリケーションを追加するには、 **[追加]** を選択します。

    ![結果リストの Cornerstone OnDemand](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Cornerstone OnDemand にユーザーを割り当てる

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD でアプリケーションに割り当てられているユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Cornerstone OnDemand にアクセスする必要がある Azure AD のユーザーまたはグループを決定しておく必要があります。 これらのユーザーまたはグループを Cornerstone OnDemand に割り当てるには、「[エンタープライズ アプリにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)」の指示に従います。

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>ユーザーを Cornerstone OnDemand に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを Cornerstone OnDemand に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後で、追加のユーザーまたはグループを割り当てることができます。

* Cornerstone OnDemand にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログ ボックスで選択します。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Cornerstone OnDemand への自動ユーザー プロビジョニングを構成する

このセクションでは、Azure AD プロビジョニング サービスを構成する手順を説明します。 これを使用して、Azure AD でのユーザーまたはグループの割り当てに基づいて、Cornerstone OnDemand でのユーザーまたはグループの作成、更新、および無効化を行います。

Azure AD で Cornerstone OnDemand の自動ユーザー プロビジョニングを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]**  >  **[Cornerstone OnDemand]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーション一覧で **[Cornerstone OnDemand]** を選択します。

    ![アプリケーション一覧の [Cornerstone OnDemand] リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![Cornerstone OnDemand のプロビジョニング](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![Cornerstone OnDemand のプロビジョニング モード](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. **[管理者資格情報]** セクションに、Cornerstone OnDemand アカウントの管理ユーザー名、管理パスワード、ドメインを入力します。

    * **[管理ユーザー名]** ボックスに、Cornerstone OnDemand テナントの管理者アカウントのドメインまたはユーザー名を入力します。 たとえば、contoso\admin です。

    * **[管理パスワード]** ボックスに、管理者ユーザー名に対応するパスワードを入力します。

    * **[ドメイン]** ボックスに、Cornerstone OnDemand テナントの Web サービスの URL を入力します。 たとえば、サービスは `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx` にあり、Contoso の場合、ドメインは `https://ws-contoso.csod.com/feed30/clientdataservice.asmx` です。 Web サービスの URL を取得する方法の詳細については、[こちらの pdf](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf) を参照してください。

6. 手順 5 に示されているボックスに入力したら、**[テスト接続]** を選択して、Azure AD が Cornerstone OnDemand に接続できることを確認します。 接続できない場合は、使用中の Cornerstone OnDemand アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![Cornerstone OnDemand のテスト接続](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループのメール アドレスを入力します。 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![Cornerstone OnDemand の通知メール](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. **[保存]** を選択します。

9. **[マッピング]** セクションの **[Azure Active Directory ユーザーを Cornerstone OnDemand に同期する]** を選択します。

    ![Cornerstone OnDemand の同期](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. **[属性マッピング]** セクションで、Azure AD から Cornerstone OnDemand に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Cornerstone OnDemand のユーザー アカウントとの照合に使用されます。 すべての変更を保存するために、 **[保存]** を選択します。

    ![Cornerstone OnDemand の属性マッピング](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の手順を参照してください。

12. Cornerstone OnDemand に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![Cornerstone OnDemand のプロビジョニング状態](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Cornerstone OnDemand にプロビジョニングするユーザーまたはグループを定義します。 **[設定]** セクションで、 **[スコープ]** として指定する値を選択します。

    ![Cornerstone OnDemand の [スコープ]](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. プロビジョニングの準備ができたら、 **[保存]** を選択します。

    ![Cornerstone OnDemand の [保存]](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

この操作によって、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーまたはグループの初期同期が開始されます。 初期同期は、以降の同期よりも実行に時間がかかります。 それらは、Azure AD プロビジョニング サービスが実行されている限り、約 40 分ごとに発生します。 

**[同期の詳細]** セクションを使用して進行状況を監視し、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。 このレポートには、Azure AD プロビジョニング サービスによって Cornerstone OnDemand で実行されたすべてのアクションが記述されます。

Azure AD プロビジョニング ログの見方について詳しくは、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」を参照してください。

## <a name="connector-limitations"></a>コネクタの制限事項

Cornerstone OnDemand の **[Position]\(役職\)** 属性では、Cornerstone OnDemand ポータル上のロールに対応する値が求められます。 有効な **[Position]\(役職\)** 値の一覧を取得するには、Cornerstone OnDemand ポータルで **[Edit User Record]\(ユーザー レコードの編集\)、[Organization Structure]\(組織の構造\)、[Position]\(役職\)** の順に移動します。

![Cornerstone OnDemand のプロビジョニングの [Edit User Record]\(ユーザー レコードの編集\)](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Cornerstone OnDemand のプロビジョニングの [Position]\(役職\)](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Cornerstone OnDemand のプロビジョニングの [Position]\(役職\) 一覧](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
