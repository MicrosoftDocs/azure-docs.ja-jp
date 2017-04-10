---
title: "チュートリアル: Azure Active Directory と ADP GlobalView の統合 | Microsoft Docs"
description: "Azure Active Directory と ADP GlobalView の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: ffb6464f-714d-41a9-869a-2b7e5ae9f125
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0f767b96fa8f8f9d1a22709633bee64f0477ffb3
ms.openlocfilehash: 46fbe3233af22605d4cd89227b91313128e523f9
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-adp-globalview"></a>チュートリアル: Azure Active Directory と ADP GlobalView の統合
このチュートリアルの目的は、ADP GlobalView と Azure Active Directory (Azure AD) を統合する方法を説明することです。  

ADP GlobalView と Azure AD の統合には、次の利点があります。

* ADP GlobalView にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に ADP GlobalView にシングル サインオン (SSO) できるようにします
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Azure AD と ADP GlobalView の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* ADP GlobalView での SSO が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD の SSO をテストできるようにすることです。  

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの ADP GlobalView の追加
2. Azure AD SSO の構成とテスト

## <a name="add-adp-globalview-from-the-gallery"></a>ギャラリーからの ADP GlobalView の追加
Azure AD への ADP GlobalView の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に ADP GlobalView を追加する必要があります。

**ギャラリーから ADP GlobalView を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「 **ADP GlobalView**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_01.png)
7. 結果ウィンドウで **[ADP GlobalView]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_06.png)

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、ADP GlobalView で Azure AD の SSO を構成し、テストする方法について説明することです。

SSO を機能させるには、Azure AD ユーザーに対応する ADP GlobalView ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと ADP GlobalView の関連ユーザーの間で、リンク関係を確立する必要があります。  

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を ADP GlobalView の **[Username (ユーザー名)]** の値として割り当てます。

ADP GlobalView で Azure AD の SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[ADP GlobalView テスト ユーザーの作成](#creating-a-adp-globalview-test-user)** - ADP GlobalView で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成
このセクションの目的は、Azure クラシック ポータルで Azure AD の SSO を有効にすることと、ADP GlobalView アプリケーションで SSO を構成することです。

ADP GlobalView アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 

次のスクリーンショットはその例です。 要求の名前は常に **"PersonImmutableID"** であり、その値はユーザーの EmployeeID を含む ExtensionAttribute2 にマップされています。 ここで、Azure AD から ADP GlobalView にマップしているユーザーは EmployeeID では完了となりますが、アプリケーションの設定に基づいて、別の値にマップできます。 

最初に ADP GlobalView チームと協力してユーザーの正しい ID を使用し、その値を **"PersonImmutableID"** 要求でマップすることができます。 また、電子メールおよびユーザー ID の要求を図に示すようにマップすることができます。

![[シングル サインオンの構成]](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_02.png) 

SAML アサーションを構成する前に、ADP GlobalView サポート チームに連絡し、テナントの一意識別子属性の値を請求する必要があります。 この値は、アプリケーションのカスタム要求を構成するのに必要です。

**ADP GlobalView で Azure AD SSO を構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **ADP GlobalView** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![Configure Single Sign-On][6] 
2. **[ユーザーの ADP GlobalView へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_03.png) 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_04.png) 
  1. **[識別子]** ボックスに、ADP GlobalView アプリケーションの識別に使用する URL を、`https://<server name>.globalview.adp.com/federate2` または `https://<server name>.globalview.adp.com/federate` の形式で入力します。
  2. **[応答 URL]** ボックスに、ADP GlobalView アプリケーションに応答を投稿するために Azure AD で使用される URL を、`https://<server name>.globalview.adp.com/federate2/sp/ACS.saml2` または `https://<server name>.globalview.adp.com/federate/sp/ACS.saml2` の形式で入力します。
  3. **[次へ]**をクリックします。
4. **[ADP GlobalView でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_05.png)   
  1. **[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。
  2. **[次へ]**をクリックします。
5. アプリケーション用に構成された SSO を入手するには、ADP GlobalView サポート チームに連絡し、次のものを情報として提供してください。 
   
   * ダウンロードした**証明書**
   * **エンティティ ID**
   * **SAML SSO URL**
   * **シングル サインアウト サービス URL**

    >[!NOTE]
    >**ADP GlobalView** チームがインスタンスを構成したら、**RelayState** 値をチームから入手します。 それを構成するには、次の手順に従います。 この構成後に、統合をテストすることができます。 このアプリケーションの統合が機能するには、この構成が重要であることに注意してください。
    >

6. Azure AD で RelayState 値を構成するには、次の手順に従います。   
 1. [Azure 管理ポータル](https://portal.azure.com)に管理者としてログインします。
 2. 左のナビゲーション ウィンドウで、**[その他のサービス]** をクリックします。 
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_07.png)  
 3. **[検索]** ボックスに「**Azure Active Directory**」と入力し、関連リンクをクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_08.png)  
 4. **[エンタープライズ アプリケーション]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_09.png) 
 5. **[管理]** セクションで、**[すべてのアプリケーション]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_10.png) 
 6. **[検索]** ボックスに「**ADP eTime**」と入力し、関連リンクをクリックします。 
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_11.png) 
 7. **[管理]** セクションで、**[シングル サインオン]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_12.png)  
 8. **[詳細な URL 設定の表示]** を選択します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_13.png)
 9. **[リレー状態]** ボックスに、値を次の形式で URL を入力します。
   
    `https://<server name>.globalview.adp.com/gvolution/session/<instance name>/sso` 
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_14.png)  
 10. 設定を保存します。
7. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
8. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。  

* ユーザーの一覧で **[Britta Simon]**を選択します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_05.png)  
 1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。  
 2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。 
 3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_06.png)  
 1. **[名]** ボックスに「**Britta**」と入力します。    
 2. **[姓]** ボックスに「**Simon**」と入力します。 
 3. **[表示名]** ボックスに「**Britta Simon**」と入力します。 
 4. **[ロール]** 一覧で **[ユーザー]** を選択します。 
 5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_08.png)  
 1. **[新しいパスワード]** の値を書き留めます。  
 2. ページの下部にある **[完了]**」を参照してください。   

### <a name="create-a-adp-globalview-test-user"></a>ADP GlobalView テスト ユーザーの作成
このセクションの目的は、ADP GlobalView で Britta Simon というユーザーを作成することです。 ADP GlobalView サポート チームと協力し、ADP GlobalView アカウントにユーザーを追加してください。 

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、ADP GlobalView のサポート チームにお問い合わせください。
> 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に ADP GlobalView へのアクセスを許可し、このユーザーが Azure の SSO を使用できるようにすることです。

![ユーザーの割り当て][200] 

**ADP GlobalView に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[ADP GlobalView]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。  

アクセス パネルで [ADP GlobalView] タイルをクリックすると、自動的に ADP GlobalView アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_205.png

