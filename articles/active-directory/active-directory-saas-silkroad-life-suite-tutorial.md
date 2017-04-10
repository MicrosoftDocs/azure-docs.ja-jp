---
title: "チュートリアル: Azure Active Directory と SilkRoad Life Suite の統合 | Microsoft Docs"
description: "Azure Active Directory と SilkRoad Life Suite の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: ecf4e31ecea00d003fc47ea4cebb781ca58957f7
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>チュートリアル: Azure Active Directory と SilkRoad Life Suite の統合
このチュートリアルの目的は、SilkRoad Life Suite と Azure Active Directory (Azure AD) を統合する方法を説明することです。 

SilkRoad Life Suite と Azure AD の統合には、次の利点があります。 

* SilkRoad Life Suite にアクセスする Azure AD ユーザーを制御できます。 
* ユーザーが自分の Azure AD アカウントで自動的に SilkRoad Life Suite にシングル サインオン (SSO) できるようにします

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
SilkRoad Life Suite と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* SilkRoad Life Suite での SSO が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。 

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD の SSO をテストできるようにすることです。

このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの SilkRoad Life Suite の追加 
2. Azure AD SSO の構成とテスト

## <a name="add-silkroad-life-suite-from-the-gallery"></a>ギャラリーからの SilkRoad Life Suite の追加
Azure AD への SilkRoad Life Suite の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SilkRoad Life Suite を追加する必要があります。

**ギャラリーから SilkRoad Life Suite を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]

6. 検索ボックスに、「 **SilkRoad Life Suite**」と入力します。
   
    ![アプリケーション][5]

7. 結果ウィンドウで **[SilkRoad Life Suite]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![アプリケーション][50]

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、SilkRoad Life Suite で Azure AD の SSO を構成し、テストする方法について説明することです。

SSO を機能させるには、Azure AD ユーザーに対応する SilkRoad Life Suite ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SilkRoad Life Suite の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、SilkRoad Life Suite の **[Username]** の値として割り当てることで確立されます。

SilkRoad Life Suite で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[SilkRoad Life Suite テスト ユーザーの作成](#creating-a-silkroad-life-suite-test-user)** - SilkRoad Life Suite で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションの目的は、Azure クラシック ポータルで Azure AD の SSO を有効にすることと、SilkRoad Life Suite アプリケーションで SSO を構成することです。

**SilkRoad Life Suite で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. SilkRoad 企業サイトに管理者としてサインオンします。 

  >[!NOTE] 
  > Microsoft Azure AD とフェデレーションを構成するために SilkRoad Life Suite の認証アプリケーションへのアクセス権を取得するには、SilkRoad サポートまたは SilkRoad サービス担当者にお問い合わせください。
  > 

2. **[サービス プロバイダー]** に移動して、**[フェデレーションの詳細]** をクリックします。 
   
    ![Azure AD のシングル サインオン][10] 

3. **[Download Federation Metadata (フェデレーション メタデータのダウンロード)]**をクリックし、メタデータ ファイルをコンピューターに保存します。
   
    ![Azure AD のシングル サインオン][11] 

4. Azure クラシック ポータルの **SilkRoad Life Suite** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログ ボックスを開きます。
   
    ![Configure Single Sign-On][6] 

5. **[ユーザーの SilkRoad Life Suite へのアクセスを設定してください]** ページで、**[Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Azure AD のシングル サインオン][7] 

6. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のシングル サインオン][8]   
 1. **[サインオン URL]** ボックスに、ユーザーが SilkRoad Life Suite サイトへのサインオンに使用する URL を入力します (例: *https://defcompanytest-test-redcarpet.silkroad-eng.com/Authentication/*)。  
 2. ダウンロードした **Silkroad** メタデータ ファイルを開きます。 
 3. **AssertionConsumerService** タグを探し、**Location** 属性をコピーします。         
   
    ![Azure AD のシングル サインオン][21] 
 4. **[応答 URL]** テキスト ボックスに値を貼り付けます。  
 5. **[次へ]**をクリックします。

6. **[SilkRoad Life Suite でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![Azure AD のシングル サインオン][9]  
 1. [証明書のダウンロード] をクリックし、コンピューターにファイルを保存します。  
 2. **[次へ]**をクリックします。

7. **SilkRoad** アプリケーションで、**[Authentication Sources (認証ソース)]** をクリックします。
   
    ![Azure AD のシングル サインオン][12] 

8. **[Add Authentication Source (認証ソースの追加)]**をクリックします。 
   
    ![Azure AD Single Sign-On][13] 

9. **[Add Authentication Source (認証ソースの追加)]** セクションで、次の手順に従います。 
   
    ![Azure AD のシングル サインオン][14]  
 1. **[Option 2 - Metadata File]** の下の **[Browse]** をクリックして、ダウンロードしたメタデータ ファイルをアップロードします。  
 2. **[Create Identity Provider using File Data]** をクリックします。

10. **[Authentication Sources (認証ソース)]** セクションで、**[編集]** をクリックします。 
    
     ![Azure AD のシングル サインオン][15] 

11. **[Edit Authentication Source (認証ソースの編集)]** ダイアログ ボックスで、次の手順を実行します。 
    
     ![Azure AD のシングル サインオン][16] 
 1. **[Enabled]** で **[Yes]** を選択します。   
 2. **[IdP Description]** ボックスに構成の説明を入力します (例: *Azure AD の SSO*)。  
 3. **[IdP Name]** ボックスに、構成の固有の名前を入力します (例: *Azure SP*)。  
 4. **[Save]**をクリックします。

12. その他のすべての認証のソースを無効にします。 
    
     ![Azure AD のシングル サインオン][17]

13. Azure クラシック ポータルの **[シングル サインオンの確認]** ページで、**[次へ]** をクリックします。  
    
     ![Azure AD のシングル サインオン][18]

14. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。
    
     ![Azure AD のシングル サインオン][19]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_09.png)  

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_03.png) 

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_04.png) 

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_05.png)  
 1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。  
 2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。 
 3. **[次へ]**をクリックします。

6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_06.png)  
 1. **[名]** ボックスに「**Britta**」と入力します。    
 2. **[姓]** ボックスに「**Simon**」と入力します。 
 3. **[表示名]** ボックスに「**Britta Simon**」と入力します。 
 4. **[ロール]** 一覧で **[ユーザー]** を選択します。
 5. **[次へ]**をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_07.png) 

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_08.png)  
 1. **[新しいパスワード]** の値を書き留めます。 
 2. ページの下部にある **[完了]**」を参照してください。   

### <a name="create-a-silkroad-life-suite-test-user"></a>SilkRoad Life Suite テスト ユーザーの作成
このセクションの目的は、SilkRoad Life Suite で Britta Simon というユーザーを作成することです。 Britta は、Azure AD 内の Britta の *emailaddress*に一致する SSO ID ( **AuthParam** と呼ばれることがあります) を持っている必要があります。

**SilkRoad Life Suite で Britta Simon というユーザーを作成するには、次の手順に従います。**

- SilkRoad Life Suite サポート チームに、Azure AD 内の Britta Simon の **emailaddress** と同じ値の **SSO ID** 属性を持つユーザーを作成するように依頼します。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に SilkRoad Life Suite へのアクセスを許可することで、このユーザーが Azure の SSO を使用できるようにすることです。

![ユーザーの割り当て][200] 

**SilkRoad Life Suite に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[SilkRoad Life Suite]**を選択します。
   
    ![ユーザーの割り当て][202] 

3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 

4. ユーザーの一覧で **[Britta Simon]**を選択します。

5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。  

アクセス パネルで SilkRoad Life Suite のタイルをクリックすると、自動的に SilkRoad Life Suite アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_01.png
[50]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_02.png

[6]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_03.png
[8]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_04.png
[9]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_05.png
[10]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_13.png
[18]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_06.png
[19]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_15.png


[200]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_14.png
[203]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_205.png






