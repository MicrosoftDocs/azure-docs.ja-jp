---
title: "チュートリアル: Azure Active Directory と ITRP の統合 | Microsoft Docs"
description: "Azure Active Directory で ITRP を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e09716a3-4200-4853-9414-2390e6c10d98
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/09/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 3d1f24cb23a58b4478a30c5e4a0858b474d5d90e
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>チュートリアル: Azure Active Directory と ITRP の統合
このチュートリアルでは、Azure と ITRP の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* ITRP テナント

このチュートリアルを完了すると、ITRP に割り当てた Azure AD ユーザーは、ITRP 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. ITRP のアプリケーション統合の有効化
2. シングル サインオン (SSO) の構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-itrp-tutorial/IC775551.png "Scenario")

## <a name="enable-the-application-integration-for-itrp"></a>ITRP のアプリケーション統合の有効化
このセクションでは、ITRP のアプリケーション統合を有効にする方法について説明します。

**ITRP のアプリケーション統合を有効にするには、次の手順を実行します。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![Active Directory](./media/active-directory-saas-itrp-tutorial/IC700993.png "Active Directory")

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション](./media/active-directory-saas-itrp-tutorial/IC700994.png "Applications")

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーションの追加](./media/active-directory-saas-itrp-tutorial/IC749321.png "Add application")

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-itrp-tutorial/IC749322.png "Add an application from gallerry")

6. **検索ボックス**に、「**ITRP**」と入力します。
   
    ![アプリケーション ギャラリー](./media/active-directory-saas-itrp-tutorial/IC775565.png "Application Gallery")

7. 結果ウィンドウで **[ITRP]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![ITRP](./media/active-directory-saas-itrp-tutorial/IC775566.png "ITRP")
   
## <a name="configure-single-sign-on"></a>Configure single sign-on

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで ITRP に対する認証を行うことができるようにする方法を説明します。  

ITRP のシングル サインオンを構成するには、証明書から拇印の値を取得する必要があります。  

この手順に慣れていない場合は、「 [How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)」をご覧ください。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **ITRP** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-itrp-tutorial/IC771709.png "Configure single sign-on")

2. **[ユーザーの ITRP へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-itrp-tutorial/IC775567.png "Configure Single Sign-On")

3. **[アプリの URL の構成]** ページで、**[ITRP サインイン URL]** ボックスに、"*https://\<テナント名\>.ITRP.com*" のパターンで URL を入力し、**[次へ]** をクリックします。
   
    ![アプリケーション URL の構成](./media/active-directory-saas-itrp-tutorial/IC775568.png "Configure App URL")

4. **[ITRP でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルを **c:\\ITRP.cer** としてローカルに保存します。
   
    ![シングル サインオンの構成](./media/active-directory-saas-itrp-tutorial/IC775569.png "Configure Single Sign-On")

5. 別の Web ブラウザー ウィンドウで、ITRP 企業サイトに管理者としてログインします。

6. 上部のツールバーで **[設定]**をクリックします。
   
    ![ITRP](./media/active-directory-saas-itrp-tutorial/IC775570.png "ITRP")

7. 左側のナビゲーション ウィンドウで、 **[シングル サインオン]**を選択します。
   
    ![シングル サインオン](./media/active-directory-saas-itrp-tutorial/IC775571.png "Single Sign-On")

8. [シングル サインオン] 構成セクションで、次の手順を実行します。
   
    ![シングル サインオン](./media/active-directory-saas-itrp-tutorial/IC775572.png "Single Sign-On")
    
    ![シングル サインオン](./media/active-directory-saas-itrp-tutorial/IC775573.png "Single Sign-On")   
  1. **[有効]**をクリックします。
  2. Azure クラシック ポータルで、**[ITRP でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログアウト URL]** の値をコピーし、**[リモート ログアウト URL]** ボックスに貼り付けます。
  3. Azure クラシック ポータルで、**[ITRP でのシングル サインオンの構成]** ダイアログ ページの **[SAML SSO URL]** の値をコピーし、**[SAML SSO URL]** ボックスに貼り付けます。
  4. エクスポートした証明書から **[拇印]** の値をコピーし、**[証明書の指紋]** ボックスに貼り付けます。
      
     >[!TIP]
     >詳細については、「[How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)」をご覧ください。
     >
    
  5. **[保存]**をクリックします。

9. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-itrp-tutorial/IC775574.png "Configure Single Sign-On")
   
## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]

Azure AD ユーザーが ITRP にログインできるようにするには、ユーザーを ITRP にプロビジョニングする必要があります。  

ITRP の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **ITRP** テナントにログインします。

2. 上部のツールバーの **[レコード]**をクリックします。
   
    ![管理](./media/active-directory-saas-itrp-tutorial/IC775575.png "Admin")

3. ポップアップ メニューから **[ユーザー]**を選択します。
   
    ![ユーザー](./media/active-directory-saas-itrp-tutorial/IC775587.png "People")

4. **[新しいユーザーの追加]** (“+”) をクリックします。
   
    ![管理](./media/active-directory-saas-itrp-tutorial/IC775576.png "Admin")

5. [新しいユーザーの追加] ダイアログで、次の手順を実行します。
   
    ![ユーザー](./media/active-directory-saas-itrp-tutorial/IC775577.png "User")   
  1. プロビジョニングする有効な AAD アカウントの**名前**と**メール**を入力します。
  2. [ **Save**] をクリックします。

>[!NOTE]
>ITRP から提供されている他の ITRP ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。 
> 

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを ITRP に割り当てるには、次の手順を実行します。**

1. Azure AD ポータルで、テスト アカウントを作成します。

2. **ITRP **アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-itrp-tutorial/IC775588.png "Assign Users")

3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
    ![はい](./media/active-directory-saas-itrp-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


