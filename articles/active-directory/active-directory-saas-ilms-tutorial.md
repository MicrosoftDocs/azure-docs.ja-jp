---
title: "チュートリアル: Azure Active Directory と iLMS の統合 | Microsoft Docs"
description: "Azure Active Directory と iLMS の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2017
ms.author: jeedes
ms.openlocfilehash: 22c72020200138e78835ed7dd2661f18b824c785
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-ilms"></a>チュートリアル: Azure Active Directory と iLMS の統合

このチュートリアルでは、iLMS と Azure Active Directory (Azure AD) を統合する方法について説明します。

iLMS と Azure AD の統合には、次の利点があります。

- iLMS にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に iLMS にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

iLMS と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- iLMS でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの iLMS の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-ilms-from-the-gallery"></a>ギャラリーからの iLMS の追加
Azure AD への iLMS の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に iLMS を追加する必要があります。

**ギャラリーから iLMS を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** ボタンをクリックします。

    ![アプリケーション][3]

4. 検索ボックスに「**iLMS**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_search.png)

5. 結果ウィンドウで **[iLMS]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、iLMS で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する iLMS ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと iLMS の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を iLMS の **[Username (ユーザー名)]** の値として割り当てます。

iLMS で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[iLMS テスト ユーザーの作成](#creating-an-ilms-test-user)** - iLMS で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure ポータルで Azure AD のシングル サインオンを有効にし、iLMS アプリケーションでシングル サインオンを構成します。

**iLMS で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure ポータルの **iLMS** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_samlbase.png)

3. **[iLMS のドメインと URL]** セクションで、**IDP 開始モード**でアプリケーションを構成する場合は、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_url.png)

    a. **[識別子]** ボックスに、iLMS 管理ポータルで [SAML settings (SAML 設定)] の **[Service Provider (サービス プロバイダー)]** セクションからコピーした **[Identifier (識別子)]** の値を貼り付けます。

    b. **[応答 URL]** ボックスに、iLMS 管理ポータルで [SAML settings (SAML 設定)] の **[Service Provider (サービス プロバイダー)]** セクションからコピーした次のパターンの **[Endpoint (URL) (エンドポイント (URL))]** の値を貼り付けます。`https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

    >[!Note]
    >この '123456' は、識別子の値の例です。

4. **SP** 開始モードでアプリケーションを構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_url1.png)

    **[サインオン URL]** ボックスに、iLMS 管理ポータルで [SAML settings (SAML 設定)] の **[Service Provider (サービス プロバイダー)]** セクションからコピーした次のパターンの **[Endpoint (URL) (エンドポイント (URL))]** の値を貼り付けます。`https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`     

5. JIT のプロビジョニングを有効にするには、iLMS アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 次のスクリーンショットはその例です。
    
    ![Configure Single Sign-On](./media/active-directory-saas-ilms-tutorial/4.png)
    
    **Department、Region**、および **Division** 属性を作成し、これらの属性の名前を iLMS に追加します。 上記の属性はすべて必須です。  

    > [!NOTE] 
    > これらの属性をマップするには、iLMS で **[Create Un-recognized User Account (未認識のユーザー アカウントの作成)]** を有効にする必要があります。 属性の構成を理解するには、[こちら](http://support.inspiredelearning.com/customer/portal/articles/2204526)の手順に従ってください。

6. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、上の図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | ---------------| --------------- |    
    | division | user.department |
    | region | user.state |
    | department | user.jobtitle |

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_04.png)

    ![Configure Single Sign-On](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_05.png)
    
    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。
    
    c. **[値]** 一覧から、その行に対して表示される値を入力します。
    
    d. **[OK]** をクリックします。

7. **[SAML 署名証明書]** セクションで、**[メタデータ XML]** をクリックし、コンピューターに XML ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_certificate.png) 

8. **[保存]** ボタンをクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-iLMS-tutorial/tutorial_general_400.png)

9. 別の Web ブラウザー ウィンドウで、**iLMS 管理者ポータル**に管理者としてログインします。

10. **[Settings (設定)]** タブの **[SSO:SAML]** をクリックして SAML 設定を開き、次の手順を実行します。
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-ilms-tutorial/1.png) 

    a. **[Service Provider (サービス プロバイダー)]** セクションを展開し、**[Identifier (識別子)]** と **[Endpoint (URL) (エンドポイント (URL))]** の値をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-ilms-tutorial/2.png) 

    b. **[Identity Provider (ID プロバイダー)]** セクションで、**[Import Metadata (メタデータのインポート)]** をクリックします。
    
    c. Azure Portal で **[SAML 署名証明書]** セクションからダウンロードした**メタデータ** ファイルを選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_ssoconfig1.png) 

    d. JIT プロビジョニングを有効にして未認識のユーザーの iLMS アカウントを作成する場合は、次の手順に従います。
        
       - **[Create Un-recognized User Account (未認識のユーザー アカウントの作成)]** をクリックします。
       
       ![Configure Single Sign-On](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_ssoconfig2.png)

       -  Azure AD の属性を ILMS の属性にマップします。 属性欄に、属性名または既定値を指定します。

    e. **[Business Rules (ビジネス ルール)]** タブに移動し、次の手順を実行します。 
        
       ![Configure Single Sign-On](./media/active-directory-saas-ilms-tutorial/5.png)

       - シングル サインオンの時点で存在していないリージョン、事業部、および部署を作成するには、**[Create Un-recognized Regions, Divisions and Departments (未認識のリージョン、事業部、および部署)]** をオンにします。
        
       - シングル サインオンのたびにユーザー プロファイルが更新されるように指定するには、**[Update User Profile During Sign-in (サインイン中にユーザー プロファイルを更新する)]** をオンにします。 
        
       - **[Update Blank Values for Non Mandatory Fields in User Profile (ユーザー プロファイルの必須フィールド以外の空白の値を更新する)]** オプションをオンにした場合、サインイン時に空白であるオプションのプロファイル フィールドによって、ユーザーの iLMS プロファイルのオプションのフィールドが空白の値を含むように更新されます。
        
       - エラー通知メールを受信する場合は、**[Send Error Notification Email (エラー通知メールを送信する)]** をオンにし、ユーザーのメール アドレスを入力します。

11. **[Save (保存)]** ボタンをクリックして、設定を保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-ilms-tutorial/save.png)

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
    
### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-ilms-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-ilms-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-ilms-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-ilms-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-an-ilms-test-user"></a>iLMS テスト ユーザーの作成

アプリケーションでは、ジャストインタイムのユーザー プロビジョニングがサポートされ、認証後にユーザーがアプリケーションに自動的に作成されます。 iLMS 管理ポータルの SAML 構成設定で **[Create Un-recognized User Account (未認識のユーザー アカウントの作成)]** チェックボックスをオンにした場合は、JIT が機能します。

ユーザーを手動で作成する必要がある場合は、以下の手順に従います。

1. iLMS 企業サイトに管理者としてログインします。

2. **[Users (ユーザー)]** タブの**[Register User (ユーザーの登録)]** をクリックして **[Register User (ユーザーの登録)]** ページを開きます。 
   
   ![従業員の追加](./media/active-directory-saas-ilms-tutorial/3.png)

3. **[Register User (ユーザーの登録)]** ページで次の手順を実行します。

    ![従業員の追加](./media/active-directory-saas-ilms-tutorial/create_testuser_add.png)

    a. **[First Name (名)]** ボックスに、ユーザーの名 Britta を入力します。
   
    b. **[Last Name (姓)]** ボックスに、ユーザーの姓 Simon を入力します。

    c. **[Email ID (電子メール ID)]** ボックスに、Britta Simon アカウントの電子メール アドレスを入力します。

    d. **[Region (リージョン)]** ボックスの一覧からリージョンの値を選択します。

    e. **[Division (事業部)]** ボックスの一覧からリージョンの値を選択します。

    f. **[Department (部署)]** ボックスの一覧から部署の値を選択します。

    g. **[Save]**をクリックします。

    > [!NOTE] 
    > **[Send welcome email (ようこそメールの送信)]** チェックボックスをオンにすることで、ユーザーに登録メールを送信できます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に iLMS へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**iLMS に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[iLMS]** を選択します。

    ![Configure Single Sign-On](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [iLMS] タイルをクリックすると、自動的に iLMS アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_203.png

