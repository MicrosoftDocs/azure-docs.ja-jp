---
title: "チュートリアル: Azure Active Directory と Learningpool の統合 | Microsoft Docs"
description: "Azure Active Directory で Learningpool を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 51e8695f-31e1-4d09-8eb3-13241999d99f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 83afd8112cf7835528a1e3e8f30b52d4cf863ae3
ms.openlocfilehash: c5c530fcc158a75b1f1ba140e364c702350ce975
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-learningpool"></a>チュートリアル: Azure Active Directory と Learningpool の統合
このチュートリアルの目的は、Azure と Learningpool の統合を示すことです。  

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Learningpool でのシングル サインオン () が有効なサブスクリプション

このチュートリアルを完了すると、Learningpool に割り当てた Azure AD ユーザーは、Learningpool 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Learningpool のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-learningpool-tutorial/IC791166.png "Scenario")

## <a name="enabling-the-application-integration-for-learningpool"></a>Learningpool のアプリケーション統合の有効化
このセクションでは、Learningpool のアプリケーション統合を有効にする方法を説明します。

**Learningpool のアプリケーション統合を有効にするには、次の手順に従います。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-learningpool-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-learningpool-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-learningpool-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-learningpool-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に「**Learningpool**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-learningpool-tutorial/IC795073.png "Application Gallery")
7. 結果ウィンドウで **[Learningpool]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Learningpool](./media/active-directory-saas-learningpool-tutorial/IC809577.png "Learningpool")
   
## <a name="configuring-single-sign-on"></a>シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Learningpool に対する認証を行えるようにする方法を説明します。

Learningpool アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを **SAML トークン属性**の構成に追加する必要があります。  
次のスクリーンショットはその例です。

![SAML Token Attributes](./media/active-directory-saas-learningpool-tutorial/IC795074.png "SAML Token Attributes")

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Learningpool** アプリケーション統合ページの一番上のメニューで **[属性]** をクリックして、**[SAML トークン属性]** ダイアログを開きます。
   
   ![属性](./media/active-directory-saas-learningpool-tutorial/IC795075.png "Attributes")
2. 必要な属性のマッピングを追加するには、次の手順を実行します。
   
   ### 
   | 属性名 | 属性値 |
   | --- | --- |
   | urn:oid:1.2.840.113556.1.4.221 | User.userprincipalname |
   |  urn:oid:2.5.4.42 |User.givenname |
   | urn:oid:0.9.2342.19200300.100.1.3 |User.mail |
   | urn:oid:2.5.4.4 |User.surname |
   
   1. 上記の表の各データ行で、 **[ユーザー属性の追加]**をクリックします。
   2. **[属性名]** ボックスに、その行に対して表示される属性名を入力します。
   3. **[属性値]** リストから、その行に対して表示される属性値を選択します。
   4. **[完了]**をクリックします。
3. **[変更の適用]**をクリックします。
4. ブラウザーで、**[戻る]** をクリックして、**[クイック スタート]** ダイアログをもう一度開きます。
5. **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログ ボックスを開きます。
   
   ![Configure Singel Sign-On](./media/active-directory-saas-learningpool-tutorial/IC795076.png "Configure Singel Sign-On")
6. **[ユーザーの Learningpool へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-learningpool-tutorial/IC795077.png "Configure Single Sign-On")
7. **[アプリケーション URL の構成]** ページの **[Learningpool のサインオン URL]** ボックスに、ユーザーが Learningpool アプリケーションへのサインオンに使用する URL (例: https://parliament.preview.learningpool.com/auth/shibboleth/index.php) を入力し、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-learningpool-tutorial/IC795078.png "Configure App URL")
8. **[Learningpool でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックしてメタデータをダウンロードし、証明書ファイルをコンピューターのローカルに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-learningpool-tutorial/IC795079.png "Configure Single Sign-On")
9. メタデータ ファイルを Learningpool サポート チームに転送します。
   
   >[!NOTE]
   >SSO は、Learningpool サポート チームが有効にする必要があります。
   > 
   
10. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
    
    ![シングル サインオンの構成](./media/active-directory-saas-learningpool-tutorial/IC795080.png "Configure Single Sign-On")
    
## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成

Azure AD ユーザーが Learningpool にログインできるようにするには、ユーザーを Learningpool にプロビジョニングする必要があります。

Learningpool へのユーザー プロビジョニングの構成にあたって必要な操作はありません。  
ユーザーは、Learningpool サポート チームが作成する必要があります。

>[!NOTE]
>Learningpool から提供されている他の Learningpool ユーザー アカウント作成ツールや API を使用して、AAD ユーザー アカウントをプロビジョニングできます。 
> 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを Learningpool に割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Learningpool** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-learningpool-tutorial/IC795081.png "Assign Users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-learningpool-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


