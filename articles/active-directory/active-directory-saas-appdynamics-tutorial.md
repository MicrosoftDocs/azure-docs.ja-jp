---
title: "チュートリアル: Azure Active Directory と AppDynamics の統合 | Microsoft Docs"
description: "Azure Active Directory で AppDynamics を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 25fd1df0-411c-4f55-8be3-4273b543100f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 6d2c8c8170562f398ae0dbb7339b0e9bfce86313
ms.openlocfilehash: 1feed4aac0241096fcccd715e01418ff86b8c06d
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-appdynamics"></a>チュートリアル: Azure Active Directory と AppDynamics の統合
このチュートリアルでは、Azure と AppDynamics の統合について説明します。 このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* AppDynamics でのシングル サインオン (SSO) が有効なサブスクリプション

このチュートリアルを完了すると、AppDynamics に割り当てた Azure AD ユーザーは、AppDynamics 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

* AppDynamics のアプリケーション統合の有効化
* シングル サインオン (SSO) の構成
* ユーザー プロビジョニングの構成
* ユーザーの割り当て

![シナリオ](./media/active-directory-saas-appdynamics-tutorial/IC790209.png "Scenario")

## <a name="enabling-the-application-integration-for-appdynamics"></a>AppDynamics のアプリケーション統合の有効化
このセクションでは、AppDynamics のアプリケーション統合を有効にする方法を説明します。

**AppDynamics のアプリケーション統合を有効にするには、次の手順に従います。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-appdynamics-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-appdynamics-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-appdynamics-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-appdynamics-tutorial/IC749322.png "Add an application from gallerry")
6. **[検索]** ボックスに、「**AppDynamics**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-appdynamics-tutorial/IC790210.png "Application Gallery")
7. 結果ウィンドウで **[AppDynamics]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![AppDynamics](./media/active-directory-saas-appdynamics-tutorial/IC790211.png "AppDynamics")
   
## <a name="configure-single-sign-on"></a>Configure single sign-on

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで AppDynamics に対する認証を行えるようにする方法を説明します。  

この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。 この手順に慣れていない場合は、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **AppDynamics** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-appdynamics-tutorial/IC790212.png "Configure Single SignOn")
2. **[ユーザーの AppDynamics へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-appdynamics-tutorial/IC790213.png "Configure Single SignOn")
3. **[アプリケーション URL の構成]** ページの **[AppDynamics サインオン URL]** ボックスに、ユーザーが AppDynamics アプリケーションのサインオンに使用する URL (例: "*https://companyname.saas.appdynamics.com*") を入力して、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-appdynamics-tutorial/IC790214.png "Configure App URL")
4. **[AppDynamics でのシングル サインオン構成]** ページで、証明書をダウンロードするには、**[証明書のダウンロード]** をクリックし、コンピューターに証明書ファイルを保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-appdynamics-tutorial/IC790215.png "Configure Single SignOn")
5. 別の Web ブラウザーのウィンドウで、管理者として AppDynamics 企業サイトにログインします。
6. 上部にあるツールバーで **[Settings]**、**[Administration]** の順にクリックします。
   
   ![Administration](./media/active-directory-saas-appdynamics-tutorial/IC790216.png "Administration")
7. **[Authentication Provider]** タブをクリックします。
   
   ![Authentication Provider](./media/active-directory-saas-appdynamics-tutorial/IC790224.png "Authentication Provider")
8. **[Authentication Provider]** セクションで、次の手順を実行します。
   
   ![SAML Configuration](./media/active-directory-saas-appdynamics-tutorial/IC790225.png "SAML Configuration")   
   1. **[Authentication Provider]** として、**[SAML]** を選択します。
   2. Azure クラシック ポータルの **[AppDynamics でのシングル サインオンの構成]** ページで、**[リモート ログイン URL]** 値をコピーして、**[ログイン URL]** ボックスに貼り付けます。
   3. Azure クラシック ポータルの **[AppDynamics でのシングル サインオンの構成]** ページで、**[リモート ログアウト URL]** 値をコピーして、**[ログアウト URL]** ボックスに貼り付けます。
   4. ダウンロードした証明書から **base-64 でエンコードされた** ファイルを作成します。  
   
      >[!TIP]
      >詳細については、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。
      > 
   5. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、 **[証明書]** テキストボックスに貼り付けます。
   6. **[保存]**をクリックします。

     ![保存](./media/active-directory-saas-appdynamics-tutorial/IC777673.png "保存")
9. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-appdynamics-tutorial/IC790226.png "Configure Single SignOn")
   
## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]

Azure AD ユーザーが AppDynamics にログインできるようにするには、そのユーザーを AppDynamics にプロビジョニングする必要があります。  
AppDynamics の場合、プロビジョニングは手動で行います。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. AppDynamics 企業サイトに管理者としてログインします。
2. **[Users]** に移動して、[**+**] をクリックして **[Create User]** ダイアログを開きます。
   
   ![ユーザー](./media/active-directory-saas-appdynamics-tutorial/IC790229.png "Users")
3. **[Create User]** セクションで、次の手順に従います。
   
   ![Create User](./media/active-directory-saas-appdynamics-tutorial/IC790230.png "Create User")
   
   1. 関連するテキスト ボックスに、プロビジョニングする有効な AAD アカウントの **[Username]**、**[Name]**、**[Email]**、**[New Password]**、**[Repeat New Password]** を入力します。
   2. [**Save**] をクリックします。

>[!NOTE]
>他の AppDynamics ユーザー アカウント作成ツールまたは AppDynamics から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。
> 

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを AppDynamics に割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **AppDynamics** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-appdynamics-tutorial/IC790231.png "Assign Users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-appdynamics-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


