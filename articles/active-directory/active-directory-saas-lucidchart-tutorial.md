---
title: "チュートリアル: Azure Active Directory と Lucidchart の統合 | Microsoft Docs"
description: "Azure Active Directory で Lucidchart を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 1068d364-11f3-43b5-bd6d-26f00ecd5baa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: c9a026e68526ea7f5fc5695349d04c724a8acd9b
ms.openlocfilehash: 6bb2f0672983fa3f4cb81291ee12306f21b9cebf
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-lucidchart"></a>チュートリアル: Azure Active Directory と Lucidchart の統合
このチュートリアルでは、Azure と Lucidchart の統合について説明します。  

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Lucidchart でのシングル サインオン (SSO) が有効なサブスクリプション

このチュートリアルを完了すると、Lucidchart に割り当てた Azure AD ユーザーは、Lucidchart 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Lucidchart のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-lucidchart-tutorial/IC791183.png "Scenario")

## <a name="enabling-the-application-integration-for-lucidchart"></a>Lucidchart のアプリケーション統合の有効化
このセクションでは、Lucidchart のアプリケーション統合を有効にする方法を説明します。

**Lucidchart のアプリケーション統合を有効にするには、次の手順に従います。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-lucidchart-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-lucidchart-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-lucidchart-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-lucidchart-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**Lucidchart**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-lucidchart-tutorial/IC791184.png "Application Gallery")
7. 結果ウィンドウで **[Lucidchart]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Lucidchart](./media/active-directory-saas-lucidchart-tutorial/IC791185.png "Lucidchart")
   
## <a name="configuring-single-sign-on"></a>シングル サインオンの構成

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Lucidchart に対する認証を行うことができるようにする方法を説明します。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **[Lucidchart]** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-lucidchart-tutorial/IC791186.png "Configure Single Sign-On")
2. **[ユーザーの Lucidchart へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-lucidchart-tutorial/IC791187.png "Configure Single Sign-On")
3. **[アプリ URL の構成]** ページの **[Lucidchart サインオン URL]** ボックスに、ユーザーが Lucidchart アプリケーションにサインオンするときに使用する URL (例: "*https://chart2.office.lucidchart.com/saml/sso/azure*") を入力し、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-lucidchart-tutorial/IC791188.png "Configure App URL")
4. **[Lucidchart でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックしてメタデータをダウンロードし、データ ファイルをコンピューターのローカルに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-lucidchart-tutorial/IC791189.png "Configure Single Sign-On")
5. 別の Web ブラウザー ウィンドウで、Lucidchart の企業サイトに管理者としてログインします。
6. 上部のメニューで **[チーム]**をクリックします。
   
   ![Team](./media/active-directory-saas-lucidchart-tutorial/IC791190.png "Team")
7. **[アプリケーション] \> [SAML の管理]** をクリックします。
   
   ![Manage SAML](./media/active-directory-saas-lucidchart-tutorial/IC791191.png "Manage SAML")
8. **[SAML Authentication Settings]** ダイアログ ページで、次の手順に従います。
   
   1. **[SAML 認証を有効にする]** を選んで、**[オプション]** をクリックします。

  ![SAML Authentication Settings](./media/active-directory-saas-lucidchart-tutorial/IC791192.png "SAML Authentication Settings")
   2. **[ドメイン]** ボックスで、使用するドメインを入力して、**[証明書の変更]** をクリックします。

  ![Change Certificate](./media/active-directory-saas-lucidchart-tutorial/IC791193.png "Change Certificate")
   3. ダウンロードしたメタデータ ファイルを開いて内容をコピーし、 **[Upload Metadata (メタデータのアップロード)]** ボックスに貼り付けます。

  ![Upload Metadata](./media/active-directory-saas-lucidchart-tutorial/IC791194.png "Upload Metadata")
   4. **[Automatically Add new user to the team (新しいユーザーを自動的にチームに追加する)]** を選んで、**[変更を保存]** をクリックします。

  ![変更を保存](./media/active-directory-saas-lucidchart-tutorial/IC791195.png "Save Changes")
9. [シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
  ![シングル サインオンの構成](./media/active-directory-saas-lucidchart-tutorial/IC791196.png "Configure Single Sign-On")
   
## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成

Lucidchart へのユーザー プロビジョニングの構成にあたって必要な操作はありません。  割り当て済みユーザーがアクセス パネルを使用して Lucidchart にログインしようとすると、そのユーザーが存在するかどうかが Lucidchart によって確認されます。  

使用可能なユーザー アカウントがない場合、ユーザー アカウントは Lucidchart により自動的に作成されます。

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを Lucidchart に割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Lucidchart** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-lucidchart-tutorial/IC791197.png "Assign Users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-lucidchart-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


