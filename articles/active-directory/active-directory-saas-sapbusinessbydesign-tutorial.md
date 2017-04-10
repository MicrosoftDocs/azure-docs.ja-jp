---
title: "チュートリアル: Azure Active Directory と SAP Business ByDesign の統合 | Microsoft Docs"
description: "Azure Active Directory と SAP Business ByDesign の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 82938920-33ba-47cb-b141-511b46d19e66
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: f454e7e218764e00cc19ca67b0edade213834b75
ms.openlocfilehash: e2506fc2fc7802dd0097cbb97687d9d2b0382871


---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>チュートリアル: Azure Active Directory と SAP Business ByDesign の統合
このチュートリアルでは、SAP Business ByDesign と Azure Active Directory (Azure AD) を統合する方法について説明します。

SAP Business ByDesign と Azure AD の統合には、次の利点があります。

* SAP Business ByDesign にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に SAP Business ByDesign にサインオン (シングル サインオン) できるようにします。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Azure AD と SAP Business ByDesign の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* SAP Business ByDesign でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
> 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。

このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから SAP Business ByDesign を追加する
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>ギャラリーから SAP Business ByDesign を追加する
Azure AD への SAP Business ByDesign の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SAP Business ByDesign を追加する必要があります。

**ギャラリーから SAP Business ByDesign を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]

6. 検索ボックスに、「 **SAP Business ByDesign**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_01.png)

7. 結果ウィンドウで **[SAP Business ByDesign]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Active Directory](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、SAP Business ByDesign で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SAP Business ByDesign ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SAP Business ByDesign の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、SAP Business ByDesign の **[Username]** の値として割り当てることで確立されます。

SAP Business ByDesign で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[SAP Business ByDesign のテスト ユーザーの作成](#creating-an-sap-business-bydesign-test-user)** - SAP Business ByDesign で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションでは、クラシック ポータルで Azure AD のシングル サインオンを有効にして、SAP Business ByDesign アプリケーションでシングル サインオンを構成します。

SAP Business ByDesign アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成してください。 この属性の値は、アプリケーションの **[属性]** タブから管理できます。 次のスクリーンショットはその例です。 

**SAP Business ByDesign で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **[SAP Business ByDesign]** アプリケーション統合ページで、上部のメニューから **[属性]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_80.png) 

2. [SAML トークンの属性] 一覧で、nameidentifier 属性を選択して **[編集]**をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_84.png) 

3. [ユーザー属性の編集] ダイアログで、次の手順を実行します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_85.png) 
   
    a. [属性値] 一覧から **ExtractMailPrefix()** 関数を選択します
   
    b. [メール] 一覧から、実装で使用するユーザー属性を選択します。 
    たとえば、一意のユーザー識別子として EmployeeID を使用し、その属性値を ExtensionAttribute2 に保存している場合、**[user.extensionattribute2]** を選択します。 
   
    c. ページの下部にある **[完了]**」を参照してください。 

4. クラシック ポータルの **SAP Business ByDesign** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![Configure Single Sign-On][6] 

5. **[ユーザーの SAP Business ByDesign へのアクセスを設定してください]** ページで、**[Azure AD のシングル サインオン]** を選択してから **[次へ]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_03.png) 

6. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_04.png) 
   
    a. **[サインオン URL]** ボックスに、`https://<servername>.sapbydesign.com` という形式で、ユーザーが SAP Business ByDesign アプリケーションへのサインオンに使用する URL を入力します。
   
    b. click **[次へ]**
7. **[SAP Business ByDesign でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_05.png)
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[メタデータのダウンロード]** をクリックし、コンピューターにファイルを保存します。
   
    b. ページの下部にある [次へ]」を参照してください。
8. アプリケーション用に構成された SSO を取得するには、次の手順を実行します。
   
    a. SAP Business ByDesign ポータルに管理者権限でサインオンします。
   
    b. **[Application and User Management Common Task (アプリケーションとユーザー管理の共通タスク)]** に移動し、**[ID プロバイダー]** タブをクリックします。
   
    c. **[New Identity Provider (新しい ID プロバイダー)]** をクリックし、Azure クラシック ポータルからダウンロードしたメタデータの XML ファイルを選択します。 メタデータをインポートすることによって、必要な署名証明書と暗号化証明書が自動的にアップロードされます。
   
    ![Configure Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)
   
    d. **Assertion Consumer Service URL** を SAML 要求に追加するには、**[Include Assertion Consumer Service URL (Assertion Consumer Service URL を含める)]** を選択します。
   
    e. **[Activate Single Sign-On (シングル サインオンを有効にする)]** をクリックします。
   
    f. 変更を保存します。
   
    g. **[My System (自分のシステム)]** タブをクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)
   
    h. **SSO URL** をコピーし、**[Azure AD Sign On URL (Azure AD サインオン URL)]** ボックスに貼り付けます。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)
   
    i. ユーザー ID とパスワードでログオンするか、SSO でログオンするかを従業員が手動で選択できるかどうかを、**[Manual Identity Provider Selection (ID プロバイダーの手動選択)]** を選択して指定します。
   
    j. **[SSO URL]** セクションには、従業員がシステムへのログオンに使用する URL を指定します。 
    [URL Sent to Employee (従業員に送信する URL)] ボックスの一覧で、次のオプションを選択できます。
   
    **[Non-SSO URL (非 SSO URL)]**
   
    従業員に送信されるのは、システムの通常の URL のみです。 従業員のログオンに SSO は使用できず、パスワードまたは証明書を使用する必要があります。
   
    **[SSO URL (SSO の URL)]** 
   
    従業員に送信されるのは、SSO の URL のみです。 従業員は SSO を使用してログオンすることができます。 認証要求は IdP を介してリダイレクトされます。
   
    **[Automatic Selection (自動選択)]**
   
    SSO が有効ではない場合、システムの通常の URL が従業員に送信されます。 SSO が有効である場合は、従業員がパスワードを持っているかどうかがシステムによってチェックされます。 パスワードを持っていた場合は、SSO の URL と非 SSO の URL の両方が従業員に送信されます。 一方、従業員がパスワードを持っていない場合は、SSO の URL だけが従業員に送信されます。
   
    k. 変更を保存します。

9. クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]

10. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
    
    ![Azure AD のシングル サインオン][11]

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションでは、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_09.png) 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_03.png) 

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_04.png) 

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_05.png) 
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
   
    b. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
   
    c. **[次へ]**をクリックします。

6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_06.png) 
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[名]** ボックスに「**Britta**」と入力します。  
   
    b. **[姓]** ボックスに「**Simon**」と入力します。
   
    c. **[表示名]** ボックスに「**Britta Simon**」と入力します。
   
    d. **[ロール]** 一覧で **[ユーザー]** を選択します。
   
    e. **[次へ]**をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_07.png) 

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_08.png) 
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[新しいパスワード]** の値を書き留めます。
   
    b. ページの下部にある [完了]」を参照してください。   

### <a name="creating-an-sap-business-bydesign-test-user"></a>SAP Business ByDesign のテスト ユーザーの作成
このセクションでは、SAP Business ByDesign で Britta Simon というユーザーを作成します。 SAP Business ByDesign プラットフォームにユーザーを追加する方法についてご不明な点がある場合は、SAP Business ByDesign サポート チームにお問い合わせください。 

> [!NOTE]
> NameID 値は必ず、SAP Business ByDesign プラットフォームのユーザー名フィールドと一致させてください。
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションでは、Britta Simon に SAP Business ByDesign へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**SAP Business ByDesign に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[SAP Business ByDesign]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_50.png) 

3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203]

4. ユーザーの一覧で **[Britta Simon]**を選択します。

5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト
このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [SAP Business ByDesign] タイルをクリックすると、自動的に SAP Business ByDesign アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO1-->


