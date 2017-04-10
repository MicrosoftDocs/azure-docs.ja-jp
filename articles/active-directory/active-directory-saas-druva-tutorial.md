---
title: "チュートリアル: Azure Active Directory と Druva の統合 | Microsoft Docs"
description: "Azure Active Directory で Druva を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 325d92e493f6e011367d2c85b52c92838327101e
ms.openlocfilehash: 0c24b01924d5fcec0d4600e044e679cfc295815f
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-integration-with-druva"></a>チュートリアル: Azure Active Directory と Druva の統合
このチュートリアルでは、Azure と Druva の統合について説明します。  

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Druva でのシングル サインオン (SSO) が有効なサブスクリプション

このチュートリアルを完了すると、Druva に割り当てた Azure AD ユーザーは、Druva 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

* Druva のアプリケーション統合の有効化
* シングル サインオンの構成
* ユーザー プロビジョニングの構成
* ユーザーの割り当て

![シナリオ](./media/active-directory-saas-druva-tutorial/IC795084.png "Scenario")

## <a name="enable-the-application-integration-for-druva"></a>Druva のアプリケーション統合の有効化
このセクションでは、Druva のアプリケーション統合を有効にする方法について説明します。

**Druva のアプリケーション統合を有効にするには、次の手順を実行します。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-druva-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-druva-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-druva-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-druva-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**Druva**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-druva-tutorial/IC795085.png "Application Gallery")
7. 結果ウィンドウで **[Druva]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Druva](./media/active-directory-saas-druva-tutorial/IC795086.png "Druva")
   
## <a name="configure-single-sign-on"></a>Configure single sign-on

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Druva に対する認証を行うことができるようにする方法を説明します。  

この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。 この手順に慣れていない場合は、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

Druva アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを **SAML トークン属性** の構成に追加する必要があります。 

次のスクリーンショットはその例です。

![SAML Token Attributes](./media/active-directory-saas-druva-tutorial/IC795087.png "SAML Token Attributes")

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Druva** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-druva-tutorial/IC795027.png "Configure Single Sign-On")
2. **[ユーザーの Druva へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-druva-tutorial/IC795088.png "Configure Single Sign-On")
3. **[アプリケーション URL の構成]** ページの **[Druva のサインオン URL]** ボックスに、ユーザーが Druva アプリケーションにサインオンする際に使用する URL (例: "*https://cloud.druva.com/home/*”) を入力し、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-druva-tutorial/IC795089.png "Configure App URL")
4. **[Druva でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルをコンピューターのローカルに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-druva-tutorial/IC795090.png "Configure Single Sign-On")
5. 別の Web ブラウザー ウィンドウで、Druva 企業サイトに管理者としてログインします。
6. **[管理]\>[設定]** の順にクリックします。
   
   ![設定](./media/active-directory-saas-druva-tutorial/IC795091.png "Settings")
7. [シングル サインオンの設定] ダイアログで、次の手順を実行します。
   
   ![Singl Sign-On Settings](./media/active-directory-saas-druva-tutorial/IC795092.png "Singl Sign-On Settings")   
   1. Azure クラシック ポータルの **[Druva でのシングル サインオンの構成]** ダイアログ ページで、**[リモート ログイン URL]** の値をコピーし、**[ID プロバイダーのログイン URL]** ボックスに貼り付けます。
   2. Azure クラシック ポータルの **[Druva でのシングル サインオンの構成]** ダイアログ ページで、**[リモート ログアウト URL]** の値をコピーし、**[ID プロバイダーのログアウト URL]** ボックスに貼り付けます。
   3. ダウンロードした証明書から **base-64 でエンコードされた** ファイルを作成します。  
     >[!TIP]
     >詳細については、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)
     > 
   4. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、 **[ID プロバイダー証明書]** ボックスに貼り付けます。
   5. **[設定]** ページを開くには、**[保存]** をクリックします。
8. **[設定]** ページで、**[SSO トークンの生成]** をクリックします。
   
   ![設定](./media/active-directory-saas-druva-tutorial/IC795093.png "Settings")
9. **[シングル サインオン認証トークン]** ダイアログで、次の手順を実行します。
   
   ![SSO Token](./media/active-directory-saas-druva-tutorial/IC795094.png "SSO Token")
   1. **[コピー]**をクリックします。
   2. **[閉じる]**をクリックします。
10. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
    
   ![シングル サインオンの構成](./media/active-directory-saas-druva-tutorial/IC795095.png "Configure Single Sign-On")
11. 上部のメニューで、 **属性** to open the **SAML Token 属性** ダイアログを開きます。
    
    ![属性](./media/active-directory-saas-druva-tutorial/IC795096.png "Attributes")
12. 必要な属性のマッピングを追加するには、次の手順を実行します。
    
   | 属性名 | 属性値 |
   | --- | --- |
   | insync\_auth\_token |<*クリップボードの値*> |
    
   1. 上記の表の各データ行で、 **[ユーザー属性の追加]**をクリックします。
   2. **[属性名]** ボックスに、その行に対して表示される属性名を入力します。
   3. **[属性値]** ボックスに、その行に対して表示される属性値を入力します。
   4. **[完了]**をクリックします。
13. **[変更の適用]**をクリックします。
    
## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]

Azure AD ユーザーが Druva にログインできるようにするには、そのユーザーを Druva にプロビジョニングする必要があります。 

* Druva の場合、プロビジョニングは手動で行います。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. **Druva** 企業サイトに管理者としてログインします。
2. **[管理]\>[ユーザー]** の順に移動します。
   
   ![Manage Users](./media/active-directory-saas-druva-tutorial/IC795097.png "Manage Users")
3. **[Create New]**をクリックします。
   
   ![Manage Users](./media/active-directory-saas-druva-tutorial/IC795098.png "Manage Users")
4. [新しいユーザーの作成] ダイアログで、次の手順を実行します。
   
   ![Create NewUser](./media/active-directory-saas-druva-tutorial/IC795099.png "Create NewUser")
   
   1. 関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory ユーザー アカウントの電子メール アドレスと名前を入力します。
   2. **[ユーザーの作成]**をクリックします。

>[!NOTE]
>Druva から提供されている他の Druva ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 
> 

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを Druva に割り当てるには、次の手順を実行します。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Druva** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-druva-tutorial/IC795100.png "Assign Users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-druva-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


