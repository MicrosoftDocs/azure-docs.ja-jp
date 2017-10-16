---
title: "チュートリアル: Azure Active Directory と LearnUpon の統合 | Microsoft Docs"
description: "Azure Active Directory と LearnUpon の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: b6ac8acc244e9029be01ede5e0865c280171217d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>チュートリアル: Azure Active Directory と LearnUpon の統合

このチュートリアルでは、LearnUpon と Azure Active Directory (Azure AD) を統合する方法について説明します。

LearnUpon と Azure AD の統合には、次の利点があります。

- LearnUpon にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に LearnUpon にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

LearnUpon と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- LearnUpon でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの LearnUpon の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-learnupon-from-the-gallery"></a>ギャラリーからの LearnUpon の追加
Azure AD への LearnUpon の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に LearnUpon を追加する必要があります。

**ギャラリーから LearnUpon を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに「 **LearnUpon**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_search.png)

5. 結果ウィンドウで **LearnUpon** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、LearnUpon で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する LearnUpon ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと LearnUpon の関連ユーザーの間で、リンク関係が確立されている必要があります。

LearnUpon で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

LearnUpon で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[LearnUpon テスト ユーザーの作成](#creating-a-learnupon-test-user)** - LearnUpon で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、LearnUpon アプリケーションでシングル サインオンを構成します。

**LearnUpon で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **LearnUpon** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_samlbase.png)

3. **[LearnUpon のドメインと URL]** セクションで、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_url.png)

    **[応答 URL]** ボックスに、`https://<companyname>.learnupon.com/saml/consumer` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これは実際の値ではないので注意してください。 この値は実際の応答 URL で更新する必要があります。 この値を取得するには、[LearnUpon サポート チーム](https://www.learnupon.com/features/support/)に問い合わせてください。



4. **[SAML 署名証明書]** セクションで、**[Certificate (Raw) (証明書 (Raw))]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-learnupon-tutorial/tutorial_general_400.png)

6. **[LearnUpon 構成]** セクションで、**[LearnUpon の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_configure.png) 

7. 別のブラウザー インスタンスを開き、管理者アカウントを使用して LearnUpon にログインします。 

8. **[settings (設定)]** タブをクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_06.png)

9. **[Single Sign On - SAML (シングル サインオン - SAML)]**、**[General Settings (全般設定)]** の順にクリックし、SAML 設定を構成します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_07.png) 

10. **[General Settings (全般設定)]** セクションで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. **[Enabled]**を選択します。

    b. **[Version]\(バージョン\)** として **[2.0]** を選択します。

    c. **[Skip conditions]\(条件をスキップする\)** で **[いいえ]** を選択します。

    d. **[SAML Token POST param name (SAML トークン POST パラメーター名)]** ボックスに、前述の SAML コンシューマー URL に対する POST 要求パラメーターの名前を入力します。ここには、確認と認証の対象である SAML アサーションが含まれます (**SAMLResponse** など)。

    e. **[Name Identifier Format (名前識別子形式)]** ボックスに、SAML アサーション内のユーザー ID (メール アドレス) の場所を示す値を入力します (**urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress** など)。
  
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 Azure Portal のログイン画面でアップロード済みアイコンをクリックした際のユーザーの移動先を示す値を、**[Identify Provider Location]\(プロバイダーの場所を特定\)** ボックスに入力します。
  
    g. **[Sign out URL]\(サインアウト URL\)** ボックスに、Azure Portal からコピーした**サインアウト URL** を貼り付けます。
    
    h. **[Manage finger prints (指紋の管理)]** をクリックし、ダウンロードした証明書の指紋をアップロードします。

11. **[User Settings (ユーザー設定)]**をクリックし、次の手順を実行します。
   
     ![[シングル サインオンの構成]](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_11.png)  
 
    a. **[First Name Identifier Format]\(名識別子形式\)** ボックスに、SAML アサーション内のユーザーの名の場所を示す値を入力します (例: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**)。
  
    b. **[Last Name Identifier Format]\(姓識別子形式\)** ボックスに、SAML アサーション内のユーザーの姓の場所を示す値を入力します (例: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**)。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-learnupon-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-learnupon-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-learnupon-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-learnupon-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-learnupon-test-user"></a>LearnUpon テスト ユーザーの作成

このセクションの目的は、LearnUpon で Britta Simon というユーザーを作成することです。 LearnUpon では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 存在しない LearnUpon ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。 [Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、[LearnUpon のサポート チーム](https://www.learnupon.com/features/support/)にお問い合わせください。 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に LearnUpon へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**LearnUpon に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[LearnUpon]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [LearnUpon] タイルをクリックすると、LearnUpon アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_203.png

