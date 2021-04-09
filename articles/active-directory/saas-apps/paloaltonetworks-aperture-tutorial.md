---
title: 'チュートリアル: Azure Active Directory と Palo Alto Networks - Aperture の統合 | Microsoft Docs'
description: Azure Active Directory と Palo Alto Networks - Aperture の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 7b4c8ab55a9b2afc9c1a215236fa1276a630a5cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97963623"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>チュートリアル: Azure Active Directory と Palo Alto Networks - Aperture の統合

このチュートリアルでは、Palo Alto Networks - Aperture と Azure Active Directory (Azure AD) を統合する方法について説明します。
Palo Alto Networks - Aperture と Azure AD の統合には、次のメリットがあります。

* Palo Alto Networks - Aperture にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで Palo Alto Networks - Aperture に自動的にサインイン (シングル サインオン) できるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

## <a name="prerequisites"></a>前提条件

Azure AD と Palo Alto Networks - Aperture の統合を構成するには、次のアイテムが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Palo Alto Networks - Aperture でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Palo Alto Networks - Aperture では、**SP** と **IDP** によって開始される SSO がサポートされます

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>ギャラリーからの Palo Alto Networks - Aperture の追加

Azure AD への Palo Alto Networks - Aperture の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Palo Alto Networks - Aperture を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Palo Alto Networks - Aperture**」と入力します。
1. 結果パネルで **[Palo Alto Networks - Aperture]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト

このセクションでは、**B.Simon** というテスト ユーザーに基づいて、Palo Alto Networks - Aperture で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Palo Alto Networks - Aperture 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Palo Alto Networks - Aperture で Azure AD シングル サインオンを構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[Configure Palo Alto Networks - Aperture の SSO の構成](#configure-palo-alto-networks---aperture-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[Palo Alto Networks - Aperture テスト ユーザーの作成](#create-palo-alto-networks---aperture-test-user)** - Palo Alto Networks - Aperture で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクします。
3. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Palo Alto Networks - Aperture** アプリケーション統合ページで、 **[管理]** セクションを探し、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[基本的な SAML 構成] のスクリーンショット。テキスト ボックスの [識別子] と [応答 URL] が強調表示され、[保存] アクションが選択されています。](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[Palo Alto Networks - Aperture のドメインと URL] のシングル サインオン情報 SP](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、`https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Palo Alto Networks - Aperture クライアント サポート チーム](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **[Palo Alto Networks - Aperture の設定]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Palo Alto Networks - Aperture へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Palo Alto Networks - Aperture]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-palo-alto-networks---aperture-sso"></a>Palo Alto Networks - Aperture の SSO の構成

1. 別の Web ブラウザー ウィンドウで、Palo Alto Networks - Aperture に管理者としてログインします。

2. 上部のメニュー バーで **[設定]** をクリックします。

    ![[設定] タブ](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

3. **[アプリケーション]** に移動し、メニューの左にある **[認証]** をクリックします。

    ![[認証] タブ](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
4. **[認証]** ページで、次の手順を実行します。
    
    ![[認証] タブ](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a。 **[Single Sign-On]\(シングル サインオン\)** フィールドの **[Enable Single Sign-On(Supported SSP Providers are Okta, Onelogin)]\(シングル サインオンを有効にする (サポートされている SSP プロバイダーは Okta、Onelogin)\)** をオンにします。

    b. **[Identity Provider ID]\(ID プロバイダー ID\)** ボックスに、Azure portal からコピーした **[Azure AD 識別子]** の値を貼り付けます。

    c. **[ファイルの選択]** をクリックして、Azure AD からダウンロードした証明書を **[ID プロバイダー証明書]** フィールドにアップロードします。

    d. **[Identity Provider SSO URL]\(ID プロバイダーの SSO URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    e. **[Aperture 情報]** セクションで IdP 情報を確認し、**[Aperture キー]** フィールドから証明書をダウンロードします。

    f. **[保存]** をクリックします。


### <a name="create-palo-alto-networks---aperture-test-user"></a>Palo Alto Networks - Aperture テスト ユーザーの作成

このセクションでは、Palo Alto Networks - Aperture で Britta Simon というユーザーを作成します。 [Palo Alto Networks - Aperture Client サポート チーム](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support)と協力し合い、Palo Alto Networks - Aperture プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Palo Alto Networks - Aperture のサインオン URL にリダイレクトされます。  

* Palo Alto Networks - Aperture のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Palo Alto Networks - Aperture に自動的にサインインされます 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Palo Alto Networks - Aperture] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Palo Alto Networks - Aperture に自動的にサインインされるはずです。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Palo Alto Networks - Aperture を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。