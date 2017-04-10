---
title: "チュートリアル: Azure Active Directory と Cisco Spark の統合 | Microsoft Docs"
description: "Azure Active Directory と Cisco Spark の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 931004d458fd0cbf3e002805ca701d777ddc57c2
ms.openlocfilehash: f0f12b1667b7f45fd164fac658502c93e8afb855
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-cisco-spark"></a>チュートリアル: Azure Active Directory と Cisco Spark の統合
このチュートリアルでは、Cisco Spark と Azure Active Directory (Azure AD) を統合する方法について説明します。

Cisco Spark と Azure AD の統合には、次の利点があります。

* Cisco Spark にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Cisco Spark にシングル サインオン (SSO) できるようにします
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Cisco Spark と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* **Cisco Spark** でのシングル サインオン (SSO) が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの Cisco Spark の追加
2. Azure AD SSO の構成とテスト

## <a name="add-cisco-spark-from-the-gallery"></a>ギャラリーからの Cisco Spark の追加
Azure AD への Cisco Spark の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Cisco Spark を追加する必要があります。

**ギャラリーから Cisco Spark を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「**Cisco Spark**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_01.png)
7. 結果ウィンドウで **[Cisco Spark]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Cisco Spark で Azure AD の SSO を構成し、テストします。

SSO を機能させるには、Azure AD ユーザーに対応する Cisco Spark ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Cisco Spark の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Cisco Spark の **[Username]** の値として割り当てます。 Cisco Spark で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Cisco Spark のテスト ユーザーの作成](#creating-a-cisco-spark-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Cisco Spark で作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成
このセクションの目的は、Azure クラシック ポータルで Azure AD の SSO を有効にすることと、Cisco Spark アプリケーションでシングル サインオンを構成することです。

Cisco Spark アプリケーションは SAML アサーションを使用し、特定の属性を含みます。 このアプリケーションには、次の属性を構成します。 この属性の値は、アプリケーションの **[属性]** タブから管理できます。 次のスクリーンショットはその例です。

![[シングル サインオンの構成]](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_03.png) 

**Cisco Spark で Azure AD SSO を構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Cisco Spark** アプリケーション統合ページで、上部のメニューの **[属性]** をクリックします。
   
    ![Configure Single Sign-On][5]
2. **[Saml トークン属性]** ダイアログで、次の手順を実行します。
  1. **[ユーザー属性の追加]** をクリックして、**[ユーザー属性の追加]** ダイアログを開きます。
   
    ![Configure Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_05.png)
  2. **[属性名]** ボックスに、「**uid**」と入力します。
  3. **[属性値]** の一覧で、**[user.userprincipal]** を選択します。
  4. ページの下部にある **[完了]**」を参照してください。 ページの下部にある **[変更の適用]** をクリックします。
3. 上部のメニューで **[クイック スタート]**をクリックします。
   
    ![[シングル サインオンの構成]][6]
4. クラシック ポータルの **Cisco Spark** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][7] 
5. **[How would you like users to sign on to Cisco Spark (ユーザーの Cisco Spark へのアクセスを設定してください)]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_06.png)
6. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![Configure Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_07.png)
  1. [サインオン URL] ボックスに、`https://web.ciscospark.com/#/signin` のパターンを使用して URL を入力します。
  2. **[次へ]**をクリックします。
7. **[Cisco Spark でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックしてメタデータをダウンロードし、コンピューターにファイルを保存します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_09.png)
8. 完全な管理者権限を備えた資格情報を使って [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) にサインインします。
9. **[設定]** を選択し、**[認証]** セクションの下で **[変更]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_10.png)
9. **[Integrate a 3rd-party identity provider.(Advanced)] (サード パーティの ID プロバイダーを統合する。(詳細))** を選択して、次の画面に移動します。
10. **[Download Metadata File] (メタデータ ファイルのダウンロード)** をクリックし、コンピューターにファイルを保存します。
11. **[Import Idp Metadata] (Idp メタデータのインポート)** ページで、ページ上に Azure AD メタデータ ファイルをドラッグ アンド ドロップするか、ファイルのブラウザー オプションを使用して Azure AD メタデータ ファイルを見つけてアップロードします。 次に、**[Require certificate signed by a certificate authority in Metadata (more secure)(証明機関の署名付き証明書がメタデータに必要 (高セキュリティ))]** を選択し、**[Next (次へ)]** をクリックします。 
   
   ![[シングル サインオンの構成]](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_11.png)
12. **[Test SSO Connection (SSO 接続のテスト)]** を選択し、ブラウザーの新しいタブが開いたら、サインインして Azure AD で認証します。
13. ブラウザーの**[Cisco Cloud Collaboration Management]** タブに戻ります。 テストが成功した場合は、**[This test was successful.Enable Single Sign-On option] (このテストは正常に完了しました。シングル サインオン オプションを有効にします)** を選択して、**[次へ]** をクリックします。
14. Azure AD クラシック ポータルで、[single sign-on configuration confirmation] (シングル サインオンの構成の確認) を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
15. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
    
     ![Azure AD のシングル サインオン][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションでは、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_05.png) 
  1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
  2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
  3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_06.png) 
  1. **[名]** ボックスに「**Britta**」と入力します。  
  2. **[姓]** ボックスに「**Simon**」と入力します。
  3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
  4. **[ロール]** 一覧で **[ユーザー]** を選択します。
  5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_08.png) 
  1. **[新しいパスワード]** の値を書き留めます。
  2. **[完了]** をクリックします。   

### <a name="create-a-cisco-spark-test-user"></a>Cisco Spark テスト ユーザーの作成
このセクションでは、Cisco Spark で Britta Simon というユーザーを作成します。 このセクションでは、Cisco Spark で Britta Simon というユーザーを作成します。

1. 完全な管理者権限を備えた資格情報を使って [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) にアクセスします。
2. **[Users (ユーザー)]** と **[Manage Users (ユーザーの管理)]** を順にクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_12.png) 
3. **[Manage User (ユーザーの管理)]** ウィンドウで、**[Manually add or modify users (ユーザーを手動で追加または変更)]** を選択し、**[Next (次へ)]** をクリックします。
4. **[Names and Email address (名前と電子メール アドレス)]** を選択します。 次のようにテキスト ボックスに入力します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_13.png) 
  1. **[名]** ボックスに「**Britta**」と入力します。 
  2. **[姓]** ボックスに「**Simon**」と入力します。
  3. **[電子メール アドレス]** ボックスに「**britta.simon@contoso.com**」と入力します。
5. プラス記号をクリックして、Britta Simon を追加します。 次に、 **[次へ]**をクリックします。
6. **[Add Services for Users (ユーザーのサービスを追加)]** ウィンドウで、**[Save (保存)]** をクリックしてから **[Finish (完了)]** をクリックします。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションでは、Britta Simon に Cisco Spark へのアクセスを許可することで、このユーザーが Azure SSO を使用できるようにします。

![ユーザーの割り当て][200] 

**Cisco Spark に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[Cisco Spark]**を選択します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_14.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 
4. [すべてのユーザー] の一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
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


[5]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_205.png

