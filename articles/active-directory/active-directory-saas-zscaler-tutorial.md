---
title: "チュートリアル: Azure Active Directory と Zscaler の統合 | Microsoft Docs"
description: "Azure Active Directory で Zscaler を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 68c453f7-aff1-4614-92d3-9b86f3ad99dc
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: f9e6f640b97a35aab84063f17a155a32f809d374
ms.openlocfilehash: 835f2e0b636af17d2c2c032cda572b00a4fdbef7
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-zscaler"></a>チュートリアル: Azure Active Directory と Zscaler の統合
このチュートリアルでは、Azure と Zscaler の統合について説明します。 このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Zscaler のテナント

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Zscaler のアプリケーション統合の有効化
2. シングル サインオンの構成
3. プロキシ設定の構成
4. ユーザー プロビジョニングの構成
5. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-zscaler-tutorial/IC769226.png "Scenario")

## <a name="enabling-the-application-integration-for-zscaler"></a>Zscaler のアプリケーション統合の有効化
このセクションでは、Zscaler のアプリケーション統合を有効にする方法を説明します。

### <a name="to-enable-the-application-integration-for-zscaler-perform-the-following-steps"></a>Zscaler のアプリケーション統合を有効にするには、次の手順を実行します。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![Active Directory](./media/active-directory-saas-zscaler-tutorial/IC700993.png "Active Directory")

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション](./media/active-directory-saas-zscaler-tutorial/IC700994.png "Applications")

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーションの追加](./media/active-directory-saas-zscaler-tutorial/IC749321.png "Add application")

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-zscaler-tutorial/IC749322.png "Add an application from gallerry")

6. **検索ボックス**に、「**Zscaler**」と入力します。
   
    ![アプリケーション ギャラリー](./media/active-directory-saas-zscaler-tutorial/IC769227.png "Application Gallery")

7. 結果ウィンドウで **[Zscaler]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Zscaler](./media/active-directory-saas-zscaler-tutorial/IC769228.png "Zscaler")

## <a name="configuring-single-sign-on"></a>シングル サインオンの構成
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Zscaler に対する認証を行うことができるようにする方法を説明します。  
この手順の途中で、証明書を Zscaler にアップロードする必要があります。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順を実行します。
1. Azure クラシック ポータルの **Zscaler** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオンの有効化](./media/active-directory-saas-zscaler-tutorial/IC769229.png "シングル サインオンの有効化")

2. **[ユーザーの Zscaler へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-zscaler-tutorial/IC769230.png "Configure single sign on")

3. **[アプリの URL の構成]** ページの **[Zscaler サインイン URL]** ボックスに、Zscaler のテナント サインイン URL を入力し、**[次へ]** をクリックします。 
   
    > [!NOTE]
    > サインインの URL がわからない場合は、Zscaler サポート チームに問い合わせてください。
    > 
    > 
   
    ![アプリケーション URL の構成](./media/active-directory-saas-zscaler-tutorial/IC769231.png "Configure app URL")

4. **[Zscaler でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![シングル サインオンの構成](./media/active-directory-saas-zscaler-tutorial/IC769232.png "Configure single sign-on")
   
    1. **[証明書のダウンロード]** をクリックし、証明書ファイルを **c:\\Zscaler.cer** としてローカルに保存します。
    2. **[認証要求 URL]** をクリップボードにコピーします。

5. Zscaler テナントにログインします。

6. 上部のメニューで **[管理]**をクリックします。
   
    ![Administration](./media/active-directory-saas-zscaler-tutorial/IC769486.png "Administration")

7. **[管理者とロールの管理]** をクリックし、**[ユーザーと認証の管理]** をクリックします。
   
    ![管理者とロールの管理](./media/active-directory-saas-zscaler-tutorial/IC769487.png "Manage Administrators & Roles")

8. **[組織の認証オプションの選択]** セクションで、次の手順を実行します。
   
    ![認証オプションの選択](./media/active-directory-saas-zscaler-tutorial/IC769488.png "Choose Authentication Options")
   
    1. **[SAML シングル サインオンを使用した認証]**を選択します。
    2. **[SAML シングル サインオン パラメーターの構成]**をクリックします。

9. **[SAML シングル サインオン パラメーターの構成]** ダイアログ ページで、次の手順に従い、**[完了]** をクリックします。
   
    ![証明書のアップロード](./media/active-directory-saas-zscaler-tutorial/IC769489.png "Upload certificate")
   
    1. **[URL of the SAML Portal to which users are sent for authentication (認証のためにユーザーに表示される SAML ポータルの URL)]** ボックスに、Azure クラシック ポータルの **[認証要求 URL]** フィールドの値を貼り付けます。
   
    2. **[ログイン名を含む属性]** ボックスに「**NameID**」と入力します。
   
    3. **[Upload SSL Public Certificate (SSL パブリック証明書のアップロード)]** フィールドで、Azure クラシック ポータルからダウンロードした証明書をアップロードします。
   
    4. **[SAML 自動プロビジョニングを有効にする]**を選択します。

10. **[ユーザー認証の構成]** ダイアログ ページで、次の手順に従います。
    
    ![ユーザー認証の構成](./media/active-directory-saas-zscaler-tutorial/IC769490.png "Configure User Authentication")
    
    1. **[保存]**をクリックします。
    2. **[今すぐ認証する]**をクリックします。

11. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
    
    ![シングル サインオンの構成](./media/active-directory-saas-zscaler-tutorial/IC769491.png "Configure single sign-on")


## <a name="configuring-proxy-settings"></a>プロキシ設定の構成
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Internet Explorer でプロキシ設定を構成するには

1. **Internet Explorer**を開始します。

2. **[ツール]** メニューの **[インターネット オプション]** を選択し、**[インターネット オプション]** ダイアログを開きます。
   
    ![インターネット オプション](./media/active-directory-saas-zscaler-tutorial/IC769492.png "Internet Options")

3. **[接続]** タブをクリックします。
   
    ![接続](./media/active-directory-saas-zscaler-tutorial/IC769493.png "Connections")

4. **[LAN の設定]** をクリックして **[LAN の設定]** ダイアログを開きます。

5. [プロキシ サーバー] セクションで、次の手順を実行します。
   
    ![プロキシ サーバー](./media/active-directory-saas-zscaler-tutorial/IC769494.png "Proxy server")
   
    1. [LAN にプロキシ サーバーを使用する] をオンにします。

    2. [アドレス] ボックスに「 **gateway.zscalertwo.net**」と入力します。
   
    3. [ポート] ボックスに「 **80**」と入力します。
   
    4. **[ローカル アドレスにはプロキシ サーバーを使用しない]**を選択します。
   
    5. **[OK]** をクリックして **[ローカル エリア ネットワーク (LAN) の設定]** ダイアログを閉じます。

6. **[OK]** をクリックして **[インターネット オプション]** ダイアログを閉じます。

## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成
Azure AD ユーザーが Zscaler にログインできるようにするには、そのユーザーを Zscaler にプロビジョニングする必要があります。  
Zscaler の場合、プロビジョニングは手動で行います。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>ユーザー プロビジョニングを構成するには、次の手順に従います。

1. **Zscaler** テナントにログインします。

2. **[管理]**をクリックします。

    ![Administration](./media/active-directory-saas-zscaler-tutorial/IC781035.png "Administration")

3. **[ユーザー管理]**をクリックします。
   
    ![ユーザー管理](./media/active-directory-saas-zscaler-tutorial/IC781036.png "User Management")

4. **[ユーザー]** タブで、**[追加]** をクリックします。
   
    ![Add](./media/active-directory-saas-zscaler-tutorial/IC781037.png "Add")

5. [ユーザーの追加] セクションで、次の手順を実行します。
   
    ![ユーザーの追加](./media/active-directory-saas-zscaler-tutorial/IC781038.png "Add User")
   
    1. プロビジョニングする有効な AAD アカウントの **[ユーザー ID]**、**[ユーザー表示名]**、**[パスワード]**、**[パスワードの確認]** に入力し、**[グループ]** と **[部署]** を選択します。
   
    2. **[Save]**をクリックします。

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-zscaler-perform-the-following-steps"></a>ユーザーを Zscaler に割り当てるには、次の手順を実行します。

1. Azure クラシック ポータルで、テスト アカウントを作成します。

2. **Zscaler** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-zscaler-tutorial/IC769495.png "Assign users")

3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
    ![はい](./media/active-directory-saas-zscaler-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


