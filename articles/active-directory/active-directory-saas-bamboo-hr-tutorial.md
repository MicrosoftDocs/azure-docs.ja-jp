---
title: "チュートリアル: Azure Active Directory と BambooHR の統合 | Microsoft Docs"
description: "Azure Active Directory で BambooHR を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f826b5d2-9c64-47df-bbbf-0adf9eb0fa71
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 308c9f4d59f6c6981af74f4208c9963e3d0102dd
ms.openlocfilehash: 6644afc1de68230ccca089529b32195c4c8ddfe7
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-bamboo-hr"></a>チュートリアル: Azure Active Directory と Bamboo HR の統合
このチュートリアルでは、Azure と Bamboo HR の統合について説明します。  

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Bamboo HR でのシングル サインオン (SSO) が有効なサブスクリプション

このチュートリアルを完了すると、BambooHR に割り当てた Azure AD ユーザーは、BambooHR 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

* Bamboo HR のアプリケーション統合の有効化
* シングル サインオンの構成
* ユーザー プロビジョニングの構成
* ユーザーの割り当て

![シナリオ](./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Scenario")

## <a name="enable-the-application-integration-for-bamboohr"></a>Bamboo HR のアプリケーション統合の有効化
このセクションでは、Bamboo HR のアプリケーション統合を有効にする方法について説明します。

**Bamboo HR のアプリケーション統合を有効にするには、次の手順に従います。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-bamboo-hr-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-bamboo-hr-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-bamboo-hr-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-bamboo-hr-tutorial/IC749322.png "Add an application from gallerry")
6. **[検索] ボックス**に、「**BambooHR**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-bamboo-hr-tutorial/IC796686.png "Application Gallery")
7. 結果ウィンドウで **[BambooHR]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/IC796687.png "BambooHR")
   
## <a name="configure-single-sign-on"></a>Configure single sign-on

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで BambooHR に対する認証を行えるようにする方法を説明します。  

この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。 この手順に慣れていない場合は、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **BambooHR** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シナリオ](./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Scenario")
2. **[ユーザーの BambooHR へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-bamboo-hr-tutorial/IC796688.png "Configure single sign-on")
3. **[アプリケーション URL の構成]** ページの **[BambooHR のサインオン URL]** テキスト ボックスに、ユーザーが BambooHR アプリケーションにサインオンする際に使用する URL (例: https://company.bamboohr.com) を入力し、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-bamboo-hr-tutorial/IC796689.png "Configure app URL")
4. **[BambooHR でのシングル サインオン構成]** ページで、**[証明書のダウンロード]** をクリックし、コンピューターに証明書ファイルを保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-bamboo-hr-tutorial/IC796690.png "Configure single sign-on")
5. 別の Web ブラウザーのウィンドウで、管理者として BambooHR 企業サイトにログインします。
6. このホーム ページで、次の手順を実行します。
   
   ![シングル サインオン](./media/active-directory-saas-bamboo-hr-tutorial/IC796691.png "Single Sign-On")   
   1. **[Apps]**をクリックします。
   2. 左側の [Apps] メニューで、 **[Single Sign-On]**をクリックします。
   3. **[SAML Single Sign-On]**をクリックします。
7. **[SAML Single Sign-On]** セクションで、次の手順に従います。
   
   ![SAML シングル サインオン](./media/active-directory-saas-bamboo-hr-tutorial/IC796692.png "SAML Single Sign-On")
   
   1. Azure クラシック ポータルの **[BambooHR でのシングル サインオンの構成]** ダイアログ ページで、**[シングル サインオン サービス URL]** の値をコピーし、**[SSO Login URL]** テキストボックスに貼り付けます。
   2. ダウンロードした証明書から **base-64 でエンコードされた** ファイルを作成します。  
   
      >[!TIP]
      >詳細については、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。 
      > 
   3. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、 **[X.509 Certificate]** テキスト ボックスに貼り付けます。
   4. **[Save]**をクリックします。
8. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-bamboo-hr-tutorial/IC796693.png "Configure single sign-on")
   
## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]

Azure AD ユーザーが BambooHR にログインできるようにするには、そのユーザーを BambooHR にプロビジョニングする必要があります。  

* BambooHR の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **BambooHR** 企業サイトに管理者としてログインします。
2. 上部のツールバーで **[設定]**をクリックします。
   
   ![設定](./media/active-directory-saas-bamboo-hr-tutorial/IC796694.png "Setting")
3. **[Overview]**をクリックします。
4. 左側のナビゲーション ウィンドウの **[Security] \> [Users]** を選択します。
5. 関連するテキスト ボックスに、プロビジョニングする有効な AAD アカウントのユーザー名、パスワード、および電子メール アドレスを入力します。
6. **[保存]**をクリックします。

>[!NOTE]
>他の BambooHR ユーザー アカウントの作成ツールまたは BambooHR から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。 
> 

## <a name="assig-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを BambooHR に割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **BambooHR** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-bamboo-hr-tutorial/IC796695.png "Assign users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-bamboo-hr-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


