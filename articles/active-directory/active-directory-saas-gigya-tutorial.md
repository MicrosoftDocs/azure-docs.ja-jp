---
title: "チュートリアル: Azure Active Directory と Gigya の統合 | Microsoft Docs"
description: "Azure Active Directory と Gigya の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 2c7d200b-9242-44a5-ac8a-ab3214a78e41
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: b65a33989a045a3e0b57fda522a9bc3b0770c7f3
ms.contentlocale: ja-jp
ms.lasthandoff: 06/20/2017


---
# <a name="tutorial-azure-active-directory-integration-with-gigya"></a>チュートリアル: Azure Active Directory と Gigya の統合

このチュートリアルでは、Gigya と Azure Active Directory (Azure AD) を統合する方法について説明します。

Gigya と Azure AD の統合には、次の利点があります。

- Gigya にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に Gigya にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Gigya と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Gigya でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Gigya の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-gigya-from-the-gallery"></a>ギャラリーからの Gigya の追加
Azure AD への Gigya の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Gigya を追加する必要があります。

**ギャラリーから Gigya を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**Gigya**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-gigya-tutorial/tutorial_gigya_search.png)

5. 結果ウィンドウで **Gigya** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-gigya-tutorial/tutorial_gigya_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Gigya で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Gigya ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Gigya の関連ユーザーの間で、リンク関係が確立されている必要があります。

Gigya で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Gigya で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Gigya テスト ユーザーの作成](#creating-a-gigya-test-user)** - Gigya で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Gigya アプリケーションでシングル サインオンを構成します。

**Gigya で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Gigya** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-gigya-tutorial/tutorial_gigya_samlbase.png)

3. **[Gigya のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-gigya-tutorial/tutorial_gigya_url.png)

    a. **[サインオン URL]** ボックスに、`http://<companyname>.gigya.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://fidm.gigya.com/saml/v2.0/<companyname>` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Gigya クライアント サポート チーム](https://www.gigya.com/support-policy/)に問い合わせてください。 
 
4. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-gigya-tutorial/tutorial_gigya_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-gigya-tutorial/tutorial_general_400.png)

6. **[Gigya 構成]** セクションで、**[Gigya の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML エンティティ ID と SAML シングル サインオン サービス URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-gigya-tutorial/tutorial_gigya_configure.png) 

7. 別の Web ブラウザー ウィンドウで、Gigya 企業サイトに管理者としてログインします。

8. **[設定] \> [SAML ログイン]** の順にクリックし、**[追加]** をクリックします。
   
    ![SAML ログイン](./media/active-directory-saas-gigya-tutorial/ic789532.png "SAML ログイン")

9. **[SAML ログイン]** セクションで、次の手順を実行します。
   
    ![SAML の構成](./media/active-directory-saas-gigya-tutorial/ic789533.png "SAML の構成")
   
    a. **[名前]** テキスト ボックスに、構成の名前を入力します。
   
    b. **[Issuer]\(発行者\)** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。 
   
    c. **[Single Sign-On Service URL]\(シングル サインオン サービス URL\)** ボックスに、Azure Portal からコピーした**シングル サインオン サービス URL** の値を貼り付けます。
   
    d. **[Name ID Format]\(名前 ID 形式\)** ボックスに、Azure Portal からコピーした**名前識別子形式**の値を貼り付けます。
   
    e. Azure Portal からダウンロードした Base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーしてから、それを **[X.509 証明書]** ボックスに貼り付けます。
   
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[設定の保存]**をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-gigya-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-gigya-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-gigya-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-gigya-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="creating-a-gigya-test-user"></a>Gigya テスト ユーザーの作成

Azure AD ユーザーが Gigya にログインできるようにするには、ユーザーを Gigya にプロビジョニングする必要があります。  
Gigya の場合、プロビジョニングは手動で行います。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順に従います。

1. **Gigya** 企業サイトに管理者としてログインします。

2. **[管理者] \> [ユーザーの管理]** の順にクリックし、**[ユーザーの招待]** をクリックします。
   
    ![Manage Users](./media/active-directory-saas-gigya-tutorial/ic789535.png "Manage Users")

3. [ユーザーの招待] ダイアログで、次の手順を実行します。
   
    ![ユーザーの招待](./media/active-directory-saas-gigya-tutorial/ic789536.png "ユーザーの招待")
   
    a. **[電子メール]** テキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの電子メール エイリアスを入力します。
    
    b. **[ユーザーの招待]**をクリックします。
      
    > [!NOTE]
    > Azure Active Directory のアカウント所有者には、アカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。
    > 
    

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Gigya へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Gigya に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Gigya]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-gigya-tutorial/tutorial_gigya_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。

アクセス パネルで Gigya のタイルをクリックすると、自動的に Gigya アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_203.png


