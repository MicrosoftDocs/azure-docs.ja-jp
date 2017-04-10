---
title: "チュートリアル: Azure Active Directory と AnswerHub の統合 | Microsoft Docs"
description: "Azure Active Directory で AnswerHub を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/17/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ff8a2bc6350be35fa368e11d2ebd4a994f01cedc
ms.openlocfilehash: af99741f5f5f8b2fb1c4fc8975571c65c2fc98c6
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>チュートリアル: Azure Active Directory と AnswerHub の統合
このチュートリアルでは、Azure と [AnswerHub](http://www.dzonesoftware.com/products/answerhub-question-answer-software)の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* [AnswerHub](http://www.dzonesoftware.com/products/answerhub-question-answer-software) のシングル サインオンに対応したサブスクリプション

このチュートリアルを完了すると、AnswerHub に割り当てられた Azure AD ユーザーは、AnswerHub 企業サイト (サービス プロバイダーが開始したサインオン) で、または[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関するページに従って、アプリケーションにシングル サインオン (SSO) でアクセスできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. AnswerHub のアプリケーション統合の有効化
2. シングル サインオン (SSO) の構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-answerhub-tutorial/IC785165.png "Scenario")

## <a name="enable-the-application-integration-for-answerhub"></a>AnswerHub のアプリケーション統合の有効化
このセクションでは、AnswerHub のアプリケーション統合を有効にする方法を説明します。

**AnswerHub のアプリケーション統合を有効にするには、次の手順に従います。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-answerhub-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-answerhub-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-answerhub-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-answerhub-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**AnswerHub**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-answerhub-tutorial/IC785166.png "Application Gallery")
7. 結果ウィンドウで **[AnswerHub]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![AnswerHub](./media/active-directory-saas-answerhub-tutorial/IC785167.png "AnswerHub")

## <a name="configure-single-sign-on"></a>Configure single sign-on
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで AnswerHub に対する認証を行えるようにする方法を説明します。  

この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。 この手順に慣れていない場合は、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **AnswerHub** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-answerhub-tutorial/IC785168.png "Configure single sign-on")
2. **[ユーザーの AnswerHub へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-answerhub-tutorial/IC785169.png "Configure single sign-on")
3. **[アプリケーション URL の構成]** ページで、**[AnswerHub サインイン URL]** ボックスに、"*https://company.answerhub.com*" のパターンを使用して URL を入力し、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-answerhub-tutorial/IC785170.png "Configure App URL")
4. **[AnswerHub でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルをコンピューターのローカルに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-answerhub-tutorial/IC785171.png "Configure single sign-on")
5. 別の Web ブラウザーのウィンドウで、管理者として AnswerHub 企業サイトにログインします。
   
   >[!NOTE]
   >AnswerHub の構成について不明点がある場合は、[AnswerHub サポート チーム](mailto:success@answerhub.com.)にお問い合わせください。
   > 
6. [ **Administration**] に移動します。
7. [ **User and Group** ] タブをクリックします。
8. 左側のナビゲーション ウィンドウで、**[Social Settings]** セクションの **[SAML Setup]** をクリックします。
9. [ **IDP Config** ] タブをクリックします。
10. [ **IDP Config** ] タブで、次の手順を実行します。

  ![SAML のセットアップ](./media/active-directory-saas-answerhub-tutorial/IC785172.png "SAML Setup")  
  1. Azure クラシック ポータルで、**[AnswerHub でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログイン URL]** の値をコピーし、**[IDP Login URL]** ボックスに貼り付けます。
  2. Azure クラシック ポータルで、**[AnswerHub でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログアウト URL]** の値をコピーし、**[IDP Logout URL]** ボックスに貼り付けます。
  3. Azure クラシック ポータルで、**[AnswerHub でのシングル サインオンの構成]** ダイアログ ページの **[名前識別子形式]** の値をコピーし、**[IDP Name Identifier Format]** ボックスに貼り付けます。
  4. [ **Keys and Certificates**] をクリックします。
11. [Keys and Certificates] タブでは、次の手順を実行します。
    
  ![Keys and Certificates (キーと証明書)](./media/active-directory-saas-answerhub-tutorial/IC785173.png "Keys and Certificates")  
  1. ダウンロードした証明書から **base-64 でエンコードされた** ファイルを作成します。
  
    >[!TIP]
    >詳細については、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。 
    > 
  2. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、[ **IDP Public Key (x509 Format)** ] テキストボックスに貼り付けます。
  3. **[保存]**をクリックします。
12. **[IDP Config]** タブで、**[Save]** をクリックします。
13. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
    
  ![シングル サインオンの構成](./media/active-directory-saas-answerhub-tutorial/IC785174.png "Configure single sign-on")

## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]
Azure AD ユーザーが AnswerHub にログインできるようにするには、そのユーザーを AnswerHub にプロビジョニングする必要があります。  

* AnswerHub の場合、プロビジョニングは手動で行います。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. **AnswerHub** 企業サイトに管理者としてログインします。
2. [ **Administration**] に移動します。
3. **[Users & Groups]** タブをクリックします。
4. 左側のナビゲーション ウィンドウで、**[Manage Users]** セクションの **[Create or import users]** をクリックします。
   
   ![Users & Groups (ユーザーとグループ)](./media/active-directory-saas-answerhub-tutorial/IC785175.png "Users & Groups")
5. プロビジョニングする有効な Azure Active Directory ユーザー アカウントの**電子メール アドレス**、**ユーザー名**、**パスワード**を対応するボックスに入力し、**[Save]** をクリックします。

>[!NOTE]
>他の AnswerHub ユーザー アカウントの作成ツールまたは AnswerHub から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを AnswerHub に割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **AnswerHub** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-answerhub-tutorial/IC785176.png "Assign users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-answerhub-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


