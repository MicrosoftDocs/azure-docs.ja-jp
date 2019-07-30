---
title: チュートリアル:Azure Active Directory と Workplace by Facebook の統合 | Microsoft Docs
description: Azure Active Directory と Workplace by Facebook の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: db14df2dc24afbc8f7982d8d1e4e6429c1c4b1ba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086794"
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>チュートリアル:Azure Active Directory と Workplace by Facebook の統合

このチュートリアルでは、Workplace by Facebook と Azure Active Directory (Azure AD) を統合する方法について説明します。
Workplace by Facebook と Azure AD の統合には、次の利点があります。

* Workplace by Facebook にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して自動的に Workplace by Facebook にサインイン (シングル サインオン) できるようにすることが可能です。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Workplace by Facebook の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Workplace by Facebook でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

> [!NOTE]
> Facebook には、Workplace Standard (無料) と Workplace Premium (有料) の 2 つの製品があります。 すべての Workplace Premium テナントは、他のコストやライセンスを必要とせずに、SCIM と SSO の統合を構成できます。 SSO と SCIM は、Workplace Standard インスタンスでは利用できません。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Workplace by Facebook では、**SP** によって開始される SSO がサポートされます
* Workplace by Facebook では、**Just-In-Time プロビジョニング**がサポートされます
* Workplace by Facebook では、 **[自動ユーザー プロビジョニング](workplacebyfacebook-provisioning-tutorial.md)** がサポートされます

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>ギャラリーからの Workplace by Facebook の追加

Azure AD への Workplace by Facebook の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Workplace by Facebook を追加する必要があります。

**ギャラリーから Workplace by Facebook を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Workplace by Facebook**」と入力し、結果ウィンドウで **Workplace by Facebook** を選び、 **[追加]** をクリックしてアプリケーションを追加します。

     ![結果リストの Workplace by Facebook](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Workplace by Facebook で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Workplace by Facebook 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Workplace by Facebook で Azure AD のシングル サインオンを構成してテストするには、次の一連の作業を完了させる必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Workplace by Facebook シングル サインオンの構成](#configure-workplace-by-facebook-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Workplace by Facebook のテスト ユーザーの作成](#create-workplace-by-facebook-test-user)** - Workplace by Facebook で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Workplace by Facebook で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Workplace by Facebook** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Workplace by Facebook のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<instancename>.facebook.com`

    b. **[識別子 (エンティティ ID)]** ボックスに、`https://www.facebook.com/company/<instanceID>` という形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 Workplace コミュニティの適切な値については、Workplace Company Dashboard の認証ページを参照してください。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Workplace by Facebook のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-workplace-by-facebook-single-sign-on"></a>Workplace by Facebook シングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、Workplace by Facebook 企業サイトに管理者としてログインします。
  
    > [!NOTE]
    > SAML 認証プロセスの一環として、Azure AD にパラメーターを渡すために Workplace が最大サイズ 2.5 KBのクエリ文字列を使用する可能性があります。

2. **管理パネル**で、 **[セキュリティ]** タブに移動します。

    ![管理パネル](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

3. **[認証]** タブで **[シングル サインオン (SSO)]** を選択し、次の手順を実行します。

    ![[認証] タブ](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. **[SAML URL]** ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    b. **[SAML Issuer URL]\(SAML 発行者の URL\)** ボックスに、Azure portal からコピーした **Azure AD 識別子**の値を貼り付けます。

    c. **[SAML Logout redirect]\(SAML ログアウト リダイレクト\)** (オプション) に、Azure portal からコピーした**ログアウト URL** の値を貼り付けます。

    d. Azure Portal からダウンロードした **Base-64 でエンコードされた証明書**をメモ帳で開き、その内容をクリップボードにコピーしてから、それを **[SAML Certificate]\(SAML 証明書\)** ボックスに貼り付けます。

    e. インスタンスの **[Audience URL]\(対象 URL\)** をコピーして、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子 (エンティティ ID)]** ボックスに貼り付けます。

    f. インスタンスの **[Recipient URL]\(受信者 URL\)** をコピーして、Azure portal の **[基本的な SAML 構成]** セクションの **[サインオン URL]** ボックスに貼り付けます。

    g. セクションの下部にスクロールし、 **[Test SSO]\(SSO のテスト\)** をクリックします。 これにより、Azure AD ログイン ページで表示されるポップアップ ウィンドウが表示されます。 通常どおり資格情報を入力して認証を行います。

    **トラブルシューティング:** Azure AD から返された電子メール アドレスが、ログインに使用した Workplace アカウントと同じであることを確認してください。

    h. テストが正常に完了したら、ページの下部にスクロールして **[保存]** をクリックします。

    i. Workplace を使用しているすべてのユーザーに、認証用の Azure AD ログイン ページが表示されるようになりました。

4. **SAML ログアウト リダイレクト (オプション)**  -

    必要に応じて、Azure AD のログアウト ページの指定に使用される SAML ログアウト URL を構成できます。 この設定が有効に構成されている場合は、ユーザーに対して Workplace ログアウト ページが表示されなくなります。 代わりに、SAML ログアウト リダイレクトの設定に追加された URL にユーザーがリダイレクトされるようになります。

### <a name="configuring-reauthentication-frequency"></a>再認証の頻度の構成

SAML チェックの要求を毎日、3 日ごと、1 週間ごと、2 週間ごと、1 か月ごとに行う、または行わないように、Workplace を構成できます。

> [!NOTE]
> モバイル アプリケーションで設定できる SAML チェック頻度の最小値は 1 週間です。

次のボタンを使用して、すべてのユーザーに対して SAML のリセットを強制することもできます。[Require SAML authentication for all users now]\(今すぐすべてのユーザーの SAML 認証を要求する\)。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[User name]\(ユーザー名\)** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Workplace by Facebook へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[Workplace by Facebook]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、「**Workplace by Facebook**」と入力して選択します。

    ![アプリケーションの一覧の Workplace by Facebook リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-workplace-by-facebook-test-user"></a>Workplace by Facebook のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Workplace by Facebook に作成します。 Workplace by Facebook では、Just-In-Time プロビジョニングがサポートされています。この設定は既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 Workplace by Facebook にユーザーが 1 人もいない場合は、Workplace by Facebook にアクセスしようとしたときに新しいユーザーが作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[Workplace by Facebook クライアント サポート チーム](https://workplace.fb.com/faq/)にお問い合わせください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Workplace by Facebook] タイルをクリックすると、SSO を設定した Workplace by Facebook に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [[ユーザー プロビジョニングの構成]](workplacebyfacebook-provisioning-tutorial.md)
