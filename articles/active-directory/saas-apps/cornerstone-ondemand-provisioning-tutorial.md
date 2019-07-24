---
title: チュートリアル:Cornerstone OnDemand を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Cornerstone OnDemand に自動的にプロビジョニングおよびプロビジョニング解除する方法を説明します。
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d17a3c81784d56c6fcad7c7608559abf732882a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59274095"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>チュートリアル:Cornerstone OnDemand を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure AD が自動的にユーザーまたはグループを Cornerstone OnDemand に追加または Cornerstone OnDemand から削除するように構成するために、Cornerstone OnDemand と Azure Active Directory (Azure AD) で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* Cornerstone OnDemand テナント
* 管理者アクセス許可がある Cornerstone OnDemand のユーザー アカウント

> [!NOTE]
> Azure AD プロビジョニングの統合は [Cornerstone OnDemand Webservice](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf) に依存し、Cornerstone OnDemand チームが利用できます。

## <a name="adding-cornerstone-ondemand-from-the-gallery"></a>ギャラリーからの Cornerstone OnDemand の追加

Azure AD で自動ユーザー プロビジョニング用に Cornerstone OnDemand を構成する前に、Azure AD アプリケーション ギャラリーから Cornerstone OnDemand を管理対象の SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Cornerstone OnDemand を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Cornerstone OnDemand**」と入力し、結果パネルから **[Cornerstone OnDemand]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Cornerstone OnDemand](common/search-new-app.png)

## <a name="assigning-users-to-cornerstone-ondemand"></a>Cornerstone OnDemand にユーザーを割り当てる

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成および有効にする前に、Cornerstone OnDemand にアクセスが必要な Azure AD のユーザーやグループを決定する必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Cornerstone OnDemand に割り当てることができます。

* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>ユーザーを Cornerstone OnDemand に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを Cornerstone OnDemand に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをおすすめします。 後でユーザーやグループを追加で割り当てられます。

* Cornerstone OnDemand にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="configuring-automatic-user-provisioning-to-cornerstone-ondemand"></a>Cornerstone OnDemand への自動ユーザー プロビジョニングの構成

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Cornerstone OnDemand のユーザーやグループを作成、更新、削除する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-cornerstone-ondemand-in-azure-ad"></a>次のようにして、Azure AD で Cornerstone OnDemand の自動ユーザー プロビジョニングを構成します。

1. [Azure portal](https://portal.azure.com) にサインインして、**[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[Cornerstone OnDemand]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーション一覧で **[Cornerstone OnDemand]** を選択します。

    ![アプリケーション一覧の [Cornerstone OnDemand] リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![Cornerstone OnDemand のプロビジョニング](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![Cornerstone OnDemand のプロビジョニング](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. **[管理者資格情報]** セクションに、Cornerstone OnDemand アカウントの **[管理ユーザー名]**、**[管理パスワード]**、**[ドメイン]** を入力します。

    * **[管理ユーザー名]** フィールドに、Cornerstone OnDemand テナントの管理者アカウントのドメイン\ユーザー名を入力します。 例: contoso\admin。

    * **[管理パスワード]** フィールドに、管理ユーザー名に対応するパスワードを入力します。

    * **[ドメイン]** フィールドに、Cornerstone OnDemand テナントの Web サービスの URL を入力します。 例:サービスは `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx` にあり、Contoso の場合、ドメインは `https://ws-contoso.csod.com/feed30/clientdataservice.asmx` です。 Web サービスの URL を取得する方法の詳細については、[こちら](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf)を参照してください。

6. 手順 5 のフィールドに入力したら、**[テスト接続]** をクリックして Azure AD が Cornerstone OnDemand できることを確認します。 接続できない場合は、使用中の Cornerstone OnDemand アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![Cornerstone OnDemand のプロビジョニング](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、**[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![Cornerstone OnDemand のプロビジョニング](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. **[Save]** をクリックします。

9. **[マッピング]** セクションの **[Azure Active Directory ユーザーを Cornerstone OnDemand に同期する]** を選択します。

    ![Cornerstone OnDemand のプロビジョニング](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. **[属性マッピング]** セクションで、Azure AD から Cornerstone OnDemand に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Cornerstone OnDemand のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Cornerstone OnDemand のプロビジョニング](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

12. Cornerstone OnDemand に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![Cornerstone OnDemand のプロビジョニング](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Cornerstone OnDemand にプロビジョニングするユーザーやグループを定義します。

    ![Cornerstone OnDemand のプロビジョニング](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. プロビジョニングの準備ができたら、**[保存]** をクリックします。

    ![Cornerstone OnDemand のプロビジョニング](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

これにより、**[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Cornerstone OnDemand に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="connector-limitations"></a>コネクタの制限事項

* Cornerstone OnDemand の **[Position]\(役職\)** 属性では、Cornerstone OnDemand ポータル上のロールに対応する値が求められます。 有効な **Position** 値のリストは、Cornerstone OnDemand ポータルで **[Edit User Record]\(ユーザー レコードの編集\)、[Organization Structure]\(組織の構造\)、[Position]\(役職\)** の順に移動して入手できます。

    ![Cornerstone OnDemand のプロビジョニングのユーザーの編集](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png) ![Cornerstone OnDemand のプロビジョニングの役職](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png) ![Cornerstone OnDemand のプロビジョニングの役職リスト](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
