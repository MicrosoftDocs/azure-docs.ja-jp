---
title: "チュートリアル: Azure Active Directory と Aha! | Microsoft Docs"
description: "Azure Active Directory で Aha! を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ad955d3d-896a-41bb-800d-68e8cb5ff48d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 76b3bab6c995b93e907d32814c851de5105b8b33
ms.openlocfilehash: b293545b35954e60569a56b900c247f55ddbec1e
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-aha"></a>チュートリアル: Azure Active Directory と Aha!
このチュートリアルでは、Azure と Aha! の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Aha! でのシングル サインオン (SSO) が有効なサブスクリプション

このチュートリアルを完了すると、Aha! に割り当てた Azure AD ユーザーは、Aha! 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

* Aha! のアプリケーション統合の有効化
* シングル サインオン (SSO) の構成
* ユーザー プロビジョニングの構成
* ユーザーの割り当て

![シナリオ](./media/active-directory-saas-aha-tutorial/IC798944.png "Scenario")

## <a name="enable-the-application-integration-for-aha"></a>Aha! のアプリケーション統合の有効化
このセクションでは、Aha! のアプリケーション統合を有効にする方法について説明します。

**Aha! のアプリケーション統合を有効にするには、次の手順に従います。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-aha-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-aha-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-aha-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-aha-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**Aha!**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-aha-tutorial/IC798945.png "Application Gallery")
7. 結果ウィンドウで **[Aha!]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Aha!](./media/active-directory-saas-aha-tutorial/IC802746.png "Aha!")
   
## <a name="configure-single-sign-on"></a>Configure single sign-on

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Aha! に対する認証を行えるようにする方法を説明します。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Aha!** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-aha-tutorial/IC798946.png "Configure Single Sign-On")
2. **[ユーザーの Aha! へのアクセスを設定してください]**  ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-aha-tutorial/IC798947.png "Configure Single Sign-On")
3. **[アプリケーション URL の構成]** ページの **[Aha! サインオン URL**] テキスト ボックスに、Aha! アプリケーションへのサインオンにユーザーが使用する URL (例: "*https://company.aha.io/session/new*") を入力し、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-aha-tutorial/IC798948.png "Configure App URL")
4. **[Aha! でのシングル サインオンの構成]**  ページで、**[メタデータのダウンロード]** をクリックしてメタデータをダウンロードし、コンピューターでメタデータ ファイルをローカルに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-aha-tutorial/IC798949.png "Configure Single Sign-On")
5. 別の Web ブラウザーのウィンドウで、管理者として Aha! 企業サイトにログインします。
6. 上部のメニューで **[Settings]**をクリックします。
   
   ![設定](./media/active-directory-saas-aha-tutorial/IC798950.png "Settings")
7. [ **アカウント**] クリックします。
   
   ![プロファイル](./media/active-directory-saas-aha-tutorial/IC798951.png "Profile")
8. [ **セキュリティとシングル サインオン**] クリックします。
   
   ![セキュリティとシングル サインオン](./media/active-directory-saas-aha-tutorial/IC798952.png "Security and single sign-on")
9. **[シングル サインオン]** セクションで、**[ID プロバイダー]** として **[SAML2.0]** を選択します。
   
   ![セキュリティとシングル サインオン](./media/active-directory-saas-aha-tutorial/IC798953.png "Security and single sign-on")
10. [ **シングル サインオン** ] 構成ページで、次の手順を実行します。
    
   ![シングル サインオン](./media/active-directory-saas-aha-tutorial/IC798954.png "Single Sign-On")    
    1. **[名前]** テキスト ボックスに、構成の名前を入力します。
    2. **[Configure using]** には **[メタデータ ファイル]** を選択します。
    3. ダウンロードしたメタデータ ファイルをアップロードするには、[ **参照**] をクリックします。
    4. **[Update]**をクリックします。
11. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
    
   ![シングル サインオンの構成](./media/active-directory-saas-aha-tutorial/IC798955.png "Configure Single Sign-On")
    
## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]

Azure AD ユーザーが Aha! にログインできるようにするには、そのユーザーを Aha! にプロビジョニングする必要があります。  

* Aha! の場合、プロビジョニングは自動化されています。 ユーザー側で必要な操作はありません。

最初のシングル サインオンの試行中に、必要に応じてユーザーが自動的に作成されます。

>[!NOTE]
>他の Aha! ユーザー アカウントの作成ツールまたは Aha! から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 
> 

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを Aha! に割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Aha!** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-aha-tutorial/IC798956.png "Assign Users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-aha-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


