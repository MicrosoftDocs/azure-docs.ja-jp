---
title: "チュートリアル: Azure Active Directory と SAP HANA Cloud Platform の統合 | Microsoft Docs"
description: "Azure Active Directory で SAP HANA Cloud Platform を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 9a653ac435198e89a527070a0174a1adaf830dc3
ms.openlocfilehash: c58d2c68b6b95d7020e03333e36b281ad7d0f9f9
ms.lasthandoff: 01/05/2017


---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana-cloud-platform"></a>チュートリアル: Azure Active Directory と SAP HANA Cloud Platform の統合
このチュートリアルでは、Azure と SAP HANA Cloud Platform の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* SAP HANA Cloud Platform アカウント

このチュートリアルを完了すると、SAP HANA Cloud Platform に割り当てた Azure AD ユーザーは、「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。

> [!IMPORTANT]
> シングル サインオンをテストするには、独自のアプリケーションをデプロイするか、SAP HANA Cloud Platform アカウントでアプリケーションをサブスクライブする必要があります。 このチュートリアルでは、アプリケーションはアカウントにデプロイされます。
> 
> 

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. SAP HANA Cloud Platform でアプリケーション統合を有効にする
2. シングル サインオンの構成
3. ユーザーにロールを割り当てる
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790795.png "Scenario")

## <a name="enabling-the-application-integration-for-sap-hana-cloud-platform"></a>SAP HANA Cloud Platform でアプリケーション統合を有効にする
このセクションでは、SAP HANA Cloud Platform のアプリケーション統合を有効にする方法について説明します。

### <a name="to-enable-the-application-integration-for-sap-hana-cloud-platform-perform-the-following-steps"></a>SAP HANA Cloud Platform のアプリケーション統合を有効にするには、次の手順に従います。
1. Azure 管理ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![Active Directory](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700993.png "Active Directory")

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700994.png "Applications")

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーションの追加](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749321.png "Add application")

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749322.png "Add an application from gallerry")

6. **検索ボックス**に、「**SAP HANA Cloud Platform**」と入力します。
   
    ![アプリケーション ギャラリー](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790796.png "Application Gallery")

7. 結果ウィンドウで **[SAP HANA Cloud Platform]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![SAP Hana](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793929.png "SAP Hana")
   
## <a name="configuring-single-sign-on"></a>シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで SAP HANA Cloud Platform に対する認証を行えるようにする方法を説明します。  
この手順の途中で、base-64 でエンコードされた証明書を SAP HANA Cloud Platform テナントにアップロードする必要があります。  
この手順に慣れていない場合は、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順を実行します。
1. Azure クラシック ポータルの **SAP HANA Cloud Platform** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC778552.png "Configure single sign-on")

2. **[ユーザーの SAP HANA Cloud Platform へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790797.png "Configure Single Sign-On")

3. 別の Web ブラウザー ウィンドウで、SAP HANA Cloud Platform コックピット (https://account.\<landscape host\>.ondemand.com/cockpit) にサインインします (例: *https://account.hanatrial.ondemand.com/cockpit*)。

4. **[Trust (信頼)]** タブをクリックします。
   
    ![Trust](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790800.png "Trust")

5. 信頼管理セクションで、次の手順に従います。
   
    ![Get Metadata](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793930.png "Get Metadata")
   
    1. **[Local Service Provider (ローカル サービス プロバイダー)]** タブをクリックします。
    2. SAP HANA クラウド プラットフォームのメタデータ ファイルをダウンロードするには、 **[Get Metadata (メタデータの取得)]**をクリックします。

6. Azure Active クラシック ポータルの **[アプリケーション URL の構成]** ページで、次の手順を実行してから、**[次へ]** をクリックします。
   
    ![アプリケーション URL の構成](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790798.png "Configure App URL")
   
    a. **[サインオン URL]** ボックスに、ユーザーが **SAP HANA Cloud Platform** アプリケーションへのサインオンに使用する URL を入力します。 これは、SAP HANA Cloud Platform アプリケーションで保護されたリソースのアカウント固有の URL です。 この URL は、*https://\<アプリケーション名\>\<アカウント名\>.\<ランドスケープ ホスト\>.ondemand.com/\<path\_to\_protected\_resource\>* のパターンに基づいています (例: *https://xleavep1941203872trial.hanatrial.ondemand.com/xleave*)。
      
    > [!NOTE]
    > これは、ユーザーが認証を必要とする SAP HANA Cloud Platform アプリケーションの URL です。
    > 
    > 

    b. ダウンロードした SAP HANA Cloud Platform のメタデータ ファイルを開き、 **ns3:AssertionConsumerService** タグを見つけます。

    c. **Location** 属性の値をコピーして、**[SAP HANA Cloud Platform 応答 URL]** ボックスに貼り付けます。

7. **[SAP HANA Cloud Platform でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックしてメタデータをダウンロードし、コンピューターにファイルを保存します。
   
    ![シングル サインオンの構成](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790799.png "Configure Single Sign-On")

8. SAP HANA Cloud Platform コックピットの **[Local Service Provider (ローカル サービス プロバイダー)]** セクションで、次の手順に従います。
   
    ![Trust Management](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793931.png "Trust Management")
   
    1. **[編集]**をクリックします。

    2. **[構成の種類]** として **[カスタム]** を選択します。

    3. **[Local Provider Name (ローカル プロバイダー名)]**は既定値のままにします。

    4. **署名キー**と**署名証明書**キーのペアを生成するには、**[Generate Key Pair (キー ペアの生成)]** をクリックします。

    5. **[Principal Propagation (プリンシパル伝達)]** で **[無効]** を選択します。

    6. **[Force Authentication (強制認証)]** で **[無効]** を選択します。

    7. [ **Save**] をクリックします。

9. **[信頼できる ID プロバイダー]** タブをクリックし、**[Add Trusted Identity Provider (信頼できる ID プロバイダーの追加)]** をクリックします。
   
    ![Trust Management](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790802.png "Trust Management")
   
    > [!NOTE]
    > 信頼できる ID プロバイダーの一覧を管理するには、ローカル サービス プロバイダーのセクションでカスタム構成タイプを選んでおく必要があります。 既定の構成タイプでは、SAP ID サービスに対する編集不可能で暗黙的な信頼があります。 [なし] では、いずれの信頼設定もありません。
    > 
    > 

10. **[全般]** タブをクリックし、**[参照]** をクリックして、ダウンロードしたメタデータ ファイルをアップロードします。
    
    ![Trust Management](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793932.png "Trust Management")
    
    > [!NOTE]
    > メタデータ ファイルをアップロードすると、**[シングル サインオン URL]**、**[Single Logout URL (シングル ログアウト URL)]**、**[署名証明書]** の値が自動的に設定されます。
    > 
    > 

11. **[属性]** タブをクリックします。

12. **[属性]** タブで、次の手順に従います。
    
    ![属性](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790804.png "Attributes")
    
    - **[Add Assertion-Based Attribute (アサーション ベースの属性の追加)]**をクリックして、次のアサーション ベースの属性を追加します。
       
    | アサーション属性 | プリンシパル属性 |
    | --- | --- |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname |firstname |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname |Lastname |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress |電子メール |
    
    > [!NOTE]
    > 属性の構成は、HCP 上のアプリケーションがどのように作成されているかに依存します。つまり、SAML 応答で必要になる属性の種類や、コード内でこの属性にアクセスする際に使用される名前 (プリンシパル属性) によって異なります。
    > 
    > a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。  スクリーンショットの **[既定の属性]** に指定されている値はサンプルです。 このシナリオでは必要ありません。  
    > 
    > b.  スクリーンショットに表示されている **[プリンシパル属性]** の名前と値は、アプリケーションの開発方法によって異なります。 使用するアプリケーションによって、異なるマッピングが必要になる場合があります。
    > 
    > 

13. Azure クラシック ポータルの **[SAP HANA Cloud Platform でのシングル サインオンの構成]** ダイアログ ページで、シングル サインオンの構成確認を選択し、**[完了]** をクリックします。
    
    ![シングル サインオンの構成](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC796933.png "Configure Single Sign-On")


オプションの手順として、Azure Active Directory ID プロバイダーのアサーション ベースのグループを構成できます。

> [!NOTE]
> SAP HANA Cloud Platform でグループを使用すると、SAP HANA Cloud Platform アプリケーションで、1 つ以上のロールに 1 人以上のユーザーを動的に割り当てることができます。この割り当ては SAML 2.0 アサーションの属性値によって決定されます。 たとえば、アサーションに属性 "*contract=temporary*" が含まれている場合、対象となるすべてのユーザーが "*TEMPORARY*" グループに追加されます。 "*TEMPORARY*" グループには、SAP HANA Cloud Platform アカウントにデプロイされた&1; つ以上のアプリケーションの&1; つ以上のロールが含まれます。
> 
> SAP HANA Cloud Platform アカウントでアプリケーションの&1; つ以上のロールに多くのユーザーを一括で割り当てる場合は、アサーション ベースのグループを使用します。 1 人または少数のユーザーを特定のロールに割り当てるだけの場合は、SAP HANA Cloud Platform コックピットの**[Authorizations]**タブで直接割り当てることをお勧めします。
> 
> 

## <a name="assigning-a-role-to-a-user"></a>ユーザーにロールを割り当てる
Azure AD ユーザーが SAP HANA Cloud Platform にログインできるようにするには、SAP HANA Cloud Platform のロールをそれらのユーザーに割り当てる必要があります。

### <a name="to-assign-a-role-to-a-user-perform-the-following-steps"></a>ロールをユーザーに割り当てるには、次の手順に従います。
1. **SAP HANA Cloud Platform** コックピットにログインします。

2. 次の手順に従います。
   
   ![Authorizations](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790805.png "Authorizations")
   
   1. **[Authorization]**をクリックします。
   2. **[Users]** タブをクリックします。
   3. **[User]** テキストボックスに、ユーザーのメール アドレスを入力します。
   4. **[Assign]** をクリックしてユーザーをロールに割り当てます。
   5. [ **Save**] をクリックします。

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-sap-hana-cloud-platform-perform-the-following-steps"></a>ユーザーを SAP HANA Cloud Platform に割り当てるには、次の手順に従います。
1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **SAP HANA Cloud Platform** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790806.png "Assign Users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


