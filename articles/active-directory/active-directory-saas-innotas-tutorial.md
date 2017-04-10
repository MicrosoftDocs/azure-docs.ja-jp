---
title: "チュートリアル: Azure Active Directory と Innotas の統合 | Microsoft Docs"
description: "Azure Active Directory で Innotas を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: eb9e9c2c-4b09-4177-bbab-423fd657448e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: bf68d6ebbc353988e203bcfa773b2e3fe1539b8c
ms.openlocfilehash: ddd7d6dc48dcc1e61ebe348db51248330e24cdd7
ms.lasthandoff: 02/17/2017


---

# <a name="tutorial-azure-active-directory-integration-with-innotas"></a>チュートリアル: Azure Active Directory と Innotas の統合
このチュートリアルでは、Azure と Innotas の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Innotas テナント

このチュートリアルを完了すると、Innotas に割り当てた Azure AD ユーザーは、Innotas 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

* Innotas のアプリケーション統合の有効化
* シングル サインオンの構成
* ユーザー プロビジョニングの構成
*  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-innotas-tutorial/IC777331.png "Scenario")

## <a name="enable-the-application-integration-for-innotas"></a>Innotas のアプリケーション統合の有効化
このセクションでは、Innotas のアプリケーション統合を有効にする方法について説明します。

**Innotas のアプリケーション統合を有効にするには、次の手順を実行します。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-innotas-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-innotas-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-innotas-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-innotas-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**Innotas**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-innotas-tutorial/IC777332.png "Application Gallery")
7. 結果ウィンドウで **[Innotas]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Innotas](./media/active-directory-saas-innotas-tutorial/IC777333.png "Innotas")
   
## <a name="configure-single-sign-on"></a>Configure single sign-on

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Innotas に対する認証を行うことができるようにする方法を説明します。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Innotas** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-innotas-tutorial/IC777334.png "Configure single sign-on")
2. **[ユーザーの Innotas へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-innotas-tutorial/IC777335.png "Configure single sign-on")
3. **[アプリケーション URL の構成]** ページで、**[サインオン URL]** ボックスに、"*https://\<テナント名\>.Innotas.com*" のパターンで URL を入力し、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-innotas-tutorial/IC777336.png "Configure app URL")
4. **[Innotas でのシングル サインオンの構成]** ページで、メタデータをダウンロードするには、**[メタデータのダウンロード]** をクリックし、データ ファイルを **c:\\InnotasMetaData.xml** としてローカルに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-innotas-tutorial/IC777337.png "Configure single sign-on")
5. メタデータ ファイルを Innotas サポート チームに転送します。 サポート チームは、シングル サインオンを構成する必要があります。
6. [シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-innotas-tutorial/IC777338.png "Configure single sign-on")
   
## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]

Innotas へのユーザー プロビジョニングの構成にあたって必要な操作はありません。  
割り当てられたユーザーがアクセス パネルを使用して Innotas にログインしようとすると、そのユーザーが存在するかどうかが Innotas によって確認されます。  

>[!NOTE]
>使用可能なユーザー アカウントがない場合、ユーザー アカウントは Innotas によって自動的に作成されます。
>

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを Innotas に割り当てるには、次の手順を実行します。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Innotas** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-innotas-tutorial/IC777339.png "Assign users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-innotas-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


