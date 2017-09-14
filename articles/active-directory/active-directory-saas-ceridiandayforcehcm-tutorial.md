---
title: "チュートリアル: Azure Active Directory と Ceridian Dayforce HCM の統合 | Microsoft Docs"
description: "Azure Active Directory と Ceridian Dayforce HCM の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7adf1eb3-d063-45d6-96a8-fd53b329b3f3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: b2ea3d92f233dab5bd6814e4875f881117eac8e3
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>チュートリアル: Azure Active Directory と Ceridian Dayforce HCM の統合

このチュートリアルでは、Ceridian Dayforce HCM と Azure Active Directory (Azure AD) を統合する方法について説明します。

Ceridian Dayforce HCM と Azure AD の統合には、次の利点があります。

- Ceridian Dayforce HCM にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Ceridian Dayforce HCM にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Ceridian Dayforce HCM と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Ceridian Dayforce HCM でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Ceridian Dayforce HCM の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-ceridian-dayforce-hcm-from-the-gallery"></a>ギャラリーからの Ceridian Dayforce HCM の追加
Azure AD への Ceridian Dayforce HCM の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Ceridian Dayforce HCM を追加する必要があります。

**ギャラリーから Ceridian Dayforce HCM を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Ceridian Dayforce HCM**」と入力して、結果パネルから **[Ceridian Dayforce HCM]** を選択し、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の Ceridian Dayforce HCM](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" という名前のテスト ユーザーに基づいて、Ceridian Dayforce HCM で Azure AD のシングル サインオンを構成、テストします。

シングル サインオンを機能させるには、Azure AD が Azure AD のユーザーに対応する Ceridian Dayforce HCM のユーザーを認識している必要があります。 言い換えると、Azure AD ユーザーと Ceridian Dayforce HCM の関連ユーザーの間で、リンク関係が確立されている必要があります。

Ceridian Dayforce HCM で、Azure AD での **[ユーザー名]** の値を **[ユーザー名]** の値として割り当てることによってリンク関係を確立します。

Ceridian Dayforce HCM で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Ceridian Dayforce HCM テスト ユーザーの作成](#create-a-ceridian-dayforce-hcm-test-user)** - Ceridian Dayforce HCM で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Ceridian Dayforce HCM アプリケーションでシングル サインオンを構成します。

**Ceridian Dayforce HCM で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **[Ceridian Dayforce HCM]** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_samlbase.png)

3. **[Ceridian Dayforce HCM Domain and URLs] \(Ceridian Dayforce HCM のドメインと URL)** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_url.png)
    
    a. **[サインオン URL]** ボックスに、ユーザーが Ceridian Dayforce HCM アプリケーションへのサインオンに使用する URL を入力します。
    
    | 環境 | URL |
    | :-- | :-- |
    | 実稼動用 | `https://sso.dayforcehcm.com/<DayforcehcmNamespace>` |
    | テスト用 | `https://ssotest.dayforcehcm.com/<DayforcehcmNamespace>` |
    
    b. **[識別子]** ボックスに、次の形式で URL を入力します。
    
    | 環境 | URL |
    | :-- | :-- |
    | 実稼動用 | `https://ncpingfederate.dayforcehcm.com/sp` |
    | テスト用 | `https://fs-test.dayforcehcm.com/sp` |
    
    c. **[応答 URL]** テキストボックスで、Azure AD が応答を投稿するために使用する URL を入力します。
    
    | 環境 | URL |
    | :-- | :-- |
    | 実稼動用 | `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` |
    | テスト用 | `https://fs-test.dayforcehcm.com/sp/ACS.saml2` |
    
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Ceridian Dayforce HCM クライアント サポート チーム](https://www.ceridian.com/contact-us/index.html)に問い合わせてください。

4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_certificate.png) 

5. Ceridian Dayforce HCM アプリケーションでは、特定の形式での SAML アサーションが必要です。 正しいユーザー識別子を識別するには、まず [Ceridian Dayforce HCM サポート チーム](https://www.ceridian.com/contact-us/index.html)と協力してください。 ユーザー ID として **"name"** 属性を使用することをお勧めします。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 次のスクリーンショットはその例です。  

    ![[シングル サインオンの構成]](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_07.png)

6. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、上の図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名  | 属性値 |
    | --------------- | -------------------- |    
    | name  | user.extensionattribute2 |    

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_attribute_05.png)
    
    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[値]** 一覧で、実装に使用するユーザー属性を選択します。
    たとえば、一意のユーザー識別子として EmployeeID を使用し、その属性値を ExtensionAttribute2 に保存している場合、**[user.extensionattribute2]** を選択します。
    
    d. **[OK]**をクリックします。

7. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_400.png)
    
8. **[Ceridian Dayforce HCM Configuration] \(Ceridian Dayforce HCM 構成)** セクションで、**[Configure Ceridian Dayforce HCM] \(Ceridian Dayforce HCM の構成)** をクリックして **[Configure sign-on] \(サインオンの構成)** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![Ceridian Dayforce HCM の構成](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_configure.png) 

9. **Ceridian Dayforce HCM** 側にシングル サインオンを構成するには、ダウンロードされた**メタデータ XML**、**サインアウト URL、SAML エンティティ ID、および SAML シングル サインオン サービス URL** を [Ceridian Dayforce HCM サポート チーム](https://www.ceridian.com/contact-us/index.html)に送信する必要があります。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="create-a-ceridian-dayforce-hcm-test-user"></a>Ceridian Dayforce HCM テスト ユーザーの作成

このセクションの目的は、Ceridian Dayforce HCM で Britta Simon というユーザーを作成することです。 [Ceridian Dayforce HCM サポート チーム](https://www.ceridian.com/contact-us/index.html)と協力して、Ceridian Dayforce HCM アプリケーションにユーザーが追加されるようにします。 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Ceridian Dayforce HCM へのアクセス権を付与することによって、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Ceridian Dayforce HCMに Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Ceridian Dayforce HCM]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Ceridian Dayforce HCM へのアクセス権を付与することによって、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Ceridian Dayforce HCMに Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Ceridian Dayforce HCM]**を選択します。

    ![アプリケーションの一覧の The Ceridian Dayforce HCM リンク](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。  
アクセス パネルで [Ceridian Dayforce HCM] タイルをクリックすると、自動的に Ceridian Dayforce HCM アプリケーションにサインオンします。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_203.png


