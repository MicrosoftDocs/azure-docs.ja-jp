---
title: "チュートリアル: Azure Active Directory と Envoy の統合 | Microsoft Docs"
description: "Azure Active Directory で Envoy を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 71f7afcc-1033-4098-9b7e-4f9f2b26f734
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 913f0688d757829771a8456b3e3be13f69c5b1da
ms.openlocfilehash: a72feeb919f2bbc1d96736e33c8c99e340ff6914
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-envoy"></a>チュートリアル: Azure Active Directory と Envoy の統合
このチュートリアルでは、Azure と Envoy の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Envoy テナント

このチュートリアルを完了すると、Envoy に割り当てた Azure AD ユーザーは、Envoy 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

* Envoy のアプリケーション統合の有効化
* シングル サインオン (SSO) の構成
* ユーザー プロビジョニングの構成
* ユーザーの割り当て

![シナリオ](./media/active-directory-saas-envoy-tutorial/IC776759.png "Scenario")

## <a name="enable-the-application-integration-for-envoy"></a>Envoy のアプリケーション統合の有効化
このセクションでは、Envoy のアプリケーション統合を有効にする方法について説明します。

**Envoy のアプリケーション統合を有効にするには、次の手順を実行します。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-envoy-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-envoy-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-envoy-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-envoy-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**Envoy**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-envoy-tutorial/IC776760.png "Application Gallery")
7. 結果ウィンドウで **[Envoy]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Envoy](./media/active-directory-saas-envoy-tutorial/IC776777.png "Envoy")
   
## <a name="configure-single-sign-on"></a>Configure single sign-on

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Envoy に対する認証を行うことができるようにする方法を説明します。  

Envoy の SSO を構成するには、証明書からサムプリント値を取得する必要があります。 この手順に慣れていない場合は、「 [How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)」をご覧ください。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Envoy** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの有効化](./media/active-directory-saas-envoy-tutorial/IC776778.png "シングル サインオンの有効化")
2. **[ユーザーの Envoy へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-envoy-tutorial/IC776779.png "Configure single sign-on")
3. **[アプリケーション URL の構成]** ページの **[Envoy サインイン URL]** ボックスに、"*https://\<テナント名\>.Envoy.com*" というパターンの URL を入力し、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-envoy-tutorial/IC776780.png "Configure app URL")
4. **[Envoy でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルを **c:\\Envoy.cer** としてローカルに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-envoy-tutorial/IC776781.png "Configure single sign-on")
5. 別の Web ブラウザー ウィンドウで、Envoy 企業サイトに管理者としてログインします。
6. 上部のツールバーで **[Settings]**をクリックします。
   
   ![Envoy](./media/active-directory-saas-envoy-tutorial/IC776782.png "Envoy")
7. **[会社]**をクリックします。
   
   ![Company](./media/active-directory-saas-envoy-tutorial/IC776783.png "Company")
8. **[SAML]**をクリックします。
   
   ![SAML](./media/active-directory-saas-envoy-tutorial/IC776784.png "SAML")
9. **[SAML 認証]** 構成セクションで、次の手順を実行します。
   
   ![SAML authentication](./media/active-directory-saas-envoy-tutorial/IC776785.png "SAML authentication")   
   >[!NOTE]
   >[HQ 場所 ID] の値は、アプリケーションによって自動的に生成されます。 
   > 
   
   1. エクスポートした証明書から **[拇印]** の値をコピーし、**[指紋]** ボックスに貼り付けます。  
   
      >[!TIP]
      >詳細については、「[How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)」をご覧ください。 
      > 
   2. Azure クラシック ポータルの **[Envoy でのシングル サインオンの構成]** ダイアログ ページで、**[SAML SSO URL]** の値をコピーし、**[ID プロバイダー HTTP SAML URL]** ボックスに貼り付けます。
   3. **[変更を保存]**をクリックします。
10. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
    
    ![シングル サインオンの構成](./media/active-directory-saas-envoy-tutorial/IC776786.png "Configure single sign-on")
    
## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]

Envoy へのユーザー プロビジョニングの構成にあたって必要な操作はありません。  
割り当てられたユーザーがアクセス パネルを使用して Envoy にログインしようとすると、そのユーザーが存在するかどうかが Envoy によって確認されます。  

* 使用可能なユーザー アカウントがない場合、ユーザー アカウントは自動的に作成されます。

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-envoy-perform-the-following-steps"></a>ユーザーを Envoy に割り当てるには、次の手順を実行します。
1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Envoy** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-envoy-tutorial/IC776787.png "Assign users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-envoy-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


