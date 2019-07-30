---
title: チュートリアル:Azure Active Directory と Citrix NetScaler の統合 | Microsoft Docs
description: Azure Active Directory と Citrix NetScaler の間でシングル サインオンを構成する方法について学習します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af501bd0-8ff5-468f-9b06-21e607ae25de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 64dd67680626857db7f39fa7fd721b28a02d1561
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68276898"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-netscaler"></a>チュートリアル:Azure Active Directory と Citrix NetScaler の統合

このチュートリアルでは、Citrix NetScaler と Azure Active Directory (Azure AD) を統合する方法について学習します。
Citrix NetScaler と Azure AD の統合には、次の利点があります。

* Citrix NetScaler にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが Azure AD アカウントで自動的に Citrix NetScaler にサインインできるようにします (シングル サインオン)。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Citrix NetScaler の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Citrix NetScaler でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Citrix NetScaler では、**SP** によって開始される SSO がサポートされます

* Citrix NetScaler では、**Just In Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-citrix-netscaler-from-the-gallery"></a>ギャラリーからの Citrix NetScaler の追加

Azure AD への Citrix NetScaler の統合を構成するには、ギャラリーから管理対象 SaaS アプリのリストに Citrix NetScaler を追加する必要があります。

**ギャラリーから Citrix NetScaler を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Citrix NetScaler**」と入力し、結果パネルから **[Citrix NetScaler]** を選択し、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

     ![結果リストの Citrix NetScaler](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Citrix NetScaler で Azure AD シングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Citrix NetScaler 内の関連ユーザー間にリンク関係が確立されている必要があります。

Citrix NetScaler で Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Citrix NetScaler シングル サインオンの構成](#configure-citrix-netscaler-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Citrix NetScaler テスト ユーザーの作成](#create-citrix-netscaler-test-user)** - Citrix NetScaler で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Citrix NetScaler で Azure AD シングル サインオンを構成するには、次の手順を行います。

1. [Azure portal](https://portal.azure.com/) の **Citrix NetScaler** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![Citrix NetScaler のドメインと URL のシングル サインオン情報](common/sp-identifier-reply.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`
    
    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<<Your FQDN>>`

    c. **[応答 URL (Assertion Consumer Service URL)]** テキスト ボックスに、`https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx` というパターンを使用して URL を入力します。
    
    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得する場合は、[Citrix NetScaler クライアント サポート チーム](https://www.citrix.com/contact/technical-support.html)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

    > [!NOTE]
    > SSO を動作させるには、パブリック サイトからこれらの URL にアクセスできる必要があります。 Netscaler 側でファイアウォールまたは他のセキュリティ設定を有効にし、Azure AD で構成済みの ACS URL にトークンをポストできるようにする必要があります。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Set up Citrix NetScaler]\(Citrix NetScaler のセットアップ\)** セクションで、要件のとおりに適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-citrix-netscaler-single-sign-on"></a>Citrix NetScaler のシングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、管理者として Citrix NetScaler テナントにサインオンします。

2. **NetScaler ファームウェアのバージョンが NS12.1:ビルド 48.13.nc** であることを確認します。

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure01.png)

3. **[VPN Virtual Server]\(VPN 仮想サーバー\)** ページで、次の手順を行います。

     ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure02.png)

    a. ゲートウェイ設定の **[ICA Only]\(ICA のみ\)** を **[true]** に設定します。
    
    b. **[Enable Authentication]\(認証を有効にする\)** を **[true]** に設定します。
    
    c. **[DTLS]** は省略可能です。
    
    d. **[SSLv3]** が **[Disabled]\(無効\)** であることを確認します。

4. カスタマイズされた **SSL 暗号化**グループが作成され、以下のように https://www.ssllabs.com で A+ を獲得できます。

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure03.png)

5. **[Configure Authentication SAML Server]\(認証 SAML サーバーの構成\)** ページで、次の手順を行います。

      ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure04.png)

    a. **[Name]\(名前\)** テキストボックスに、サーバーの名前を入力します。

    b. **[Redirect URL]\(リダイレクト URL\)** テキストボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    c. **[Single Logout URL]\(シングル ログアウト URL\)** テキストボックスに、Azure portal からコピーした**ログアウト URL** の値を貼り付けます。

    d. **[IDP Certificate Name]\(IDP 証明書名\)** で、 **[+]** 記号をクリックし、Azure portal からダウンロードした証明書を追加します。 アップロードされた後、ドロップダウンから証明書を選択してください。

    e. このページでさらに以下のフィールドを設定する必要があります

      ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure24.png)

    f. **[Requested Authentication Context]\(要求された認証コンテキスト\)** で **[Exact]\(完全\)** を選択します。

    g. **[Signature Algorithm]\(署名アルゴリズム\)** で **[RSA-SHA256]** を選択します。

    h. **[Digest Method]\(ダイジェスト方法\)** で **[SHA256]** を選択します。

    i. **[Enforce Username]\(ユーザー名の適用\)** をオンにします。

    j. **[OK]**

6. **セッション プロファイル**を構成するには、次の手順を行います。

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure06.png)

    a. **[Name]\(名前\)** テキストボックスに、セッション プロファイルの名前を入力します。

    b. **[Client Experience]\(クライアント エクスペリエンス\)** タブで、次のスクリーンショットに示すように変更します。

    c. 以下のように **[General]\(全般\) タブ**で変更を続行し、 **[OK]** をクリックします

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure07.png)

    d. **[Published Applications]\(公開されたアプリケーション\)** タブで、以下のスクリーンショットに示すように変更し、 **[OK]** をクリックします。

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure08.png)

    e. **[Security]\(セキュリティ\)** タブで、以下のスクリーンショットに示すように変更し、 **[OK]** をクリックします。

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure09.png)

7. 以下のスクリーンショットに示すように、ICA 接続がセッション信頼性ポート **2598** での接続となっていることを確認します。

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure10.png)

8. 以下のスクリーンショットに示すように、 **[SAML]** セクションで**サーバー**を追加します。

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure11.png)

9. 以下のスクリーンショットに示すように、 **[SAML]** セクションで**ポリシー**を追加します。

     ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure12.png)

10. **[Global Settings]\(グローバル設定\)** ページで、 **[Clientless Access]\(クライアントレス アクセス\)** セクションに移動します。

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure13.png)

11. **[Configuration]\(構成\)** タブで、次の手順を行います。

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure14.png)

    a. **[Allow Domains]\(ドメインを許可\)** を選択します。

    b. **[Domain Name]\(ドメイン名\)** テキストボックスで、ドメインを選択します。

    c. Click **OK**.

12. 以下のスクリーンショットに示すように、 **[Receiver for Web Sites]\(Receiver for Web サイト\)** の設定が **[StoreFront]** になっていることを確認します。

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure15.png)

13. **[Manage Authentication Methods - Corp]\(認証方法の管理 - Corp\)** ポップアップで、次の手順を行います。

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure16.png)

    a. **[ユーザー名とパスワード]** を選択します。

    b. **[Pass-through from NetScaler Gateway]\(NetScaler ゲートウェイからのパススルー\)** を選択します。

    c. Click **OK**.

14. **[Configure Trusted Domains]\(信頼できるドメインの構成\)** ポップアップで、次の手順を行います。

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure17.png)

    a. **[Trusted domains only]\(信頼できるドメインのみ\)** を選択します。

    b. **[Add]\(追加\)** をクリックし、 **[Trusted domains]\(信頼できるドメイン\)** テキストボックスでドメインを追加します。

    c. **[Default domain]\(既定のドメイン\)** リストから既定のドメインを選択します。

    d. **[Show domains list in logon page]\(ログオン ページにドメイン リストを表示\)** を選択します。

    e. Click **OK**.

15. **[Manage NetScaler Gateways]\(NetScaler ゲートウェイの管理\)** ポップアップで、次の手順を行います。

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure18.png)

    a. **[Add]\(追加\)** をクリックし、 **[NetScaler Gateways]\(NetScaler ゲートウェイ\)** テキストボックスで NetScaler ゲートウェイを追加します。

    b. **[閉じる]** をクリックします。

16. **StoreFront の [General Settings]\(全般設定\)** タブで、次の手順を行います。

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure19.png)

    a. **[Display name]\(表示名\)** テキストボックスに、NetScaler ゲートウェイの名前を入力します。

    b. **[NetScaler Gateway URL]\(NetScaler ゲートウェイの URL\)** テキストボックスに、NetScaler ゲートウェイの URL を入力します。

    c. **[Usage or role]\(使用方法またはロール\)** で、 **[Authentication and HDX routing]\(認証と HDX ルーティング\)** を選択します。

    d. Click **OK**.

17. **StoreFront の [Secure Ticket Authority]\(セキュア チケット機関\)** タブで、次の手順を行います。

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure20.png)

    a. **[Add]\(追加\)** ボタンをクリックし、テキストボックスでご利用の **[Secure Ticket Authority URLs]\(セキュア チケット機関の URL\)** を追加します。

    b. **[Enable session reliability]\(セッションの信頼性を有効にする\)** を選択します。

    c. Click **OK**.

18. **StoreFront の [Authentication Settings]\(認証設定\)** タブで、次の手順を行います。

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure21.png)

    a. ご利用の**バージョン**を選択します。

    b. **[Domain]\(ログオンの種類\)** で **[Domain]\(ドメイン\)** を選択します。

    c. ご利用の **コールバック URL** を入力します。

    d. Click **OK**.

19. **StoreFront の [Deploy Citrix Receiver]\(Citrix Receiver の展開\)** タブで、次の手順を行います。

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure22.png)

    a. **[Deployment option]\(展開オプション\)** で **[Use Receiver for HTML5 if local Receiver is unavailable]\(ローカルの Receiver が使用できない場合は Receiver for HTML5 を使用する\)** を選択します。

    b. Click **OK**.

20. **[Manage Beacons]\(ビーコンの管理\)** ポップアップで、次の手順を行います。

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure23.png)

    a. **[Internal beacon]\(内部ビーコン\)** で **[Use the service URL]\(サービス URL を使用する\)** を選択します。

    b. **[Add]\(追加\)** をクリックし、 **[External beacons]\(外部ビーコン\)** テキストボックスで URL を追加します。

    c. Click **OK**.

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに **brittasimon@yourcompanydomain.extension** と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Citrix NetScaler へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** 、 **[すべてのアプリケーション]** 、 **[Citrix NetScaler]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーション リストで、 **[Citrix NetScaler]** を選択します。

    ![アプリケーション リストの Citrix NetScaler リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-citrix-netscaler-test-user"></a>Citrix NetScaler テスト ユーザーを作成する

このセクションでは、Britta Simon というユーザーを Citrix NetScaler に作成します。 Citrix NetScaler では、Just-In-Time ユーザー プロビジョニングがサポートされています。これは既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Citrix NetScaler にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、[Citrix NetScaler クライアント サポート チーム](https://www.citrix.com/contact/technical-support.html)に問い合わせる必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Citrix NetScaler] タイルをクリックすると、SSO を設定した Citrix NetScaler に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

