---
title: チュートリアル:Azure Active Directory と Sansan の統合 | Microsoft Docs
description: Azure Active Directory と Sansan の間でシングル サインオンを構成する方法について確認します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f653a0f2-c44a-4670-b936-68c136b578ea
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: jeedes
ms.openlocfilehash: 59cfdfaae597feb8f1cab212b407a8879d78f9f9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59265578"
---
# <a name="tutorial-azure-active-directory-integration-with-sansan"></a>チュートリアル:Azure Active Directory と Sansan の統合

このチュートリアルでは、Sansan と Azure Active Directory (Azure AD) を統合する方法について説明します。
Sansan と Azure AD の統合には、次の利点があります。

* Sansan にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで Sansan に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Sansan と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Sansan でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Sansan では、**SP** Initiated SSO がサポートされます

## <a name="adding-sansan-from-the-gallery"></a>ギャラリーから Sansan を追加する

Azure AD への Sansan の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Sansan を追加する必要があります。

**ギャラリーから Sansan を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Sansan**」と入力し、結果パネルで **[Sansan]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の Sansan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Sansan で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Sansan 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Sansan で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Sansan のシングル サインオンの構成](#configure-sansan-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Sansan のテスト ユーザーの作成](#create-sansan-test-user)** - Sansan で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Sansan で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Sansan** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Sansan のドメインと URL] のシングル サインオン情報](common/sp-signonurl.png)

    **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。
    | 環境 | URL |
    |:--- |:--- |
    | PC Web |`https://ap.sansan.com/v/saml2/<company name>/acs` |
    | ネイティブ モバイル アプリ |`https://internal.api.sansan.com/saml2/<company name>/acs` |
    | モバイル ブラウザーの設定 |`https://ap.sansan.com/s/saml2/<company name>/acs` |  

    > [!NOTE]
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Sansan クライアント サポート チーム](https://www.sansan.com/form/contact)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. Sansan アプリケーションは、 複数の環境（PC web、 ネイティブ モバイル アプリ、モバイル ブラウザーの設定）をサポートするために、複数の**識別子**と**返信用 URL** が必要です。これは、PowerShell スクリプトを使用して　構成することができます。 細かい手順について、下記に説明します。

7. PowerShell スクリプトを　使用して、Sansan アプリケーションに、複数の**識別子**と**返信用 URL** を構成するには、次の手順を実行します：

    ![シングル サインオン obj を構成します](./media/sansan-tutorial/tutorial_sansan_objid.png)  

    a. 次に、**Sansan** アプリケションの**プロパティ** ページに移動し、**[コピー]** ボタンを使用して **[オブジェクト ID]** をコピーし、メモ帳に貼り付けます。

    b. Azure Portal から　コピーした**オブジェクト ID** は、チュートリアルの後半で使用される PowerShell スクリプトに、**ServicePrincipalObjectId** とし使用されます。

    c. 次に、管理者特権での Windows PowerShell コマンド プロンプトを開きます。

    >[!NOTE]
    > Azure AD モジュールをインストールする必要があります (`Install-Module -Name AzureAD` コマンドを使用してください)。 NuGet モジュールまたは新しい Azure Active Directory V2 PowerShell モジュールをインストールするように求められたら「Y」と入力し、ENTER キーを押します。

    d. `Connect-AzureAD` を実行してグローバル管理者ユーザー アカウントでサインインします。

    e. 次のスクリプトを使用して、アプリケーションの複数の URL を更新します。

    ```powershell
     Param(
    [Parameter(Mandatory=$true)][guid]$ServicePrincipalObjectId,
    [Parameter(Mandatory=$false)][string[]]$ReplyUrls,
    [Parameter(Mandatory=$false)][string[]]$IdentifierUrls
    )

    $servicePrincipal = Get-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId

    if($ReplyUrls.Length)
    {
    echo "Updating Reply urls"
    Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls $ReplyUrls
    echo "updated"
    }
    if($IdentifierUrls.Length)
    {
    echo "Updating Identifier urls"
    $applications = Get-AzureADApplication -SearchString $servicePrincipal.AppDisplayName 
    echo "Found Applications =" $applications.Length
    $i = 0;
    do
    {  
    $application = $applications[$i];
    if($application.AppId -eq $servicePrincipal.AppId){
    Set-AzureADApplication -ObjectId $application.ObjectId -IdentifierUris $IdentifierUrls
    $servicePrincipal = Get-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId
    echo "Updated"
    return;
    }
    $i++;
    }while($i -lt $applications.Length);
    echo "Not able to find the matched application with this service principal"
    }
    ```

8. PowerShell スクリプトが正常に完了した後、スクリプトの結果は、下記のようになります。URL の値は更新されますが、それらは、Azure portal には反映されません。

    ![シングル サインオン スクリプトの構成](./media/sansan-tutorial/tutorial_sansan_powershell.png)

9. **[Sansan のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-sansan-single-sign-on"></a>Sansan のシングル サインオンの構成

**Sansan** 側でシングル サインオンを構成するには、ダウンロードした**証明書 (Base64)** と Azure portal からコピーした適切な URL を [Sansan クライアント サポート チーム](https://www.sansan.com/form/contact)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Sansan へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[Sansan]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Sansan]** を選択します。

    ![アプリケーションの一覧の Sansan のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-sansan-test-user"></a>Sansan のテスト ユーザーの作成

このセクションでは、SanSan に、 Britta Simon というユーザーを作成します。 SanSan アプリケーションでは、SSO を実行する前に、ユーザーをアプリケーションにプロビジョニングする必要があります。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合、またはユーザーのバッチを作成する必要がある場合は、[Sansan のサポート チーム](https://www.sansan.com/form/contact)に問い合わせてください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Sansan] タイルをクリックすると、SSO を設定した Sansan に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)