---
title: "チュートリアル: Azure Active Directory と Central Desktop の統合 | Microsoft Docs"
description: "Azure Active Directory で Central Desktop を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 325d92e493f6e011367d2c85b52c92838327101e
ms.openlocfilehash: fe32c1d68040ceb9d9de2ad6c4a6dc9ea93f5aef
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>チュートリアル: Azure Active Directory と Central Desktop の統合
このチュートリアルでは、Azure と Central Desktop の統合について説明します。 このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Central Desktop でのシングル サインオンが有効なサブスクリプション/Central Desktop テナント

このチュートリアルで説明するシナリオは、次の要素で構成されています。

* Central Desktop のアプリケーション統合の有効化
* シングル サインオン (SSO) の構成
* ユーザー プロビジョニングの構成
* ユーザーの割り当て

![シナリオ](./media/active-directory-saas-central-desktop-tutorial/IC769558.png "Scenario")

## <a name="enable-the-application-integration-for-central-desktop"></a>Central Desktop のアプリケーション統合の有効化
このセクションでは、Central Desktop のアプリケーション統合を有効にする方法を説明します。

**Central Desktop のアプリケーション統合を有効にするには、次の手順を実行します。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-central-desktop-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-central-desktop-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-central-desktop-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-central-desktop-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**Central Desktop**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-central-desktop-tutorial/IC769559.png "Application Gallery")
7. 結果ウィンドウで **[Central Desktop]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Central Desktop](./media/active-directory-saas-central-desktop-tutorial/IC769560.png "Central Desktop")
   
## <a name="configure-single-sign-on"></a>Configure single sign-on

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Central Desktop に対する認証を行えるようにする方法を説明します。

この手順の途中で、Base-64 でエンコードされた証明書を Central Desktop テナントにアップロードする必要があります。  
この手順に慣れていない場合は、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Central Desktop** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-central-desktop-tutorial/IC749323.png "Configure single sign-on")
2. **[ユーザーの Central Desktop へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-central-desktop-tutorial/IC777628.png "Configure single sign-on")
3. **[アプリケーション URL の構成]** ページで、次の手順を実行し、**[次へ]** をクリックします。 
   
   1. **[Central Desktop サインイン URL]** ボックスに、Central Desktop テナント の URL を入力します (例: *http://contoso.centraldesktop.com*)。
   2. [Central Desktop 応答 URL] ボックスに、Central Desktop AssertionConsumerService URL を入力します (例: https://contoso.centraldesktop.com/saml2-assertion.php)。
   
   >[!NOTE]
   >この値は、Central Desktop のメタデータから取得できます (例: *http://contoso.centraldesktop.com*)。
   >  
   
   ![アプリケーション URL の構成](./media/active-directory-saas-central-desktop-tutorial/IC769561.png "Configure app URL")
4. **[Central Desktop でのシングル サインオンの構成]** ページで、証明書をダウンロードするために、**[証明書のダウンロード]** をクリックし、証明書ファイルをコンピューターに保存します。
   
  ![シングル サインオンの構成](./media/active-directory-saas-central-desktop-tutorial/IC769562.png "Configure single sign-on")
5. **Central Desktop** テナントにログインします。
6. **[Settings]** に移動し、**[Advanced]** をクリックして、**[Single Sign On]** をクリックします。
   
  ![Setup - Advanced](./media/active-directory-saas-central-desktop-tutorial/IC769563.png "Setup - Advanced")
7. **[Single Sign On Settings]** ページで、次の手順を実行します。
   
  ![Single Sign On Settings](./media/active-directory-saas-central-desktop-tutorial/IC769564.png "Single Sign On Settings")
   
  1. **[Enable SAML v2 Single Sign On]**チェック ボックスをオンにします。
  2. Azure クラシック ポータルの **[Central Desktop でのシングル サインオンの構成]** ページにある **[発行者の URL]** の値をコピーし、**[SSO URL]** ボックスに貼り付けます。
  3. Azure クラシック ポータルの **[Central Desktop でのシングル サインオンの構成]** ページにある **[リモート ログイン URL]** の値をコピーし、**[SSO Login URL]** ボックスに貼り付けます。
  4. Azure クラシック ポータルの **[Central Desktop でのシングル サインオンの構成]** ページにある **[シングル サインアウト サービス URL]** の値をコピーし、**[SSO Logout URL]** ボックスに貼り付けます。
8. **[Message Signature Verification Method]** セクションで、次の手順を実行します。
   
   ![Message Signature Verification Method](./media/active-directory-saas-central-desktop-tutorial/IC769565.png "Message Signature Verification Method")
   
  1. **[Certificate]**を選択します。
  2. **[SSO Certificate]** ボックスの一覧で、**[RSH SHA256]** を選択します。
  3. ダウンロードした証明書からテキスト ファイルを作成し、そのテキスト ファイルの内容をコピーして **[SSO Certificate]** フィールドに貼り付けます。  
     >[!TIP]
     >詳細については、 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)
      >  
   4. **[Display a link to your SAMLv2 login page]**チェック ボックスをオンにします。
9. **[Update]**をクリックします。
10. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
    
    ![シングル サインオンの構成](./media/active-directory-saas-central-desktop-tutorial/IC769566.png "Configure single sign-on")
    
## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]

AAD ユーザーがサインインできるようにするには、ユーザーを Central Desktop アプリケーションにプロビジョニングする必要があります。 このセクションでは、Central Desktop で AAD ユーザー アカウントを作成する方法について説明します。

**Central Desktop にユーザー アカウントをプロビジョニングするには:**
1. Central Desktop テナントにログインします。
2. **[People]\>[Internal Members]** の順にクリックします。
3. **[Add Internal Members]**をクリックします。
   
  ![ユーザー](./media/active-directory-saas-central-desktop-tutorial/IC781051.png "People")
4. **[Email Address of New Members]** ボックスに、プロビジョニングする AAD アカウントを入力し、**[Next]** をクリックします。
   
  ![Email Addresses of New Members](./media/active-directory-saas-central-desktop-tutorial/IC781052.png "Email Addresses of New Members")
5. **[Add Internal member(s)]**をクリックします。
   
  ![Add Internal Member](./media/active-directory-saas-central-desktop-tutorial/IC781053.png "Add Internal Member")
   
   >[!NOTE]
   >追加したユーザーには、アカウントをアクティブ化するためにクリックする必要がある確認リンクが含まれた電子メールが送信されます。
   > 

>[!NOTE]
>Central Desktop から提供されている他の Central Desktop ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
>  

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを Central Desktop に割り当てるには、次の手順を実行します。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Central Desktop** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-central-desktop-tutorial/IC769567.png "Assign users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-central-desktop-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


