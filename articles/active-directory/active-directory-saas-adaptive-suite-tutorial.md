---
title: "チュートリアル: Azure Active Directory と Adaptive Suite の統合 | Microsoft Docs"
description: "Azure Active Directory で Adaptive Suite を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/12/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 9e83f60701158f791811ce368ee3541358215071
ms.openlocfilehash: aecd4ebb1e905c7341bd02d8db451eb82d64604b
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-suite"></a>チュートリアル: Azure Active Directory と Adaptive Suite の統合
このチュートリアルでは、Azure と Adaptive Suite の統合について説明します。  

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Adaptive Suite のテナント

このチュートリアルを完了すると、Adaptive Suite に割り当てた Azure AD ユーザーは、Adaptive Suite 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

* Adaptive Suite のアプリケーション統合の有効化
* シングル サインオン (SSO) の構成
* ユーザー プロビジョニングの構成
* ユーザーの割り当て

![シナリオ](./media/active-directory-saas-adaptive-suite-tutorial/IC805637.png "Scenario")

## <a name="enable-the-application-integration-for-adaptive-suite"></a>Adaptive Suite のアプリケーション統合の有効化
このセクションでは、Adaptive Suite のアプリケーション統合を有効にする方法を説明します。

**Adaptive Suite のアプリケーション統合を有効にするには、次の手順に従います。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-adaptive-suite-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-adaptive-suite-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-adaptive-suite-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-adaptive-suite-tutorial/IC749322.png "Add an application from gallerry")
6. **[検索]** ボックスに、「**Adaptive Suite**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-adaptive-suite-tutorial/IC805638.png "Application Gallery")
7. 結果ウィンドウで **[Adaptive Suite]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Adaptive Suite](./media/active-directory-saas-adaptive-suite-tutorial/IC805639.png "Adaptive Suite")
   
## <a name="configure-single-sign-on"></a>Configure single sign-on

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Adaptive Suite に対する認証を行えるようにする方法を説明します。

Adaptive Suite にシングル サインオンを構成するには、証明書の拇印の値を取得する必要があります。 この手順に慣れていない場合は、「 [How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)」をご覧ください。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Adaptive Suite** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-adaptive-suite-tutorial/IC805640.png "Configure Single Sign-On")
2. **[ユーザーの Adaptive Suite へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-adaptive-suite-tutorial/IC805641.png "Configure Single Sign-On")
3. **[アプリケーション設定の構成]** ページの **[応答 URL]** テキスト ボックスに、次のパターン "*https://login.adaptiveinsights.com:443/samlsso/RlJFRVRSSUFMMTI3MTE=*" を使用して URL を入力してから、**[次へ]** をクリックします。
   
>[!NOTE]
> この値は、Adaptive Suite の **[SAML SSO 設定]** ページから取得できます。
>  
   
  ![アプリケーションの設定の構成](./media/active-directory-saas-adaptive-suite-tutorial/IC805642.png "Configure App Settings")
4. **[Adaptive Suite でのシングル サインオン構成]** ページで、証明書をダウンロードするには、**[証明書のダウンロード]** をクリックし、コンピューターにローカルで証明書ファイルを保存します。
   
  ![シングル サインオンの構成](./media/active-directory-saas-adaptive-suite-tutorial/IC805643.png "Configure Single Sign-On")
5. 別の Web ブラウザーのウィンドウで、管理者として Adaptive Suite 企業サイトにログインします。
6. **[Admin]**に移動します。
   
  ![管理者](./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Admin")
7. **[ユーザーとロール]** セクションで、**[SAML SSO 設定の管理]** をクリックします。
   
  ![SAML SSO 設定の管理](./media/active-directory-saas-adaptive-suite-tutorial/IC805645.png "Manage SAML SSO Settings")
8. **[SAML SSO 設定]** ページで、次の手順を実行します。
   
  ![SAML SSO 設定](./media/active-directory-saas-adaptive-suite-tutorial/IC805646.png "SAML SSO Settings")
   
  1. **[ID プロバイダー名]** テキスト ボックスに、構成の名前を入力します。
  2. Azure クラシック ポータルの **[Adaptive Suite でのシングル サインオンの構成]** ダイアログ ページで、**[エンティティ ID]** の値をコピーし、**[ID プロバイダー エンティティ ID]** テキスト ボックスに貼り付けます。
  3. Azure クラシック ポータルの **[Adaptive Suite でのシングル サインオンの構成]** ダイアログ ページで、**[SAML SSO URL]** の値をコピーし、**[ID プロバイダー SSO URL]** テキスト ボックスに貼り付けます。
  4. Azure クラシック ポータルの **[Adaptive Suite でのシングル サインオンの構成]** ダイアログ ページで、**[SAML SSO URL]** の値をコピーし、**[カスタム ログアウト URL]** テキスト ボックスに貼り付けます。
  5. ダウンロードした証明書をアップロードするには、 **[ファイルの選択]**をクリックします。
  6. 次のように選択します。
    * **[SAML ユーザー ID]** では、**[ユーザーの Adaptive Insights ユーザー名]** を選択します。
    * **[SAML ユーザー ID の場所]** では、**[サブジェクトの NameID 内のユーザー ID ]** を選択します。
    * **[SAML NameID 形式]** では、**[電子メール アドレス]** を選択します。
    * **[SAML を有効にする]** では、**[SAML SSO を許可して、Adaptive Insights に直接ログインする]** を選択します。
   7. **[保存]**をクリックします。
9. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-adaptive-suite-tutorial/IC805647.png "Configure Single Sign-On")
   
## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]

Azure AD ユーザーが Adaptive Suite にログインできるようにするには、そのユーザーを Adaptive Suite にプロビジョニングする必要があります。  

* Adaptive Suite の場合、プロビジョニングは手動で実行します。

**ユーザー プロビジョニングを構成するには、次の手順に従います。** 

1. **Adaptive Suite** 企業サイトに管理者としてログインします。
2. **[Admin]**に移動します。
   
   ![管理者](./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Admin")
3. **[ユーザーとロール]** セクションで **[ユーザーの追加]** をクリックします。
   
   ![ユーザーの追加](./media/active-directory-saas-adaptive-suite-tutorial/IC805648.png "Add User")
4. **[新しいユーザー]** セクションで、次の手順に従います。
   
   ![送信](./media/active-directory-saas-adaptive-suite-tutorial/IC805649.png "Submit")   
  1. 関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory ユーザーの **[名前]**、**[ログイン]**、**[電子メール]**、**[パスワード]** を入力します。
  2. **[ロール]**を選択します。
  3. **[送信]**をクリックします。

>[!NOTE]
>他の Adaptive Suite ユーザー アカウント作成ツールまたは Adaptive Suite から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
>  

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを Adaptive Suite に割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Adaptive Suite** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-adaptive-suite-tutorial/IC805650.png "Assign Users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-adaptive-suite-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


