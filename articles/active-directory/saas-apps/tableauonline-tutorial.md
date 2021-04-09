---
title: チュートリアル:Azure Active Directory と Tableau Online の統合 | Microsoft Docs
description: Azure Active Directory と Tableau Online の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 5318570ed77e3352f37c2306ecd003195992d010
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98732003"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Tableau Online の統合

このチュートリアルでは、Tableau Online と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Tableau Online を統合すると、次のことができます。

* Tableau Online にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Tableau Online に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Tableau Online でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Tableau Online では、**SP** Initiated SSO がサポートされます

## <a name="adding-tableau-online-from-the-gallery"></a>ギャラリーからの Tableau Online の追加

Azure AD への Tableau Online の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Tableau Online を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Tableau Online**」と入力します。
1. 結果パネルで **[Tableau Online]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-tableau-online"></a>Tableau Online の Azure AD SSO の構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Tableau Online で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Tableau Online 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Tableau Online で Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Tableau Online の SSO の構成](#configure-tableau-online-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Tableau Online のテスト ユーザーの作成](#create-tableau-online-test-user)** - Tableau Online で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Tableau Online** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、URL として「`https://sso.online.tableau.com/public/sp/login?alias=<entityid>`」と入力します。

    b. **[識別子 (エンティティ ID)]** ボックスに `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>` という URL を入力します。

    > [!NOTE]
    > `<entityid>` 値は、このチュートリアルの **[Tableau Online のセットアップ]** セクションで取得します。 エンティティ ID 値は、 **[Tableau Online のセットアップ]** セクションの **[Azure AD 識別子]** 値になります。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Tableau Online のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Tableau Online へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Tableau Online]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-tableau-online-sso"></a>Tableau Online の SSO の構成

1. Tableau Online 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Tableau Online のセットアップ]** をクリックすると、Tableau Online アプリケーションに移動します。 そこから、管理者資格情報を提供して Tableau Online にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 7 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Tableau Online を手動でセットアップする場合は、別の Web ブラウザー ウィンドウで、Tableau Online 企業サイトに管理者としてサインインします。

1. **[設定]** 、 **[認証]** の順にクリックします。

    ![[設定] メニューから [認証] が選択された画面のスクリーンショット。](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. SAML を有効にするには、 **[Authentication types]\(認証の種類\)** セクションで、 **[Enable an additional authentication method]\(追加の認証方法を有効にする\)** をオンにし、 **[SAML]** チェック ボックスをオンにします。

    ![[Authentication types]\(認証の種類\) セクションのスクリーンショット。ここで値を選択することができます。](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. 下へスクロールして、 **[Import metadata file into Tableau Online (Tableau Online にメタデータ ファイルをインポートする)]** セクションを表示します。  [Browse]\(参照\) をクリックし、Azure AD からダウンロードしたメタデータ ファイルをインポートします。 次に、 **[Apply (適用)]** をクリックします。

   ![メタデータ ファイルをインポートするためのセクションのスクリーンショット。](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. **[Match assertions (アサーションの一致)]** セクションで、**メール アドレス**、**名**、**姓** に対応する ID プロバイダーのアサーション名を挿入します。 Azure AD から情報を取得するには、以下の手順に従います。 
  
    a. Azure Portal で **Tableau Online** アプリケーション統合ページに移動します。

    b. **[User Attributes & Claims]\(ユーザー属性とクレーム\)** セクションで、編集アイコンをクリックします。

   ![編集アイコンを選択できる [User Attributes & Claims]\(ユーザー属性とクレーム\) セクションのスクリーンショット。](./media/tableauonline-tutorial/attributesection.png)

    c. 以下の手順で、属性 givenname、email、surname の名前空間の値をコピーします。

   ![Givenname、Surname、Emailaddress の各属性を示すスクリーンショット。](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. **user.givenname** 値をクリックします

    e. **[名前空間]** ボックスの値をコピーします。

    ![[Manage user claims]\(ユーザー要求の管理\) セクションのスクリーンショット。ここに名前空間を入力できます。](./media/tableauonline-tutorial/attributesection2.png)

    f. email、surname の名前空間の値をコピーするには、上の手順を繰り返します。

    g. Tableau Online アプリケーションに切り替えて、 **[User Attributes & Claims]\(ユーザー属性とクレーム\)** セクションを次のように設定します。

    * Email (電子メール): **mail** または **userprincipalname**

    * First name (名): **givenname**

    * Last name (姓): **surname**

    ![[Match attributes]\(属性の対応付け\) セクションのスクリーンショット。ここに値を入力することができます。](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>Tableau Online テスト ユーザーの作成

このセクションでは、Tableau Online で Britta Simon というユーザーを作成します。

1. **Tableau Online** で、 **[設定]** 、 **[認証]** セクションの順にクリックします。 下へスクロールして、 **[Manage Users]\(ユーザーの管理\)** セクションを表示します。 **[Add Users]\(ユーザーの追加\)** をクリックし、 **[Enter email addresses]\(メール アドレスを入力\)** をクリックします。
  
    ![[Manage users]\(ユーザーの管理\) セクションのスクリーンショット。ここで [Add users]\(ユーザーの追加\) を選択できます。](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. **[Add users for (SAML) authentication]\((SAML) 認証用にユーザーを追加する\)** を選択します。 **[Enter email addresses]\(メール アドレスを入力\)** テキスト ボックスに「britta.simon\@contoso.com」と入力します
  
    ![[Add Users]\(ユーザーの追加\) ページのスクリーンショット。ここでメール アドレスを入力することができます。](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. **[ユーザーの追加]** をクリックします。

### <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Tableau Online のサインオン URL にリダイレクトされます。

* Tableau Online のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Tableau Online] タイルをクリックすると、Tableau Online サインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Tableau Online を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。