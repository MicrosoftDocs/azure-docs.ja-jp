---
title: "チュートリアル: Azure Active Directory と Qualtrics の統合 | Microsoft Docs"
description: "Azure Active Directory で Qualtrics を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 4df889ab-2685-4d15-a163-1ba26567eeda
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/18/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 8e209d70a0bdcac4d3d1f7fefe265bf4824520e3
ms.openlocfilehash: 20c198577349a2173d5b7bafd1b84fd4c24fa711
ms.lasthandoff: 02/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-qualtrics"></a>チュートリアル: Azure Active Directory と Qualtrics の統合
このチュートリアルでは、Azure と Qualtrics の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Qualtrics でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、Qualtrics に割り当てた Azure AD ユーザーは、Qualtrics 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Qualtrics のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-qualtrics-tutorial/IC789542.png "Scenario")

## <a name="enabling-the-application-integration-for-qualtrics"></a>Qualtrics のアプリケーション統合の有効化
このセクションでは、Qualtrics のアプリケーション統合を有効にする方法を説明します。

### <a name="to-enable-the-application-integration-for-qualtrics-perform-the-following-steps"></a>Qualtrics のアプリケーション統合を有効にするには、次の手順に従います。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-qualtrics-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-qualtrics-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-qualtrics-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-qualtrics-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**Qualtrics**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-qualtrics-tutorial/IC789543.png "Application Gallery")
7. 結果ウィンドウで **[Qualtrics]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Qualtrics](./media/active-directory-saas-qualtrics-tutorial/IC789544.png "Qualtrics")
   
## <a name="configuring-single-sign-on"></a>シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Qualtrics に対する認証を行えるようにする方法を説明します。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順を実行します。
1. Azure クラシック ポータルの **Qualtrics** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-qualtrics-tutorial/IC789545.png "Configure Single Sign-On")
2. **[ユーザーの Qualtrics へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-qualtrics-tutorial/IC789546.png "Configure Single Sign-On")
3. **[アプリケーション URL の構成]** ページで、**[Qualtrics サインオン URL]** ボックスに URL (例: "*https://ssotest2ut1.qualtrics.com*") を入力し、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-qualtrics-tutorial/IC789547.png "Configure App URL")
4. **[Qualtrics でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックし、メタデータ ファイルをコンピューターに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-qualtrics-tutorial/IC789548.png "Configure Single Sign-On")
5. Qualtrics サポート チームに、メタデータ ファイルを送信します。
   
   > [!NOTE]
   > シングル サインオンの構成は、Qualtrics サポート チームが実行する必要があります。 構成が完了すると、サポート チームから通知が届きます。
   > 
   > 
6. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-qualtrics-tutorial/IC789549.png "Configure Single Sign-On")
   
## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成

Qualtrics へのユーザー プロビジョニングの構成にあたって必要な操作はありません。  
割り当てられたユーザーがアクセス パネルを使用して Qualtrics にログインしようとすると、そのユーザーが存在するかどうかが Qualtrics によって確認されます。  

使用可能なユーザー アカウントがない場合、ユーザー アカウントは Qualtrics により自動的に作成されます。

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-qualtrics-perform-the-following-steps"></a>ユーザーを Qualtrics に割り当てるには、次の手順に従います。
1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Qualtrics** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-qualtrics-tutorial/IC789550.png "Assign Users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-qualtrics-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


