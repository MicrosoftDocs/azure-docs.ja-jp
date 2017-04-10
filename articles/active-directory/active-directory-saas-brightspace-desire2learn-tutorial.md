---
title: "チュートリアル: Azure Active Directory と Brightspace by Desire2Learn の統合 | Microsoft Docs"
description: "Azure Active Directory で Brightspace by Desire2Learn を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e2d3065b-1f6c-4c45-af78-0d5da3266999
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 325d92e493f6e011367d2c85b52c92838327101e
ms.openlocfilehash: cdd7a42f12c145327249ff7c03ede9a465374e35
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-brightspace-by-desire2learn"></a>チュートリアル: Azure Active Directory と Brightspace by Desire2Learn の統合
このチュートリアルでは、Azure と Brightspace by Desire2Learn の統合について説明します。  

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Brightspace by Desire2Learn でのシングル サインオン (SSO) が有効なサブスクリプション

このチュートリアルを完了すると、Brightspace by Desire2Learn に割り当てた Azure AD ユーザーは、Brightspace by Desire2Learn 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

* Brightspace by Desire2Learn のアプリケーション統合の有効化
* シングル サインオン (SSO) の構成
* ユーザー プロビジョニングの構成
* ユーザーの割り当て

![シナリオ](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798957.png "Scenario")

## <a name="enable-the-application-integration-for-brightspace-by-desire2learn"></a>Brightspace by Desire2Learn のアプリケーション統合の有効化
このセクションでは、Brightspace by Desire2Learn のアプリケーション統合を有効にする方法を説明します。

**Brightspace by Desire2Learn のアプリケーション統合を有効にするには、次の手順に従います。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**Brightspace by Desire2Learn**」と入力します。
   
   ![Apllication Gallery](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798958.png "Apllication Gallery")
7. 結果ウィンドウで **[Brightspace by Desire2Learn]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Brightspace by Desire2Learn](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC799321.png "Brightspace by Desire2Learn")
   
## <a name="configure-single-sign-on"></a>Configure single sign-on

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Brightspace by Desire2Learn に対する認証を行えるようにする方法を説明します。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Brightspace by Desire2Learn** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798959.png "Configure Single Sign-On")
2. **[ユーザーの Brightspace by Desire2Learn へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798960.png "Configure Single Sign-On")
3. **[アプリケーション URL の構成]** ページで、次の手順を実行します。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798961.png "Configure App URL")   
   1. **[サインオン URL]** ボックスに、ユーザーが **Brightspace by Desire2Learn** へのサインオンに使用する URL (例: *https://partnershowcase.desire2learn.com/Shibboleth.sso/Login?entityID=https://sts.windows-ppe.net/5caf9349-fd93-4a74-b064-0070f65bfb49/&target=https%3A%2F%2Fpartnershowcase.desire2learn.com%2Fd2l%2FshibbolethSSO%2Faspinfo.asp*) を入力します。
   2. **[次へ]**をクリックします。
4. **[Brightspace by Desire2Learn でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックしてメタデータをダウンロードし、メタデータをコンピューターに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798962.png "Configure Single Sign-On")
5. ダウンロードしたメタデータ ファイルを Brightspace by Desire2Learn サポート チームに送信します。
   
   >[!NOTE]
   >Brightspace by Desire2Learn サポート チームが、実際に SSO を構成する必要があります。
   >ご使用のサブスクリプションで SSO が有効になると通知が届きます。
   > 
 
6. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798963.png "Configure Single Sign-On")
   
## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]

Azure AD ユーザーが Brightspace by Desire2Learn にログインできるようにするには、ユーザーを Brightspace by Desire2Learn にプロビジョニングする必要があります。  

Brightspace by Desire2Learn の場合、Brightspace by Desire2Learn サポート チームがユーザー アカウントを作成する必要があります。

>[!NOTE]
>Brightspace by Desire2Learn から提供されている他の Brightspace by Desire2Learn ユーザー アカウント作成ツールまたは API を使用して、Azure Active Directory ユーザー アカウントをプロビジョニングできます。 
> 

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを Brightspace by Desire2Learn に割り当てるには、次の手順を実行します。**
1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Brightspace by Desire2Learn** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
  ![ユーザーの割り当て](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798964.png "Assign Users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
  ![はい](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


