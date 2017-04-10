---
title: "チュートリアル: Azure Active Directory と Amazon Web Services (AWS) の統合 | Microsoft Docs"
description: "Azure Active Directory で Amazon Web Services (AWS) を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 8ea8de916a36a1b5274f42513b0446f99707c179
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>チュートリアル: Azure Active Directory と Amazon Web Services (AWS) の統合
このチュートリアルの目的は、Amazon Web Services (AWS) と Azure Active Directory (Azure AD) を統合する方法を説明することです。  

Amazon Web Services (AWS) と Azure AD の統合には、次の利点があります。 

* Amazon Web Services (AWS) にアクセスする Azure AD ユーザーを制御できます。 
* ユーザーが自分の Azure AD アカウントで自動的に Amazon Web Services (AWS) シングル サインオン (SSO) にサインオンできるようにできます。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Amazon Web Services (AWS) と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* Amazon Web Services (AWS) SSO が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。 

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD の SSO をテストできるようにすることです。  

このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Amazon Web Services (AWS) の追加 
2. Azure AD SSO の構成とテスト

## <a name="add-amazon-web-services-aws-from-the-gallery"></a>ギャラリーからの Amazon Web Services (AWS) の追加
Azure AD への Amazon Web Services (AWS) の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Amazon Web Services (AWS) を追加する必要があります。

**ギャラリーから Amazon Web Services (AWS) を追加するには、次の手順を実行します:**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1] 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。 
   
    ![[アプリケーション]][2]

4. ページの下部にある **[追加]** をクリックします。 
   
    ![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。 
   
    ![アプリケーション][4]

6. 検索ボックスに、「**Amazon Web Services (AWS)**」と入力します。
   
    ![アプリケーション][5]

7. 結果ウィンドウで **[Amazon Web Services (AWS)]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![アプリケーション][6]

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーを使用して、Amazon Web Services (AWS) との Azure AD SSO を構成、テストする方法について説明します。

SSO を機能させるには、Azure AD ユーザーに対応する Amazon Web Services (AWS) ユーザーが Azure AD で認識される必要があります。 言い換えると、Azure AD ユーザーと Amazon Web Services (AWS) の関連ユーザーの間で、リンク関係が確立されている必要があります。  

このリンク関係は、Azure AD の **[ユーザー名]** の値を、Amazon Web Services (AWS) の **[Username]** の値として割り当てることで確立されます。

Amazon Web Services (AWS) との Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Amazon Web Services (AWS) テスト ユーザーの作成](#creating-a-halogen-software-test-user)** - Amazon Web Services (AWS) で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションでは、Azure クラシック ポータルで Azure AD SSO を有効にし、Amazon Web Services (AWS) アプリケーションでシングル サインオンを構成します。  

Amazon Web Services (AWS) アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを **SAML トークン属性**の構成に追加する必要があります。 

次のスクリーンショットはその例です。

![[シングル サインオンの構成]][27]

**Amazon Web Services (AWS) との Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure クラシック ポータルの **Amazon Web Services (AWS)** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][7]

2. **[ユーザーの Amazon Web Services (AWS) へのアクセスを設定してください]** ページで、**[Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Configure Single Sign-On][8]

3. **[アプリケーション設定の構成]** ページで、**[次へ]** をクリックします。 
   
    ![[アプリケーション設定の構成]][9]

4. **[Amazon Web Services (AWS) でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックし、コンピューターにローカルでメタデータ ファイルを保存します。
   
    ![Configure Single Sign-On][10]

5. 別の Web ブラウザーのウィンドウで、管理者として Amazon Web Services (AWS) 企業サイトにサインオンします。

6. **[Console Home]**をクリックします。
   
    ![[シングル サインオンの構成]][11]

7. **[Identity and Access Management]**をクリックします。 
   
    ![[シングル サインオンの構成]][12]

8. **[Identity Providers]**、**[Create Provider]** の順にクリックします。 
   
    ![[シングル サインオンの構成]][13]

9. **[Configure Provider]** ダイアログ ページで、次の手順を実行します。 
   
    ![[シングル サインオンの構成]][14]   
  1. **[Provider Type]** として **[SAML]** を選択します。
  2. **[Provider Name]** ボックスにプロバイダー名を入力します (例: *WAAD*)。
  3. ダウンロードしたメタデータ ファイルをアップロードするには、 **[Choose file]**をクリックします。
  4. ページの下部にある **[Next Step]**」を参照してください。

10. **[Verify Provider Information]** ダイアログ ボックスで、**[Create]** をクリックします。 
    
    ![[シングル サインオンの構成]][15]

11. **[Roles]** をクリックしてから **[Create New Role]** をクリックします。 
    
    ![Configure Single Sign-On][16]

12. **[Set Role Name]** ダイアログで、次の手順を実行します。 
    
    ![Configure Single Sign-On][17] 
  1. **[Role Name]** ボックスにロール名を入力します (例: *TestUser*)。 
  2. ページの下部にある **[Next Step]**」を参照してください。

13. **[Select Role Type]** ダイアログで、次の手順を実行します。 
    
    ![Configure Single Sign-On][18] 
  1. **[Role For Identity Provider Access]** を選択します。 
  2. **[Grant Web Single Sign-On (WebSSO) access to SAML providers]** セクションで、**[Select]** をクリックします。

14. **Establish Trust** ダイアログで、次の手順を実行します。  
    
    ![Configure Single Sign-On][19] 
  1. SAML プロバイダーとして、前に作成した SAML プロバイダーを選択します (例: *WAAD*)。   
  2. ページの下部にある **[Next Step]**」を参照してください。

15. **[Verify Role Trust]** ダイアログで、**[Next Step]** をクリックします。 
    
    ![[シングル サインオンの構成]][32]

16. **[Attach Policy]** ダイアログで、**[Next Step]** をクリックします。  
    
    ![[シングル サインオンの構成]][33]

17. **[Review]** ダイアログで、次の手順を実行します。   
    
    ![[シングル サインオンの構成]][34] 
  1. **Role ARN** の値 をコピーします。  
  2. **Trusted Entities** の ARN 値をコピーします。 
  3. **[Create Role]** をクリックします。 

18. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
    
    ![What is Azure AD Connect][20]

19. **[シングル サインオンの確認]** ページで **[完了]** を選択して、**[シングル サインオンの構成]** ダイアログを閉じます。
    
    ![What is Azure AD Connect][22]

20. 上部のメニューで、 **属性** to open the **SAML Token 属性** ダイアログを開きます。 
    
    ![Configure Single Sign-On][21]

21. **[ユーザー属性の追加]**をクリックします。 
    
    ![[シングル サインオンの構成]][23]

22. [ユーザー属性の追加] ダイアログで、次の手順を実行します。 
    
    ![[シングル サインオンの構成]][24]  
  1. **[属性名]** ボックスに、**https://aws.amazon.com/SAML/Attributes/Role** と入力します。  
  2. **[属性値]** ボックスに、**[Role の ARN 値] と [Trusted Entity の ARN 値]** を入力します。
    
     >[!TIP]
     >これらは、ロールの作成時に [Review] ダイアログでコピーした値です。 
     > 
     
  3. **[完了]** をクリックして **[ユーザー属性の追加]** ダイアログ ボックスを閉じます。

23. **[ユーザー属性の追加]**をクリックします。 
    
    ![[シングル サインオンの構成]][23]

24. [ユーザー属性の追加] ダイアログで、次の手順を実行し、**[変更の適用]** をクリックします。 
    
    ![Configure Single Sign-On][25]
 1. **[属性名]** ボックスに、**https://aws.amazon.com/SAML/Attributes/RoleSessionName** と入力します。
 2. **[属性値]** ボックスに「**user.userprincipalname**」と入力するか、ドロップダウン リストから選択します。

     ![Configure Single Sign-On][35]
 3. **[完了]** をクリックして **[ユーザー属性の追加]** ダイアログ ボックスを閉じます。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png)

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png) 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png) 

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png) 

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_05.png) 
 1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
 2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
 3. [次へ] をクリックします。

6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_06.png)  
 1. **[名]** ボックスに「**Britta**」と入力します。   
 2. **[姓]** ボックスに「**Simon**」と入力します。 
 3. **[表示名]** ボックスに「**Britta Simon**」と入力します。 
 4. **[ロール]** 一覧で **[ユーザー]** を選択します。 
 5. **[次へ]**をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_07.png) 

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_08.png)  
 1. **[新しいパスワード]** の値を書き留めます。  
 2. **[完了]** をクリックします。   

### <a name="create-a-amazon-web-services-aws-test-user"></a>Amazon Web Services (AWS) テスト ユーザーの作成
このセクションの目的は、Amazon Web Services (AWS) で Britta Simon というユーザーを作成することです。

**Amazon Web Services (AWS) で Britta Simon というユーザーを作成するには、次の手順に従います:**

1. 管理者として **Amazon Web Services (AWS)** 企業サイトにログインします。

2. **[Console Home (コンソール ホーム)]** アイコンをクリックします。 
   
    ![[シングル サインオンの構成]][11]

3. [Identity and Access Management] をクリックします。 
   
    ![[シングル サインオンの構成]][28]

4. Dashboard で、[Users]、[Create New Users] の順にクリックします。 
   
    ![[シングル サインオンの構成]][29]

5. [Create User] ダイアログで、次の手順に従います。 
   
    ![[シングル サインオンの構成]][30]   
 1. **[Enter User Names]** ボックスに、Brita Simon の Azure AD でのユーザー名 (userprincipalname) を入力します。 
 2. **[作成]**をクリックします。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションでは、Britta Simon に Amazon Web Services (AWS) へのアクセスを許可してこのユーザーが Azure SSO を使用できるようにします。

![ユーザーの割り当て][31]

**Britta Simon を Amazon Web Services (AWS) に割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][26]

2. アプリケーションの一覧で、 **[Amazon Web Services (AWS)]**を選択します。
   
    ![ユーザーの割り当て][27]

3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][25]

4. ユーザーの一覧で **[Britta Simon]**を選択します。

5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][29]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。  

アクセス パネルで [Amazon Web Services (AWS)] タイルをクリックすると、自動的に Amazon Web Services (AWS) アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795019.png
[6]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795020.png
[7]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795027.png
[8]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795028.png
[9]: ./media/active-directory-saas-amazon-web-service-tutorial/capture23.png
[10]: ./media/active-directory-saas-amazon-web-service-tutorial/capture24.png
[11]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795025.png
[20]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_15.png
[26]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_18.png
[27]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950357.png
[28]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_16.png
[30]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795038.png
[31]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_17.png
[32]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950253.png
[35]: ./media/active-directory-saas-amazon-web-service-tutorial/user_attributes_01.png























