---
title: "チュートリアル: Azure Active Directory と M-Files の統合 | Microsoft Docs"
description: "Azure Active Directory と M-Files の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 4536fd49-3a65-4cff-9620-860904f726d0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 0f2682cf7cd3e11a5a7156938fbe9d4c7f541312
ms.contentlocale: ja-jp
ms.lasthandoff: 07/04/2017


---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>チュートリアル: Azure Active Directory と M-Files の統合

このチュートリアルでは、M-Files と Azure Active Directory (Azure AD) を統合する方法について説明します。

M-Files と Azure AD の統合には、次の利点があります。

- M-Files にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に M-Files にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

M-Files と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- M-Files でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの M-Files の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-m-files-from-the-gallery"></a>ギャラリーからの M-Files の追加
Azure AD への M-Files の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に M-Files を追加する必要があります。

**ギャラリーから M-Files を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**M-Files**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-m-files-tutorial/tutorial_m-files_search.png)

5. 結果ウィンドウで **[M-Files]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-m-files-tutorial/tutorial_m-files_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、M-Files で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する M-Files ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと M-Files の関連ユーザーの間で、リンク関係が確立されている必要があります。

M-Files で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

M-Files で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[M-Files テスト ユーザーの作成](#creating-a-m-files-test-user)** - M-Files で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、M-Files アプリケーションでシングル サインオンを構成します。

**M-Files で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **M-Files** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-m-files-tutorial/tutorial_m-files_samlbase.png)

3. **[M-Files のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-m-files-tutorial/tutorial_m-files_url.png)

    a. **[サインオン URL]** ボックスに、`https://<tenantname>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<tenantname>.cloudvault.m-files.com` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[M-Files クライアント サポート チーム](mailto:support@m-files.com)に問い合わせてください。 
 
4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-m-files-tutorial/tutorial_m-files_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-m-files-tutorial/tutorial_general_400.png)

6. アプリケーション用に構成された SSO を入手するには、[M-Files サポート チーム](mailto:support@m-files.com)に連絡して、ダウンロードしたメタデータを提供してください。
   
    >[!NOTE]
    >M-File デスクトップ アプリケーション用に SSO を構成する場合は、次の手順に従います。 M-Files の Web バージョン用に SSO を構成するだけの場合は、追加の手順は必要ありません。  

7. 次の手順に従って M-File デスクトップ アプリケーションを構成し、Azure AD の SSO を有効にします。 M-Files をダウンロードするには、[M-Files のダウンロード](https://www.m-files.com/en/download-latest-version)ページに移動します。

8. **[M-Files デスクトップ設定]** ウィンドウを開きます。 **[追加]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_10.png)

9. **[Document Vault Connection Properties] \(資格情報コンテナーの接続プロパティのドキュメント化)** ウィンドウで、次の手順を実行します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_11.png)  

    [サーバー] セクションで、次のように値を入力します。  

    a. **[名前]** は「`<tenant-name>.cloudvault.m-files.com`」と入力します。 
 
    b. **[ポート番号]** は「**4466**」と入力します。 

    c. **[プロトコル]** は **[HTTPS]** を選択します。 

    d. **[認証]** フィールドで、**[特定の Windows ユーザー]** を選択します。 署名の入力を求められます。 Azure AD の資格情報を入力します。 

    e. **[Vault on Server] \(サーバーの資格情報コンテナー)** は、サーバー上の対応する資格情報コンテナーを選択します。
 
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[OK]**をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-m-files-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-m-files-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-m-files-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-m-files-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **[Create]** をクリックします。
 
### <a name="creating-a-m-files-test-user"></a>M-Files のテスト ユーザーの作成

このセクションの目的は、M-Files で Britta Simon というユーザーを作成することです。 M-Files にユーザーを追加するには、[M-Files サポート チーム](mailto:support@m-files.com)と連携します。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に M-Files へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**M-Files に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[M-Files]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-m-files-tutorial/tutorial_m-files_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。

アクセス パネルで M-Files のタイルをクリックすると、自動的に M-Files アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_203.png


