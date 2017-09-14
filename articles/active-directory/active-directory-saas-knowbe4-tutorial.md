---
title: "チュートリアル: Azure Active Directory と KnowBe4 Security Awareness Training の統合 | Microsoft Docs"
description: "Azure Active Directory と KnowBe4 Security Awareness Training の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: b80d2212-cc5f-4adb-836c-570640810c39
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 3b18737112a8aef101fab7fac1904f7c2e194d64
ms.contentlocale: ja-jp
ms.lasthandoff: 06/29/2017


---
# <a name="tutorial-azure-active-directory-integration-with-knowbe4-security-awareness-training"></a>チュートリアル: Azure Active Directory と KnowBe4 Security Awareness Training の統合

このチュートリアルでは、KnowBe4 Security Awareness Training と Azure Active Directory (Azure AD) を統合する方法について説明します。

KnowBe4 Security Awareness Training と Azure AD の統合には、次の利点があります。

- KnowBe4 Security Awareness Training にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで KnowBe4 Security Awareness Training に自動的にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と KnowBe4 Security Awareness Training の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- KnowBe4 Security Awareness Training シングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. KnowBe4 Security Awareness Training をギャラリーから追加する
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-knowbe4-security-awareness-training-from-the-gallery"></a>KnowBe4 Security Awareness Training をギャラリーから追加する
KnowBe4 Security Awareness Training の Azure AD への統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に KnowBe4 Security Awareness Training を追加する必要があります。

**ギャラリーから KnowBe4 Security Awareness Training を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**KnowBe4 Security Awareness Training**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-KnowBe4-tutorial/tutorial_knowbe4sat_search.png)

5. 結果ウィンドウで **[KnowBe4 Security Awareness Training]** を選択し、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-KnowBe4-tutorial/tutorial_knowbe4sat_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、KnowBe4 Security Awareness Training で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する KnowBe4 Security Awareness Training ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと KnowBe4 Security Awareness Training の関連ユーザーの間で、リンク関係が確立されている必要があります。

KnowBe4 Security Awareness Training で、Azure AD の **[ユーザー名]** の値を **[ユーザー名]** の値として割り当ててリンク関係を確立します。

KnowBe4 Security Awareness Training で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[KnowBe4 Security Awareness Training テスト ユーザーの作成](#creating-a-knowbe4-security-awareness-training-test-user)** - KnowBe4 Security Awareness Training で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、KnowBe4 Security Awareness Training アプリケーションでシングル サインオンを構成します。

**KnowBe4 Security Awareness Training で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の**KnowBe4 Security Awareness Training** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-KnowBe4-tutorial/tutorial_knowbe4sat_samlbase.png)

3. **[KnowBe4 Security Awareness Training Domain and URLs]\(KnowBe4 Security Awareness Training のドメインと URL\)** セクションで、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-KnowBe4-tutorial/tutorial_knowbe4sat_url.png)

    **[サインオン URL]** ボックスに、`https://<companyname>.KnowBe4.com/auth/saml/<instancename>` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 [KnowBe4 Security Awareness Training Client サポート チーム](mailto:support@KnowBe4.com)に連絡して、値を取得します。 
 

4. **[SAML 署名証明書]** セクションで、**[Certificate (Raw) (証明書 (Raw))]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-KnowBe4-tutorial/tutorial_knowbe4sat_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_400.png)

6. **[KnowBe4 Security Awareness Training]** セクションで、**[Configure KnowBe4 Security Awareness Training]\(KnowBe4 Security Awareness Training の構成\)** をクリックして **[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-KnowBe4-tutorial/tutorial_knowbe4sat_configure.png) 

7. **KnowBe4 Security Awareness Training** 側でシングル サインオンを構成するには、ダウンロードした**証明書 (未加工)**、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** を、[KnowBe4 Security Awareness Training Client サポート チーム](mailto:support@KnowBe4.com)に送信する必要があります。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-KnowBe4-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-KnowBe4-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-KnowBe4-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-KnowBe4-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="creating-a-knowbe4-security-awareness-training-test-user"></a>KnowBe4 Security Awareness Training のテスト ユーザーの作成

このセクションの目的は、KnowBe4 Security Awareness Training で Britta Simon というユーザーを作成することです。 KnowBe4 Security Awareness Training では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 存在しない KnowBe4 Security Awareness Training ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。 

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、[KnowBe4 Security Awareness Training のサポート チーム](mailto:support@KnowBe4.com)にお問い合わせください。
> 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に KnowBe4 Security Awareness Training へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**KnowBe4 Security Awareness Training に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で、**[KnowBe4 Security Awareness Training]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-KnowBe4-tutorial/tutorial_knowbe4sat_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。
  
アクセス パネルで [KnowBe4 Security Awareness Training] タイルをクリックすると、KnowBe4 Security Awareness Training アプリケーションに自動的にサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_203.png


