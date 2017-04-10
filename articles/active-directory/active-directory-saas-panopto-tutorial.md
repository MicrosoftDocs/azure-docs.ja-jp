---
title: "チュートリアル: Azure Active Directory と Panopto の統合 | Microsoft Docs"
description: "Azure Active Directory で Panopto を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 89c88e23-93ce-4970-9baa-1104c4e8fe4a
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 41e04425a291c9f89bc58341c6bad22ad1bdb8ef
ms.openlocfilehash: 90f962fbd19f125cfb609e439f157c4822acfce4


---

# <a name="tutorial-azure-active-directory-integration-with-panopto"></a>チュートリアル: Azure Active Directory と Panopto の統合
このチュートリアルでは、Azure と Panopto の統合について説明します。 このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Panopto テナント

このチュートリアルを完了すると、Panopto に割り当てた Azure AD ユーザーは、Panopto 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Panopto のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-panopto-tutorial/IC777665.png "Scenario")

## <a name="enabling-the-application-integration-for-panopto"></a>Panopto のアプリケーション統合の有効化
このセクションでは、Panopto のアプリケーション統合を有効にする方法について説明します。

**Panopto のアプリケーション統合を有効にするには、次の手順に従います。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-panopto-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-panopto-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-panopto-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-panopto-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**Panopto**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-panopto-tutorial/IC777666.png "アプリケーション ギャラリー")
7. 結果ウィンドウで **[Panopto]** を選び、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Panopto](./media/active-directory-saas-panopto-tutorial/IC782936.png "Panopto")
   
## <a name="configure-single-sign-on"></a>Configure single sign-on

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD のアカウントで Panopto に対する認証を行えるようにする方法を説明します。  

この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。  
この手順に慣れていない場合は、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Panopto** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-panopto-tutorial/IC777667.png "Configure single sign-on")
2. **[ユーザーの Panopto へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選び、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-panopto-tutorial/IC777668.png "Configure single sign-on")
3. **[アプリケーション URL の構成]** ページで、**[Panopto サインイン URL]** ボックスに、"*https://\<tenant-name\>.Panopto.com*" というパターンの URL を入力し、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-panopto-tutorial/IC777528.png "Configure app URL")
4. **[Panopto でのシングル サインオンの構成]** ページで、証明書をダウンロードするために、**[証明書のダウンロード]** をクリックし、証明書ファイルをコンピューターに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-panopto-tutorial/IC777669.png "Configure single sign-on")
5. 別の Web ブラウザーのウィンドウで、Panopto 企業サイトに管理者としてログインします。
6. 左側のツールバーで、**[システム]**、**[ID プロバイダー]** の順にクリックします。
   
   ![システム](./media/active-directory-saas-panopto-tutorial/IC777670.png "システム")
7. **[プロバイダーを追加する]**を追加します。
   
   ![ID プロバイダー](./media/active-directory-saas-panopto-tutorial/IC777671.png "ID プロバイダー")
8. [SAML プロバイダー] セクションで、次の手順に従います。
   
   ![SaaS の構成](./media/active-directory-saas-panopto-tutorial/IC777672.png "SaaS の構成")
   
   1. **[プロバイダーの種類]** 一覧で **[SAML20]** を選択します。
   2. **[インスタンス名]** テキストボックスに、インスタンスの名前を入力します。
   3. **[わかりやすい説明]** テキストボックスに、わかりやすい説明を入力します。
   4. Azure クラシック ポータルの **[Panopto でのシングル サインオンの構成]** ダイアログ ページで、**[発行者の URL]** の値をコピーし、**[Issuer]** ボックスに貼り付けます。
   5. Azure クラシック ポータルの **[Panopto でのシングル サインオンの構成]** ダイアログ ページで、**[SAML SSO URL]** の値をコピーし、**[Bounce Page URL]** テキストボックスに貼り付けます。
   6. ダウンロードした証明書から **base-64 でエンコードされた** ファイルを作成します。    
   
      >[!TIP]
      >詳細については、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。
      >
      
   7. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、 **[公開キー]** テキストボックスに貼り付けます。
   8. **[保存]**をクリックします。

 ![保存](./media/active-directory-saas-panopto-tutorial/IC777673.png "保存")
9. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-panopto-tutorial/IC777674.png "Configure single sign-on")
   
## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成

Panopto へのユーザー プロビジョニングの構成にあたって必要な操作はありません。  
割り当てられたユーザーがアクセス パネルを使用して Panopto にログインしようとすると、そのユーザーが存在するかどうかが Panopto によって確認されます。  

使用可能なユーザー アカウントがない場合、ユーザー アカウントは Panopto により自動的に作成されます。

> [!NOTE]
> Panopto から提供されている他の Panopto ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。
> 


## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを Panopto に割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Panopto** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-panopto-tutorial/IC777675.png "Assign users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-panopto-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。




<!--HONumber=Feb17_HO1-->


