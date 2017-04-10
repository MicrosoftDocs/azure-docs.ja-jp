---
title: "チュートリアル: Azure Active Directory と Citrix GoToMeeting の統合 | Microsoft Docs"
description: "Azure Active Directory で Citrix GoToMeeting を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 7d7897f6-b88e-4dd5-8f3a-e612337b1413
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 400793331aa2d56358a83a51ce64c67f59bbf3b7
ms.openlocfilehash: d81de30a46f7b849e70b99e7f7423cbc90f25326
ms.lasthandoff: 02/16/2017


---
# <a name="tutorial-azure-active-directory-integration-with-citrix-gotomeeting"></a>チュートリアル: Azure Active Directory と Citrix GoToMeeting の統合

このチュートリアルでは、Azure と Citrix GoToMeeting の統合について説明します。 このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Citrix GoToMeeting のテナント

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Citrix GoToMeeting のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![Configuration](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768996.png "Configuration")

## <a name="enabling-the-application-integration-for-citrix-gotomeeting"></a>Citrix GoToMeeting のアプリケーション統合の有効化
このセクションでは、Citrix GoToMeeting のアプリケーション統合を有効にする方法を説明します。

### <a name="to-enable-the-application-integration-for-citrix-gotomeeting-perform-the-following-steps"></a>Citrix GoToMeeting のアプリケーション統合を有効にするには、次の手順を実行します。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![Active Directory](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700993.png "Active Directory")

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700994.png "Applications")

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーションの追加](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749321.png "Add application")

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![Add an application from gallery](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749322.png "Add an application from gallery")

6. **検索ボックス**に「**Citrix GoToMeeting**」と入力します。
   
    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701006.png "Citrix GoToMeeting")

7. 結果ウィンドウで **[Citrix GoToMeeting]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701012.png "Citrix GoToMeeting")
   
## <a name="configuring-single-sign-on"></a>シングル サインオンの構成

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Citrix GoToMeeting に対する認証を行えるようにする方法を説明します。  
この手順の途中で、Base-64 でエンコードされた証明書を Citrix GoToMeeting テナントにアップロードする必要があります。  
この手順に慣れていない場合は、「 [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o)(バイナリ証明書をテキスト ファイルに変換する方法)」をご覧ください。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順を実行します。
1. **Citrix GoToMeeting** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして **[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオンの有効化](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768997.png "シングル サインオンの有効化")

2. **[ユーザーの Citrix GoToMeeting へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択します。
   
    ![シングル サインオンの構成](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768998.png "Configure single sign-on")

3. **[アプリ設定の構成]** ページで、**[次へ]** をクリックします。 
   
    ![シングル サインオンの有効化](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689981.png "シングル サインオンの有効化")

4. **[Citrix GoToMeeting でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、証明書ファイルをコンピューターに保存します。
   
    ![シングル サインオンの構成](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768999.png "Configure single sign-on")

5. 別のブラウザ ウィンドウで、[Citrix Organization Center](https://account.citrixonline.com/organization/administration/) にログインします。

6. **[ID プロバイダー]** タブをクリックしてから、次の手順を実行します。  
   
    ![SAML setup](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC6892321.png "SAML setup")
   
    a. **[Manual]** を選択します。

    b. Azure クラシック ポータルの **[Citrix GoToMeeting でのシングル サインオンの構成]** ダイアログ ページの **[サインイン ページの URL]** の値をコピーし、**[Sign-in page URL]** ボックスに貼り付けます。 

    c. Azure クラシック ポータルの **[Citrix GoToMeeting でのシングル サインオンの構成]** ダイアログ ページの **[サインアウト ページの URL]** の値をコピーし、**[Sign-out page URL]** ボックスに貼り付けます。

    d. Azure クラシック ポータルの **[Citrix GoToMeeting でのシングル サインオンの構成]** ダイアログ ページの **[エンティティ ID]** の値をコピーし、**[Identity Provider Entity ID]** ボックスに貼り付けます。

    e. **[証明書のアップロード]** をクリックして、ダウンロードした証明書をアップロードします。

    f. **[保存]**をクリックします。


1. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769000.png "Configure single sign-on")

2. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。
   
    ![SAML setup](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689982.png "SAML setup")

## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成
このセクションでは、Citrix GoToMeeting への Active Directory ユーザー アカウントのプロビジョニングを有効にする方法を説明します。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>ユーザー プロビジョニングを構成するには、次の手順に従います。
1. Azure クラシック ポータルの **Citrix GoToMeeting** アプリケーション統合ページで、**[ユーザー プロビジョニングの構成]** をクリックして **[ユーザー プロビジョニングの構成]** ダイアログを開きます。
   
    ![Configure user provisioning](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769001.png "Configure user provisioning")

2. **[設定と管理者資格情報]** ページで、次の手順を実行します。
   
    ![Configure user provisioning](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769002.png "Configure user provisioning")
   
    a. **[Citrix GoToMeeting 管理ユーザー名]** ボックスに、管理者のユーザー名を入力します。

    b. **[Citrix GoToMeeting 管理パスワード]** ボックスに、管理者のパスワードを入力します。

    c. **[次へ]**をクリックします。

1. **[確認]** ページで、チェックマークをクリックして構成を保存します。
2. **[検証]** ボタンをクリックして、構成を検証します。

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、ユーザーにアプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-citrix-gotomeeting-perform-the-following-steps"></a>ユーザーを Citrix GoToMeeting に割り当てるには、次の手順を実行します。
1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Citrix GoToMeeting** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769003.png "Assign users")

3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
    ![はい](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC767830.png "Yes")

10 分間待機し、アカウントが Dropbox for Business に同期されていることを確認します。

最初の検証手順として、Azure クラシック ポータルの **Citrix GoToMeeting** アプリケーション統合ページの D でダッシュボードをクリックして、プロビジョニングの状態を確認できます。

![Dashboard](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769004.png "Dashboard")

正常に完了したユーザー プロビジョニング サイクルは、関連する状態で示されます。

![Integration status](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769005.png "Integration status")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。

アクセス パネルの詳細については、 [アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)を参照してください。


