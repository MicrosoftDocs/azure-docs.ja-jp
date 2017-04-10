---
title: "チュートリアル: Azure Active Directory と TOPdesk - Public の統合 | Microsoft Docs"
description: "Azure Active Directory で TOPdesk - Public を使用して、シングル サインオンを有効にする方法、プロビジョニングを自動化する方法などについて説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1cef7ff21a8d076c89688f1fe75cebdb7c468199
ms.openlocfilehash: ee413f5044d541a7a127690fab0115341c7619f3
ms.lasthandoff: 02/11/2017


---
# <a name="tutorial-azure-directory-integration-with-topdesk---public"></a>チュートリアル: Azure Active Directory と TOPdesk - Public の統合
このチュートリアルでは、Azure と TOPdesk - Public の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* TOPdesk - Public でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、TOPdesk - Public に割り当てた Azure AD ユーザーは、TOPdesk - Public 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. TOPdesk - Public のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-topdesk-public-tutorial/IC790613.png "Scenario")

## <a name="enabling-the-application-integration-for-topdesk---public"></a>TOPdesk - Public のアプリケーション統合の有効化
このセクションでは、TOPdesk - Public のアプリケーション統合を有効にする方法を説明します。

### <a name="to-enable-the-application-integration-for-topdesk---public-perform-the-following-steps"></a>TOPdesk - Public のアプリケーション統合を有効にするには、次の手順を実行します。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![Active Directory](./media/active-directory-saas-topdesk-public-tutorial/IC700993.png "Active Directory")

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション](./media/active-directory-saas-topdesk-public-tutorial/IC700994.png "Applications")

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーションの追加](./media/active-directory-saas-topdesk-public-tutorial/IC749321.png "Add application")

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-topdesk-public-tutorial/IC749322.png "Add an application from gallerry")

6. **検索ボックス**に、「**TOPdesk - Public**」と入力します。
   
    ![アプリケーション ギャラリー](./media/active-directory-saas-topdesk-public-tutorial/IC790614.png "Application Gallery")

7. 結果ウィンドウで **[TOPdesk - Public]** を選び、**[完了]** をクリックしてアプリを追加します。
   
    ![TOPdesk Public](./media/active-directory-saas-topdesk-public-tutorial/IC791317.png "TOPdesk Public")

## <a name="configuring-single-sign-on"></a>シングル サインオンの構成
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで TOPdesk - Public に対する認証を行うことができるようにする方法を説明します。  
TOPdesk - Public のシングル サインオンを構成するには、ロゴのアイコン ファイルをアップロードする必要があります。 アイコン ファイルを取得するには、TOPdesk サポート チームにお問い合わせください。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順に従います。
1. **TOPdesk - Public** 企業サイトに管理者としてサインオンします。

2. **[TOPdesk]** メニューで **[Settings]** をクリックします。
   
    ![設定](./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "Settings")

3. **[Login Settings]**をクリックします。
   
    ![Login Settings](./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "Login Settings")

4. **[Login Settings]** メニューを展開し、**[General]** をクリックします。
   
    ![General](./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "General")

5. **[SAML login]** 構成セクションの **[Public]** で、次の手順に従います。
   
    ![Technical Settings](./media/active-directory-saas-topdesk-public-tutorial/IC790601.png "Technical Settings")
   
    a. **[Download]** をクリックしてパブリック メタデータ ファイルをダウンロードし、コンピューターにローカルに保存します。
   
    b. メタデータ ファイルを開いて、 **AssertionConsumerService** ノードを探します。
    ![AssertionConsumerService](./media/active-directory-saas-topdesk-public-tutorial/IC790619.png "AssertionConsumerService")
   
    c. **AssertionConsumerService** の値をコピーします。  
      
    > [!NOTE]
    > この値は、このチュートリアルの以降の「 **アプリケーション URL の構成** 」セクションで必要になります。
    > 
    > 

6. 別の Web ブラウザーのウィンドウで、 **Azure クラシック ポータル** に管理者としてログインします。

7. **TOPdesk - Public** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-topdesk-public-tutorial/IC790620.png "Configure Single Sign-On")

8. **[ユーザーの TOPdesk - Public へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-topdesk-public-tutorial/IC790621.png "Configure Single Sign-On")

9. **[アプリケーション URL の構成]** ページで、次の手順を実行します。
   
    ![アプリケーション URL の構成](./media/active-directory-saas-topdesk-public-tutorial/IC790622.png "Configure App URL")
   
    a. **[TOPdesk - Public サインオン URL]** テキストボックスに、ユーザーが TOPdesk - Public アプリケーションへのサインインに使用する URL (例: "*https://qssolutions.topdesk.net*") を入力します。
   
    b. **[TOPdesk – Public 応答 URL]** テキストボックスに、**TOPdesk - Public AssertionConsumerService URL** (例: "*https://qssolutions.topdesk.net/tas/public/login/saml*") を貼り付けます。
   
    c. **[次へ]**をクリックします。

10. **[TOPdesk - Public でのシングル サインオンの構成]** ページで、メタデータ ファイルをダウンロードするには、**[メタデータのダウンロード]** をクリックし、メタデータ ファイルをコンピューターのローカルに保存します。
    
    ![シングル サインオンの構成](./media/active-directory-saas-topdesk-public-tutorial/IC790623.png "Configure Single Sign-On")

11. 証明書ファイルを作成するには、次の手順を実行します。
    
    ![Certificate](./media/active-directory-saas-topdesk-public-tutorial/IC790606.png "Certificate")
    
    1. ダウンロードしたメタデータ ファイルを開きます。
    2. **fed:ApplicationServiceType** の **xsi:type** を持つ **RoleDescriptor** ノードを展開します。
    3. **X509Certificate** ノードの値をコピーします。
    4. コピーした **X509Certificate** の値をコンピューター上のファイル内にローカルに保存します。
12. TOPdesk - Public 企業サイトの **[TOPdesk]** メニューで、**[Settings]** をクリックします。
    
    ![設定](./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "Settings")
13. **[Login Settings]**をクリックします。
    
    ![Login Settings](./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "Login Settings")
14. **[Login Settings]** メニューを展開し、**[General]** をクリックします。
    
    ![General](./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "General")
15. **[Public]** セクションで、**[Add]** をクリックします。
    
    ![SAML ログイン](./media/active-directory-saas-topdesk-public-tutorial/IC790625.png "SAML ログイン")
16. **[SAML configuration assistant]** ダイアログ ページで、次の手順を実行します。
    
    ![SAML Configuration Assistant](./media/active-directory-saas-topdesk-public-tutorial/IC790608.png "SAML Configuration Assistant")
    
    a. ダウンロードしたメタデータ ファイルをアップロードするには、**[Federation Metadata]** で **[Browse]** をクリックします。

    b. 証明書ファイルをアップロードするには、**[Certificate (RSA)]** で **[Browse]** をクリックします。

    c. TOPdesk サポート チームから入手したロゴのファイルをアップロードするには、**[Logo icon]** の下の **[Browse]** をクリックします。

    d. **[User name attribute]** ボックスに、**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** と入力します。

    e. **[Display name]** テキスト ボックスに、構成の名前を入力します。

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 [ **Save**] をクリックします。

17. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
    
    ![シングル サインオンの構成](./media/active-directory-saas-topdesk-public-tutorial/IC790627.png "Configure Single Sign-On")

## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成
Azure AD ユーザーが TOPdesk - Public にログインできるようにするには、そのユーザーを TOPdesk - Public にプロビジョニングする必要があります。  
TOPdesk - Public の場合、プロビジョニングは手動で行います。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>ユーザー プロビジョニングを構成するには、次の手順に従います。
1. **TOPdesk - Public** 企業サイトに管理者としてサインオンします。

2. 上部のメニューで、**[TOPdesk]\>[New]\>[Support Files]\>[Person]** の順にクリックします。
   
    ![Person](./media/active-directory-saas-topdesk-public-tutorial/IC790628.png "Person")

3. [New Person] ダイアログで、次の手順を実行します。
   
    ![New Person](./media/active-directory-saas-topdesk-public-tutorial/IC790629.png "New Person")
   
    a. [General] タブをクリックします。

    b. [Surname] テキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの姓を入力します。
 
    c. アカウントの **[Site]** を選択します。
 
    d. **[保存]**をクリックします。

> [!NOTE]
> 他の TOPdesk - Public ユーザー アカウントの作成ツールまたは TOPdesk - Public から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 
> 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-topdesk---public-perform-the-following-steps"></a>ユーザーを TOPdesk - Public に割り当てるには、次の手順に従います。
1. Azure クラシック ポータルで、テスト アカウントを作成します。

2. **TOPdesk - Public** アプリ統合ページで、**[ユーザーの割り当て]** をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-topdesk-public-tutorial/IC790630.png "Assign Users")

3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
    ![はい](./media/active-directory-saas-topdesk-public-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


