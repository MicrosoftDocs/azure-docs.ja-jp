---
title: "チュートリアル: Azure Active Directory と Teamphoria の統合 | Microsoft Docs"
description: "Azure Active Directory と Teamphoria の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jeedes
ms.openlocfilehash: 2a35efb04d7fe22abc6894c149caf090666ce016
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>チュートリアル: Azure Active Directory と Teamphoria の統合

このチュートリアルでは、Teamphoria と Azure Active Directory (Azure AD) を統合する方法について説明します。

Teamphoria と Azure AD の統合には、次の利点があります。

- Teamphoria にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Teamphoria にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Microsoft Azure 管理ポータル) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

<!--## Overview

To enable single sign-on with Teamphoria, it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in Teamphoria.

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>前提条件

Teamphoria と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Teamphoria でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の試用環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の試用版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Teamphoria の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-teamphoria-from-the-gallery"></a>ギャラリーからの Teamphoria の追加
Azure AD への Teamphoria の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Teamphoria を追加する必要があります。

**ギャラリーから Teamphoria を追加するには、次の手順に従います。**

1. **[Microsoft Azure 管理ポータル](https://portal.azure.com)**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. ダイアログの上部にある **[追加]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**Teamphoria**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_search.png)

5. 結果ウィンドウで **[Teamphoria]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Teamphoria で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Teamphoria ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Teamphoria の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Teamphoria の **[Username]** の値として割り当てます。

Teamphoria で Azure AD のシングル サインオンを構成およびテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Teamphoria のテスト ユーザーの作成](#creating-a-teamphoria-test-user)** - Teamphoria で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Microsoft Azure 管理ポータルで Azure AD のシングル サインオンを有効にして、Teamphoria アプリケーションにシングル サインオンを構成します。

**Teamphoria で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Microsoft Azure 管理ポータルの **Teamphoria** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_samlbase.png)

3. **[Teamphoria のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure Single Sign-On](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_url.png)

    a. **[サインオン URL]** ボックスに、`https://<sub-domain>.teamphoria.com/login` という形式で URL を入力します。    

    > [!NOTE] 
    > これは実際の値ではないので注意してください。 この値を実際のシングル サインオン URL に置き換える必要があります。 シングル サインオン URL を取得するには、[Teamphoria クライアント サポート チーム](https://www.teamphoria.com/)に問い合わせてください。 

4. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書を保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-teamphoria-tutorial/tutorial_general_400.png)

6. **[Teamphoria Configuration (Teamphoria 構成)]** セクションで、**[Configure Teamphoria (Teamphoria を構成する)]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![Configure Single Sign-On](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_configure.png) 

7. **Teamphoria** 側でシングル サインオンを構成するには、Teamphoria アプリケーションに管理者としてログインします。

8. 左のツールバーの [構成] タブの **[ADMIN SETTINGS (管理者設定)]** オプションに移動し、**[シングル サインオン]** をクリックして SSO 構成ウィンドウを開きます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-teamphoria-tutorial/admin_sso_configure.png)

9. 右上隅の **[ADD NEW IDENTITY PROVIDER (新しい ID プロバイダーの追加)]** をクリックして、SSO の設定を追加するためのフォームを開きます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-teamphoria-tutorial/add_new_identity_provider.png)

10. 以下の説明に従って、フィールドに詳細を入力します。

    ![Configure Single Sign-On](./media/active-directory-saas-teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **表示名**: 管理者ページでのプラグインの表示名を入力します。

    b. **ボタン名**: SSO を使用してログインするためのログイン ページに表示されるタブの名前。

    c. **証明書**: Azure ポータルから先ほどダウンロードした証明書をメモ帳で開き、内容をコピーして、ボックス内に貼り付けます。

    d. **エントリ ポイント**: Azure ポータルから先ほどコピーした **SAML シングル サインオン サービス URL** を貼り付けます。

    e. オプションを **[ON (オン)]** に切り替え、**[保存]** をクリックします。   

<!--### Next steps

To ensure users can sign-in to Teamphoria after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Teamphoria prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Teamphoria in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Teamphoria users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Microsoft Azure 管理ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-teamphoria-test-user"></a>Teamphoria テスト ユーザーの作成

Azure AD ユーザーが Teamphoria にログインできるようにするには、そのユーザーを Teamphoria にプロビジョニングする必要があります。 Teamphoria の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Teamphoria 企業サイトに管理者としてログインします。

2. 左のツールバーの **[管理]** タブで**管理者**設定をクリックし、**[ユーザー]** をクリックして、ユーザーの管理者ページを開きます。

    ![従業員の追加](./media/active-directory-saas-teamphoria-tutorial/admin_manage_users.png)

3. **[MANUAL INVITE (手動招待)]** オプションをクリックします。

    ![[ユーザーの招待]](./media/active-directory-saas-teamphoria-tutorial/admin_manage_add_users.png)  

4. このページで、次の操作を実行します。 
    
    ![[ユーザーの招待]](./media/active-directory-saas-teamphoria-tutorial/manual_user_invite.png)  

    a. **[EMAIL ADDRESS (電子メール アドレス)]** ボックスに、Britta Simon の**電子メール アドレス**を入力します。

    b. **[名]** ボックスに「**Britta**」と入力します。

    c. **[姓]** ボックスに「**Simon**」と入力します。

    d. **[INVITE 1 USER (1 ユーザーを招待)]** をクリックします。 ユーザーをシステムに作成するには、そのユーザーが招待を受け入れる必要があります。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Teamphoria へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を Teamphoria に割り当てるには、次の手順を実行します。**

1. Azure 管理ポータルでアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Teamphoria]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_203.png

