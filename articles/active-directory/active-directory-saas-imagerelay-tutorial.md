---
title: "チュートリアル: Azure Active Directory と Image Relay の統合 | Microsoft Docs"
description: "Azure Active Directory と Image Relay の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: 0bfbbaee7a74df6508584b7c8846fd07c2dc15c9
ms.contentlocale: ja-jp
ms.lasthandoff: 06/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>チュートリアル: Azure Active Directory と Image Relay の統合

このチュートリアルでは、Image Relay と Azure Active Directory (Azure AD) を統合する方法について説明します。

Image Relay と Azure AD の統合には、次の利点があります。

- Image Relay にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Image Relay にサインオンする機能 (シングル サインオン) を有効にできます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Image Relay の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Image Relay でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Image Relay の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-image-relay-from-the-gallery"></a>ギャラリーからの Image Relay の追加
Azure AD への Image Relay の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Image Relay を追加する必要があります。

**ギャラリーから ImageRelay を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに「**Image Relay**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_search.png)

5. 結果ウィンドウで **Image Relay** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Image Relay で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Image Relay ユーザーが Azure AD で認識されている必要があります。 つまり、Azure AD ユーザーと Image Relay の関連ユーザーの間でリンク関係が確立されている必要があるということです。

Image Relay で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Image Relay で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Image Relay テスト ユーザーの作成](#creating-an-image-relay-test-user)** - Image Relay で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Image Relay アプリケーションでシングル サインオンを構成します。

**Image Relay で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Image Relay** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_samlbase.png)

3. **[Image Relay のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_url.png)

    a. **[サインオン URL]** ボックスに、`https://<companyname>.imagerelay.com/` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<companyname>.imagerelay.com/sso/metadata` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Image Relay クライアント サポート チーム](http://support.imagerelay.com/)に問い合わせてください。 
 


4. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-imagerelay-tutorial/tutorial_general_400.png)

6. **[Image Relay 構成]** セクションで、**[Image Relay の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト サービス URL と SAML シングル サインオン サービス URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_configure.png) 

7. 別のブラウザー ウィンドウで、管理者として Image Relay 企業サイトにサインインします。

8. 上部にあるツール バーで **[Users & Permissions]** ワークロードをクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_06.png) 

9. **[Create New Permission (新しいアクセス許可を作成)]**をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_08.png)

10. **[Single Sign On Settings]** ワークロードで、**[This Group can only sign-in via Single Sign On]** チェックボックスをオンにして **[Save]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_09.png) 

11. **[Account Settings (アカウントの設定)]**に移動します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_10.png) 

12. **[Single Sign On Settings (シングル サインオンの設定)]** ワークロードに移動します。
    
     ![[シングル サインオンの構成]](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_11.png)

13. **[SAML Settings (SAML の設定)]** ダイアログで、次の手順を実行します。
    
    ![Configure Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_12.png)
    
    a. **[Login URL]\(ログイン URL\)** ボックスに、Azure Portal からコピーした**シングル サインオン サービス URL** の値を貼り付けます。

    b. **[Logout URL]\(ログアウト URL\)** ボックスに、Azure Portal からコピーした**シングル サインアウト サービス URL** の値を貼り付けます。

    c. **[Name Id Format]** として **[urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress]** を選択します。

    d. **[Binding Options for Requests from the Service Provider (Image Relay)]** で **[POST Binding]** を選択します。

    e. **[x.509 Certificate]** の下にある **[Update Certificate]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. ダウンロードした証明書をメモ帳で開き、その内容をコピーして、[x.509 Certificate (x.509 証明書)] ボックスに貼り付けます。

    ![Configure Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. **[Just-In-Time User Provisioning]** で、**[Enable Just-In-Time User Provisioning]** をオンにします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. シングル サインオンによるサインインのみを許可するアクセス許可グループを選択します (**[SSO Basic]** など)。

    ![[シングル サインオンの構成]](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. [ **Save**] をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。


### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="creating-an-image-relay-test-user"></a>Image Relay の テスト ユーザーの作成

このセクションの目的は、Image Relay で Britta Simon というユーザーを作成することです。

**Image Relay で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. Image Relay 企業サイトに管理者としてログインします。

2. **[Users & Permissions]** に移動して **[Create SSO User]** を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_21.png) 

3. プロビジョニングするユーザーの **[Email]\(電子メール\)**、**[First Name]\(名\)**、**[Last Name]\(姓\)**、**[Company]\(会社\)** を入力し、シングル サインオンのみでサインインできるアクセス許可グループ ([SSO Basic]\(SSO Basic\) など) を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_22.png) 

4. **[作成]**をクリックします。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Image Relay へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Image Relay に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Image Relay]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。    

アクセス パネルで [Image Relay] タイルをクリックすると、自動的に Image Relay アプリケーションにサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_04.png


[100]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_203.png


