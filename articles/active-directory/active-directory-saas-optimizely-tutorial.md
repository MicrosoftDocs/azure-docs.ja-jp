---
title: "チュートリアル: Azure Active Directory と Optimizely の統合 | Microsoft Docs"
description: "Azure Active Directory と Optimizely の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 28ef03e1-9aad-4301-af97-d94e853edc74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: 4d6f6da6bace09fbd6ab105530a1162653675c99
ms.contentlocale: ja-jp
ms.lasthandoff: 06/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>チュートリアル: Azure Active Directory と Optimizely の統合

このチュートリアルでは、Optimizely と Azure Active Directory (Azure AD) を統合する方法について説明します。

Optimizely と Azure AD の統合には、次の利点があります。

- Optimizely にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが各自の Azure AD アカウントで Optimizely に自動的にサインオン (シングル サインオン) するように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Optimizely の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Optimizely でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Optimizely の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-optimizely-from-the-gallery"></a>ギャラリーからの Optimizely の追加
Azure AD への Optimizely の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Optimizely を追加する必要があります。

**ギャラリーから Optimizely を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「 **Optimizely**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_search.png)

5. 結果ウィンドウで **Optimizely** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Optimizely で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Optimizely ユーザーが Azure AD で認識されている必要があります。 つまり、Azure AD ユーザーと Optimizely の関連ユーザーの間でリンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、Optimizely の **[Username]** の値として割り当てることで確立されます。

Optimizely で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Optimizely のテスト ユーザーの作成](#creating-an-optimizely-test-user)** - Optimizely で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Optimizely アプリケーションでシングル サインオンを構成します。

**Optimizely で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Optimizely** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_samlbase.png)

3. **[Optimizely のドメインと URL]** セクションで、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_url.png)

    a. **[サインオン URL]** ボックスに、`https://app.optimizely.net/<instance name>` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`urn:auth0:optimizely:contoso` のパターンで URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子に値を置き換えます。実際の値については後で説明します。 

4. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-optimizely-tutorial/tutorial_general_400.png)

6. **[Optimizely 構成]** セクションで、**[Optimizely の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_configure.png) 

7. **Optimizely** 側にシングル サインオンを構成するには、Optimizely アカウント マネージャーに連絡し、ダウンロードした **証明書 (Base64)** と **SAML シングル サインオン サービス URL** を提供します。 

8. 電子メールに応じて、Optimizely では、[サインオン URL] \(SP によって開始された SSO) と [識別子] \(サービス プロバイダーのエンティティ ID) の値が提供されます。

    a. Optimizely によって提供された **SP 開始 SSO URL** をコピーし、Azure Portal の **[Optimizely のドメインと URL]** セクションの **[サインオン URL]** ボックスに貼り付けます 

    b. Optimizely によって提供された **サービス プロバイダー エンティティ ID** をコピーし、Azure Portal の **[Optimizely のドメインと URL]** セクションの **[識別子]** ボックスに貼り付けます 

9. 別のブラウザー ウィンドウで、Optimizely アプリケーションにサインオンします。

10. 右上隅のアカウント名をクリックし、 **[アカウント設定]**に移動します。
   
    ![Azure AD のシングル サインオン](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_09.png)

11. [アカウント] タブで、**[概要]** セクションの [シングル サインオン] にある **[Enable SSO (SSO の有効化)]** チェック ボックスをオンにします。
   
    ![Azure AD のシングル サインオン](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_10.png)
    
12. **[保存]**

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-optimizely-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-optimizely-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-optimizely-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-optimizely-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、Britta Simon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="creating-an-optimizely-test-user"></a>Optimizely のテスト ユーザーの作成

このセクションでは、Optimizely で Britta Simon というユーザーを作成します。

1. ホーム ページで、**[コラボレーター]** タブを選択します。

2. 新しいコラボレーターをプロジェクトに追加するには、**[New Collaborator]\(新しいコラボレーター\)** クリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-optimizely-tutorial/create_aaduser_10.png)

3. 電子メール アドレスを入力して、役割を割り当てます。 **[招待]**をクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-optimizely-tutorial/create_aaduser_11.png)

4. 電子メールの招待を受信します。 メール アドレスを使用すると、Optimizely にログインする必要があります。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Optimizely へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようします。

![ユーザーの割り当て][200] 

**Britta Simon を Optimizely に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Optimizely]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Optimizely] タイルをクリックすると、Optimizely アプリケーションに自動的にサインオンします。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_203.png


