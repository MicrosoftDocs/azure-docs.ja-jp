---
title: "チュートリアル: Azure Active Directory と SAP HANA の統合 | Microsoft Docs"
description: "Azure Active Directory と SAP HANA の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: a7e73f6ee763d1005ad85935cf2d8f6b24ecf116
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>チュートリアル: Azure Active Directory と SAP HANA の統合

このチュートリアルでは、SAP HANA と Azure Active Directory (Azure AD) を統合する方法について説明します。

SAP HANA と Azure AD の統合には、次の利点があります。

- SAP HANA にアクセスするユーザーを Azure AD で管理できます
- ユーザーが自分の Azure AD アカウントで自動的に SAP HANA にサインオン (シングル サインオン) できるように、設定が可能です
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

SAP HANA と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- SAP HANA でのシングル サインオンが有効なサブスクリプション
- 任意のパブリック IaaS、オンプレミス、Azure VM、または Azure 内の SAP Large Instances で実行している HANA インスタンス
- HANA インスタンスにインストールされた XSA 管理 Web インターフェイスと HANA Studio。

> [!NOTE]
> このチュートリアルの手順をテストする場合、SAP HANA の運用環境を使わないことをお勧めします。 最初にアプリケーションの開発環境またはステージング環境で統合をテストし、そのあとに運用環境を使用してください。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの SAP HANA の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-sap-hana-from-the-gallery"></a>ギャラリーからの SAP HANA の追加
Azure AD への SAP HANA の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SAP HANA を追加する必要があります。

**ギャラリーから SAP HANA を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**SAP HANA**」と入力し、結果ウィンドウで **SAP HANA** を選び、**[追加]** をクリックして、アプリケーションを追加します。 

    ![新規アプリケーション](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、SAP HANA で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SAP HANA ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SAP HANA の関連ユーザーの間で、リンク関係が確立されている必要があります。

SAP HANA で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

SAP HANA で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[SAP HANA テスト ユーザーの作成](#creating-a-sap-hana-test-user)** - SAP HANA で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、SAP HANA アプリケーションでシングル サインオンを構成します。

**SAP HANA で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **SAP HANA** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_samlbase.png)

3. **[SAP HANA のドメインと URL]** セクションで、次の手順に従います。

    ![[SAP HANA のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_url.png)

    a. **[識別子]** ボックスに、「`HA100`」と入力します。 

    b. **[応答 URL]** ボックスに、`https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[SAP HANA クライアント サポート チーム](https://cloudplatform.sap.com/contact.html)に問い合わせてください。 

4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_certificate.png) 

    >[!Note]
    >証明書がアクティブでない場合、Azure AD で [新しい証明書をアクティブにする] チェック ボックスをオンにしてアクティブにします。 

5. SAP HANA アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 次のスクリーンショットはその例です。 ここでは、**[ユーザー ID]** を **user.mail** の **ExtractMailPrefix()** 関数とマップしてあります。 これにより、ユーザーのメール アドレスのプレフィックス値が提供され、これは一意のユーザー ID です。 これは、すべての正常な応答で SAP HANA アプリケーションに送信されます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-saphana-tutorial/attribute.png)

6. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、次の手順を実行します。

    a. **[User Identifier] \(ユーザー識別子)** ドロップダウン リストで、**[ExtractMailPrefix]** を選択します。
    
    b. **[メール]** ドロップダウン リストで、**[user.mail]** を選びます。

7. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-saphana-tutorial/tutorial_general_400.png)
    
8. **SAP HANA**側でシングル サインオンを構成するには、それぞれの HTTPS エンドポイントにアクセスして **HANA XSA Web Console** にログインします。

    > [!Note]
    > 既定の構成では、URL は要求をログオン画面にリダイレクトし、この画面ではログオン処理を完了するために承認された SAP HANA データベース ユーザーの資格情報を要求されます。 ログオンするユーザーには、SAML 管理タスクを実行するための権限が必要です。

9. XSA Web インターフェイスで、**[SAML Identity Provider]\(SAML ID プロバイダー\)** に移動し、画面の下部にある **[+]** ボタンをクリックして [Add Identity Provider Info]\(ID プロバイダーの情報の追加\) ウィンドウを表示し、以下の手順を実行します。

    ![ID プロバイダーの追加](./media/active-directory-saas-saphana-tutorial/sap1.png)

    a. **[Add Identity Provider Info]\(ID プロバイダーの情報の追加\)** ウィンドウで、Azure Portal からダウンロードしたメタデータ XML の内容を、**[Metadata]\(メタデータ\)** ボックスに貼り付けます。

    ![ID プロバイダーの設定の追加](./media/active-directory-saas-saphana-tutorial/sap2.png)

    b. XML ドキュメントの内容が有効な場合は、解析プロセスによって必要な情報が抽出されて、[General Data]\(全般データ\) 画面領域の **[Subject]\(サブジェクト\)、[Entity ID]\(エンティティ ID\)、[Issuer]\(発行者\)** フィールド、および [Destination]\(移動先\) 画面領域の URL フィールド (**[Base URL]\(ベース URL\)、[SingleSignOn URL]\(シングル サインオン URL\) (*)** など) に導入されます。

    ![ID プロバイダーの設定の追加](./media/active-directory-saas-saphana-tutorial/sap3.png)

    c. [General Data]\(全般データ\) 画面領域の [Name]\(名前\) ボックスに、新しい SAML SSO ID プロバイダーの名前を入力します。

    > [!Note]
    > SAML IDP の名前は必須であり、一意である必要があります。この名前は、たとえば XS Artifact Administration ツールの [Authentication]\(認証\) 画面領域で使う SAP HANA XS アプリケーションの認証方法として SAML を選んだ場合に、使用可能な SAML IDP の一覧に表示されます。

10. 新しい SAML ID プロバイダーの詳細を保存します。 **[Save]\(保存\)** を選んで SAML ID プロバイダーの詳細を保存し、既知の SAML IDP の一覧に新しい SAML IDP を追加します。

    ![保存ボタン](./media/active-directory-saas-saphana-tutorial/sap4.png)

11. HANA Studio の **[Configuration]\(構成\)** タブのシステム プロパティで、フィルター設定に「**saml**」と入力し、**[assertion_timeout]\(アサーション タイムアウト\)** を **10 秒**から **120 秒**に変更します。

    ![assertion_timeout の設定](./media/active-directory-saas-saphana-tutorial/sap7.png)

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-saphana-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-saphana-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![[追加] ボタン](./media/active-directory-saas-saphana-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-saphana-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-sap-hana-test-user"></a>SAP HANA テスト ユーザーの作成

Azure AD ユーザーが SAP HANA にログインできるようにするには、そのユーザーを SAP HANA にプロビジョニングする必要があります。
SAP HANA では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。

ユーザーを手動で作成する必要がある場合は、次の手順を実行します。

>[!Note]
>ユーザーが使う外部認証を変更することができます。
外部ユーザーは、Kerberos システムなどの外部システムを使って認証を行います。 外部 ID について詳しくは、[ドメイン管理者](https://cloudplatform.sap.com/contact.html)に問い合わせてください。

1. 管理者として [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) を開き、SAML SSO の DB-User を有効にします。

    ![create user](./media/active-directory-saas-saphana-tutorial/sap5.png)

2. **[SAML]** の左側にある非表示のチェック ボックスをオンにし、[Configure]\(構成\) リンクに従います。

3. **[Add]\(追加\)** をクリックして SAML IDP を追加し、**[OK]** をクリックして適切な SAML IDP を選びます。

4. **[External Identity]\(外部 ID\)** を追加するか (ここでは  BrittaSimon)、**[Any]\(任意\)** を選び、**[OK]** をクリックします。

    >[!Note]
    >[ANY]\(任意\) チェック ボックスがオフの場合、HANA のユーザー名は UPN でドメイン サフィックスの前のユーザーの名前と正確に一致している必要があります (つまり、BrittaSimon@contoso.com は HANA では BrittaSimon になります)。

5. テストの場合は、すべての **"XS"** ロールをユーザーに割り当てます。

    ![ロールの割り当て](./media/active-directory-saas-saphana-tutorial/sap6.png)

    > [!TIP]
    > ユース ケースに適切なアクセス許可のみを付与する必要があります。

6. ユーザーを保存します。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SAP HANA へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**SAP HANA に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[SAP HANA]** を選択します。

    ![ユーザーの割り当て](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで SAP HANA のタイルをクリックすると、自動的に SAP HANA アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_203.png

