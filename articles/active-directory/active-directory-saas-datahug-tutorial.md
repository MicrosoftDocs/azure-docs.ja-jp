---
title: "チュートリアル: Azure Active Directory と Datahug の統合 | Microsoft Docs"
description: "Azure Active Directory と Datahug の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 5c0dc1ea-7ff4-4554-b60b-0f2fa9f5abaa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2017
ms.author: jeedes
ms.openlocfilehash: ec431dd5ccfa53e4b975e46da247704dd1e15c2c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-datahug"></a>チュートリアル: Azure Active Directory と Datahug の統合

このチュートリアルでは、Datahug と Azure Active Directory (Azure AD) を統合する方法について説明します。

Datahug と Azure AD の統合には、次の利点があります。

- Datahug にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが Azure AD アカウントで Datahug に自動的にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、 「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Datahug と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Datahug でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Datahug の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-datahug-from-the-gallery"></a>ギャラリーからの Datahug の追加
Azure AD への Datahug の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Datahug を追加する必要があります。

**ギャラリーから Datahug を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**Datahug**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_search.png)

5. 結果ウィンドウで **[Datahug]** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Datahug で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Datahug ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Datahug の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Datahug の **[Username]** の値として割り当てます。

Datahug で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Datahug テスト ユーザーの作成](#creating-a-datahug-test-user)** - Datahug で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Datahug アプリケーションでシングル サインオンを構成します。

**Datahug で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Datahug** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_samlbase.png)

3. **[Datahug のドメインと URL]** セクションで、**IDP 開始モード**でアプリケーションを構成する場合は、次の手順に従います。

    ![Configure Single Sign-On](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_ur1.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[識別子]** ボックスに、`https://apps.datahug.com/identity/<uniqueID>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://apps.datahug.com/identity/<uniqueID>/acs` のパターンを使用して URL を入力します。

4. **[詳細な URL 設定の表示]** をクリックします。 **SP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_url2.png)

    **[サインオン URL]** ボックスに、「`https://apps.datahug.com/`」と入力します。
     
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 ここでは、識別子と応答 URL に一意の文字列値を使用することをお勧めします。 これらの値を取得するには、[Datahug クライアント サポート チーム](http://datahug.com/about/contact-us/)に連絡してください。 

5. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_certificate.png) 

6.  **[証明書署名の詳細設定を表示する]** チェック ボックスをオンにし、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_cert.png)

    a. **[署名オプション]** で **[SAML アサーションへの署名]** を選択します。
    
    b. **[署名アルゴリズム]** で **[SHA-1]** を選択します。
 
7. **[保存]** ボタンをクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-datahug-tutorial/tutorial_general_400.png)
    
8. **[Datahug Configuration (Datahug 構成)]** セクションで、**[Configure Datahug (Datahug を構成する)]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML エンティティ ID** と **SAML シングル サインオン サービス URL** をコピーします。

    ![Configure Single Sign-On](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_configure.png) 

9. **Datahug** 側でシングル サインオンを構成するには、ダウンロードした**メタデータ XML**、**SAML エンティティ ID**、**SAML シングル サインオン サービス URL** を [Datahug サポート](http://datahug.com/about/contact-us/)に送る必要があります。 両方の側で SAML SSO 接続を正しく設定するために、サポート チームがこのアプリケーションをセットアップします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-datahug-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-datahug-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-datahug-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-datahug-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-datahug-test-user"></a>Datahug テスト ユーザーの作成

Azure AD ユーザーが Datahug にログインできるようにするには、ユーザーを Datahug にプロビジョニングする必要があります。  
Datahug の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Datahug 企業サイトに管理者としてログインします。

2. 右上隅の**歯車**をポイントし、**[Settings]** をクリックします。
   
   ![従業員の追加](./media/active-directory-saas-datahug-tutorial/1.png)

3. **[People]** を選択し、**[Add Users]** タブをクリックします。

    ![従業員の追加](./media/active-directory-saas-datahug-tutorial/2.png)

4. アカウントを作成するユーザーの電子メール アドレスを入力し、**[Add]** をクリックします。

    ![従業員の追加](./media/active-directory-saas-datahug-tutorial/3.png)

    > [!NOTE] 
    > **[Send welcome email]** チェック ボックスをオンにすると、ユーザーに登録メールを送信できます。  
    > Salesforce のアカウントを作成する場合は、ウェルカム メールを送信しないでください。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Datahug へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Datahug に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Datahug]** を選択します。

    ![Configure Single Sign-On](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。
アクセス パネルで [Datahug] タイルをクリックすると、自動的に Datahug アプリケーションにサインオンします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_203.png

