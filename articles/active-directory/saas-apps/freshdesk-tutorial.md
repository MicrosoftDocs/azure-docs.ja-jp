---
title: チュートリアル:Azure Active Directory と FreshDesk の統合 | Microsoft Docs
description: Azure Active Directory と FreshDesk の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4bdeecc5682eb09ac0a65d3834ad1770258c4e0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57874729"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>チュートリアル:Azure Active Directory と FreshDesk の統合

このチュートリアルでは、FreshDesk と Azure Active Directory (Azure AD) を統合する方法について説明します。
FreshDesk と Azure AD の統合には、次の利点があります。

* FreshDesk にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで FreshDesk に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

FreshDesk と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* FreshDesk でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* FreshDesk では、**SP** によって開始される SSO がサポートされます

## <a name="adding-freshdesk-from-the-gallery"></a>ギャラリーからの FreshDesk の追加

Azure AD への FreshDesk の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に FreshDesk を追加する必要があります。

**ギャラリーから FreshDesk を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**FreshDesk**」と入力し、結果ウィンドウで **[FreshDesk]** を選び、**[追加]** をクリックしてアプリケーションを追加します。

     ![結果一覧の FreshDesk](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、FreshDesk で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと FreshDesk 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

FreshDesk で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[FreshDesk のシングル サインオンの構成](#configure-freshdesk-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[FreshDesk テスト ユーザーの作成](#create-freshdesk-test-user)** - FreshDesk で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

FreshDesk で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **FreshDesk** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[FreshDesk のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、`https://<tenant-name>.freshdesk.com` のパターン、または FreshDesk から示されたその他の値を使用して URL を入力します。

    b. **[Identifier (Entity ID)]\(ID (エンティティ ID)\)** ボックスに、`https://<tenant-name>.freshdesk.com` のパターン、または FreshDesk から示されたその他の値を使用して URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[FreshDesk サポート チーム](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. FreshDesk アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットはその例です。 **[一意のユーザー ID]** の既定値は **user.userprincipalname** ですが、FreshDesk では、これがユーザーのメール アドレスにマップされると想定されます。 そのため、一覧の **user.mail** 属性を使用するか、組織構成に基づいて適切な属性値を使用できます。 

    ![image](common/edit-attribute.png)

6. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、**編集アイコン**を使用して要求を編集するか、**[新しい要求の追加]** を使用して要求を追加することで、上の図のように SAML トークン属性を構成し、次の手順を実行します。
    
    | Name | ソース属性 |
    | ---------------| --------------- |
    | 一意のユーザー ID | User.mail |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[Save]** をクリックします。

7. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

8. **コマンド プロンプト**を開き、次のコマンドを実行します。

    a. コマンド プロンプトに、値として `certutil.exe -dump FreshDesk.cer` を入力します。

    > [!NOTE]
    > ここでの **FreshDesk.cer** は、Azure portal からダウンロードした証明書です。

    b. **Cert ハッシュ (sha256)** 値をコピーし、メモ帳に貼り付けます。 

9. **[FreshDesk の設定]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-freshdesk-single-sign-on"></a>FreshDesk のシングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、Freshdesk 企業サイトに管理者としてログインします。

2. **[設定]** アイコンを選択し、**[セキュリティ]** セクションで次の手順を実行します。

    ![Single Sign On](./media/freshdesk-tutorial/IC776770.png "Single Sign On")
  
    a. **[シングル サインオン (SSO)]** で **[オン]** を選択します。

    b. **[SAML SSO]** を選択します。

    c. **[SAML Login URL]** \(SAML ログイン URL\) テキストボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    d. **[ログアウト URL]** テキストボックスに、Azure portal からコピーした**ログアウト URL** の値を貼り付けます。

    e. **[セキュリティ証明書フィンガープリント]** テキストボックスに、先ほど得た **Cert ハッシュ (sha256)** の値を貼り付けます。
  
    f. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に FreshDesk へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[FreshDesk]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、「**FreshDesk**」と入力して選択します。

    ![アプリケーションの一覧の FreshDesk リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-freshdesk-test-user"></a>FreshDesk のテスト ユーザーの作成

Azure AD ユーザーが FreshDesk にログインできるようにするには、そのユーザーを FreshDesk にプロビジョニングする必要があります。  
FreshDesk の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Freshdesk** テナントにログインします。

2. 上部のメニューで **[Admin]** をクリックします。

    ![管理](./media/freshdesk-tutorial/IC776772.png "Admin")

3. **[全般設定]** タブで **[エージェント]** をクリックします。
  
    ![Agents](./media/freshdesk-tutorial/IC776773.png "Agents")

4. **[新しいエージェント]** をクリックします。

    ![New Agent](./media/freshdesk-tutorial/IC776774.png "New Agent")

5. [エージェント情報] ダイアログで、次の手順を実行します。

    ![Agent Information](./media/freshdesk-tutorial/IC776775.png "Agent Information")

    a. **[電子メール]** ボックスに、プロビジョニングする Azure AD アカウントの Azure AD 電子メール アドレスを入力します。

    b. **[フル ネーム]** ボックスに、プロビジョニングする Azure AD のアカウントの名前を入力します。

    c. **[タイトル]** ボックスに、プロビジョニングする Azure AD アカウントのタイトルを入力します。

    d. **[Save]** をクリックします。

    >[!NOTE]
    >Azure AD のアカウント所有者には、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。
    >
    >[!NOTE]
    >Freshdesk から提供されているその他の Freshdesk ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントを Freshdesk にプロビジョニングできます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [FreshDesk] タイルをクリックすると、SSO を設定した FreshDesk に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

