---
title: "チュートリアル: Azure Active Directory と AirWatch の統合 | Microsoft Docs"
description: "Azure Active Directory で AirWatch を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 8bb5433fb8d81635185d8bfc765ea240461e8a33
ms.openlocfilehash: 4659531575e21823441eb75905b79d79592fa695
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-airwatch"></a>チュートリアル: Azure Active Directory と AirWatch の統合
このチュートリアルでは、Azure と AirWatch の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* AirWatch でのシングル サインオン (SSO) が有効なサブスクリプション

このチュートリアルを完了すると、AirWatch に割り当てた Azure AD ユーザーは、AirWatch 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

* AirWatch のアプリケーション統合の有効化
* シングル サインオン (SSO) の構成
* ユーザー プロビジョニングの構成
* ユーザーの割り当て

![AirWatch](./media/active-directory-saas-airwatch-tutorial/IC791913.png "AirWatch")

## <a name="enable-the-application-integration-for-airwatch"></a>AirWatch のアプリケーション統合の有効化
このセクションでは、AirWatch のアプリケーション統合を有効にする方法について説明します。

**AirWatch のアプリケーション統合を有効にするには、次の手順に従います。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-airwatch-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-airwatch-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-airwatch-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-airwatch-tutorial/IC749322.png "Add an application from gallerry")
6. **[検索]** ボックスに、「**AirWatch**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-airwatch-tutorial/IC791914.png "Application Gallery")
7. 結果ウィンドウで **[AirWatch]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![AirWatch](./media/active-directory-saas-airwatch-tutorial/IC791915.png "AirWatch")
   
## <a name="configure-single-sign-on"></a>Configure single sign-on

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで AirWatch に対する認証を行えるようにする方法を説明します。  

この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。 この手順に慣れていない場合は、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **[AirWatch]** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-airwatch-tutorial/IC791916.png "Configure Single Sign-On")
2. **[ユーザーの AirWatch へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-airwatch-tutorial/IC791917.png "Configure Single Sign-On")
3. **[アプリケーション URL の構成]** ページの **[AirWatch サインオン URL]** テキスト ボックスに、AirWatch アプリケーションへのサインインにユーザーが使用する URL (例: ”*https:// companycode.awmdm.com/AirWatch/Login?gid=companycode*”) を入力し、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-airwatch-tutorial/IC791918.png "Configure App URL")
4. **[AirWatch でのシングル サインオン構成]** ページで、**[証明書のダウンロード]** をクリックし、コンピューターに証明書ファイルを保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-airwatch-tutorial/IC791919.png "Configure Single Sign-On")
5. 別の Web ブラウザーのウィンドウで、管理者として AirWatch 企業サイトにログインします。
6. 左側のナビゲーション ウィンドウで、**[Accounts]**、**[Administrators]** の順にクリックします。
   
   ![管理者](./media/active-directory-saas-airwatch-tutorial/IC791920.png "Administrators")
7. **[Settings]** メニューを展開し、**[Directory Services]** をクリックします。
   
   ![設定](./media/active-directory-saas-airwatch-tutorial/IC791921.png "Settings")
8. **[User]** タブをクリックし、**[Base DN]** テキスト フィールドにドメイン名を入力してから **[Save]** をクリックします。
   
   ![ユーザー](./media/active-directory-saas-airwatch-tutorial/IC791922.png "User")
9. [ **Server** ] タブをクリックします。
   
   ![サーバー](./media/active-directory-saas-airwatch-tutorial/IC791923.png "Server")
10. 次の手順に従います。
    
   ![アップロード](./media/active-directory-saas-airwatch-tutorial/IC791924.png "Upload")    
    1. **[Directory Type]** として **[None]** を選択します。
    2. [ **Use SAML For Authentication**] を選択します。
    3. ダウンロードした証明書をアップロードするには、[ **Upload**] をクリックします。
11. [ **Request** ] セクションで、次の手順に従います。
    
    ![要求](./media/active-directory-saas-airwatch-tutorial/IC791925.png "Request")  
    1. **[Request Binding Type]** として **[POST]** を選択します。
    2. Azure クラシック ポータルの **[Airwatch でのシングル サインオンの構成]** ダイアログ ページで、**[シングル サインオン サービス URL]** の値をコピーし、**[ID プロバイダー シングル サインオン URL]** テキスト ボックスに貼り付けます。
    3. **[NameID Format]** として **[Email Address]** を選択します。
    4. [ **Save**] をクリックします。
12. [ **User** ] タブをもう一度クリックします。
    
    ![ユーザー](./media/active-directory-saas-airwatch-tutorial/IC791926.png "User")
13. [ **Attribute** ] セクションで、次の手順に従います。
    
    ![属性](./media/active-directory-saas-airwatch-tutorial/IC791927.png "Attribute")
    1. **[Object Identifier]** ボックスに、**http://schemas.microsoft.com/identity/claims/objectidentifier** と入力します。
    2. **[Username]** ボックスに、**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** と入力します。
    3. **[Display Name]** ボックスに、**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname** と入力します。
    4. **[First Name]** ボックスに、**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname** と入力します。
    5. **[Last Name]** ボックスに、**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname** と入力します。
    6. **[Email]** ボックスに、**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** と入力します。
    7. **[Save]**をクリックします。
14. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
    
   ![シングル サインオンの構成](./media/active-directory-saas-airwatch-tutorial/IC791928.png "Configure Single Sign-On")
    
## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]

Azure AD ユーザーが AirWatch にログインできるようにするには、そのユーザーを AirWatch にプロビジョニングする必要があります。

* AirWatch の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **AirWatch** 企業サイトに管理者としてログインします。
2. 左側のナビゲーション ウィンドウで、**[Accounts]**、**[Users]** の順にクリックします。
   
   ![ユーザー](./media/active-directory-saas-airwatch-tutorial/IC791929.png "Users")
3. **[Users]** メニューで、**[List View]**、**[Add] \> [Add User]** の順にクリックします。
   
   ![ユーザーの追加](./media/active-directory-saas-airwatch-tutorial/IC791930.png "Add User")
4. [ **Add / Edit User** ] ダイアログで、次の手順を実行します。
   
   ![ユーザーの追加](./media/active-directory-saas-airwatch-tutorial/IC791931.png "Add User")   
   1. 関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの **[Username]**、**[Password]**、**[Confirm Password]**、**[First Name]**、**[Last Name]**、**[Email Address]** を入力します。
   2. **[Save]**をクリックします。

>[!NOTE]
>他の AirWatch ユーザー アカウントの作成ツールまたは AirWatch から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
>  

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを AirWatch に割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **AirWatch** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-airwatch-tutorial/IC791932.png "Assign Users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-airwatch-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


