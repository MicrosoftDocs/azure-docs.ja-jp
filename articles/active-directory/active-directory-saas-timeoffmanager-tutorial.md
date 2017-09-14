---
title: "チュートリアル: Azure Active Directory と TimeOffManager の統合 | Microsoft Docs"
description: "Azure Active Directory と TimeOffManager の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3685912f-d5aa-4730-ab58-35a088fc1cc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 99523f27fe43f07081bd43f5d563e554bda4426f
ms.openlocfilehash: 3f944ffbf704694b293b4b1e5bdb4f2c93ae35a1
ms.contentlocale: ja-jp
ms.lasthandoff: 08/05/2017

---
# <a name="tutorial-azure-active-directory-integration-with-timeoffmanager"></a>チュートリアル: Azure Active Directory と TimeOffManager の統合

このチュートリアルでは、TimeOffManager と Azure Active Directory (Azure AD) を統合する方法について説明します。

TimeOffManager と Azure AD の統合には、次の利点があります。

- TimeOffManager にアクセスする Azure AD ユーザーを制御できます
- ユーザーが各自の Azure AD アカウントで TimeOffManager に自動的にサインオン (シングル サインオン) するように、設定が可能です
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

TimeOffManager と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- TimeOffManager でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの TimeOffManager の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="add-timeoffmanager-from-the-gallery"></a>ギャラリーからの TimeOffManager の追加
Azure AD への TimeOffManager の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に TimeOffManager を追加する必要があります。

**ギャラリーから TimeOffManager を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに「**TimeOffManager**」と入力し、結果ウィンドウで **[TimeOffManager]** を選び、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![ギャラリーから追加する](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、TimeOffManager で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する TimeOffManager ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと TimeOffManager の関連ユーザーの間で、リンク関係が確立されている必要があります。

TimeOffManager で、Azure AD の **[ユーザー名]** の値を **[ユーザー名]** の値として割り当ててリンク関係を確立します。

TimeOffManager で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[TimeOffManager のテスト ユーザーの作成](#create-a-timeoffmanager-test-user)** - TimeOffManager で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、TimeOffManager アプリケーションでシングル サインオンを構成します。

**TimeOffManager で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **TimeOffManager** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![SAML ベースのサインオン](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_samlbase.png)

3. **[TimeOffManager のドメインと URL]** セクションで、次の手順を実行します。

     ![[TimeOffManager のドメインと URL] セクション](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_url.png)

    **[応答 URL]** ボックスに、`https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company_id=<companyid>` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これは実際の値ではありません。 実際の応答 URL でこの値を更新します。 チュートリアルの後半で説明する**シングル サインオン設定ページ**からこの値を取得するか、[TimeOffManager サポート チーム](http://www.timeoffmanager.com/contact-us.aspx)に連絡してください。
 
4. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[SAML 署名証明書] セクション](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_certificate.png) 

5. このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで TimeOffManager に対する認証を行うことができるようにする方法を説明します。
    
    TimeOffManger アプリケーションでは、特定の形式の SAML アサーションが求められます。そのため、カスタム属性マッピングを SAML トークン属性構成に追加する必要があります。 次のスクリーンショットはその例です。

    ![saml token attributes](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_attrb.png "saml token attributes")
    
    | 属性名 | 属性値 |
    | --- | --- |
    | Firstname |User.givenname |
    | Lastname |User.surname |
    | 電子メール |User.mail |
    
    a.この問題では、ターゲット (またはクラス) ラベルは "tip_amount" です。  上記の表の各データ行で、 **[ユーザー属性の追加]**をクリックします。
    
    ![saml token attributes](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_addattrb.png "saml token attributes")
    
    ![saml token attributes](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_addattrb1.png "saml token attributes")
    
    b.  **[属性名]** ボックスに、その行に対して表示される属性名を入力します。
    
    c.  **[属性値]** テキスト ボックスで、その行に対して表示される属性値を選択します。
    
    d.  **[OK]**をクリックします。
    
6. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_400.png)

7. **[TimeOffManager 構成]** セクションで、**[Configure TimeOffManager]\(TimeOffManager を構成する\)** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![TimeOffManager 構成セクション](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_configure.png) 

8. 別の Web ブラウザーのウィンドウで、管理者として TimeOffManager 企業サイトにログインします。

9. **[Account] \> [Account Options] \> [Single Sign-On Settings]** に移動します。
   
   ![Single Sign-On Settings](./media/active-directory-saas-timeoffmanager-tutorial/ic795917.png "Single Sign-On Settings")
7. **[シングル サインオンの設定]** セクションで、次の手順に従います。
   
   ![Single Sign-On Settings](./media/active-directory-saas-timeoffmanager-tutorial/ic795918.png "Single Sign-On Settings")
   
   a. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、証明書全体を **[X.509 Certificate]** テキスト ボックスに貼り付けます。
   
   b. **[IdP Issuer]\(IdP 発行者\)** テキスト ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。
   
   c. **[IdP Endpoint URL]\(IdP エンドポイント URL\)** テキスト ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。
   
   d. **[Enforce SAML]** で **[No]** を選択します。
   
   e. **[Auto-Create Users]** で **[Yes]** を選択します。
   
   f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[Logout URL]\(ログアウト URL\)** ボックスに、Azure Portal からコピーした **サインアウト URL** の値を貼り付けます。
   
   g. **[変更を保存]** をクリックします。

11. **[シングル サインオン設定]** ページで **[Assertion Consumer Service URL]** の値をコピーして、それを Azure Portal の **[TimeOffManager のドメインと URL]** セクション下で **[応答 URL]** テキスト ボックスに貼り付けます。 

      ![Single Sign-On Settings](./media/active-directory-saas-timeoffmanager-tutorial/ic795915.png "Single Sign-On Settings")

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-timeoffmanager-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![[ユーザーとグループ] --> [すべてのユーザー]](./media/active-directory-saas-timeoffmanager-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![[追加] ボタン](./media/active-directory-saas-timeoffmanager-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![[ユーザー] ダイアログ ページ](./media/active-directory-saas-timeoffmanager-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **[作成]**をクリックします。
 
### <a name="create-a-timeoffmanager-test-user"></a>TimeOffManager のテスト ユーザーの作成

Azure AD ユーザーが TimeOffManager にログインできるようにするには、そのユーザーを TimeOffManager にプロビジョニングする必要があります。  

TimeOffManager は、ジャストインタイム ユーザー プロビジョニングをサポートしています。 ユーザー側で必要な操作はありません。  

シングル サインオンを使用して初めてログインするときに、ユーザーが自動的に追加されます。

>[!NOTE]
>TimeOffManager から提供されている他の TimeOffManager ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。
> 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に TimeOffManager へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**TimeOffManager に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **TimeOffManager** を選択します。

    ![アプリ一覧の TimeOffManager](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [TimeOffManager] タイルをクリックすると、TimeOffManager アプリケーションに自動的にサインオンします。 アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_203.png


