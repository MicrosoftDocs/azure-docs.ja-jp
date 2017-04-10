---
title: "チュートリアル: Azure Active Directory と Kontiki の統合 | Microsoft Docs"
description: "Azure Active Directory で Kontiki を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 8d5e5413-da4c-40d8-b1d0-f03ecfef030b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 230facec9f8f83e8a94eaaac5a1495195ff45cc7
ms.openlocfilehash: 3af125b186e3905572dd5d621c948a6639f9b023
ms.lasthandoff: 02/17/2017


---

# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>チュートリアル: Azure Active Directory と Kontiki の統合
このチュートリアルの目的は、Azure と Kontiki の統合を紹介することです。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Kontiki でのシングル サインオン (SSO) が有効なサブスクリプション

このチュートリアルを完了すると、Kontiki に割り当てた Azure AD ユーザーは、Kontiki 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

* Kontiki のアプリケーション統合の有効化
* シングル サインオンの構成
* ユーザー プロビジョニングの構成
* ユーザーの割り当て

![シナリオ](./media/active-directory-saas-kontiki-tutorial/IC790235.png "Scenario")

## <a name="enable-the-application-integration-for-kontiki"></a>Kontiki のアプリケーション統合の有効化
このセクションでは、Kontiki のアプリケーション統合を有効にする方法について説明します。

**Kontiki のアプリケーション統合を有効にするには、次の手順に従います。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-kontiki-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-kontiki-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-kontiki-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-kontiki-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**Kontiki**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-kontiki-tutorial/IC790236.png "Application Gallery")
7. 結果ウィンドウで **[Kontiki]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Kontiki](./media/active-directory-saas-kontiki-tutorial/IC790237.png "Kontiki")
   
## <a name="configure-single-sign-on"></a>Configure single sign-on

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Kontiki に対する認証を行えるようにする方法を説明します。

*シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Kontiki** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-kontiki-tutorial/IC790238.png "Configure Single SignOn")
2. **[ユーザーの Kontiki へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-kontiki-tutorial/IC790239.png "Configure Single SignOn")
3. **[アプリケーションの URL を構成する]** ページの **[Kontiki サインオン URL]** テキストボックスに、ユーザーが Kontiki アプリのサインオンに使用する URL (例: “*https://company.mc.eval.kontiki.com/*") を入力して、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-kontiki-tutorial/IC790240.png "Configure App URL")
4. **[Kontiki でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックしてメタデータをダウンロードし、コンピューターのローカルに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-kontiki-tutorial/IC790241.png "Configure Single SignOn")
5. Kontiki サポート チームに、メタデータ ファイルを送信します。
   
   >[!NOTE]
   >シングル サインオンの構成は、Kontiki サポート チームが実行する必要があります。 構成が完了すると、サポート チームから通知が届きます。 
   > 
6. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
  ![シングル サインオンの構成](./media/active-directory-saas-kontiki-tutorial/IC790242.png "Configure Single SignOn")
   
## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]

Kontiki へのユーザー プロビジョニングの構成にあたって必要な操作はありません。 割り当てられたユーザーがアクセス パネルを使用して Kontiki にログインしようとすると、そのユーザーが存在するかどうかが Kontiki によって確認されます。  

>[注] 使用可能なユーザー アカウントがない場合、ユーザー アカウントは Kontiki により自動的に作成されます。
>

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを Kontiki に割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **[Kontiki]** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-kontiki-tutorial/IC790243.png "Assign Users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-kontiki-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


