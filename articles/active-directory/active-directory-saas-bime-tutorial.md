---
title: "チュートリアル: Azure Active Directory と Bime の統合 | Microsoft Docs"
description: "Azure Active Directory で Bime を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: bdcf0729-c880-4c95-b739-0f6345b17dd8
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: bd3dd077bef87a78904ffd5d2be469b6b8bc8959
ms.openlocfilehash: 7857480d033e4d570aa48569e08bb30846b280f6
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-bime"></a>チュートリアル: Azure Active Directory と Bime の統合
このチュートリアルでは、Azure と Bime の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Bime テナント

このチュートリアルを完了すると、Bime に割り当てた Azure AD ユーザーは、Bime 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

* Bime のアプリケーション統合の有効化
* シングル サインオン (SSO) の構成
* ユーザー プロビジョニングの構成
* ユーザーの割り当て

![シナリオ](./media/active-directory-saas-bime-tutorial/IC775552.png "Scenario")

## <a name="enable-the-application-integration-for-bime"></a>Bime のアプリケーション統合の有効化
このセクションでは、Bime のアプリケーション統合を有効にする方法について説明します。

**Bime のアプリケーション統合を有効にするには、次の手順を実行します。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-bime-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-bime-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-bime-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-bime-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**Bime**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-bime-tutorial/IC775553.png "Application Gallery")
7. 結果ウィンドウで **[Bime]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Bime](./media/active-directory-saas-bime-tutorial/IC775554.png "Bime")
   
## <a name="configure-single-sign-on"></a>Configure single sign-on

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Bime に対する認証を行えるようにする方法を説明します。  

Bime の SSO を構成するには、証明書からサムプリント値を取得する必要があります。 この手順に慣れていない場合は、「 [How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)」をご覧ください。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Bime** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-bime-tutorial/IC771709.png "Configure single sign-on")
2. **[ユーザーの Bime へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-bime-tutorial/IC775555.png "Configure Single Sign-On")
3. **[アプリの URL の構成]** ページで、**[Bime サインイン URL]** ボックスに、"*https://\<テナント名\>.Bimeapp.com*" のパターンで URL を入力し、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-bime-tutorial/IC775556.png "Configure App URL")
4. **[Bime でのシングル サインオンの構成]** ページで、証明書をダウンロードするために、**[証明書のダウンロード]** をクリックし、証明書ファイルを **c:\\Bime.cer** としてローカルに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-bime-tutorial/IC775557.png "Configure Single Sign-On")
5. 別の Web ブラウザー ウィンドウで、Bime 企業サイトに管理者としてログインします。
6. ツールバーで、**[管理者]**、**[アカウント]** の順にクリックします。
   
   ![管理者](./media/active-directory-saas-bime-tutorial/IC775558.png "Admin")
7. アカウント構成ページで、次の手順に従います。
   
   ![シングル サインオンの構成](./media/active-directory-saas-bime-tutorial/IC775559.png "Configure Single Sign-On")
   
   1. **[SAML 認証を有効にする]**を選択します。
   2. Azure クラシック ポータルで、**[Bime でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログイン URL]** の値をコピーし、**[リモート ログイン URL]** ボックスに貼り付けます。
   3. エクスポートした証明書から **[拇印]** の値をコピーし、**[証明書の指紋]** ボックスに貼り付けます。       
      
      >[!TIP]
      >詳細については、「[How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)」をご覧ください。 
      > 
   4. **[保存]**をクリックします。
8. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-bime-tutorial/IC775560.png "Configure Single Sign-On")
   
## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]

Azure AD ユーザーが Bime にログインできるようにするには、ユーザーを Bime にプロビジョニングする必要があります。  

* Bime の場合、プロビジョニングは手動で行います。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. **Bime** テナントにログインします。
2. ツールバーで、**[管理者]**、**[ユーザー]** の順にクリックします。
   
   ![管理者](./media/active-directory-saas-bime-tutorial/IC775561.png "Admin")
3. **[ユーザー リスト]** で、**[新しいユーザーの追加]** ("+") をクリックします。
   
   ![ユーザー](./media/active-directory-saas-bime-tutorial/IC775562.png "Users")
4. **[ユーザーの詳細]** ダイアログ ページで、次の手順を実行します。
   
   ![ユーザーの詳細](./media/active-directory-saas-bime-tutorial/IC775563.png "User Details")   
  1. プロビジョニングする有効な AAD アカウントの名、姓、ログイン、電子メールを入力します。
  2. [保存] をクリックします。

>[!NOTE]
>Bime から提供されている他の Bime ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
>  

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを Bime に割り当てるには、次の手順を実行します。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Bime** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-bime-tutorial/IC775564.png "Assign users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-bime-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


