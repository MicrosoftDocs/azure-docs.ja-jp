---
title: "チュートリアル: Azure Active Directory と ADP eTime の統合 | Microsoft Docs"
description: "Azure Active Directory と ADP eTime の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: a3e9f0be-19ed-4b99-a180-619e7624fc26
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: 84c23a43b87c7357ed99f33c275717c68cf7c8bb
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-adp-etime"></a>チュートリアル: Azure Active Directory と ADP eTime の統合
このチュートリアルの目的は、ADP eTime と Azure Active Directory (Azure AD) を統合する方法を説明することです。

ADP eTime と Azure AD の統合には、次の利点があります。

* ADP eTime にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に ADP eTime にシングル サインオン (SSO) できるようにします
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Azure AD と ADP eTime の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* ADP eTime での SSO が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの ADP eTime の追加
2. Azure AD SSO の構成とテスト

## <a name="add-adp-etime-from-the-gallery"></a>ギャラリーからの ADP eTime の追加
Azure AD への ADP eTime の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に ADP eTime を追加する必要があります。

**ギャラリーから ADP eTime を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「 **ADP eTime**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_01.png)
7. 結果ウィンドウで **[ADP eTime]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_06.png)

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、ADP eTime で Azure AD の SSO を構成し、テストする方法について説明することです。

SSO を機能させるには、Azure AD ユーザーに対応する ADP eTime ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと ADP eTime の関連ユーザーの間で、リンク関係が確立されている必要があります。  

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を ADP eTime の **[Username (ユーザー名)]** の値として割り当てます。

ADP eTime で Azure AD の SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[ADP eTime テスト ユーザーの作成](#creating-a-adpetime-test-user)** - ADP eTime で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-sso"></a>Azure AD SSO の構成
このセクションの目的は、Azure クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、ADP eTime アプリケーションでシングル サインオンを構成することです。

ADP eTime アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットはその例です。 要求の名前は常に **"PersonImmutableID"** であり、その値はユーザーの EmployeeID を含む ExtensionAttribute2 にマップされています。 

ここで、Azure AD から ADP eTime にマップしているユーザーは EmployeeID では完了となりますが、アプリケーションの設定に基づいて、別の値にマップできます。 そのため、ユーザーの正しい ID を使用し、その値を **"PersonImmutableID"** 要求でマップするには、まず ADP eTime チームと協力してください。  

![[シングル サインオンの構成]](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_02.png) 

SAML アサーションを構成する前に、ADP eTime サポート チームに連絡し、テナントの一意識別子属性の値を請求する必要があります。 この値は、アプリケーションのカスタム要求を構成するのに必要です。

**ADP eTime で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **ADP eTime** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログ ボックスを開きます。
   
    ![Configure Single Sign-On][6] 
2. **[ユーザーの ADP eTime へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_03.png) 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_04.png) 
  1. **[応答 URL]** ボックスに、ユーザーが ADP eTime アプリケーションへのサインオンに使用する URL を `https://<server name>.adp.com/affwebservices/public/saml2assertionconsumer` 形式で入力します。
  2. **[次へ]**をクリックします。
4. **[ADP eTime でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_05.png)  
  1. **[メタデータのダウンロード]** をクリックし、コンピューターにファイルを保存します。 
  2. **[次へ]**をクリックします。
2. 使用しているアプリケーション用に構成された SSO を入手するには、ADP eTime のサポート チームに連絡し、SSO 統合用に構成できるように、ダウンロードしたメタデータファイルを電子メールで送ります。
   
   >[!NOTE]
   >**ADP eTime** チームがインスタンスを構成したら、そこから **RelayState** 値を入手します。これを構成するには、以下の手順に従います。 この構成後に、統合をテストすることができます。 このアプリケーションの統合が機能するには、この構成が重要であることに注意してください。
   >  
6. Azure AD で RelayState 値を構成するには、次の手順に従います。 
  1. [Azure 管理ポータル](https://portal.azure.com)に管理者としてログインします。
  2. 左のナビゲーション ウィンドウで、**[その他のサービス]** をクリックします。  

    ![Configure Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_07.png) 
 3. **[検索]** ボックスに「**Azure Active Directory**」と入力し、関連リンクをクリックします。 

    ![[シングル サインオンの構成]](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_08.png)   
 4. **[エンタープライズ アプリケーション]** をクリックします。 

    ![[シングル サインオンの構成]](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_09.png)  
 5. **[管理]** セクションで、**[すべてのアプリケーション]** をクリックします。
 
    ![Configure Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_10.png) 
 6. **[検索]** ボックスに「**ADP eTime**」と入力し、関連リンクをクリックします。 
   
    ![Configure Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_11.png)  
 7. **[管理]** セクションで、**[シングル サインオン]** をクリックします。 
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_12.png) 
 8. **[詳細な URL 設定の表示]** を選択します。
 
     ![[シングル サインオンの構成]](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_13.png) 
 9. **[リレー状態]** ボックスに、次の形式で値を入力し、設定を保存します。 
 
    * 運用環境: `https://fed.adp.com/saml/fedlanding.html?<id>` 
    * ステージング環境: `https://fed-stag.adp.com/saml/fedlanding.html?PORTAL`
     
    ![[シングル サインオンの構成]](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_14.png) 

4. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
 
 ![Azure AD のシングル サインオン][10]
5. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
 ![Azure AD のシングル サインオン][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。  

* ユーザーの一覧で **[Britta Simon]**を選択します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adpetime-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adpetime-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adpetime-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adpetime-tutorial/create_aaduser_05.png)  
 1. **[ユーザーの種類]** として **[組織内の新しいユーザー]** を選択します。
 2. **[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。 
 3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adpetime-tutorial/create_aaduser_06.png)  
 1. **[名]** ボックスに「**Britta**」と入力します。   
 2. **[姓]** ボックスに「**Simon**」と入力します。 
 3. **[表示名]** ボックスに「**Britta Simon**」と入力します。 
 4. **[ロール]** 一覧で **[ユーザー]** を選択します。 
 5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adpetime-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adpetime-tutorial/create_aaduser_08.png)  
 1. **[新しいパスワード]** の値を書き留めます。
 2. **[完了]** をクリックします。   

### <a name="create-a-adp-etime-test-user"></a>ADP eTime テスト ユーザーの作成
このセクションの目的は、ADP eTime で Britta Simon というユーザーを作成することです。 ADP eTime サポート チームと協力し、ADP eTime アカウントにユーザーを追加してください。 

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、ADP eTime のサポート チームにお問い合わせください。
> 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に ADP eTime へのアクセスを許可し、このユーザーが Azure の SSO を使用できるようにすることです。

![ユーザーの割り当て][200] 

**ADP eTime に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルのディレクトリ ビューで、トップ メニューにある **[アプリケーション]** をクリックしてアプリケーション ビューを開きます。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[ADP eTime]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで [ADP eTime] タイルをクリックすると、自動的に ADP eTime アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_205.png

