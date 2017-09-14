---
title: "チュートリアル: Azure Active Directory と Cezanne HR ソフトウェアの統合 | Microsoft Docs"
description: "Azure Active Directory と Cezanne HR ソフトウェアの間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: fb8f95bf-c3c1-4e1f-b2b3-3b67526be72d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 623c438edfce5f98c2d32d8bb25a97d86aa77909
ms.contentlocale: ja-jp
ms.lasthandoff: 08/10/2017

---
# <a name="tutorial-integrate-azure-active-directory-with-cezanne-hr-software"></a>チュートリアル: Azure Active Directory と Cezanne HR ソフトウェアの統合

このチュートリアルでは、Cezanne HR ソフトウェアと Azure Active Directory (Azure AD) を統合する方法について説明します。

Cezanne HR ソフトウェアと Azure AD の統合には、次の利点があります。 次のことが行えます。

- Cezanne HR ソフトウェアにアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントを使用して、シングル サインオン (SSO) で自動的に Cezanne HR ソフトウェアにサインインできるように設定できます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、[Azure Active Directory のアプリケーション アクセスと SSO ](active-directory-appssoaccess-whatis.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

Cezanne HR ソフトウェアと Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Cezanne HR ソフトウェアでの SSO が有効なサブスクリプション

> [!NOTE]
> このチュートリアル手順のテストには、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従います。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD SSO をテストします。 

このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

* ギャラリーからの Cezanne HR ソフトウェアの追加
* Azure AD SSO の構成とテスト

## <a name="add-cezanne-hr-software-from-the-gallery"></a>ギャラリーからの Cezanne HR ソフトウェアの追加
Azure AD への Cezanne HR ソフトウェアの統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Cezanne HR ソフトウェアを追加します。

ギャラリーから Cezanne HR ソフトウェアを追加するには、次の手順に従います。

1. **[Azure Portal](https://portal.azure.com)** の左側のウィンドウで、**Azure Active Directory** のボタンを選択します。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** > **[すべてのアプリケーション]** の順に選択します。

    ![[すべてのアプリケーション] リンク][2]
    
3. 新しいアプリケーションを追加するには、**[すべてのアプリケーション]** ダイアログ ボックスの上部で **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに、「**Cezanne HR Software**」と入力します。

    ![検索ボックス](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_search.png)

5. 結果リストで **[Cezanne HR Software]** を選択し、**[追加]** ボタンを選択してアプリケーションを追加します。

    ![結果リスト](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Cezanne HR ソフトウェアで Azure AD の SSO を構成し、テストします。

SSO を機能させるには、Azure AD ユーザーに対応する Cezanne HR ソフトウェア ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Cezanne HR ソフトウェアの関連ユーザーの間で、リンク関係を確立する必要があるということです。

リンク関係を確立するには、Cezanne HR ソフトウェアの**ユーザー名**の値を Azure AD の**ユーザー名**の値として割り当てます。

Cezanne HR ソフトウェアを使用して Azure AD の SSO を構成してテストするには、次の構成要素を完了します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

このセクションでは、次の手順で Azure Portal で Azure AD SSO を有効にし、Cezanne HR ソフトウェア アプリケーションに SSO を構成します。

1. Azure Portal の **Cezanne HR Software** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![[シングル サインオン] コマンド][4]

2. SSO を有効にするには、**[シングル サインオン]** ダイアログ ボックスの **[モード]** ボックスで **[SAML ベースのサインオン]** を選択します。
 
    ![[モード] ボックス](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_samlbase.png)

3. **[Cezanne HR Software のドメイン と URL]** で、次の操作を行います。

    ![[Cezanne HR Software のドメイン と URL] セクション](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_url.png)

    a. **[サインオン URL]** ボックスに、`https://w3.cezanneondemand.com/cezannehr/-/<tenant id>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://w3.cezanneondemand.com:443/<tenantid>` の形式で URL を入力します。    
     
    > [!NOTE] 
    > 上記の値は、実際の値ではありません。 実際の応答 URL とサインオン URL でこれらの値を更新します。 値を入手するには、[Cezanne HR ソフトウェアのクライアント サポート チーム](mailto:info@cezannehr.com)にお問い合わせください。

4. **[SAML 署名証明書]** で、**[証明書 (Base64)]** を選択し、コンピューターに証明書ファイルを保存します。

    ![[SAML 署名証明書] セクション](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_certificate.png) 

5. [ **保存**] を選択します。

    ![[保存] ボタン](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_400.png)
    
6. **[Cezanne HR Software 構成]** セクションで **[Configure Cezanne HR Software]\(Cezanne HR Software を構成する\)** を選択して、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**SAML エンティティ ID** と **SAML シングル サインオン サービス** URL をコピーします。

    ![[Cezanne HR Software 構成] セクション](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure.png) 

7. 別の Web ブラウザーのウィンドウで、管理者として Cezanne HR ソフトウェア テナントにサインオンします。

8. 左側のウィンドウで、**[システム セットアップ]** を選択します。 **[セキュリティ設定]** > **[シングル サインオン構成]** の順に選択します。

    ![[シングル サインオン構成] リンク](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

9. **[Allow users to log in using the following Single Sign-On (SSO) Service]\(ユーザーに次のシングル サインオン (SSO) サービスを使用したログインを許可する\)** ウィンドウで **[SAML 2.0]** チェック ボックスを選択して、**[詳細構成]** オプションを選択します。

    ![シングル サインオン サービスのオプション](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

10. **[新規追加]** を選択します。

    ![[新規追加] ボタン](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

11. **[SAML 2.0 ID プロバイダー]**で、次の操作を行います。

    ![[SAML 2.0 ID プロバイダー] セクション](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
    
    a. **[表示名]** ボックスに ID プロバイダー名を入力します。

    b. **[エンティティ ID]** ボックスに、Azure Portal でコピーした **SAML エンティティ ID** を貼り付けます。 

    c. **[SAML Binding]\(SAML バインド\)** リスト ボックスで、**[POST]** を選択します。

    d. **[セキュリティ トークン サービス エンドポイント]** ボックスで、Azure Portal からコピーした **SAML シングル サインオン サービス**の URL を貼り付けます。 
    
    e. **[ユーザー ID の属性名]** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`」と入力します。
    
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 Azure AD からダウンロードした証明書をアップロードするには、**[アップロード]** を選択します。
    
    g. **[OK]**を選択します。 

12. [ **保存**] を選択します。

    ![[保存] ボタン](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

> [!TIP]
> アプリのセットアップ中、上記手順の簡易版を [Azure Portal](https://portal.azure.com)でご覧いただけます。 このアプリを追加した後、**[Active Directory]** > **[エンタープライズ アプリケーション]** セクションで、**[シングル サインオン]** タブを選びます。 次に、**[構成]** セクションから組み込みドキュメントにアクセスします。 

組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションでは、Azure Portal で Britta Simon というテスト ユーザーを作成します。

![テスト ユーザー Britta Simon][100]

Azure AD でテスト ユーザーを作成するには、次の手順に従います。

1. **Azure Portal** の左側のウィンドウで、**Azure Active Directory** のボタンを選択します。

    ![Azure Active Directory のボタン](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_01.png) 

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** > **[すべてのユーザー]** の順に選択します。
    
    ![[すべてのユーザー] リンク](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_02.png) 
    
    **[すべてのユーザー]** ダイアログ ボックスが表示されます。

3. **[追加]** を選択して **[ユーザー]** ダイアログ ボックスを開きます。
 
    ![[追加] ボタン](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ボックスで、次の手順を実行します。
 
    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の**メール アドレス**を入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに生成された値を書き留めます。

    d. **[作成]**を選択します。
 
### <a name="create-a-cezanne-hr-software-test-user"></a>Cezanne HR ソフトウェアのテスト ユーザーの作成

Azure AD ユーザーが Cezanne HR ソフトウェアにサインインできるようにするには、ユーザーを Cezanne HR ソフトウェアにプロビジョニングする必要があります。 Cezanne HR ソフトウェアの場合、プロビジョニングは手動で行います。

ユーザー アカウントをプロビジョニングするには、次の手順に従います。

1.  Cezanne HR ソフトウェア企業サイトに管理者としてサインインします。

2.  左のウィンドウで、**[システム セットアップ]** > **[ユーザーの管理]** > **[新しいユーザーの追加]** の順に選択します。

    ![[新しいユーザーの追加] リンク](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "新しいユーザー")

3.  **[個人の詳細]** で、次の手順を実行します。

    ![[個人の詳細] セクション](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "新しいユーザー")
    
    a. **[内部ユーザー]** を**オフ**に設定します。
    
    b. **[名]** ボックスに、ユーザーの名前を入力します (この例では、「**Britta**」)。  
 
    c. **[姓]** ボックスに、ユーザーの名字を入力します (この例では、「**Simon**」)。
    
    d. **[電子メール]** ボックスに、ユーザーの電子メール アドレス (たとえば Brittasimon@contoso.com) を入力します。

4.  **[アカウント情報]** で、次の手順を実行します。

    ![[アカウント情報] セクション](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "新しいユーザー")
    
    a. **[ユーザー名]** ボックスに、ユーザーのメール アドレス (たとえば Brittasimon@contoso.com) を入力します。
    
    b. **[パスワード]** ボックスには、ユーザーのパスワードを入力します。
    
    c. **[セキュリティ ロール]** で **[HR Professional]\(人事担当者\)** を選択します。
    
    d. **[OK]**を選択します。

5. **[シングル サインオン]** タブで、**[SAML 2.0 Identifiers]\(SAML 2.0 識別子\)** 領域で **[新規追加]** を選択します。

    ![[新規追加] ボタン](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "ユーザー")

6. **[ID プロバイダー]** リスト ボックスで、ID プロバイダーを選択します。 **[ユーザー識別子]** ボックスで、テスト ユーザー Britta Simon のアカウントの電子メール アドレスを入力します。

    ![[ID プロバイダー] と [ユーザー識別子] ボックス](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "ユーザー")
    
7. [ **保存**] を選択します。

    ![[保存] ボタン](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "ユーザー")

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、テスト ユーザー Britta Simon に Cezanne HR ソフトウェアへのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![テスト ユーザー アクセス][200] 

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。 **[エンタープライズ アプリケーション]** > **[すべてのアプリケーション]** の順に選択します。

    ![[すべてのアプリケーション] リンク][201] 

2. アプリケーションの一覧で **[Cezanne HR Software]**を選択します。

    !["アプリケーション" の一覧](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![ユーザーの割り当て][202] 

4. **[追加]**を選択します。 次に、**[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] リンク][203]

5. **[ユーザーとグループ]** ダイアログ ボックスの **[ユーザー]** 一覧から、**[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログ ボックスで、**[選択]** を選択します。

7. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。
    
### <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD SSO の構成をテストします。

アクセス パネルで [Cezanne HR ソフトウェア] タイルを選択すると、自動的に Cezanne HR ソフトウェア アプリケーションにサインオンします。

## <a name="next-steps"></a>次のステップ

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png


