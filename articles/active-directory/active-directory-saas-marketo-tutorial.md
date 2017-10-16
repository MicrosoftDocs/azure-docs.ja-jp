---
title: "チュートリアル: Azure Active Directory と Marketo の統合 | Microsoft Docs"
description: "Azure Active Directory と Marketo の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: e146fd5a8075bc9c7ba049b25e5f301fc2645ed9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>チュートリアル: Marketo と Azure Active Directory の統合

このチュートリアルでは、Marketo と Azure Active Directory (Azure AD) を統合する方法について説明します。

Marketo と Azure AD の統合には、次の利点があります。

- Marketo にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Marketo にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Marketo と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Marketo でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Marketo の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-marketo-from-the-gallery"></a>ギャラリーからの Marketo の追加
Azure AD への Marketo の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Marketo を追加する必要があります。

**ギャラリーから Marketo を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「 **Marketo**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_search.png)

5. 結果ウィンドウで **Marketo** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Marketo で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Marketo ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Marketo の関連ユーザーの間で、リンク関係が確立されている必要があります。

Marketo で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Marketo で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Marketo のテスト ユーザーの作成](#creating-a-marketo-test-user)** - Marketo で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Marketo アプリケーションでシングル サインオンを構成します。

**Marketo で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Marketo** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_samlbase.png)

3. **[Marketo のドメインと URL]** セクションで、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_url.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[識別子]** ボックスに、`https://saml.marketo.com/sp` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://login.marketo.com/saml/assertion/\<munchkinid\>` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[Marketo サポート チーム](http://investors.marketo.com/contactus.cfm)に問い合わせてください。
 
4. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_general_400.png)

6. **[Marketo 構成]** セクションで、**[Marketo の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_configure.png) 

7. アプリケーションの Munchkin ID を取得するには、管理者の資格情報を使用して Marketo にログインし、次の操作を実行します。
   
    a. 管理者の資格情報を使用して Marketo アプリにログインします。
   
    b. 上部のナビゲーション ウィンドウで **[管理者]** ボタンをクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. 統合メニューに移動し、**Munchkin リンク**をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_11.png)
   
    d. 画面に表示される Munchkin ID をコピーし、Azure AD の構成ウィザードで、応答 URL を完了します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_12.png) 

8. アプリケーションで SSO を構成するには、以下の手順に従ってください。
   
    a. 管理者の資格情報を使用して Marketo アプリにログインします。
   
    b. 上部のナビゲーション ウィンドウで **[管理者]** ボタンをクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. 統合メニューに移動し、**[シングルサインオン]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_07.png) 
   
    d. SAML 設定を有効にするには、**[編集]** ボタンをクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_08.png) 
   
    e. シングル サインオン設定を**有効**にします。
   
    f. **SAML エンティティ ID** を **[発行者 ID]** ボックスに貼り付けます。
   
    g. **[エンティティ ID]** ボックスに、URL「`http://saml.marketo.com/sp`」を入力します。
   
    h. **[Name Identifier element]\(名前識別子要素\)** としてユーザー ID の場所を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > ユーザー識別子が UPN 値ではない場合、[属性] タブで値を変更します。
   
    i. Azure AD の構成ウィザードからダウンロードした証明書をアップロードします。 設定を**保存**します。
   
    j. ページのリダイレクト設定を編集します。
   
    k. **SAML シングル サインオン サービス URL** を **[ログイン URL]** ボックスに貼り付けます。
   
    l. **サインアウト URL** を **[ログアウト URL]** ボックスに貼り付けます。
   
    m. **[エラー URL]** に **Marketo インスタンス URL** をコピーし、**[保存]** ボタンをクリックして設定を保存します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_10.png)

9. ユーザーの SSO を有効にするには、次の操作を行います。
   
    a. 管理者の資格情報を使用して Marketo アプリにログインします。
   
    b. 上部のナビゲーション ウィンドウで **[管理者]** ボタンをクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. **[セキュリティ]** メニューに移動して、**[ログイン設定]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_13.png)
   
    d. **[SSO 必須]** オプションをオンにして、設定を**保存**します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_14.png)

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-marketo-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-marketo-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-marketo-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-marketo-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-marketo-test-user"></a>Marketo テスト ユーザーの作成

このセクションでは、Marketo で Britta Simon というユーザーを作成します。 Marketo プラットフォームでユーザーを作成するには、次の手順に従ってください。

1. 管理者の資格情報を使用して Marketo アプリにログインします。

2. 上部のナビゲーション ウィンドウで **[管理者]** ボタンをクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 

3. **[セキュリティ]** メニューに移動して、**[ユーザーと役割]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_19.png)  

4. [ユーザー] タブで **[新しいユーザーの追加]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_15.png) 

5. [新しいユーザーの追加] ウィザードで、次の情報を入力します。
   
    a. テキスト ボックスにユーザーの **[メール]** アドレスを入力します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_16.png)
   
    b. テキスト ボックスに **[名]** を入力します。
   
    c. テキスト ボックスに **[姓]** を入力します。
   
    d. **[次へ]** をクリックします

6. **[アクセス許可]** タブで **[userRoles]** を選択して **[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_17.png)
7. **[送信]** ボタンをクリックしてユーザーの招待を送信します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_18.png)

8. ユーザーは、電子メール通知を受け取った後、リンクをクリックしてパスワードを変更し、アカウントをアクティブ化する必要があります。 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Marketo へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Marketo に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Marketo]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Marketo のタイルをクリックすると、自動的に Marketo アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_203.png

