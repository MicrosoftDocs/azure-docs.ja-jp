---
title: 'チュートリアル: Azure Active Directory と Adobe Sign の統合 | Microsoft Docs'
description: Azure Active Directory と Adobe Sign の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: 7162c38aae2fec4ea21aae56fa8c3649f7a55425
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101649969"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>チュートリアル: Azure Active Directory と Adobe Sign の統合

このチュートリアルでは、Adobe Sign と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Adobe Sign を統合すると、次のことができます。

* Adobe Sign にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Adobe Sign に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。
 
* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Adobe Sign でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Adobe Sign では、**SP** によって開始される SSO がサポートされます

## <a name="add-adobe-sign-from-the-gallery"></a>ギャラリーからの Adobe Sign の追加

Azure AD への Adobe Sign の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Adobe Sign を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Adobe Sign**」と入力します。
1. 結果のパネルから **[Adobe Sign]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-adobe-sign"></a>Adobe Sign の Azure AD SSO の構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Adobe Sign で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Adobe Sign 内の関連ユーザー間にリンク関係が確立されている必要があります。

Adobe Sign で Azure AD のシングル サインオンを構成してテストするには、次の手順を実行する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Adobe Sign の SSO の構成](#configure-adobe-sign-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Adobe Sign のテスト ユーザーの作成](#create-adobe-sign-test-user)** - Adobe Sign で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Adobe Sign で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. Azure Portal の **Adobe Sign** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

1. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

1. **[SAML でシングル サインオンをセットアップします]** ページで、鉛筆アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.echosign.com/`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.echosign.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[Adobe Sign クライアント サポート チーム](https://helpx.adobe.com/in/contact/support.html)に問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Adobe Sign の設定]** セクションで、要件どおりの適切な URL をコピーします。

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

このセクションでは、B.Simon に Adobe Sign へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Adobe Sign]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="configure-adobe-sign-sso"></a>Adobe Sign の SSO の構成

1. Adobe Sign 許可リストに自分のドメインを追加するには、構成する前に、[Adobe Sign クライアント サポート チーム](https://helpx.adobe.com/in/contact/support.html)に連絡してください。 ドメインの追加方法は次のとおりです。

    a. [Adobe Sign クライアント サポート チーム](https://helpx.adobe.com/in/contact/support.html)からランダムに生成されたトークンが送信されます。 ドメインの場合、トークンは **adobe-sign-verification= xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx** のようになります。

    b. DNS テキスト レコードで検証トークンを発行し、[Adobe Sign クライアント サポート チーム](https://helpx.adobe.com/in/contact/support.html)に通知します。

    > [!NOTE]
    > これには数日以上かかる場合があります。 DNS への反映の遅延は、DNS で公開された値が 1 時間以上表示されない可能性があることを意味することに注意してください。 DNS テキスト レコードでこのトークンを発行する方法については、通常、組織の IT 管理者が熟知しています。

    c. トークンが発行された後に、サポート チケットを通じて [Adobe Sign クライアント サポート チーム](https://helpx.adobe.com/in/contact/support.html)に通知すると、サポート チームがドメインを検証し、それをユーザーのアカウントに追加します。

    d. 通常、DNS レコードでトークンを発行する手順は次のとおりです。

    * ドメイン アカウントにサインインする
    * DNS レコードを更新するためのページを検索する。 このページは、DNS 管理、ネーム サーバー管理、または詳細設定と呼ばれる場合があります。
    * 自分のドメインの TXT レコードを検索する。
    * Adobe から提供された完全なトークン値を使用して TXT レコードを追加する。
    * 変更を保存します。

1. 別の Web ブラウザーのウィンドウで、管理者として Adobe Sign 企業サイトにサインインします。

1. [SAML] メニューで、 **[アカウント設定]**  >  **[SAML 設定]** を選択します。

    ![Adobe Sign の [SAML 設定] ページのスクリーンショット](./media/adobe-echosign-tutorial/settings.png "Account")

1. **[SAML 設定]** セクションで、次の手順に従います。

    ![[SAML Mandatory] を含む SAML の設定が強調表示されているスクリーンショット。](./media/adobe-echosign-tutorial/saml1.png "SAML 設定")

   ![[SAML 設定] のスクリーンショット](./media/adobe-echosign-tutorial/saml.png "SAML 設定")

   a. **[SAML モード]** で **[SAML Mandatory]** を選択します。

   b. **[Echosign 資格情報を使用して、Echosign アカウント管理者のログインを許可する]** を選択します。

   c. **[ユーザーの作成]** で、 **[SAML を使用して認証されたユーザーを自動的に追加]** を選択します。

   d. **[Idp Entity ID]\(IDP エンティティ ID\)** ボックスに、Azure portal からコピーした **Azure AD 識別子** を貼り付けます。

   e. **[Idp Login URL]\(IDP ログイン URL\)** ボックスに、Azure portal からコピーした **ログイン URL** を貼り付けます。

   f. **[Idp Logout URL]\(IDP ログアウト URL\)** ボックスに、Azure portal からコピーした **ログアウト URL** を貼り付けます。

   g. ダウンロードした **証明書 (Base64)** ファイルをメモ帳で開きます。 その内容をクリップボードにコピーし、 **[IdP Certificate]\(IdP 証明書\)** ボックスに貼り付けます。

   h. **[変更の保存]** を選択します。

### <a name="create-adobe-sign-test-user"></a>Adobe Sign のテスト ユーザーの作成

Azure AD ユーザーが Adobe Sign にサインインできるようにするには、ユーザーを Adobe Sign にプロビジョニングする必要があります。 この設定は手動で行います。

>[!NOTE]
>他の Adobe Sign ユーザー アカウント作成ツールまたは Adobe Sign から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。 

1. **Adobe Sign** 企業サイトに管理者としてサインインします。

2. 上部のメニューで、 **[アカウント]** を選択します。 次に、左側のウィンドウで **[ユーザーとグループ]**  >  **[新規ユーザーを作成]** を選択します。

    ![Adobe Sign 企業サイトのスクリーンショット ([アカウント]、[ユーザーとグループ]、[新規ユーザーを作成] が強調表示されています)](./media/adobe-echosign-tutorial/account.png "Account")

3. **[新しいユーザーの作成]** セクションで、次の手順に従います。

    ![[新しいユーザーの作成] セクションのスクリーンショット](./media/adobe-echosign-tutorial/user.png "[Create User]")

    a. 関連するテキスト ボックスに、プロビジョニングする有効な Azure AD アカウントの **[メール アドレス]** 、 **[名]** 、 **[姓]** を入力します。

    b. **[ユーザーを作成]** を選択します。

>[!NOTE]
>Azure Active Directory のアカウント所有者には、アカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。 

### <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Adobe Sign のサインオン URL にリダイレクトされます。 

* Adobe Sign のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Adobe Sign] タイルをクリックすると、SSO を設定した Adobe Sign に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Adobe Sign を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。