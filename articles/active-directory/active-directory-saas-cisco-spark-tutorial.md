---
title: "チュートリアル: Azure Active Directory と Cisco Spark の統合 | Microsoft Docs"
description: "Azure Active Directory と Cisco Spark の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: a0a221622afe1c801a331e2319f3a7ace3111dad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-spark"></a>チュートリアル: Azure Active Directory と Cisco Spark の統合

このチュートリアルでは、Cisco Spark と Azure Active Directory (Azure AD) を統合する方法について説明します。

Cisco Spark と Azure AD の統合には、次の利点があります。

- Cisco Spark にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Cisco Spark にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Cisco Spark と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Cisco Spark でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Cisco Spark の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-cisco-spark-from-the-gallery"></a>ギャラリーからの Cisco Spark の追加
Azure AD への Cisco Spark の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Cisco Spark を追加する必要があります。

**ギャラリーから Cisco Spark を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**Cisco Spark**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_search.png)

5. 結果ウィンドウで **[Cisco Spark]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Cisco Spark で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Cisco Spark ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Cisco Spark の関連ユーザーの間で、リンク関係が確立されている必要があります。

Cisco Spark で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Cisco Spark で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Cisco Spark のテスト ユーザーの作成](#creating-a-cisco-spark-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Cisco Spark で作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Cisco Spark アプリケーションでシングル サインオンを構成します。

**Cisco Spark で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Cisco Spark** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_samlbase.png)

3. **[Cisco Spark のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_url.png)

    a. **[サインオン URL]** ボックスに、「`https://web.ciscospark.com/#/signin`」と入力します。

    b. **[識別子]** ボックスに、`https://idbroker.webex.com/<companyname>` の形式で URL を入力します。

    > [!NOTE] 
    > これは実際の値ではありません。 実際の識別子でこの値を更新します。 この値を取得するには、[Cisco Spark クライアント サポート チーム](https://support.ciscospark.com/)に問い合わせてください。 
 
4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_certificate.png) 

5. Cisco Spark アプリケーションは SAML アサーションを使用し、特定の属性を含みます。 このアプリケーションには、次の属性を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 次のスクリーンショットはその例です。
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_07.png) 

6. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、上の図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名  | 属性値 |
    | --------------- | -------------------- |    
    |   uid    | user.userprincipalname |   

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-cisco-spark-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_attribute_05.png)
    
    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。
    
    c. **[値]** 一覧から、その行に対して表示される値を入力します。
    
    d. **[OK]**をクリックします。

7. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_400.png)

8. 完全な管理者権限を備えた資格情報を使って [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) にサインインします。

9. **[設定]** を選択し、**[認証]** セクションの下で **[変更]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_10.png)
    
10. **[Integrate a 3rd-party identity provider.(Advanced)] \(サード パーティの ID プロバイダーを統合する。(詳細))** を選択して、次の画面に移動します。

11. **[Import Idp Metadata] \(Idp メタデータのインポート)** ページで、ページ上に Azure AD メタデータ ファイルをドラッグ アンド ドロップするか、ファイルのブラウザー オプションを使用して Azure AD メタデータ ファイルを見つけてアップロードします。 次に、**[Require certificate signed by a certificate authority in Metadata (more secure)(証明機関の署名付き証明書がメタデータに必要 (高セキュリティ))]** を選択し、**[Next (次へ)]** をクリックします。 
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_11.png)

12. **[Test SSO Connection (SSO 接続のテスト)]** を選択し、ブラウザーの新しいタブが開いたら、サインインして Azure AD で認証します。

13. ブラウザーの**[Cisco Cloud Collaboration Management]** タブに戻ります。テストが成功した場合は、**[This test was successful.Enable Single Sign-On option] \(このテストは正常に完了しました。シングル サインオン オプションを有効にします)** を選択して、**[次へ]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-cisco-spark-test-user"></a>Cisco Spark テスト ユーザーの作成

このセクションでは、Cisco Spark で Britta Simon というユーザーを作成します。 このセクションでは、Cisco Spark で Britta Simon というユーザーを作成します。

1. 完全な管理者権限を備えた資格情報を使って [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) にアクセスします。

2. **[Users]\(ユーザー\)** と **[Manage Users ]\(ユーザーの管理\)** を順にクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

3. **[Manage User (ユーザーの管理)]** ウィンドウで、**[Manually add or modify users (ユーザーを手動で追加または変更)]** を選択し、**[Next (次へ)]** をクリックします。

4. **[Names and Email address (名前と電子メール アドレス)]** を選択します。 次のようにテキスト ボックスに入力します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_13.png) 
    
    a. **[名]** ボックスに「**Britta**」と入力します。 
    
    b. **[姓]** ボックスに「**Simon**」と入力します。
    
    c. **[電子メール アドレス]** ボックスに「**britta.simon@contoso.com**」と入力します。

5. プラス記号をクリックして、Britta Simon を追加します。 次に、 **[次へ]**をクリックします。

6. **[Add Services for Users (ユーザーのサービスを追加)]** ウィンドウで、**[Save (保存)]** をクリックしてから **[Finish (完了)]** をクリックします。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Cisco Spark へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Cisco Spark に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Cisco Spark]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。

アクセス パネルで [Cisco Spark] タイルをクリックすると、自動的に Cisco Spark アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_04.png
[10]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_060.png
[100]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_203.png

