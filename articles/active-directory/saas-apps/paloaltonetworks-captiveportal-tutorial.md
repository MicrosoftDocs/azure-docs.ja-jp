---
title: チュートリアル:Azure Active Directory と Palo Alto Networks Captive Portal の統合 | Microsoft Docs
description: Azure Active Directory と Palo Alto Networks Captive Portal の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a62aa573d49ccdd237e692b63a6fda0dd83d52a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57888299"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>チュートリアル:Azure Active Directory と Palo Alto Networks Captive Portal の統合

このチュートリアルでは、Palo Alto Networks Captive Portal と Azure Active Directory (Azure AD) を統合する方法について説明します。

Palo Alto Networks Captive Portal と Azure AD を統合すると、次のメリットが得られます。

* Palo Alto Networks Captive Portal にアクセスできるユーザーを Azure AD で管理できます。
* Palo Alto Networks Captive Portal 内で各ユーザーに自身の Azure AD アカウントを使って自動的にサインイン (シングル サインオン) させることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

Azure サブスクリプションをお持ちでない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Palo Alto Networks Captive Portal を統合するには、次のものが必要です。

* Azure Active Directory のサブスクリプション。 Azure AD をお持ちでない場合には、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。
* シングル サインオン (SSO) に対応した Palo Alto Networks Captive Portal のサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

Palo Alto Networks Captive Portal では、次のシナリオがサポートされます。

* **IDP が起点となるシングル サインオン**
* **Just-in-time のユーザー プロビジョニング**

## <a name="add-palo-alto-networks-captive-portal-from-the-gallery"></a>ギャラリーからの Palo Alto Networks Captive Portal の追加

まずは、ギャラリー内でマネージド SaaS アプリの一覧に Palo Alto Networks Captive Portal を追加します。

1. [Azure Portal](https://portal.azure.com) の左側のメニューで、**[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** > **[すべてのアプリケーション]** の順に選択します。

    ![メニューの [エンタープライズ アプリケーション] オプション](common/enterprise-applications.png)

3. **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Palo Alto Networks Captive Portal**」と入力します。 検索結果に表示される "**Palo Alto Networks - Captive Portal**" を選択し、**[追加]** を選択します。

     ![結果一覧の Palo Alto Networks - Captive Portal](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

*Britta Simon* というテスト ユーザーをベースに、Palo Alto Networks Captive Portal を使った Azure AD のシングル サインオンを構成し、テストします。 シングル サインオンを機能させるには、Azure AD ユーザーと Palo Alto Networks Captive Portal 内の同一ユーザーとの間に一定の関係を確立する必要があります。 

Palo Alto Networks Captive Portal を使った Azure AD のシングル サインオンを構成してテストするには、次の作業を完了してください。

1. **[Azure AD のシングル サインオンの構成](#configure-azure-ad-single-sign-on)**: ユーザーがこの機能を利用できるようにします。
2. **[Palo Alto Networks Captive Portal のシングル サインオンの構成](#configure-palo-alto-networks-captive-portal-single-sign-on)**: アプリケーションでシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)**: ユーザー *Britta Simon* を使用して Azure AD のシングル サインオンをテストします。
4. **[Azure AD のテスト ユーザーの割り当て](#assign-the-azure-ad-test-user)**: Azure AD のシングル サインオンを使用するよう Britta Simon を設定します。
5. **Palo Alto Networks Captive Portal のテスト ユーザーの作成**: Palo Alto Networks Captive Portal 内で *Britta Simon* に対応するユーザーを作成し、Azure AD の Britta Simon にリンクします。
6. **[シングル サインオンのテスト](#test-single-sign-on)**: 構成が機能することを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

まずは、Azure portal 内で Azure AD のシングル サインオンを有効にします。

1. [Azure portal](https://portal.azure.com/) の **Palo Alto Networks - Captive Portal** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ウィンドウで、**[SAML]** を選択します。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ウィンドウで、鉛筆マークの **[編集]** アイコンを選択します。

    ![鉛筆マークの [編集] アイコン](common/edit-urls.png)

4. **[基本的な SAML 構成]** ウィンドウで、次の手順を実行します。

    ![Palo Alto Networks Captive Portal の [基本的な SAML 構成] ウィンドウ](common/idp-intiated.png)

   1. **[識別子]** に、`https://<customer_firewall_host_name>/SAML20/SP` の形式で URL を入力します。

   2. **[応答 URL]** に、`https://<customer_firewall_host_name>/SAML20/SP/ACS` の形式で URL を入力します。

      > [!NOTE]
      > この手順に示したプレースホルダーの値は、実際の識別子と応答 URL に置き換えてください。 実際の値を取得するには、[Palo Alto Networks Captive Portal のクライアント サポート チーム](https://support.paloaltonetworks.com/support)にご連絡ください。

5. **[SAML 署名証明書]** セクションで、**[フェデレーション メタデータ XML]** の隣にある **[ダウンロード]** を選択します。 ダウンロードしたファイルを、お使いのコンピューターに保存します。

    ![フェデレーション メタデータ XML のダウンロード リンク](common/metadataxml.png)

### <a name="configure-palo-alto-networks-captive-portal-single-sign-on"></a>Palo Alto Networks Captive Portal のシングル サインオンの構成

次に、Palo Alto Networks Captive Portal 内でシングル サインオンを設定していきます。

1. 別の Web ブラウザー ウィンドウで、Palo Alto Networks の Web サイトに管理者としてログインします。

2. **[Device]\(デバイス\)** タブを選択します。

    ![Palo Alto Networks の Web サイトの [Device]\(デバイス\) タブ](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. メニューで **[SAML Identity Provider]\(SAML ID プロバイダー\)** を選択し、**[Import]\(インポート\)** を選択します。

    ![[Import]\(インポート\) ボタン](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. **[SAML Identify Provider Server Profile Import]\(SAML ID プロバイダー サーバー プロファイルのインポート\)** ダイアログ ボックスで、次の手順を実行します。

    ![Palo Alto Networks のシングル サインオンの構成](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. **[Profile Name]\(プロファイル名\)** に、**AzureAD-CaptivePortal** のような名前を入力します。
    
    2. **[Identity Provider Metadata]\(ID プロバイダーのメタデータ\)** の隣にある **[Browse]\(参照\)** を選択します。 Azure portal からダウンロードした metadata.xml ファイルを選択します。
    
    3. **[OK]** を選択します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

次は、Azure portal 内で *Britta Simon* というテスト ユーザーを作成します。

1. Azure portal 内で、**[Azure Active Directory]** > **[ユーザー]** > **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. **[ 新規ユーザー]** を選択します。

    ![[新規ユーザー] ボタン](common/new-user.png)

3. **[ユーザー]** ウィンドウで、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    1. **[名前]** に、「**BrittaSimon**」と入力します。
  
    2. **[ユーザー名]** に、「**BrittaSimon\@\<your_company_domain\>**」と入力します。 たとえば、「**BrittaSimon\@contoso.com**」です。

    3. **[パスワード]** に、パスワードを入力します。 入力するパスワードはどこかに記録しておくことをお勧めします。 **[パスワードを表示]** チェック ボックスをオンにして、パスワードを表示することもできます。

    4. **作成**を選択します。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

次は、Britta Simon が Azure のシングル サインオンを利用できるようにするために、Palo Alto Networks Captive Portal へのアクセスを許可します。

1. Azure portal 内で、**[エンタープライズ アプリケーション]** > **[すべてのアプリケーション]** の順に選択します。

    ![[エンタープライズ アプリケーション] ウィンドウ](common/enterprise-applications.png)

2. アプリケーションの一覧で「**Palo Alto Networks - Captive Portal**」と入力し、そのアプリケーションを選択します。

    ![アプリケーションの一覧の Palo Alto Networks - Captive Portal のリンク](common/all-applications.png)

3. メニューで **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** を選択します。 **[割り当ての追加]** ウィンドウで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ウィンドウの **[ユーザー]** 一覧で、**Britta Simon** を選択します。 **[選択]** を選択します。

6. SAML アサーションにロールの値を追加するために、**[ロールの選択]** ウィンドウでユーザーに関連するロールを選択します。 **[選択]** を選択します。

7. **[割り当ての追加]** ウィンドウで **[割り当て]** を選択します。

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Palo Alto Networks Captive Portal のテスト ユーザーの作成

次は、Palo Alto Networks Captive Portal で *Britta Simon* というユーザーを作成します。 Palo Alto Networks Captive Portal では、Just-In-Time のユーザー プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、何も行う必要はありません。 Palo Alto Networks Captive Portal にユーザーがまだ存在していない場合は、認証後に新しいユーザーが作成されます。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[Palo Alto Networks Captive Portal のクライアント サポート チーム](https://support.paloaltonetworks.com/support)にお問い合わせください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

Palo Alto Networks Captive Portal は、Windows VM 上のファイアウォールの内側にインストールされます。 Palo Alto Networks Captive Portal 内でシングル サインオンをテストするには、リモート デスクトップ プロトコル (RDP) を使って Windows VM にサインインします。 RDP セッションで、ブラウザーを開いて任意の Web サイトに移動します。 SSO のための URL が開き、認証を求めるメッセージが表示されます。 認証が完了すると、Web サイトにアクセスできるようになります。

## <a name="additional-resources"></a>その他のリソース

詳細については、次の記事を参照してください。

- [SaaS アプリと Azure Active Directory の統合に関するチュートリアル](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Azure Active Directory の条件付きアクセス](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

