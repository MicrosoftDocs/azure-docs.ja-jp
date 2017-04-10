---
title: "チュートリアル: Azure Active Directory と Cherwell の統合 | Microsoft Docs"
description: "Azure Active Directory で Cherwell を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ad891f99-179e-4487-834d-35f3bc01c1ec
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: 43acab94bfde65793c4ff944cb6db5dc5de47feb
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-cherwell"></a>チュートリアル: Azure Active Directory と Cherwell の統合
このチュートリアルでは、Azure と Cherwell の統合について説明します。 このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Cherwell でのシングル サインオン (SSO) が有効なサブスクリプション

このチュートリアルを完了すると、Cherwell に割り当てた Azure AD ユーザーは、Cherwell 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Cherwell のアプリケーション統合の有効化
2. シングル サインオン (SSO) の構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-cherwell-tutorial/IC798988.png "Scenario")

## <a name="enable-the-application-integration-for-cherwell"></a>Cherwell のアプリケーション統合の有効化
このセクションでは、Cherwell のアプリケーション統合を有効にする方法について説明します。

**Cherwell のアプリケーション統合を有効にするには、次の手順を実行します。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-cherwell-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-cherwell-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-cherwell-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-cherwell-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**Cherwell**」と入力します。
   
   ![Cherwell](./media/active-directory-saas-cherwell-tutorial/IC798989.png "Cherwell")
7. 結果ウィンドウで **[Cherwell]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
## <a name="configure-single-sign-on"></a>Configure single sign-on
   ![Cherwell](./media/active-directory-saas-cherwell-tutorial/IC798996.png "Cherwell")

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Cherwell に対する認証を行えるようにする方法を説明します。

**構成された SSO を取得するには、次の手順を実行します。**

1. Azure クラシック ポータルの **Cherwell** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-cherwell-tutorial/IC798990.png "Configure Single Sign-On")
2. **[ユーザーの Cherwell へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-cherwell-tutorial/IC798991.png "Configure Single Sign-On")
3. **[アプリケーション URL の構成]** ページで、次の手順を実行します。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-cherwell-tutorial/IC798992.png "Configure App URL")
  1. **[サインオン URL]** ボックスに、ユーザーが **Cherwell** へのサインインに使用する URL を入力します (例: *https://\<会社名\>.cherwellondemand.com/cherwellclient*)。 
  2.  **[次へ]**をクリックします。
4. **[Cherwell でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-cherwell-tutorial/IC798993.png "Configure Single Sign-On")
  1.  **[証明書のダウンロード]** をクリックし、コンピューターで証明書をローカルに保存します。
  2.  **[ID プロバイダー URL]** をコピーします。
  3.  **[シングル サインオン サービス URL]** をコピーします。
  4.  **[次へ]**をクリックします。
5. ダウンロードした証明書、**ID プロバイダー URL**、**シングル サインオン サービス URL** を、Cherwell サポート チームに送信します。
   
   >[!NOTE]
   >Cherwell サポート チームが、実際に SSO を構成する必要があります。 ご使用のサブスクリプションで SSO が有効になると通知が届きます。
   > 
6. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
  ![シングル サインオンの構成](./media/active-directory-saas-cherwell-tutorial/IC798994.png "Configure Single Sign-On")

## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]
Azure AD ユーザーが Cherwell にログインできるようにするには、ユーザーを Cherwell にプロビジョニングする必要があります。

Cherwell の場合、Cherwell サポート チームがユーザー アカウントを作成する必要があります。

>[!NOTE]
>Cherwell から提供されている他の Cherwell ユーザー アカウント作成ツールまたは API を使用して、Azure Active Directory ユーザー アカウントをプロビジョニングできます。
>  

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを Cherwell に割り当てるには、次の手順を実行します。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Cherwell** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-cherwell-tutorial/IC798995.png "Assign Users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-cherwell-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


