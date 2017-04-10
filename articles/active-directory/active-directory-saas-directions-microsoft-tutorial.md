---
title: "チュートリアル: Azure Active Directory と Directions on Microsoft の統合 | Microsoft Docs"
description: "Azure Active Directory で Directions on Microsoft を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e0c8986f-2acd-418d-a306-437abc44b640
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: efea82512dad3f2e5d1c0dd45e9e2f6db9a283d4
ms.openlocfilehash: a5538e89b1c23e4a2ad45c5515de8167ef95b46a
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-directions-on-microsoft"></a>チュートリアル: Azure Active Directory と Directions on Microsoft の統合
このチュートリアルでは、Azure Active Directory と Directions on Microsoft の統合について説明します。  

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Directions on Microsoft サブスクリプション

フェデレーション Directions on Microsoft サブスクリプションをまだ持っていない場合は、**service@DirectionsOnMicrosoft.com** に電子メールで要求してください。

このチュートリアルを完了すると、Directions on Microsoft に割り当てた Azure Active Directory ユーザーは、シングル サインオンを使用して、アプリケーションにシングル サインインできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

* Directions on Microsoft のアプリケーション統合の有効化
* シングル サインオン (SSO) の構成
* ユーザー プロビジョニングの構成
* ユーザーの割り当て

![シナリオ](./media/active-directory-saas-directions-microsoft-tutorial/IC786877.png "Scenario")

## <a name="enable-the-application-integration-for-directions-on-microsoft"></a>Directions on Microsoft のアプリケーション統合の有効化
このセクションでは、Directions on Microsoft のアプリケーション統合を有効にする方法について説明します。

**Directions on Microsoft のアプリケーション統合を有効にするには、次の手順を実行します。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-directions-microsoft-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-directions-microsoft-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-directions-microsoft-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-directions-microsoft-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**Directions on Microsoft**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-directions-microsoft-tutorial/IC786878.png "Application Gallery")
7. 結果ウィンドウで **Directions on Microsoft**」と入力し、 **[完了]** をクリックしてアプリケーションを追加します。
   
   ![シナリオ](./media/active-directory-saas-directions-microsoft-tutorial/IC793922.png "Scenario")
   
## <a name="configure-single-sign-on"></a>Configure single sign-on

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Directions on Microsoft に対する認証を行うことができるようにする方法を説明します。

**構成された SSO を取得するには、次の手順を実行します。**

1. Azure クラシック ポータルの **Directions on Microsoft** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![Enable Single Sign-On](./media/active-directory-saas-directions-microsoft-tutorial/IC786879.png "Enable Single Sign-On")
2. **[ユーザーの Directions on Microsoft へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![Microsoft Azure AD Singel Sign-On](./media/active-directory-saas-directions-microsoft-tutorial/IC786880.png "Microsoft Azure AD Singel Sign-On")
3. **[アプリケーション URL の構成]** ページで、[サインオン URL] ボックスに、「**https://www.directionsonmicrosoft.com/user/login**」と入力し、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-directions-microsoft-tutorial/IC786881.png "Configure App URL")
4. **[Directions on Microsoft でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックし、メタデータ ファイルをコンピューターにローカルに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-directions-microsoft-tutorial/IC786882.png "Configure Single Sign-On")
5. メタデータ ファイルを Directions on Microsoft サポート チーム (*service@DirectionsOnMicrosoft.com*) に送信します。 Directions on Microsoft サポート チームがフェデレーション サイトのメンバーシップを見つけられるように、電子メールには会社の情報を含めます。
   
   >[!NOTE]
   >Directions on Microsoft のシングル サインオンは、Directions on Microsoft サポート チームが有効にする必要があります。 シングル サインオンが有効になると通知が届きます。 
   > 
6. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
  ![シングル サインオンの構成](./media/active-directory-saas-directions-microsoft-tutorial/IC786883.png "Configure Single Sign-On")
   
## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]

Directions on Microsoft へのユーザー プロビジョニングの構成にあたって必要な操作はありません。  

割り当てられたユーザーがアクセス パネルを使用して Directions on Microsoft にログインしようとすると、そのユーザーが存在するかどうかが Directions on Microsoft によって確認されます。 使用可能なユーザー アカウントがない場合、ユーザー アカウントは自動的に作成されます。

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを Directions on Microsoft に割り当てるには、次の手順を実行します。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Directions on Microsoft** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-directions-microsoft-tutorial/IC786884.png "Assign users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-directions-microsoft-tutorial/IC767830.png "Yes")


