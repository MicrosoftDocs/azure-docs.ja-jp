---
title: "チュートリアル: Azure Active Directory と Sprinklr の統合 | Microsoft Docs"
description: "Azure Active Directory で Sprinklr を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 3/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 44b5314a250d88f7ea2f8db2c1270a9090f083cd
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>チュートリアル: Azure Active Directory と Sprinklr の統合
このチュートリアルでは、Azure と Sprinklr の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Sprinklr テナント

このチュートリアルを完了すると、Sprinklr に割り当てた Azure AD ユーザーは、Sprinklr 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Sprinklr のアプリケーション統合の有効化
2. シングル サインオン (SSO) の構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-sprinklr-tutorial/IC782900.png "Scenario")

## <a name="enable-the-application-integration-for-sprinklr"></a>Sprinklr のアプリケーション統合の有効化
このセクションでは、Sprinklr のアプリケーション統合を有効にする方法について説明します。

**Sprinklr のアプリケーション統合を有効にするには、次の手順に従います。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![Active Directory](./media/active-directory-saas-sprinklr-tutorial/IC700993.png "Active Directory")

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション](./media/active-directory-saas-sprinklr-tutorial/IC700994.png "Applications")

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーションの追加](./media/active-directory-saas-sprinklr-tutorial/IC749321.png "Add application")

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-sprinklr-tutorial/IC749322.png "Add an application from gallerry")

6. **検索ボックス**に、「**Sprinklr**」と入力します。
   
    ![アプリケーション ギャラリー](./media/active-directory-saas-sprinklr-tutorial/IC782901.png "Application Gallery")

7. 結果ウィンドウで **[Sprinklr]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Sprinklr](./media/active-directory-saas-sprinklr-tutorial/IC782902.png "Sprinklr")

## <a name="configure-single-sign-on"></a>Configure single sign-on
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、Sprinklr で Azure AD のユーザー アカウントを使用してユーザーを認証できるようにする方法を説明します。 


この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。  

この手順に慣れていない場合は、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Sprinklr** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-sprinklr-tutorial/IC782903.png "Configure single sign-on")

2. **[ユーザーの Sprinklr へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-sprinklr-tutorial/IC782904.png "Configure single sign-on")

3. **[アプリケーション URL の構成]** ページで、**[Sprinklr サインイン URL]** ボックスに、"*https://\<テナント名\>.sprinklr.com*" というパターンで URL を入力し、**[次へ]** をクリックします。
   
    ![アプリケーション URL の構成](./media/active-directory-saas-sprinklr-tutorial/IC782905.png "Configure App URL")

4. **[Sprinklr でのシングル サインオンの構成]** ページで、証明書をダウンロードするために **[証明書のダウンロード]** をクリックし、証明書ファイルをコンピューターに保存します。
   
    ![シングル サインオンの構成](./media/active-directory-saas-sprinklr-tutorial/IC782906.png "Configure single sign-on")

5. 別の Web ブラウザー ウィンドウで、Sprinklr 企業サイトに管理者としてログインします。

6. **[管理] \> [設定]** の順に選択します。
   
    ![Administration](./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Administration")

7. 左側のパネルで、**[Manage Partner] \> [Single Sign on]** の順に選択します。
   
    ![Manage Partner](./media/active-directory-saas-sprinklr-tutorial/IC782908.png "Manage Partner")

8. **[+Add Single Sign Ons]**をクリックします。
   
    ![Single Sign-Ons](./media/active-directory-saas-sprinklr-tutorial/IC782909.png "Single Sign-Ons")

9. **[Single Sign on]** ページで、次の手順に従います。
   
    ![Single Sign-Ons](./media/active-directory-saas-sprinklr-tutorial/IC782910.png "Single Sign-Ons")
  1. **[Name]** テキスト ボックスに、構成の名前を入力します (例: *WAADSSOTest*)。
  2. **[Enabled]**を選択します。
  3. **[Use new SSO Certificate]**を選択します。
  4. ダウンロードした証明書から **base-64 でエンコードされた** ファイルを作成します。  
  
     >[!TIP]
     >詳細については、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。 
     >    
     
  5. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして **[ID プロバイダー証明書]** テキスト ボックスに貼り付けます。
  6. Azure クラシック ポータルの **[Sprinklr での SSO の構成]** ダイアログ ボックスで、次の手順を実行します。
     *  **[プロバイダー ID の識別]** の値をコピーし、**[エンティティ ID]** テキストボックスに貼り付けます。
     * **[リモート ログイン URL]** の値をコピーし、**[ID プロバイダーのログイン URL]** テキストボックスに貼り付けます。
     * **[リモート ログアウト URL]** の値をコピーし、**[ID プロバイダーのログアウト URL]** テキストボックスに貼り付けます。
  7. **[SAML User ID Type]** として**[Assertion contains User”s sprinklr.com username]** を選択します。
  8. **[SAML User ID Location]** として **[User ID is in the Name Identifier element of the Subject statement]** を選択します。
  9. **[Save]**をクリックします。
       
    ![SAML](./media/active-directory-saas-sprinklr-tutorial/IC782911.png "SAML")
10. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
    
    ![シングル サインオンの構成](./media/active-directory-saas-sprinklr-tutorial/IC782912.png "Configure single sign-on")

## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]
AAD ユーザーがサインインできるようにするには、ユーザーを Sprinklr アプリケーションにプロビジョニングする必要があります。  
このセクションでは、Sprinklr で AAD ユーザー アカウントを作成する方法について説明します。

### <a name="to-provision-a-user-account-in-sprinklr-perform-the-following-steps"></a>ユーザー アカウントを Sprinklr にプロビジョニングするには、次の手順に従います。
1. Sprinklr 企業サイトに管理者としてログインします。

2. **[管理] \> [設定]** の順に選択します。
   
    ![Administration](./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Administration")

3. 左側のパネルで、**[クライアントの管理] \> [ユーザー]** の順に選択します。
   
    ![設定](./media/active-directory-saas-sprinklr-tutorial/IC782914.png "Settings")

4. **[ユーザーの追加]**をクリックします。
   
    ![設定](./media/active-directory-saas-sprinklr-tutorial/IC782915.png "Settings")

5. **[Edit user]** ダイアログで、次の手順に従います。
   
    ![Edit user](./media/active-directory-saas-sprinklr-tutorial/IC782916.png "Edit user") 
  1. **[Email]**、**[First Name]**、および **[Last Name]** テキスト ボックスに、プロビジョニングする Azure AD のユーザー アカウントの情報を入力します。
  2. **[Password Disabled]**を選択します。
  3. **[Language]**を選択します。
  4. **[User Type]**を選択します。
  5. **[Update]**をクリックします。
   
     >[!IMPORTANT]
     >**[[Password Disabled]]** を選択する必要があります。 
     > 

6. **[Role]**に移動して、次の手順に従います。
   
    ![Partner Roles](./media/active-directory-saas-sprinklr-tutorial/IC782917.png "Partner Roles")
 1. **[Global]** ボックスの一覧から、**[ALL\_Permissions]** を選択します。  
 2. **[Update]**をクリックします。

>[!NOTE]
>Sprinklr から提供されている他の Sprinklr ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。 
> 

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを Sprinklr に割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。

2. **Sprinklr ** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-sprinklr-tutorial/IC782918.png "Assign users")

3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
    ![はい](./media/active-directory-saas-sprinklr-tutorial/IC767830.png "Yes")

SSO の設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


