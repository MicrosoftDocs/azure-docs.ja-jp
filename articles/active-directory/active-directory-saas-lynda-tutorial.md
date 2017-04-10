---
title: "チュートリアル: Azure Active Directory と Lynda.com の統合 | Microsoft Docs"
description: "Azure Active Directory で Lynda.com を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f6c92789-8b64-4049-bac9-8cb928398433
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07b9df5ab5c7df0089f001a26200bb9c3fb1c508
ms.openlocfilehash: 7f4f1c22e0b3382a9de770800ccb733c61d4f5b0
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-lyndacom"></a>チュートリアル: Azure Active Directory と Lynda.com の統合
このチュートリアルでは、Azure と Lynda.com の統合について説明します。  

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Lynda.com テナント

このチュートリアルを完了すると、Lynda.com に割り当てた Azure AD ユーザーは、Lynda.com 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Lynda.com のアプリケーション統合の有効化
2. シングル サインオン (SSO) の構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-lynda-tutorial/IC781046.png "Scenario")

## <a name="enabling-the-application-integration-for-lyndacom"></a>Lynda.com のアプリケーション統合の有効化
このセクションでは、Lynda.com のアプリケーション統合を有効にする方法について説明します。

**Lynda.com のアプリケーション統合を有効にするには、次の手順に従います。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-lynda-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-lynda-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-lynda-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-lynda-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**Lynda.com**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-lynda-tutorial/IC777524.png "Application Gallery")
7. 結果ウィンドウで **[Lynda.com]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Lynda.com](./media/active-directory-saas-lynda-tutorial/IC777525.png "Lynda.com")
   
## <a name="configuring-single-sign-on"></a>シングル サインオンの構成

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Lynda.com に対する認証を行うことができるようにする方法を説明します。

>[!IMPORTANT]
>Lynda.com テナントでシングル サインオンを構成できるようにするには、まず Lynda.com テクニカル サポートに連絡してこの機能を有効にする必要があります。 
> 

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Lynda.com** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-lynda-tutorial/IC777526.png "Configure single sign-on")
2. **[ユーザーの Lynda.com へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-lynda-tutorial/IC777527.png "Configure single sign-on")
3. **[アプリケーション URL の構成]** ページの、**[Lynda.com のサインイン URL]** ボックスに、Lynda.com のテナント URL (例: *https://shib.lynda.com/Shibboleth.sso/InCommon?providerId=https://sts.windows-ppe.net/6247032d-9415-403c-b72b-277e3fb6f2c8/&target=https://shib.lynda.com/InCommon*) を入力し、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-lynda-tutorial/IC781047.png "Configure app URL")
4. **[Lynda.com でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックしてメタデータをダウンロードし、証明書ファイルをコンピューターのローカルに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-lynda-tutorial/IC777529.png "Configure single sign-on")
5. ダウンロードしたメタデータ ファイルを Lynda.com サポート チームに送信します。 シングル サインオンの構成は、Lynda.com のサポート チームが行います。
6. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-lynda-tutorial/IC777530.png "Configure single sign-on")
   
## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成

Lynda.com へのユーザー プロビジョニングの構成にあたって必要な操作はありません。  
割り当て済みユーザーがアクセス パネルを使用して Lynda.com にログインしようとすると、そのユーザーが存在するかどうかが Lynda.com によって確認されます。  

使用可能なユーザー アカウントがない場合、ユーザー アカウントは Lynda.com により自動的に作成されます。

>[!NOTE]
>Lynda.com から提供されている他の Lynda.com ユーザー アカウント作成ツールや API を使用して、AAD ユーザー アカウントをプロビジョニングできます。 
> 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを Lynda.com に割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Lynda.com** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-lynda-tutorial/IC777531.png "Assign users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-lynda-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


