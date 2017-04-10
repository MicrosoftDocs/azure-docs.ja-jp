---
title: "チュートリアル: Azure Active Directory と Projectplace の統合 | Microsoft Docs"
description: "Azure Active Directory で Projectplace を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/19/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 04a045f41965b093aab71e59cd9b5f328b44de84
ms.openlocfilehash: aee8458ef8d6e564685cbf4d56f91af7766d1e2a


---
# <a name="tutorial-azure-active-directory-integration-with-projectplace"></a>チュートリアル: Azure Active Directory と Projectplace の統合
このチュートリアルでは、Azure と Projectplace の統合について説明します。 このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Projectplace でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、Projectplace に割り当てた Azure AD ユーザーは、Projectplace 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Projectplace のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-projectplace-tutorial/IC790217.png "Scenario")

## <a name="enabling-the-application-integration-for-projectplace"></a>Projectplace のアプリケーション統合の有効化
このセクションでは、Projectplace のアプリケーション統合を有効にする方法を説明します。

**Projectplace のアプリケーション統合を有効にするには、次の手順に従います。**
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-projectplace-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-projectplace-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-projectplace-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-projectplace-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**Projectplace**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-projectplace-tutorial/IC790218.png "Application Gallery")
7. 結果ウィンドウで **[Projectplace]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![ProjectPlace](./media/active-directory-saas-projectplace-tutorial/IC790219.png "ProjectPlace")
   
## <a name="configuring-single-sign-on"></a>シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Projectplace に対する認証を行えるようにする方法を説明します。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Projectplace** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-projectplace-tutorial/IC790220.png "Configure Single SignOn")
2. **[ユーザーの Projectplace へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-projectplace-tutorial/IC790221.png "Configure Single SignOn")
3. **[アプリケーション URL の構成]** ページの **[Projectplace サインオン URL]** ボックスに、Projectplace テナント URL (例: "*http://company.projectplace.com*") を入力し、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-projectplace-tutorial/IC790222.png "Configure App URL")
4. **[Projectplace でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックし、メタデータ ファイルをコンピューターに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-projectplace-tutorial/IC790223.png "Configure Single SignOn")
5. Projectplace サポート チームにメタデータ ファイルを送信します。
   
   > [!NOTE]
   > シングル サインオンの構成は、Projectplace サポート チームが実行する必要があります。 構成が完了すると、サポート チームから通知が届きます。
   > 
   > 
6. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-projectplace-tutorial/IC790227.png "Configure Single SignOn")
   
## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成

Azure AD ユーザーが Projectplace にログインできるようにするには、ユーザーを Projectplace にプロビジョニングする必要があります。 Projectplace の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Projectplace** 企業サイトに管理者としてログインします。
2. **[People]** に移動し、**[Members]** をクリックします。
   
   ![ユーザー](./media/active-directory-saas-projectplace-tutorial/IC790228.png "People")
3. **[Add Member]**をクリックします。
   
   ![メンバーの追加](./media/active-directory-saas-projectplace-tutorial/IC790232.png "メンバーの追加")
4. **[Add Member]** セクションで、次の手順に従います。
   
   ![新しいメンバー](./media/active-directory-saas-projectplace-tutorial/IC790233.png "新しいメンバー")
   
   1. **[New Members]** テキストボックスに、プロビジョニングする有効な AAD アカウントのメール アドレスを入力します。
   2. [ **送信**] をクリックします。

 Azure Active Directory のアカウント所有者には、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含むメールが送信されます。


> [!NOTE]
> Projectplace から提供されている他の Projectplace ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 
> 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを Projectplace に割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Projectplace** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-projectplace-tutorial/IC790234.png "Assign Users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-projectplace-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。




<!--HONumber=Feb17_HO1-->


