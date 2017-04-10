---
title: "チュートリアル: Azure Active Directory と EmpCenter の統合 | Microsoft Docs"
description: "Azure Active Directory で EmpCenter を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: a00ecf6e-917a-4284-b998-41506931585e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0837cb33bf438fb7fd9665d21d411f0170cdd393
ms.openlocfilehash: 6f217ee0398933cfad713398952a79d39b6020c3
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-empcenter"></a>チュートリアル: Azure Active Directory と EmpCenter の統合
このチュートリアルでは、Azure と EmpCenter の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* EmpCenter でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、EmpCenter に割り当てた Azure AD ユーザーは、EmpCenter 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. EmpCenter のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-empcenter-tutorial/IC802916.png "Scenario")

## <a name="enabling-the-application-integration-for-empcenter"></a>EmpCenter のアプリケーション統合の有効化
このセクションでは、EmpCenter のアプリケーション統合を有効にする方法について説明します。

### <a name="to-enable-the-application-integration-for-empcenter-perform-the-following-steps"></a>EmpCenter のアプリケーション統合を有効にするには、次の手順を実行します。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-empcenter-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-empcenter-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-empcenter-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-empcenter-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**EmpCenter**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-empcenter-tutorial/IC802917.png "Application Gallery")
7. 結果ウィンドウで **[EmpCenter]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![EmpCentral](./media/active-directory-saas-empcenter-tutorial/IC802918.png "EmpCentral")
   

## <a name="configuring-single-sign-on"></a>シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで EmpCenter に対する認証を行うことができるようにする方法を説明します。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順を実行します。
1. Azure クラシック ポータルの **EmpCenter** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-empcenter-tutorial/IC802919.png "Configure Single Sign-On")
2. **[ユーザーの EmpCenter へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-empcenter-tutorial/IC802920.png "Configure Single Sign-On")
3. **[アプリケーション設定の構成]** ページで、次の手順を実行します。
   
   ![アプリケーションの設定の構成](./media/active-directory-saas-empcenter-tutorial/IC802921.png "Configure App Settings")
   
   1. **[サインオン URL]** ボックスに、ユーザーが EmpCenter アプリケーションへのサインオンに使用する URL (例: *https://partner-authenticati.empcenter.com/workforce/SSO.do*) を入力します。
   2. **[次へ]**
4. **[EmpCenter でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックしてメタデータをダウンロードし、メタデータ ファイルをコンピューターに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-empcenter-tutorial/IC802922.png "Configure Single Sign-On")
5. ダウンロードしたメタデータ ファイルを EmpCenter サポート チームに送信します。
   
   > [!NOTE]
   > EmpCenter サポート チームが、実際に SSO を構成する必要があります。
   > ご使用のサブスクリプションで SSO が有効になると通知が届きます。
   > 
   > 
6. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-empcenter-tutorial/IC802923.png "Configure Single Sign-On")
   
## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成

Azure AD ユーザーが EmpCenter にログインできるようにするには、そのユーザーを EmpCenter にプロビジョニングする必要があります。  
EmpCenter の場合、ユーザー アカウントは、EmpCenter サポート チームが作成する必要があります。

> [!NOTE]
> EmpCenter から提供されている他の EmpCenter ユーザー アカウント作成ツールまたは API を使用して、Azure Active Directory ユーザー アカウントをプロビジョニングできます。
> 
> 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-empcenter-perform-the-following-steps"></a>ユーザーを EmpCenter に割り当てるには、次の手順を実行します。
1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **EmpCenter** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-empcenter-tutorial/IC802924.png "Assign Users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-empcenter-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


