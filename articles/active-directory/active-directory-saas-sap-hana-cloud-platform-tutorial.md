---
title: "チュートリアル: Azure Active Directory と SAP Cloud Platform の統合 | Microsoft Docs"
description: "Azure Active Directory と SAP Cloud Platform の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 6b4b0e3ab126e70e4b27b971b15127aae0486bed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>チュートリアル: Azure Active Directory と SAP Cloud Platform の統合

このチュートリアルでは、SAP Cloud Platform と Azure Active Directory (Azure AD) を統合する方法について説明します。

SAP Cloud Platform と Azure AD の統合には、次の利点があります。

- SAP Cloud Platform にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に SAP Cloud Platform にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

SAP Cloud Platform と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- SAP Cloud Platform でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、SAP Cloud Platform に割り当てた Azure AD ユーザーは、「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。

>[!IMPORTANT]
>シングル サインオンをテストするには、独自のアプリケーションをデプロイするか、SAP Cloud Platform アカウントでアプリケーションをサブスクライブする必要があります。 このチュートリアルでは、アプリケーションはアカウントにデプロイされます。
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの SAP Cloud Platform の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>ギャラリーからの SAP Cloud Platform の追加
Azure AD への SAP Cloud Platform の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SAP Cloud Platform を追加する必要があります。

**ギャラリーから SAP Cloud Platform を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**SAP Cloud Platform**」と入力して、結果パネルで **[SAP Cloud Platform]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの SAP Cloud Platform](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、SAP Cloud Platform で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SAP Cloud Platform ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SAP Cloud Platform の関連ユーザーの間で、リンク関係が確立されている必要があります。

SAP Cloud Platform で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当てて、リンク関係を確立します。

SAP Cloud Platform で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[SAP Cloud Platform テスト ユーザーの作成](#create-a-sap-cloud-platform-test-user)** - SAP Cloud Platform で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、SAP Cloud Platform アプリケーションでシングル サインオンを構成します。

**SAP Cloud Platform で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **SAP Cloud Platform** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_samlbase.png)

3. **[SAP Cloud Platform のドメインと URL]** セクションで、次の手順を実行します。

    ![[SAP Cloud Platform のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_url.png)

    a. **[サインオン URL]** ボックスに、ユーザーが **SAP Cloud Platform** アプリケーションへのサインオンに使用する URL を入力します。 これは、SAP Cloud Platform アプリケーションで保護されたリソースのアカウント固有の URL です。 URL は次のパターンに基づいています。`https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
     >[!NOTE]
     >これは、ユーザーが認証を必要とする SAP Cloud Platform アプリケーションの URL です。
     > 

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    b. **[識別子]** ボックスに、次のいずれかの形式で SAP Cloud Platform の URL を入力します。 

    | |
    |--|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    c. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>` |

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 サインオン URL と識別子を取得するには、[SAP Cloud Platform クライアント サポート チーム](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html)に問い合わせてください。 応答 URL については、チュートリアルの後半で説明されている信頼管理セクションから入手できます。
    > 
     
4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_400.png)

6. 別の Web ブラウザー ウィンドウで、SAP Cloud Platform コックピット (`https://account.<landscape host>.ondemand.com/cockpit`) にサインインします (例: https://account.hanatrial.ondemand.com/cockpit)。

7. **[Trust (信頼)]** タブをクリックします。
   
    ![Trust](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790800.png "Trust")

8. 信頼管理セクションの **[Local Service Provider]\(ローカル サービス プロバイダー\)** で、次の手順に従います。

    ![Trust Management](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793931.png "Trust Management")
   
    a. **[編集]**をクリックします。

    b. **[構成の種類]** として **[カスタム]** を選択します。

    c. **[Local Provider Name (ローカル プロバイダー名)]**は既定値のままにします。 この値をコピーして、SAP Cloud Platform 用の Azure AD 構成の **[識別子]** フィールドに貼り付けます。

    d. **署名キー**と**署名証明書**キーのペアを生成するには、**[Generate Key Pair (キー ペアの生成)]** をクリックします。

    e. **[Principal Propagation (プリンシパル伝達)]** で **[無効]** を選択します。

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[Force Authentication (強制認証)]** で **[無効]** を選択します。

    g. [ **Save**] をクリックします。

9. **[Local Service Provider]\(ローカル サービス プロバイダー\)** の設定を保存したら、次を実行して応答 URL を取得します。
   
    ![Get Metadata](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793930.png "Get Metadata")

    a. **[Get Metadata]\(メタデータの取得\)** をクリックして、SAP Cloud Platform メタデータ ファイルをダウンロードします。

    b. ダウンロードした SAP Cloud Platform のメタデータ XML ファイルを開き、**ns3:AssertionConsumerService** タグを見つけます。
 
    c. **Location** 属性の値をコピーして、SAP Cloud Platform 用の Azure AD 構成の **[応答 URL]** フィールドに貼り付けます。

10. **[信頼できる ID プロバイダー]** タブをクリックし、**[Add Trusted Identity Provider (信頼できる ID プロバイダーの追加)]** をクリックします。
   
    ![Trust Management](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790802.png "Trust Management")
   
    >[!NOTE]
    >信頼できる ID プロバイダーの一覧を管理するには、ローカル サービス プロバイダーのセクションでカスタム構成タイプを選んでおく必要があります。 既定の構成タイプでは、SAP ID サービスに対する編集不可能で暗黙的な信頼があります。 [なし] では、いずれの信頼設定もありません。
    > 
    > 

11. **[全般]** タブをクリックし、**[参照]** をクリックして、ダウンロードしたメタデータ ファイルをアップロードします。
    
    ![Trust Management](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793932.png "Trust Management")
    
    >[!NOTE]
    >メタデータ ファイルをアップロードすると、**[シングル サインオン URL]**、**[Single Logout URL]\(シングル ログアウト URL\)**、**[署名証明書]** の値が自動的に設定されます。
    > 
     
12. **[属性]** タブをクリックします。

13. **[属性]** タブで、次の手順に従います。
    
    ![属性](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790804.png "Attributes") 

    a. **[Add Assertion-Based Attribute (アサーション ベースの属性の追加)]**をクリックして、次のアサーション ベースの属性を追加します。
       
    | アサーション属性 | プリンシパル属性 |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |firstname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |lastname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |email |
   
     >[!NOTE]
     >属性の構成は、SCP 上のアプリケーションがどのように作成されているかに依存します。つまり、SAML 応答で必要になる属性の種類や、コード内でこの属性にアクセスする際に使用される名前 (プリンシパル属性) によって異なります。
     > 
    
    b. スクリーンショットの **[既定の属性]** に指定されている値はサンプルです。 このシナリオでは必要ありません。  
 
    c. スクリーンショットに表示されている **[プリンシパル属性]** の名前と値は、アプリケーションの開発方法によって異なります。 使用するアプリケーションによって、異なるマッピングが必要になる場合があります。

###<a name="assertion-based-groups"></a>アサーション ベースのグループ

オプションの手順として、Azure Active Directory ID プロバイダーのアサーション ベースのグループを構成できます。

SAP Cloud Platform でグループを使用すると、SAP Cloud Platform アプリケーションで、1 つ以上のロールに 1 人以上のユーザーを動的に割り当てることができます。この割り当ては SAML 2.0 アサーションの属性値によって決定されます。 

たとえば、アサーションに属性 "*contract=temporary*" が含まれている場合、対象となるすべてのユーザーが "*TEMPORARY*" グループに追加されます。 "*TEMPORARY*" グループには、SAP Cloud Platform アカウントにデプロイされた 1 つ以上のアプリケーションの 1 つ以上のロールが含まれます。
 
SAP Cloud Platform アカウントでアプリケーションの 1 つ以上のロールに多くのユーザーを同時に割り当てる場合は、アサーション ベースのグループを使用します。 1 人または少数のユーザーのみを特定のロールに割り当てる場合は、SAP Cloud Platform コックピットの **[承認]** タブで直接割り当てることをお勧めします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-sap-cloud-platform-test-user"></a>SAP Cloud Platform テスト ユーザーの作成

Azure AD ユーザーが SAP Cloud Platform にログインできるようにするには、SAP Cloud Platform のロールをそのユーザーに割り当てる必要があります。

**ロールをユーザーに割り当てるには、次の手順を実行します。**

1. **SAP Cloud Platform** コックピットにログインします。

2. 次の手順を実行します。
   
    ![Authorizations](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790805.png "Authorizations")
   
    a. **[Authorization]**をクリックします。

    b. **[Users]** タブをクリックします。

    c. **[User]** テキストボックスに、ユーザーのメール アドレスを入力します。

    d. **[Assign]** をクリックしてユーザーをロールに割り当てます。

    e. [ **Save**] をクリックします。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SAP Cloud Platform へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**SAP Cloud Platform に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[SAP Cloud Platform]** を選択します。

    ![アプリケーションの一覧の [SAP Cloud Platform] リンク](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで [SAP Cloud Platform] タイルをクリックすると、自動的に SAP Cloud Platform アプリケーションにサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_203.png

