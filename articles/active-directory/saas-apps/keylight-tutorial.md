---
title: チュートリアル:Azure Active Directory と LockPath Keylight の統合 | Microsoft Docs
description: Azure Active Directory と LockPath Keylight の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 234a32f1-9f56-4650-9e31-7b38ad734b1a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bc5121f6604fae9a28b52db1bfb308d7cdb968d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56205088"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>チュートリアル:Azure Active Directory と LockPath Keylight の統合

このチュートリアルでは、LockPath Keylight と Azure Active Directory (Azure AD) を統合する方法について説明します。

LockPath Keylight と Azure AD の統合には、次の利点があります。

- LockPath Keylight にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に LockPath Keylight にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

LockPath Keylight と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- LockPath Keylight でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの LockPath Keylight の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-lockpath-keylight-from-the-gallery"></a>ギャラリーからの LockPath Keylight の追加
Azure AD への LockPath Keylight の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に LockPath Keylight を追加する必要があります。

**ギャラリーから LockPath Keylight を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「 **LockPath Keylight**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/keylight-tutorial/tutorial_keylight_search.png)

1. 結果ウィンドウで **LockPath Keylight** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/keylight-tutorial/tutorial_keylight_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、LockPath Keylight で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する LockPath Keylight ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと LockPath Keylight の関連ユーザーの間で、リンク関係が確立されている必要があります。

LockPath Keylight で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

LockPath Keylight で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[LockPath Keylight テスト ユーザーの作成](#creating-a-lockpath-keylight-test-user)** - LockPath Keylight で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、LockPath Keylight アプリケーションでシングル サインオンを構成します。

**LockPath Keylight で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **LockPath Keylight** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/keylight-tutorial/tutorial_keylight_samlbase.png)

1. **[LockPath Keylight のドメインと URL]** セクションで、次の手順に従います。

    ![Configure single sign-on](./media/keylight-tutorial/tutorial_keylight_url.png)

    a. **[サインオン URL]** ボックスに、`https://<company name>.keylightgrc.com/` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<company name>.keylightgrc.com` の形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<company name>.keylightgrc.com/Login.aspx` のパターンを使用して URL を入力します。
    
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[LockPath Keylight クライアント サポート チーム](https://www.lockpath.com/contact/)に問い合わせてください。 

1. **[SAML 署名証明書]** セクションで、**[証明書 (未加工)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/keylight-tutorial/tutorial_keylight_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/keylight-tutorial/tutorial_general_400.png)
    
1. **[LockPath Keylight 構成]** セクションで、**[LockPath Keylight の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/keylight-tutorial/tutorial_keylight_configure.png) 

1. LockPath Keylight で SSO を有効にするには、次の手順に従います。
   
    a. 管理者として LockPath Keylight アカウントにサインオンします。
    
    b. 上部のメニューで **[人]** をクリックして **[Keylight セットアップ]** を選択します。
   
    ![Configure single sign-on](./media/keylight-tutorial/401.png) 

    c. 左側のツリー ビューで **[SAML]** をクリックします。
   
    ![Configure single sign-on](./media/keylight-tutorial/402.png) 

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **[SAML 設定]** ダイアログで **[編集]** をクリックします。
   
    ![Configure single sign-on](./media/keylight-tutorial/404.png) 

1. **[SAML 設定の編集]** ダイアログ ページで、次の手順を実行します。
   
    ![Configure single sign-on](./media/keylight-tutorial/405.png) 
   
    a. **[SAML 認証]** を **[アクティブ]** に設定します。

    b. Azure Portal からコピーした **SAML シングル サインオン サービスの URL** を **[Identity Provider Login URL]\(ID プロバイダーのログイン URL\)** ボックスに貼り付けます。

    c. Azure Portal からコピーした **シングル サインアウト サービスの URL** を **[Identity Provider Logout URL]\(ID プロバイダーのログアウト URL\)** ボックスに貼り付けます。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **[Choose File]\(ファイルの選択\)** をクリックし、ダウンロードした LockPath Keylight 証明書を選択して、**[Open]\(開く\)** をクリックして証明書をアップロードします。

    e. **[SAML ユーザー ID の場所]** を **[Subject ステートメントの NameIdentifier 要素]** に設定します。
    
    f. **Keylight サービス プロバイダー**を **https://&lt;会社名&gt;.keylightgrc.com** の形式で指定します。
    
    g. **[ユーザーの自動プロビジョニング]** を **[アクティブ]** に設定します。

    h. **[アカウント タイプの自動プロビジョニング]** を **[すべてのユーザー]** に設定します。

    i. **[Auto-provision security role]\(セキュリティ ロールの自動プロビジョニング\)** を設定し、**[Standard User with SAML]\(SAML を使用する標準ユーザー\)** を選択します。
    
    j. **[Auto-provision security config]\(セキュリティ構成の自動プロビジョニング\)** を設定し、**[Standard User Configuration]\(標準ユーザー構成\)** を選択します。
     
    k. **[Email Attribute]\(電子メール属性\)** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」と入力します。
    
    l. **[First name attribute]\(名属性\)** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`」と入力します。
    
    m. **[Last name attribute]\(姓属性\)** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`」と入力します。
    
    n. **[Save]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 埋め込みドキュメント機能の詳細については、[Azure AD の埋め込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/keylight-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/keylight-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/keylight-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/keylight-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **Create** をクリックしてください。
 
### <a name="creating-a-lockpath-keylight-test-user"></a>LockPath Keylight テスト ユーザーの作成

このセクションでは、LockPath Keylight で Britta Simon というユーザーを作成します。 LockPath Keylight では、Just-In-Time プロビジョニングがサポートされています。この設定は既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ存在しない場合は、LockPath Keylight へのアクセス時に新しいユーザーが作成されます。 

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、[LockPath Keylight クライアント サポート チーム](https://www.lockpath.com/contact/)にお問い合わせください。 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に LockPath Keylight へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**LockPath Keylight に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[LockPath Keylight]** を選択します。

    ![Configure single sign-on](./media/keylight-tutorial/tutorial_keylight_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [LockPath Keylight] タイルをクリックすると、自動的に LockPath Keylight アプリケーションにサインオンします。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/keylight-tutorial/tutorial_general_01.png
[2]: ./media/keylight-tutorial/tutorial_general_02.png
[3]: ./media/keylight-tutorial/tutorial_general_03.png
[4]: ./media/keylight-tutorial/tutorial_general_04.png

[100]: ./media/keylight-tutorial/tutorial_general_100.png

[200]: ./media/keylight-tutorial/tutorial_general_200.png
[201]: ./media/keylight-tutorial/tutorial_general_201.png
[202]: ./media/keylight-tutorial/tutorial_general_202.png
[203]: ./media/keylight-tutorial/tutorial_general_203.png

