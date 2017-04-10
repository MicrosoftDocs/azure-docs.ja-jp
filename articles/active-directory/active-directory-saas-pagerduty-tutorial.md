---
title: "チュートリアル: Azure Active Directory と PagerDuty の統合 | Microsoft Docs"
description: "Azure Active Directory で Pagerduty を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/26/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: d78e5a50f5c0416aa5f7caa92d51f688abb1efcc
ms.openlocfilehash: adfc80090c732fc0dfa2fa35056451921d242f6a


---

# <a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>チュートリアル: Azure Active Directory と PagerDuty の統合
このチュートリアルでは、Azure と PagerDuty の統合について説明します。 このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* PagerDuty テナント

このチュートリアルを完了すると、PagerDuty に割り当てた Azure AD ユーザーは、PagerDuty 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオン (SSO) できるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. PagerDuty のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-pagerduty-tutorial/IC778528.png "Scenario")

## <a name="enabling-the-application-integration-for-pagerduty"></a>PagerDuty のアプリケーション統合の有効化
このセクションでは、PagerDuty のアプリケーション統合を有効にする方法を説明します。

**PagerDuty のアプリケーション統合を有効にするには、次の手順に従います。**

1. Azure 管理ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-pagerduty-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-pagerduty-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-pagerduty-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-pagerduty-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**PagerDuty**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-pagerduty-tutorial/IC778529.png "Application Gallery")
7. 結果ウィンドウで **[PagerDuty]** を選び、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![PagerDuty](./media/active-directory-saas-pagerduty-tutorial/IC778530.png "PagerDuty")
   
## <a name="configuring-single-sign-on"></a>シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで PagerDuty に対する認証を行えるようにする方法を説明します。  

この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。  

この手順に慣れていない場合は、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **PagerDuty** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-pagerduty-tutorial/IC778531.png "Configure single sign-on")
2. **[ユーザーの PagerDuty へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選び、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-pagerduty-tutorial/IC778532.png "Configure single sign-on")
3. **[アプリ URL の構成]** ページで、**[PagerDuty サインイン URL]** ボックスに、"*https://\<tenant-name\>.Pagerduty.com*" というパターンの URL を入力し、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-pagerduty-tutorial/IC778533.png "アプリケーション URL の構成")
4. **[PagerDuty でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、コンピューターに証明書ファイルを保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-pagerduty-tutorial/IC778534.png "Configure single sign-on")
5. 別の Web ブラウザーのウィンドウで、PagerDuty 企業サイトに管理者としてログインします。
6. 上部のメニューで **[アカウント設定]**をクリックします。
   
   ![Account Settings](./media/active-directory-saas-pagerduty-tutorial/IC778535.png "Account Settings")
7. **[シングル サインオン]**をクリックします。
   
   ![シングル サインオン](./media/active-directory-saas-pagerduty-tutorial/IC778536.png "シングル サインオン")
8. [シングル サインオンの有効化 (SSO)] ページで、次の手順に従います。
   
   ![シングル サインオンの有効化](./media/active-directory-saas-pagerduty-tutorial/IC778537.png "シングル サインオンの有効化")
   
   1. ダウンロードした証明書から **base-64 でエンコードされた** ファイルを作成します。  
      
      >[!TIP]
      >詳細については、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。
      >

  2. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、 **[X.509 Certificate]** テキスト ボックスに貼り付けます。
  3. Azure クラシック ポータルの **[PagerDuty でのシングル サインオンの構成]** ダイアログ ページで、**[リモート ログイン URL]** の値をコピーし、**[Login URL]** ボックスに貼り付けます。
  4. Azure クラシック ポータルの **[PagerDuty でのシングル サインオンの構成]** ダイアログ ページで、**[リモート ログアウト URL]** の値をコピーし、**[Logout URL]** ボックスに貼り付けます。
  5. **[シングル サインオンを有効にする]**を選びます。
  6. **[変更を保存]**をクリックします。

9. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-pagerduty-tutorial/IC778538.png "Configure single sign-on")
   

## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成

Azure AD ユーザーが PagerDuty にログインできるようにするには、ユーザーを PagerDuty にプロビジョニングする必要があります。  
PagerDuty の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **PagerDuty** テナントにログインします。
2. 上部のメニューで **[ユーザー]**をクリックします。
3. **[ユーザーの追加]**をクリックします。
   
   ![ユーザーの追加](./media/active-directory-saas-pagerduty-tutorial/IC778539.png "ユーザーの追加")
4. **[チームを招待]** ダイアログで、プロビジョニングする Azure AD ユーザーの **[氏名]** と **[電子メール]** アドレスを入力し、 **[追加]**、**[招待を送信]** の順にクリックします。
   
   ![チームの招待](./media/active-directory-saas-pagerduty-tutorial/IC778540.png "チームの招待")
   
   > [!NOTE]
   > PagerDuty アカウントを作成すると、追加したすべてのユーザーが招待を受信します。
   > 
   > 

> [!NOTE]
> PagerDuty から提供されている他の PagerDuty ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 
> 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを PagerDuty に割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **PagerDuty** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-pagerduty-tutorial/IC778541.png "Assign users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-pagerduty-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。




<!--HONumber=Feb17_HO1-->


