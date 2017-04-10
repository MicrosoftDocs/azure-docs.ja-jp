---
title: "チュートリアル: Azure Active Directory と Dropbox for Business の統合 | Microsoft Docs"
description: "Azure Active Directory で Dropbox for Business を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ac53d0a2c1c0a257e86fe74e10c8781aa6305975
ms.openlocfilehash: 80a7280787086a91ceeea308c79f894bdfabb725
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>チュートリアル: Azure Active Directory と Dropbox for Business の統合
このチュートリアルでは、Azure と Dropbox for Business の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Dropbox for Business のテスト テナント

このチュートリアルを完了すると、Dropbox for Business に割り当てた Azure AD ユーザーは、Dropbox for Business 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Dropbox for Business のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769508.png "Scenario")

## <a name="enabling-the-application-integration-for-dropbox-for-business"></a>Dropbox for Business のアプリケーション統合の有効化
このセクションでは、Dropbox for Business のアプリケーション統合を有効にする方法について説明します。

### <a name="to-enable-the-application-integration-for-dropbox-for-business-perform-the-following-steps"></a>Dropbox for Business のアプリケーション統合を有効にするには、次の手順を実行します。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![Active Directory](./media/active-directory-saas-dropboxforbusiness-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション](./media/active-directory-saas-dropboxforbusiness-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーションの追加](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**Dropbox for Business**」と入力します。
   
    ![アプリケーション ギャラリー](./media/active-directory-saas-dropboxforbusiness-tutorial/IC701010.png "Application Gallery")
7. 結果ウィンドウで **[Dropbox for Business]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Dropbox for Business](./media/active-directory-saas-dropboxforbusiness-tutorial/IC701011.png "Dropbox for Business")

## <a name="configuring-single-sign-on"></a>シングル サインオンの構成
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Dropbox for Business に対する認証を行うことができるようにする方法を説明します。

この手順の途中で、Base-64 でエンコードされた証明書を Dropbox for Business テナントにアップロードする必要があります。 この手順に慣れていない場合は、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順を実行します。
1. Azure クラシック ポータルの **Dropbox for Business** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749323.png "Configure single sign-on")
2. **[Dropbox for Business へのサインオン方法を選択してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749327.png "Configure single sign-on")
3. **[アプリケーション URL の構成]** ページで、次の手順を実行します。
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 Dropbox for Business テナントにサインオンします。 
   
    ![シングル サインオンの構成](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769509.png "Configure single sign-on")
   
    b. 左側のナビゲーション ウィンドウで、**[管理コンソール]** をクリックします。 
   
    ![シングル サインオンの構成](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769510.png "Configure single sign-on")
   
    c. **[管理コンソール]** の左側のナビゲーション ウィンドウで、**[認証]** をクリックします。 
   
    ![シングル サインオンの構成](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769511.png "Configure single sign-on")
   
    d. **[シングル サインオン]** セクションで **[シングル サインオンを有効にする]** を選択し、**[詳細]** をクリックしてこのセクションを展開します。  
   
    ![シングル サインオンの構成](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769512.png "Configure single sign-on")
   
    e. **[Users can sign in by entering their email address or they can go directly to (ユーザーは電子メール アドレスを入力してサインインすることも、次の URL に直接移動することもできます)]** の横の URL をコピーします。 
   
    ![シングル サインオンの構成](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769513.png "Configure single sign-on")
   
    f. Azure クラシック ポータルで、**[DropBox for Business サインイン URL]** ボックスに URL を貼り付けます。 
   
    ![シングル サインオンの構成](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769514.png "Configure single sign-on")  
4. **[Dropbox for Business でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、証明書ファイルをコンピューターに保存します。  
   
    ![シングル サインオンの構成](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769515.png "Configure single sign-on")
5. Dropbox for Business テナントの **[認証]** ページの **[シングル サインオン]** セクションで、次の手順を実行します。 
   
    ![シングル サインオンの構成](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769516.png "Configure single sign-on")
   
    a. **[必須項目です]** をクリックします。
   
    b. Azure クラシック ポータルで、**[Dropbox for Business でのシングル サインオンの構成]** ダイアログ ページの **[サインイン ページ URL]** の値をコピーし、**[サインイン URL]** ボックスに貼り付けます。

    c. ダウンロードした証明書から **Base-64 でエンコードされた** ファイルを作成します。 

    > [!TIP] 
    > 詳細については、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

    d. **[証明書の選択]** をクリックし、**Base-64 でエンコードされた証明書ファイル**を参照します。

    e. **[変更の保存]** をクリックして、DropBox for Business テナントでの構成を完了します。

1. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。 
   
    ![シングル サインオンの構成](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749329.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成
このセクションでは、Dropbox for Business への Active Directory ユーザー アカウントのユーザー プロビジョニングを有効にする方法を説明します。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>ユーザー プロビジョニングを構成するには、次の手順に従います。
1. Azure クラシック ポータルの **Dropbox for Business** アプリケーション統合ページで、**[ユーザー プロビジョニングの構成]** をクリックして、**[ユーザー プロビジョニングの構成]** ダイアログを開きます。
2. [DropBox for Business へのユーザー プロビジョニングを有効にする] ページで、[ユーザー プロビジョニングを有効にする] をクリックして、[Dropbox にサインインして Microsoft Azure AD とリンク] ダイアログを開きます。  
   
    ![ユーザー プロビジョニング](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769517.png "ユーザー プロビジョニング")
3. **[Sign in to Dropbox to link with Azure AD (Dropbox にサインインして Microsoft Azure AD とリンク)]** ダイアログで、Dropbox for Business テナントにサインインします。 
   
    ![ユーザー プロビジョニング](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769518.png "ユーザー プロビジョニング")
4. **[許可]** をクリックして、Azure AD に Dropbox へのアクセスを許可します。 
   
    ![ユーザー プロビジョニング](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769519.png "ユーザー プロビジョニング")
5. 構成を終了するには、 **[完了]** ボタンをクリックします。  
   
    ![ユーザー プロビジョニング](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769520.png "ユーザー プロビジョニング")

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-dropbox-for-business-perform-the-following-steps"></a>ユーザーを Dropbox for Business に割り当てるには、次の手順を実行します。
1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Dropbox for Business** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769521.png "Assign users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
    ![はい](./media/active-directory-saas-dropboxforbusiness-tutorial/IC767830.png "Yes")

10 分間待機し、アカウントが Dropbox for Business に同期されていることを確認します。

最初の検証手順として、Azure クラシック ポータルの **Dropbox for Business**アプリケーション統合ページで **[ダッシュボード]** をクリックして、プロビジョニングの状態を確認できます。

![ユーザーの割り当て](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769522.png "Assign users")

正常に完了したユーザー プロビジョニング サイクルは、関連する状態で示されます。

![ユーザーの割り当て](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769523.png "Assign users")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。
アクセス パネルの詳細については、「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)


