---
title: チュートリアル:Zscaler Three を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: このチュートリアルでは、Azure Active Directory を構成して、ユーザー アカウントを Zscaler Three に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 385a1153-0f47-4e41-8f44-da1b49d7629e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 43ae028e57578634c34c69357a264fdb180b8a1f
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68515372"
---
# <a name="tutorial-configure-zscaler-three-for-automatic-user-provisioning"></a>チュートリアル:Zscaler Three を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Azure Active Directory (Azure AD) を構成して、ユーザーまたはグループを Zscaler Three に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービス上に構築されるコネクタについて説明します。 このサービスが実行する内容、しくみについての重要な情報と、よく寄せられる質問への回答については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../active-directory-saas-app-provisioning.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルに記載された手順を実行するには、次のものが必要になります。

* Azure AD テナント。
* Zscaler Three テナント。
* 管理者アクセス許可がある Zscaler Three のユーザー アカウント。

> [!NOTE]
> Azure AD プロビジョニング統合では、Enterprise アカウントで利用できる Zscaler ZSCloud SCIM API が必要です。

## <a name="adding-zscaler-three-from-the-gallery"></a>ギャラリーからの Zscaler Three の追加

Azure AD で自動ユーザー プロビジョニング用に Zscaler Three を構成する前に、Zscaler Three を Azure AD アプリケーション ギャラリーからマネージド SaaS アプリケーションの一覧に追加する必要があります。

[Azure portal](https://portal.azure.com) の左側のウィンドウで、 **[Azure Active Directory]** を選択します。

![[Azure Active Directory] を選択します。](common/select-azuread.png)

**[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

![エンタープライズ アプリケーション](common/enterprise-applications.png)

アプリケーションを追加するには、ウィンドウの上部の **[新しいアプリケーション]** を選択します。

![[新しいアプリケーション] を選択する](common/add-new-app.png)

検索ボックスに、「**Zscaler Three**」と入力します。 結果から **[Zscaler Three]** を選択して **[追加]** を選択します。

![結果リスト](common/search-new-app.png)

## <a name="assign-users-to-zscaler-three"></a>Zscaler Three にユーザーを割り当てる

Azure AD ユーザーが特定のアプリを使用するためには、そのユーザーにアプリへのアクセス権が割り当てられている必要があります。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD でアプリケーションに割り当てられているユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Zscaler Three へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 それが決まれば、[エンタープライズ アプリへのユーザーまたはグループの割り当て](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)に関するページの手順に従って、これらのユーザーとグループを Zscaler Three に割り当てることができます。

### <a name="important-tips-for-assigning-users-to-zscaler-three"></a>ユーザーを Zscaler Three に割り当てるときの重要なヒント

* まず、単一の Azure AD ユーザーを Zscaler Three に割り当て、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後で、他のユーザーとグループを割り当てることができます。

* Zscaler Three にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログ ボックスで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-automatic-user-provisioning"></a>自動ユーザー プロビジョニングをセットアップする

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーとグループの割り当てに基づいて Zscaler Three のユーザーとグループを作成、更新、無効化する手順について説明します。

> [!TIP]
> Zscaler Three では、SAML ベースのシングル サインオンを有効にすることもできます。 その場合は、[Zscaler Three のシングル サインオンに関するチュートリアル](zscaler-three-tutorial.md)の手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

1. [Azure portal](https://portal.azure.com) にサインインし、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]**  >  **[Zscaler Three]** の順に選択します。

    ![エンタープライズ アプリケーション](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[Zscaler Three]** を選択します。

    ![アプリケーションの一覧](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![Zscaler Three のプロビジョニング](./media/zscaler-three-provisioning-tutorial/provisioning-tab.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![プロビジョニング モードを設定する](./media/zscaler-three-provisioning-tutorial/provisioning-credentials.png)

5. **[管理者資格情報]** セクションで、次の手順で説明する Zscaler Three アカウントの **[テナント URL]** と **[シークレット トークン]** を入力します。

6. **[テナント URL]** と **[シークレット トークン]** を取得するには、Zscaler Three ポータルで **[管理]**  >  **[認証の設定]** の順に移動し、 **[認証の種類]** で **[SAML]** を選択します。

    ![Zscaler Three の認証の設定](./media/zscaler-three-provisioning-tutorial/secret-token-1.png)

    **[Configure SAML]\(SAML の構成\)** を選択して **[Configure SAML]\(SAML の構成\)** ウィンドウを開きます。

    ![[Configure SAML]\(SAML の構成\) ウィンドウ](./media/zscaler-three-provisioning-tutorial/secret-token-2.png)

    **[Enable SCIM-Based Provisioning]\(SCIM ベースのプロビジョニングを有効にする\)** を選択して、**ベース URL** と**ベアラー トークン**をコピーし、設定を保存します。 Azure portal で、**ベース URL** を **[テナント URL]** ボックスに、**ベアラー トークン**を **[シークレット トークン]** ボックスに貼り付けます。

7. **[テナント URL]** ボックスと **[シークレット トークン]** ボックスに値を入力したら、 **[テスト接続]** を選択して Azure AD が Zscaler Three に接続できることを確認します。 接続できない場合は、使用中の Zscaler Three アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![接続をテストする](./media/zscaler-three-provisioning-tutorial/test-connection.png)

8. プロビジョニングのエラー通知を受け取るユーザーまたはグループのメール アドレスを **[通知用メール]** フィールドに入力し、 **[エラーが発生したときにメール通知を送信します]** を選択します。

    ![通知用メールを設定する](./media/zscaler-three-provisioning-tutorial/notification.png)

9. **[保存]** を選択します。

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to ZscalerThree]\(Azure Active Directory ユーザーを ZscalerThree に同期する\)** を選びます。

    ![Azure AD ユーザーを同期する](./media/zscaler-three-provisioning-tutorial/user-mappings.png)

11. **[属性マッピング]** セクションで、Azure AD から Zscaler Three に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Zscaler Three のユーザー アカウントとの照合に使用されます。 すべての変更をコミットするには、 **[保存]** を選択します。

    ![属性マッピング](./media/zscaler-three-provisioning-tutorial/user-attribute-mappings.png)

12. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to ZscalerThree]\(Azure Active Directory グループを ZscalerThree に同期する\)** を選びます。

    ![Azure AD グループを同期する](./media/zscaler-three-provisioning-tutorial/group-mappings.png)

13. **[属性マッピング]** セクションで、Azure AD から Zscaler Three に同期されるグループ属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Zscaler Three のグループとの照合に使用されます。 すべての変更をコミットするには、 **[保存]** を選択します。

    ![属性マッピング](./media/zscaler-three-provisioning-tutorial/group-attribute-mappings.png)

14. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](./../active-directory-saas-scoping-filters.md)の手順を参照してください。

15. Zscaler Three に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニング状態](./media/zscaler-three-provisioning-tutorial/provisioning-status.png)

16. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Zscaler Three にプロビジョニングするユーザーやグループを定義します。

    ![スコープ値](./media/zscaler-three-provisioning-tutorial/scoping.png)

17. プロビジョニングの準備ができたら、 **[保存]** を選択します。

    ![[保存] の選択](./media/zscaler-three-provisioning-tutorial/save-provisioning.png)

これにより、 **[設定]** セクションの **[スコープ]** で定義したユーザーやグループの初回の同期が開始されます。 初回の同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 進行状況は、 **[同期の詳細]** セクションで監視できます。 また、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Zscaler Three に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの見方について詳しくは、「[自動ユーザー アカウント プロビジョニングについてのレポート](../active-directory-saas-provisioning-reporting.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-03.png
