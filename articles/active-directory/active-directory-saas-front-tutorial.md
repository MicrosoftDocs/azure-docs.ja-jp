---
title: "チュートリアル: Azure Active Directory と Front の統合 | Microsoft Docs"
description: "Azure Active Directory と Front の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 88270b6d-2571-434a-b139-b6ccc3a2b19f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: d936bc50a66ac2a3c17038ff08351edf9902c99f
ms.contentlocale: ja-jp
ms.lasthandoff: 07/26/2017

---
# <a name="tutorial-azure-active-directory-integration-with-front"></a>チュートリアル: Azure Active Directory と Front の統合

このチュートリアルでは、Front と Azure Active Directory (Azure AD) を統合する方法について説明します。

Front と Azure AD の統合には、次の利点があります。

- Front にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Front にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Front と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Front でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Front の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-front-from-the-gallery"></a>ギャラリーからの Front の追加
Azure AD への Front の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Front を追加する必要があります。

**ギャラリーから Front を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Front**」と入力し、結果パネルで **[Front]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Front](./media/active-directory-saas-front-tutorial/tutorial_front_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Front で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Front ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Front の関連ユーザーの間で、リンク関係が確立されている必要があります。

Front で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Front で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Front テスト ユーザーの作成](#create-a-front-test-user)** - Front で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Front アプリケーションでシングル サインオンを構成します。

**Front で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Front** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-front-tutorial/tutorial_front_samlbase.png)

3. **[Front のドメインと URL]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-front-tutorial/tutorial_front_url1.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[識別子]** ボックスに、`https://<companyname>.frontapp.com` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<companyname>.frontapp.com/sso/saml/callback` のパターンを使用して URL を入力します。

4. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-front-tutorial/tutorial_front_url2.png)

    **[サインオン URL]** ボックスに、`https://<companyname>.frontapp.com` のパターンを使用して URL を入力します。
     
    > [!NOTE] 
    > これらは実際の値ではありません。 これらの値は実際の識別子、応答 URL、およびサインオン URL に変更してください。これらの値の取得方法については、チュートリアルの後半の説明を参照するか、[Front クライアント サポート チーム](mailto:support@frontapp.com)に問い合わせてください。 

5. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-front-tutorial/tutorial_front_certificate.png) 

6. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-front-tutorial/tutorial_general_400.png)
    
7. **[Front 構成]** セクションで、**[Front の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-front-tutorial/tutorial_front_configure.png) 

8. Front テナントに管理者としてサインオンします。

9. **[Settings (設定)] \(左のサイド バーの下にある歯車アイコン)、[Preferences (ユーザー設定)]** の順に移動します。
   
    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-front-tutorial/tutorial_front_000.png)

10. **[シングル サインオン]** のリンクをクリックします。
   
    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-front-tutorial/tutorial_front_001.png)

11. **[Single Sign On]\(シングル サインオン\)** のドロップダウン リストで、**[SAML]** を選択します。
   
    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-front-tutorial/tutorial_front_002.png)

12. **[Entry Point (エントリ ポイント)]** ボックスに、Azure AD アプリケーションの構成ウィザードの **[シングル サインオン サービス URL]** の値を入力します。
    
    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-front-tutorial/tutorial_front_003.png)

13. ダウンロードした**証明書 (Base64)** ファイルをメモ帳で開き、その内容をクリップボードにコピーし、**[Signing certificate]** ボックスに貼り付けます。
    
    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-front-tutorial/tutorial_front_004.png)

14. **[Service provider settings]** セクションで、次の手順に従います。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-front-tutorial/tutorial_front_005.png)

    a. **Entity ID** の値をコピーして Azure Portal の **[Front のドメインと URL]** セクションの **[識別子]** ボックスに貼り付けます。

    b. **ACS URL** の値をコピーして Azure Portal の **[Front のドメインと URL]** セクションの**[サインオン URL]** ボックスに貼り付けます。
    
15. **[保存]** ボタンをクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-front-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-front-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-front-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-front-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **[作成]**をクリックします。
 
### <a name="create-a-front-test-user"></a>Front テスト ユーザーの作成

このセクションでは、Front で Britta Simon というユーザーを作成します。 [Front クライアント サポート チーム](mailto:support@frontapp.com)と連携し、Front プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Front へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Front に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Front]**を選択します。

    ![アプリケーションの一覧の Front のリンク](./media/active-directory-saas-front-tutorial/tutorial_front_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。

アクセス パネルで Front のタイルをクリックすると、自動的に Front アプリケーションにサインオンします。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-front-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-front-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-front-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-front-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-front-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-front-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-front-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-front-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-front-tutorial/tutorial_general_203.png


