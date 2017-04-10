---
title: "チュートリアル: Azure Active Directory と Mimecast Personal Portal の統合 | Microsoft Docs"
description: "Azure Active Directory で Mimecast Personal Portal を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: a5b24d6897fbc6fdf71e4acc3898d640ec974f51
ms.openlocfilehash: 93544a2b907629c18e8abe6405e85d4623995bd7
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>チュートリアル: Azure Active Directory と Mimecast Personal Portal の統合
このチュートリアルでは、Azure と Mimecast Personal Portal の統合について説明します。 

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Mimecast Personal Portal でのシングル サインオン (SSO) が有効なサブスクリプション

このチュートリアルを完了すると、Mimecast Personal Portal に割り当てた Azure AD ユーザーは、Mimecast Personal Portal 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Mimecast Personal Portal のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794991.png "Scenario")

## <a name="enabling-the-application-integration-for-mimecast-personal-portal"></a>Mimecast Personal Portal のアプリケーション統合の有効化
このセクションでは、Mimecast Personal Portal のアプリケーション統合を有効にする方法を説明します。

**Mimecast Personal Portal のアプリケーション統合を有効にするには、次の手順に従います。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC749322.png "Add an application from gallerry")
6. **[検索ボックス]** に、**[Mimecast Personal Portal]** と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794992.png "Application Gallery")
7. 結果ウィンドウで **[Mimecast Personal Portal]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Mimecast Personal Portal](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794993.png "Mimecast Personal Portal")
   
## <a name="configuring-single-sign-on"></a>シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Mimecast Personal Portal に対する認証を行うことができるようにする方法を説明します。  

この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。  
この手順に慣れていない場合は、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **[Mimecast Personal Portal]** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794994.png "Configure Single Sign-On")
2. **[ユーザーの Mimecast Personal Portal へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794995.png "Configure Single Sign-On")
3. **[アプリ URL の構成]** ページで、**[Mimecast Personal Portal サインオン URL]** テキストボックスに、ユーザーが Mimecast Personal Portal アプリケーションへのサインオンする際に使用する URL (例: “https://webmail-uk.mimecast.com” や “https://webmail-us.mimecast.com”) を入力して、**[次へ]** をクリックします。
   
   >[!NOTE]
   >サインオン URL は、リージョン固有のものになります。 
   > 
   
   ![アプリケーション URL の構成](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794996.png "Configure App URL")
4. **[Mimecast Personal Portal でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルをコンピューターのローカルに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794997.png "Configure Single Sign-On")
5. 別の Web ブラウザー ウィンドウで、Mimecast Personal Portal の企業サイトに管理者としてログインします。
6. **[サービス]\>[アプリケーション]** に移動します。
   
   ![アプリケーション](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794998.png "Applications")
7. **[認証プロファイル]**をクリックします。
   
   ![Authentication Profiles](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794999.png "Authentication Profiles")
8. **[新しい認証プロファイル]**をクリックします。
   
   ![New Authentication Profil](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795000.png "New Authentication Profil")
9. [ **認証プロバイダー** ] セクションで、次の手順に従います。
   
   ![Authentication Profil](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795001.png "Authentication Profil")
   
   1. **[説明]** テキストボックスに、構成の名前を入力します。
   2. **[Mimecast Personal Portal に SAML 認証を適用]**を選択します。
   3. **[プロバイダー]** で **[Azure Active Directory]** を選択します。
   4. Azure クラシック ポータルの **[Mimecast Personal Portal でのシングル サインオンの構成]** ダイアログ ページで **[発行者 URL]** の値をコピーし、それを **[発行者 URL]** テキストボックスに貼り付けます。
   5. Azure クラシック ポータルの **[Mimecast Personal Portal でのシングル サインオンの構成]** ダイアログ ページで **[リモート ログイン URL]** の値をコピーし、それを **[ログイン URL]** テキストボックスに貼り付けます。
   6. Azure クラシック ポータルの **[Mimecast Personal Portal でのシングル サインオンの構成]** ダイアログ ページで **[リモート ログイン URL]** の値をコピーし、それを **[ログアウト URL]** テキストボックスに貼り付けます。  
      >[!NOTE]
      >Mimecast Personal Portal では、[ログイン URL] と [ログアウト URL] の値は同じです。
      > 
   7. ダウンロードした証明書から **base-64 でエンコードされた** ファイルを作成します。  
      
      >[!TIP]
      >詳細については、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。
      >  
   8. base-64 でエンコードされた証明書をメモ帳で開き、最初の行 (“*--*“) と最後の行 (“*--*“) を削除し、残りの内容をクリップボードにコピーして、**[ID プロバイダー証明書 (メタデータ)]** テキストボックスに貼り付けます。
   9. **[シングル サインオンの許可]**を選択します。
   10. **[Save]**をクリックします。
10. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
    
    ![シングル サインオンの構成](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795002.png "Configure Single Sign-On")
    
## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成

Azure AD ユーザーが Mimecast Personal Portal にログインできるようにするには、そのユーザーを Mimecast Personal Portal にプロビジョニングする必要があります。 Mimecast Personal Portal の場合、プロビジョニングは手動で行います。

* ユーザーを作成する前に、ドメインを登録する必要があります。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. 管理者として **[Mimecast Personal Portal]** にサインオンします。
2. **[ディレクトリ]\>[内部]**に移動します。
   
   ![Directories](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795003.png "Directories")
3. **[新規ドメインの登録]**をクリックします。
   
   ![Register New Domain](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795004.png "Register New Domain")
4. 新しいドメインを作成したら、 **[新規アドレス]**をクリックします。
   
   ![New Address](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795005.png "New Address")
5. 新しいアドレスのダイアログで、次の手順に従います。
   
   ![保存](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795006.png "保存")
   
   1. 該当するテキストボックスに、プロビジョニングする有効な AAD アカウントの **[メール アドレス]**、**[グローバル名]**、**[パスワード]**、**[パスワードの確認]** 属性を入力します。
   2. [ **Save**] をクリックします。

>[!NOTE]
>Mimecast Personal Portal から提供されている他の IdeaScale ユーザー アカウント作成ツールや API を使用して、AAD ユーザー アカウントをプロビジョニングできます。 
> 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを Mimecast Personal Portal に割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Mimecast Personal Portal** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795007.png "Assign Users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


