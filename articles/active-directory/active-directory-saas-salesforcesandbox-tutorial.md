---
title: "チュートリアル: Azure Active Directory と Salesforce Sandbox の統合 | Microsoft Docs"
description: "Azure Active Directory と Salesforce Sandbox の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 90e08b9cf2feb93de4877bec9734352949896dca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>チュートリアル: Azure Active Directory と Salesforce Sandbox の統合

このチュートリアルでは、Salesforce Sandbox と Azure Active Directory (Azure AD) を統合する方法について説明します。

Salesforce Sandbox と Azure AD の統合には、次の利点があります。

- Salesforce Sandbox にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Salesforce Sandbox にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Salesforce Sandbox の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Salesforce Sandbox でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Salesforce Sandbox の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>ギャラリーからの Salesforce Sandbox の追加
Azure AD への Salesforce Sandbox の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Salesforce Sandbox を追加する必要があります。

**ギャラリーから Salesforce Sandbox を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. ダイアログの上部にある **[新しいアプリケーション]** ボタンをクリックします。

    ![アプリケーション][3]

4. 検索ボックスに「**Salesforce Sandbox**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_search.png)

5. 結果ウィンドウで **[Salesforce Sandbox]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Salesforce Sandbox で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Salesforce Sandbox ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Salesforce Sandbox の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、Salesforce Sandbox の **[ユーザー名]** の値として割り当てることで確立されます。

Salesforce Sandbox で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Salesforce Sandbox テスト ユーザーの作成](#creating-a-salesforce-sandbox-test-user)** - Salesforce Sandbox で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Salesforce Sandbox アプリケーションでシングル サインオンを構成します。

**Salesforce Sandbox で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Salesforce Sandbox** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_samlbase.png)

3. **[Salesforce Sandbox のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_url.png)

     **[サインオン URL]** ボックスに、`https://<subdomain>.my.salesforce.com` のパターンを使用して値を入力します。

    > [!NOTE] 
    > これは実際の値ではありません。 この値は実際のサインオン URL に変更します。この値の取得方法については、[Salesforce Sandbox クライアント サポート チーム](https://help.salesforce.com/support)にお問い合わせください。


4. ディレクトリで別の Salesforce Sandbox インスタンスのシングル サイオンを既に構成している場合は、**[サインオン URL]** と同じ値を使用するように **[識別子]** も構成する必要があります。 
    
    >[!Note]
    >**[識別子]** フィールドは、ダイアログの **[アプリケーション URL の構成]** ページで **[詳細設定の表示]** チェック ボックスをオンにすることで表示できます。 


5. **[SAML 署名証明書]** セクションで、**[証明書]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_certificate.png) 

6. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_400.png)

7. **[Salesforce Sandbox 構成]** セクションで、**[Salesforce Sandbox を構成する]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**SAML エンティティ ID と SAML シングル サインオン サービス URL** をコピーします。

    ![シングル サインオンの構成](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_configure.png) 
<CS>
8. ブラウザーで新しいタブを開き、Salesforce Sandbox の管理者アカウントにログインします。

9. 上部のメニューで **[セットアップ]**をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-salesforcesandbox-tutorial/IC781024.png)
10. 左側のナビゲーション ウィンドウで、**[セキュリティ コントロール]** をクリックし、**[シングル サインオンの設定]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-salesforcesandbox-tutorial/IC781025.png)
11. [シングル サインオンの設定] セクションで、「![シングル サインオンの構成](./media/active-directory-saas-salesforcesandbox-tutorial/IC781026.png)」の手順に従います。
     
     a.  **[SAML Enabled]**を選択します。 

     b.  **[新規]**をクリックします。

12. [SAML シングル サインオンの設定] セクションで、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-salesforcesandbox-tutorial/IC781027.png)

    a. [名前] ボックスに、構成の名前を入力します (例: *SPSSOWAAD\_Test*)。 

    b. **[発行者]** テキストボックスに **[SMAL エンティティ ID]** 値を貼り付けます。

    c. これがディレクトリに追加する最初の Salesforce Sandbox インスタンスの場合は、**[エンティティ ID]** ボックスに「**https://test.salesforce.com**」と入力します。 Salesforce Sandbox のインスタンスを既に追加している場合は、**[Entity Id (エンティティ ID)]** に次の形式の**サインオン URL** を入力します。`http://company.my.salesforce.com`  
 
    d. **[参照]** をクリックして、ダウンロードした証明書をアップロードします。  

    e. **[SAML ID の種類]** として、**[アサーションにはユーザー オブジェクトからのフェデレーション ID が含まれます]** を選択します。
 
    f. **[SAML Identity Location (SAML ID の場所)]** で、**[Identity is in the NameIdentifier element of the Subject statement (ID は Subject ステートメントの NameIdentifier 要素にあります)]** を選択します。

    g. **[Identity provider login URL]\(ID プロバイダー ログイン URL\)** テキストボックスに **[シングル サインオン サービス URL]** を貼り付けます。 

    h. SFDC は SAML ログアウトをサポートしていません。  回避策として、**[Identity Provider Logout URL] \(ID プロバイダー ログアウト URL)** ボックスに「https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0」を貼り付けます。

    i. **[サービス プロバイダーが開始した要求のバインド]** で **[HTTP POST]** を選択します。 

    j. [ **Save**] をクリックします。

### <a name="enable-your-domain"></a>ドメインの有効化
このセクションでは、ドメインが既に作成されていることを前提としています。  詳細については、「[ドメイン名の定義](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US)」をご覧ください。

**ドメインを有効にするには、次の手順に従います。**

1. 左側のナビゲーション ウィンドウで、**[ドメイン管理]** をクリックし、**[マイ ドメイン]** をクリックします。
   
     ![[シングル サインオンの構成]](./media/active-directory-saas-salesforcesandbox-tutorial/IC781029.png)
   
   >[!NOTE]
   >ドメインが正しく構成されていることを確認します。 

2. **[Login Page Settings (ログイン ページ設定)]** セクションで、**[編集]** をクリックし、**[認証サービス]** で前のセクションの [SAML シングル サインオンの設定] の名前を選択して、最後に **[保存]** をクリックします。
   
   ![[シングル サインオンの構成]](./media/active-directory-saas-salesforcesandbox-tutorial/IC781030.png)

ドメインを構成するとすぐに、ユーザーはドメインの URL を使用して Salesforce Sandbox にログインできるようになります。  

URL の値を取得するには、前のセクションで作成した SSO プロファイルをクリックします。    

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。


### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_01.png) 

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-salesforce-sandbox-test-user"></a>Salesforce Sandbox テスト ユーザーを作成する

このセクションでは、Britta Simon というユーザーを Salesforce Sandbox に作成します。 Salesforce Sandbox では、Just-In-Time プロビジョニングがサポートされています。この設定は既定で有効になっています。
このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ Salesforce Sandbox に存在しない場合は、Salesforce Sandbox にアクセスしようとしたときに新しいユーザーが作成されます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Salesforce Sandbox へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Salesforce Sandbox に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Salesforce Sandbox]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

SSO の設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)
* [[ユーザー プロビジョニングの構成]](active-directory-saas-salesforce-sandbox-provisioning-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_203.png

