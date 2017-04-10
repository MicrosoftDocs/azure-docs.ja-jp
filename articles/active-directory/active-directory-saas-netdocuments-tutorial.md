---
title: "チュートリアル: Azure Active Directory と NetDocuments の統合 | Microsoft Docs"
description: "Azure Active Directory で NetDocuments を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 1a47dc42-1a17-48a2-965e-eca4cfb2f197
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/26/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: bd503bb141b5686f149c5fb46ba069db070d5fae
ms.openlocfilehash: 99dbb3deefb066b619b839aa709fe5e9b804eb27
ms.lasthandoff: 02/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-netdocuments"></a>チュートリアル: Azure Active Directory と NetDocuments の統合
このチュートリアルの目的は、Azure と NetDocuments の統合を紹介することです。 このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* NetDocuments テナント

このチュートリアルを完了すると、NetDocuments に割り当てた Azure AD ユーザーは、NetDocuments 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. NetDocuments のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-netdocuments-tutorial/IC795040.png "Scenario")

## <a name="enabling-the-application-integration-for-netdocuments"></a>NetDocuments のアプリケーション統合の有効化
このセクションでは、NetDocuments のアプリケーション統合を有効にする方法を説明します。

**NetDocuments のアプリケーション統合を有効にするには、次の手順に従います。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-netdocuments-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-netdocuments-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-netdocuments-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-netdocuments-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**NetDocuments**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-netdocuments-tutorial/IC795041.png "Application Gallery")
7. 結果ウィンドウで **[NetDocuments]** を選び、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![NetDocuments](./media/active-directory-saas-netdocuments-tutorial/IC795042.png "NetDocuments")
   
## <a name="configuring-single-sign-on"></a>シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで NetDocuments に対する認証を行えるようにする方法を説明します。  

NetDocuments にシングル サインオンを構成するには、証明書の拇印の値を取得する必要があります。 この手順に慣れていない場合は、「 [How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)」をご覧ください。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **NetDocuments** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-netdocuments-tutorial/IC795043.png "Configure Single Sign-On")
2. **[ユーザーの NetDocuments へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選び、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-netdocuments-tutorial/IC795044.png "Configure Single Sign-On")
3. **[アプリケーション URL の構成]** ページで、次の手順を実行します。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-netdocuments-tutorial/IC795045.png "Configure App URL")
   
   1. **[サインオン URL]** ボックスに、ユーザーが NetDocuments アプリケーションへのサインオンに使用する URL を入力します (例: "*https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=CA-JI1BG3H1*")。
   2. **[NetDocuments 応答 URL]** ボックスに、**[サインオン URL]** ボックスに入力したのと同じ値を入力します。  
      
      > [!NOTE]
      > 適切な値は、 **フェデレーション ID** ダイアログの最後に見つけることができます (ステップ 9 のスクリーン ショットをご覧ください)。
      > 
      
   3. **[次へ]**をクリックします。
4. **[NetDocuments でのシングル サインオン構成]** ページで、証明書をダウンロードするには、**[証明書のダウンロード]** をクリックし、コンピューターにローカルで証明書ファイルを保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-netdocuments-tutorial/IC795046.png "Configure Single Sign-On")
5. 別の Web ブラウザーのウィンドウで、管理者として NetDocuments 企業サイトにログインします。
6. **[Admin]**に移動します。
7. **[ユーザーとグループの追加と削除]**をクリックします。
   
   ![リポジトリ](./media/active-directory-saas-netdocuments-tutorial/IC795047.png "リポジトリ")
8. **[認証オプションの詳細な構成]**をクリックします。
   
   ![認証オプションの詳細な構成](./media/active-directory-saas-netdocuments-tutorial/IC795048.png "認証オプションの詳細な構成")
9. **フェデレーション ID** ダイアログで、次の手順に従います。
   
   ![フェデレーション ID](./media/active-directory-saas-netdocuments-tutorial/IC795049.png "フェデレーション ID")
   
   1. **[フェデレーション ID のサーバーの種類]** として、**[Active Directory フェデレーション サービス]** を選びます。
   2. **[ファイルの選択]**をクリックして、ダウンロードされたメタデータ ファイルをアップロードします。
   3. **[OK]**をクリックします。
10. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
    
    ![シングル サインオンの構成](./media/active-directory-saas-netdocuments-tutorial/IC795050.png "Configure Single Sign-On")
    
## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成

Azure AD ユーザーが NetDocuments にログインできるようにするには、そのユーザーを NetDocuments にプロビジョニングする必要があります。 NetDocuments の場合、プロビジョニングは手動で行います。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. **NetDocuments** 企業サイトに管理者としてサインオンします。
2. 上部のメニューで **[Admin]**をクリックします。
   
   ![管理](./media/active-directory-saas-netdocuments-tutorial/IC795051.png "Admin")
3. **[ユーザーとグループの追加と削除]**をクリックします。
   
   ![リポジトリ](./media/active-directory-saas-netdocuments-tutorial/IC795047.png "リポジトリ")
4. **[電子メール アドレス]** テキストボックスに、プロビジョニングする有効な Azure Active Directory アカウントの電子メール アドレスを入力して、**[ユーザーの追加]** をクリックします。
   
   ![電子メール アドレス](./media/active-directory-saas-netdocuments-tutorial/IC795053.png "電子メール アドレス")
   
   > [!NOTE]
   > Azure Active Directory のアカウント所有者には、アカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。
   > 
   > 

> [!NOTE]
> 他の NetDocuments ユーザー アカウントの作成ツールまたは NetDocuments から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを NetDocuments に割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **NetDocuments** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-netdocuments-tutorial/IC795054.png "Assign Users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-netdocuments-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


