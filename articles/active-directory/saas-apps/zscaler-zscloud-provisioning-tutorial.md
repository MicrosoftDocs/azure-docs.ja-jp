---
title: チュートリアル:Zscaler ZSCloud を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: このチュートリアルでは、Azure Active Directory を構成して、ユーザー アカウントを Zscaler ZSCloud に自動的にプロビジョニングしたり、プロビジョニング解除したりする方法を説明します。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: a752be80-d3ef-45d1-ac8f-4fb814c07b07
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 34d3a7fb299ba143eee01b6b7184f1c566d41aba
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68515458"
---
# <a name="tutorial-configure-zscaler-zscloud-for-automatic-user-provisioning"></a>チュートリアル:Zscaler ZSCloud を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Azure Active Directory (Azure AD) を構成して、ユーザーまたはグループを Zscaler ZSCloud に自動的にプロビジョニングしたり、プロビジョニング解除したりする方法を説明します。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービス上に構築されるコネクタについて説明します。 このサービスが実行する内容、しくみについての重要な情報と、よく寄せられる質問への回答については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../active-directory-saas-app-provisioning.md)」を参照してください。


## <a name="prerequisites"></a>前提条件

このチュートリアルに記載された手順を実行するには、次のものが必要になります。

* Azure AD テナント。
* Zscaler ZSCloud テナント。
* Admin アクセス許可がある Zscaler ZSCloud のユーザー アカウント。

> [!NOTE]
> Azure AD プロビジョニング統合では、Enterprise アカウントで利用できる Zscaler ZSCloud SCIM API が必要です。

## <a name="add-zscaler-zscloud-from-the-gallery"></a>ギャラリーからの Zscaler ZSCloud の追加

Azure AD で自動ユーザー プロビジョニング用に Zscaler ZSCloud を構成する前に、Zscaler ZSCloud を Azure AD アプリケーション ギャラリーからマネージド SaaS アプリケーションの一覧に追加する必要があります。

[Azure portal](https://portal.azure.com) の左側のウィンドウで、 **[Azure Active Directory]** を選択します。

![[Azure Active Directory] を選択します。](common/select-azuread.png)

**[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

![エンタープライズ アプリケーション](common/enterprise-applications.png)

アプリケーションを追加するには、ウィンドウの上部の **[新しいアプリケーション]** を選択します。

![[新しいアプリケーション] を選択する](common/add-new-app.png)

検索ボックスに、「**Zscaler ZSCloud**」と入力します。 結果から **[Zscaler ZSCloud]** を選択して **[追加]** を選択します。

![結果リスト](common/search-new-app.png)

## <a name="assign-users-to-zscaler-zscloud"></a>Zscaler ZSCloud にユーザーを割り当てる

Azure AD ユーザーが特定のアプリを使用するためには、そのユーザーにアプリへのアクセス権が割り当てられている必要があります。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD でアプリケーションに割り当てられているユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Zscaler ZSCloud へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 それが決まれば、[エンタープライズ アプリへのユーザーまたはグループの割り当て](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)に関するページの手順に従って、これらのユーザーとグループを Zscaler ZSCloud に割り当てることができます。

### <a name="important-tips-for-assigning-users-to-zscaler-zscloud"></a>ユーザーを Zscaler ZSCloud に割り当てる際の重要なヒント

* まず、単一の Azure AD ユーザーを Zscaler ZSCloud に割り当て、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後で、他のユーザーとグループを割り当てることができます。

* Zscaler ZSCloud にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログ ボックスで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-automatic-user-provisioning"></a>自動ユーザー プロビジョニングをセットアップする

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーとグループの割り当てに基づいて Zscaler ZSCloud のユーザーとグループを作成、更新、無効化する手順について説明します。

> [!TIP]
> Zscaler ZSCloud では、SAML ベースのシングル サインオンを有効にすることもできます。 その場合は、[Zscaler ZSCloud のシングル サインオンに関するチュートリアル](zscaler-zsCloud-tutorial.md)の手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

1. [Azure portal](https://portal.azure.com) にサインインし、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]**  >  **[Zscaler ZSCloud]** の順に選択します。

    ![エンタープライズ アプリケーション](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[Zscaler ZSCloud]** を選択します。

    ![アプリケーションの一覧](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![Zscaler ZSCloud プロビジョニング](./media/zscaler-zscloud-provisioning-tutorial/provisioningtab.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![プロビジョニング モードを設定する](./media/zscaler-zscloud-provisioning-tutorial/provisioningcredentials.png)

5. **[管理者資格情報]** セクションで、次の手順で説明する Zscaler ZSCloud アカウントの **[テナント URL]** と **[シークレット トークン]** を入力します。

6. **[テナント URL]** と **[シークレット トークン]** を取得するには、Zscaler ZSCloud ポータルで **[Administration]\(管理\)**  >  **[Authentication Settings]\(認証設定\)** の順に移動し、 **[Authentication Type]\(認証タイプ\)** で **[SAML]** を選択します。

    ![Zscaler ZSCloud の認証の設定](./media/zscaler-zscloud-provisioning-tutorial/secrettoken1.png)

    **[Configure SAML]\(SAML の構成\)** を選択して **[Configure SAML]\(SAML の構成\)** ウィンドウを開きます。

    ![[Configure SAML]\(SAML の構成\) ウィンドウ](./media/zscaler-zscloud-provisioning-tutorial/secrettoken2.png)

    **[Enable SCIM-Based Provisioning]\(SCIM ベースのプロビジョニングを有効にする\)** を選択して、**ベース URL** と**ベアラー トークン**をコピーし、設定を保存します。 Azure portal で、**ベース URL** を **[テナント URL]** ボックスに、**ベアラー トークン**を **[シークレット トークン]** ボックスに貼り付けます。

7. **[テナント URL]** ボックスと **[シークレット トークン]** ボックスに値を入力したら、 **[テスト接続]** を選択して Azure AD が Zscaler ZSCloud に接続できることを確認します。 接続できない場合は、使用中の Zscaler ZSCloud アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![接続をテストする](./media/zscaler-zscloud-provisioning-tutorial/testconnection.png)

8. プロビジョニングのエラー通知を受け取るユーザーまたはグループのメール アドレスを **[通知用メール]** フィールドに入力し、 **[エラーが発生したときにメール通知を送信します]** を選択します。

    ![通知用メールを設定する](./media/zscaler-zscloud-provisioning-tutorial/Notification.png)

9. **[保存]** を選択します。

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to ZscalerZSCloud]\(Azure Active Directory ユーザーを ZscalerZSCloud に同期する\)** を選びます。

    ![Azure AD ユーザーを同期する](./media/zscaler-zscloud-provisioning-tutorial/usermappings.png)

11. **[属性マッピング]** セクションで、Azure AD から Zscaler ZSCloud に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Zscaler ZSCloud のユーザー アカウントとの照合に使用されます。 すべての変更をコミットするには、 **[保存]** を選択します。

    ![属性マッピング](./media/zscaler-zscloud-provisioning-tutorial/userattributemappings.png)

12. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to ZscalerZSCloud]\(Azure Active Directory グループを ZscalerZSCloud に同期する\)** を選びます。

    ![Azure AD グループを同期する](./media/zscaler-zscloud-provisioning-tutorial/groupmappings.png)

13. **[属性マッピング]** セクションで、Azure AD から Zscaler ZSCloud に同期されるグループ属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Zscaler ZSCloud のグループとの照合に使用されます。 すべての変更をコミットするには、 **[保存]** を選択します。

    ![属性マッピング](./media/zscaler-zscloud-provisioning-tutorial/groupattributemappings.png)

14. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](./../active-directory-saas-scoping-filters.md)の手順を参照してください。

15. Zscaler ZSCloud に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニング状態](./media/zscaler-zscloud-provisioning-tutorial/provisioningstatus.png)

16. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Zscaler ZSCloud にプロビジョニングするユーザーやグループを定義します。

    ![スコープ値](./media/zscaler-zscloud-provisioning-tutorial/scoping.png)

17. プロビジョニングの準備ができたら、 **[保存]** を選択します。

    ![[保存] の選択](./media/zscaler-zscloud-provisioning-tutorial/saveprovisioning.png)

これにより、 **[設定]** セクションの **[スコープ]** で定義したユーザーやグループの初回の同期が開始されます。 初回の同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 進行状況は、 **[同期の詳細]** セクションで監視できます。 また、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Zscaler ZSCloud に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの見方について詳しくは、「[自動ユーザー アカウント プロビジョニングについてのレポート](../active-directory-saas-provisioning-reporting.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-03.png
