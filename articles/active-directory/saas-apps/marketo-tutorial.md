---
title: 'チュートリアル: Azure Active Directory と Marketo の統合 | Microsoft Docs'
description: Azure Active Directory と Marketo の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: 433303bf0d51eff3bd3ab37726c9e98e8a766d25
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101686956"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>チュートリアル: Marketo と Azure Active Directory の統合

このチュートリアルでは、Marketo と Azure Active Directory (Azure AD) を統合する方法について説明します。
Marketo と Azure AD の統合には、次の利点があります。

* Marketo にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Marketo に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

## <a name="prerequisites"></a>前提条件

Marketo と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Marketo でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Marketo では、**IDP** Initiated SSO がサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-marketo-from-the-gallery"></a>ギャラリーからの Marketo の追加

Azure AD への Marketo の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Marketo を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Marketo**」と入力します。
1. 結果のパネルから **[Marketo]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-marketo"></a>Marketo の Azure AD SSO の構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Marketo で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Marketo 内の関連ユーザー間にリンク関係が確立されている必要があります。

Marketo で Azure AD シングル サインオンを構成してテストするには、次の手順に従います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon を使用して Azure AD SSO をテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD SSO を使用できるようにします。
2. **[Marketo の SSO の構成](#configure-marketo-sso)** - アプリケーション側で SSO 設定を構成します。
    1. **[Marketo のテスト ユーザーの作成](#create-marketo-test-user)** - Marketo で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
3. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Marketo** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://saml.marketo.com/sp` という URL を入力します。

    b. **[応答 URL]** ボックスに、`https://login.marketo.com/saml/assertion/<munchkinid>` のパターンを使用して URL を入力します

    c. **[リレー状態]** ボックスに、`https://<munchkinid>.marketo.com/` のパターンで URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際の応答 URL とリレー状態で更新してください。 これらの値を取得するには、[Marketo クライアント サポート チーム](https://investors.marketo.com/contactus.cfm)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Marketo のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Marketo へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Marketo]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-marketo-sso"></a>Marketo の SSO の構成

1. Marketo 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Marketo のセットアップ]** をクリックすると、Marketo アプリケーションに誘導されます。 そこから、管理者の資格情報を入力して Marketo にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Marketo を手動でセットアップする場合は、別の Web ブラウザー ウィンドウで、Marketo 企業サイトに管理者としてサインインします。

1. アプリケーションの Munchkin ID を取得するには、次のアクションを実行します。
   
    a. 管理者の資格情報を使用して Marketo アプリにログインします。
   
    b. 上部のナビゲーション ウィンドウで **[管理者]** ボタンをクリックします。
   
    ![シングル サインオンの構成 1](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. 統合メニューに移動し、**Munchkin リンク** をクリックします。
   
    ![シングル サインオンの構成 2](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. 画面に表示される Munchkin ID をコピーし、Azure AD の構成ウィザードで、応答 URL を完了します。
   
    ![シングル サインオンの構成 3](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. アプリケーションで SSO を構成するには、以下の手順に従ってください。
   
    a. 管理者の資格情報を使用して Marketo アプリにログインします。
   
    b. 上部のナビゲーション ウィンドウで **[管理者]** ボタンをクリックします。
   
    ![シングル サインオンの構成 4](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. 統合メニューに移動し、 **[シングルサインオン]** をクリックします。
   
    ![シングル サインオンの構成 5](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. SAML 設定を有効にするには、 **[編集]** ボタンをクリックします。
   
    ![シングル サインオンの構成6](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. シングル サインオン設定を **有効** にします。
   
    f. **Azure AD 識別子** を **[発行者 ID]** ボックスに貼り付けます。
   
    g. **[エンティティ ID]** ボックスに、URL「`http://saml.marketo.com/sp`」を入力します。
   
    h. **[Name Identifier element]\(名前識別子要素\)** としてユーザー ID の場所を選択します。
   
    ![シングル サインオンの構成 7](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > ユーザー識別子が UPN 値ではない場合、[属性] タブで値を変更します。
   
    i. Azure AD の構成ウィザードからダウンロードした証明書をアップロードします。 設定を **保存** します。
   
    j. ページのリダイレクト設定を編集します。
   
    k. **[ログイン URL]** ボックスに **ログイン URL** を貼り付けます。
   
    l. **[ログアウト URL]** ボックスに **ログアウト URL** を貼り付けます。
   
    m. **[エラー URL]** に **Marketo インスタンス URL** をコピーし、 **[保存]** ボタンをクリックして設定を保存します。
   
    ![シングル サインオンの構成 8](./media/marketo-tutorial/tutorial_marketo_10.png)

3. ユーザーの SSO を有効にするには、次の操作を行います。
   
    a. 管理者の資格情報を使用して Marketo アプリにログインします。
   
    b. 上部のナビゲーション ウィンドウで **[管理者]** ボタンをクリックします。
   
    ![シングル サインオンの構成 9](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. **[セキュリティ]** メニューに移動して、 **[ログイン設定]** をクリックします。
   
    ![シングル サインオンの構成 10](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. **[SSO 必須]** オプションをオンにして、設定を **保存** します。
   
    ![シングル サインオンの構成 11](./media/marketo-tutorial/tutorial_marketo_14.png)


### <a name="create-marketo-test-user"></a>Marketo のテスト ユーザーの作成

このセクションでは、Marketo で Britta Simon というユーザーを作成します。 Marketo プラットフォームでユーザーを作成するには、次の手順に従ってください。

1. 管理者の資格情報を使用して Marketo アプリにログインします。

2. 上部のナビゲーション ウィンドウで **[管理者]** ボタンをクリックします。
   
    ![ユーザーのテスト 1](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. **[セキュリティ]** メニューに移動して、 **[ユーザーと役割]** をクリックします。
   
    ![ユーザーのテスト 2](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. [ユーザー] タブで **[新しいユーザーの追加]** をクリックします。
   
    ![ユーザーのテスト 3](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. [新しいユーザーの追加] ウィザードで、次の情報を入力します。
   
    a. テキスト ボックスにユーザーの **[メール]** アドレスを入力します。
   
    ![ユーザーのテスト 4](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. テキスト ボックスに **[名]** を入力します。
   
    c. テキスト ボックスに **[姓]** を入力します。
   
    d. **[次へ]** をクリックします。

6. **[アクセス許可]** タブで **[userRoles]** を選択して **[次へ]** をクリックします。
   
    ![ユーザーのテスト 5](./media/marketo-tutorial/tutorial_marketo_17.png)
7. **[送信]** ボタンをクリックしてユーザーの招待を送信します。
   
    ![ユーザーのテスト 6](./media/marketo-tutorial/tutorial_marketo_18.png)

8. ユーザーは、電子メール通知を受け取った後、リンクをクリックしてパスワードを変更し、アカウントをアクティブ化する必要があります。 

### <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した Marketo に自動的にサインインされます

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Marketo] タイルをクリックすると、SSO を設定した Marketo に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Marketo を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。