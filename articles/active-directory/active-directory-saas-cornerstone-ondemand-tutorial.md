---
title: "チュートリアル: Azure Active Directory と Cornerstone OnDemand の統合 | Microsoft Docs"
description: "Azure Active Directory で Cornerstone OnDemand を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f57c5fef-49b0-4591-91ef-fc0de6d654ab
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: bbbae7d7a5e9b0fbfe07bd3f2aa61df77b56c5df
ms.openlocfilehash: 7fce07464b90c81da7a015246d5757ecc768792a
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-cornerstone-ondemand"></a>チュートリアル: Azure Active Directory と Cornerstone OnDemand の統合
このチュートリアルでは、Azure と Cornerstone OnDemand の統合について説明します。

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Cornerstone OnDemand テナント

このチュートリアルを完了すると、Cornerstone OnDemand に割り当てた Azure AD ユーザーは、Cornerstone OnDemand 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

* Cornerstone OnDemand のアプリケーション統合の有効化
* シングル サインオン (SSO) の構成
* ユーザー プロビジョニングの構成
* ユーザーの割り当て

![シナリオ](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781593.png "Scenario")

## <a name="enable-the-application-integration-for-cornerstone-ondemand"></a>Cornerstone OnDemand のアプリケーション統合の有効化
このセクションでは、Cornerstone OnDemand のアプリケーション統合を有効にする方法について説明します。

**Cornerstone OnDemand のアプリケーション統合を有効にするには、次の手順を実行します。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**cornerstone ondemand**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781594.png "Application Gallery")
7. 結果ウィンドウで **[Cornerstone OnDemand]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Cornerstone OnDemand](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781595.png "Cornerstone OnDemand")
   
## <a name="configure-single-sign-on"></a>Configure single sign-on

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Cornerstone OnDemand に対する認証を行うことができるようにする方法を説明します。

**構成された SSO を取得するには、次の手順を実行します。**
1. Azure クラシック ポータルの **Cornerstone OnDemand** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![Enable Single Sign-On](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781596.png "Enable Single Sign-On")
2. **[ユーザーの Cornerstone OnDemand へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![Microsoft Azure AD Single Sign-On](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781597.png "Microsoft Azure AD Single Sign-On")
3. **[アプリケーション URL の構成]** ページの **[Cornerstone OnDemand サインイン URL]** ボックスに、"*http://company.csod.com*" というパターンの URL を入力し、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781598.png "Configure App URL")
4. **[Cornerstone OnDemand でのシングル サインオン構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルをローカルでコンピューターに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781599.png "Configure Single Sign-On")
5. 次のアイテムを Cornerstone OnDemand サポート チームに送信します。  
   1. ダウンロードした証明書
   2. **リモート ログイン URL** 値
   3. **リモート ログイアウト URL** 値

   >[!NOTE]
   >シングル サインオンは、Cornerstone OnDemand サポート チームが構成する必要があります。 構成が完了すると、サポート チームから通知が届きます。
   > 
6. [シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
  ![シングル サインオンの構成](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781600.png "Configure Single Sign-On")
   
## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]

Azure AD ユーザーが Cornerstone OnDemand にログインできるようにするには、そのユーザーを Cornerstone OnDemand にプロビジョニングする必要があります。 Cornerstone OnDemand の場合、プロビジョニングは手動で行います。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. プロビジョニングする Azure AD ユーザーに関する情報 (名前、電子メールなど) を、Cornerstone OnDemand サポート チームに送信します。

>[!NOTE]
>Cornerstone OnDemand から提供されている他の Cornerstone OnDemand ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。 
> 

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを Cornerstone OnDemand に割り当てるには、次の手順を実行します。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Cornerstone OnDemand** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC775564.png "Assign users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![ユーザーの割り当て](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781601.png "Assign Users")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


