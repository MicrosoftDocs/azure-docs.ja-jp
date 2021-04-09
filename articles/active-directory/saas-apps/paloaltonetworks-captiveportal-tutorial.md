---
title: チュートリアル:Azure Active Directory と Palo Alto Networks Captive Portal の統合 | Microsoft Docs
description: Azure Active Directory と Palo Alto Networks Captive Portal の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: 1b9c9182b8ffc6e9ea2e05fd4863783f111f81dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98727151"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>チュートリアル:Azure Active Directory と Palo Alto Networks Captive Portal の統合

このチュートリアルでは、Palo Alto Networks Captive Portal と Azure Active Directory (Azure AD) を統合する方法について説明します。
Palo Alto Networks Captive Portal と Azure AD の統合には、次のメリットがあります。

* Palo Alto Networks Captive Portal にアクセスするユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで Palo Alto Networks Captive Portal に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

## <a name="prerequisites"></a>前提条件

Azure AD と Palo Alto Networks Captive Portal を統合するには、次のものが必要です。

* Azure Active Directory のサブスクリプション。 Azure AD をお持ちでない場合には、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。
* シングル サインオン (SSO) に対応した Palo Alto Networks Captive Portal のサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Palo Alto Networks Captive Portal では、**IDP** によって開始される SSO がサポートされます
* Palo Alto Networks Captive Portal では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-palo-alto-networks-captive-portal-from-the-gallery"></a>ギャラリーからの Palo Alto Networks Captive Portal の追加

Azure AD への Palo Alto Networks Captive Portal の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Palo Alto Networks Captive Portal を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Palo Alto Networks Captive Portal**」と入力します。
1. 結果パネルで **[Palo Alto Networks Captive Portal]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト

このセクションでは、**B.Simon** というテスト ユーザーに基づいて、Palo Alto Networks Captive Portal で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Palo Alto Networks Captive Portal 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Palo Alto Networks Captive Portal で Azure AD シングル サインオンを構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - ユーザー B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるように設定します。
2. **[Palo Alto Networks Captive Portal SSO の構成](#configure-palo-alto-networks-captive-portal-sso)** - アプリケーションでシングル サインオン設定を構成します。
    * **[Palo Alto Networks Captive Portal テスト ユーザーの作成](#create-a-palo-alto-networks-captive-portal-test-user)** - Palo Alto Networks Captive Portal で B.Simon に対応するユーザーを作成し、Azure AD の対応するユーザーにリンクします。
3. **[SSO のテスト](#test-sso)** - 構成が機能することを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Palo Alto Networks Captive Portal** アプリケーション統合ページで、 **[管理]** セクションを探し、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** ウィンドウで、次の手順を実行します。

   1. **[識別子]** に、`https://<customer_firewall_host_name>/SAML20/SP` の形式で URL を入力します。

   2. **[応答 URL]** に、`https://<customer_firewall_host_name>/SAML20/SP/ACS` の形式で URL を入力します。

      > [!NOTE]
      > この手順に示したプレースホルダーの値は、実際の識別子と応答 URL に置き換えてください。 実際の値を取得するには、[Palo Alto Networks Captive Portal のクライアント サポート チーム](https://support.paloaltonetworks.com/support)にご連絡ください。

5. **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** の隣にある **[ダウンロード]** を選択します。 ダウンロードしたファイルを、お使いのコンピューターに保存します。

    ![フェデレーション メタデータ XML のダウンロード リンク](common/metadataxml.png)

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

このセクションでは、B.Simon に Palo Alto Networks Captive Portal へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Palo Alto Networks Captive Portal]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-palo-alto-networks-captive-portal-sso"></a>Palo Alto Networks Captive Portal の SSO の構成

次に、Palo Alto Networks Captive Portal 内でシングル サインオンを設定していきます。

1. 別の Web ブラウザー ウィンドウで、Palo Alto Networks の Web サイトに管理者としてログインします。

2. **[Device]\(デバイス\)** タブを選択します。

    ![Palo Alto Networks の Web サイトの [Device]\(デバイス\) タブ](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. メニューで **[SAML Identity Provider]\(SAML ID プロバイダー\)** を選択し、 **[Import]\(インポート\)** を選択します。

    ![[Import]\(インポート\) ボタン](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. **[SAML Identify Provider Server Profile Import]\(SAML ID プロバイダー サーバー プロファイルのインポート\)** ダイアログ ボックスで、次の手順を実行します。

    ![Palo Alto Networks のシングル サインオンの構成](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. **[Profile Name]\(プロファイル名\)** に、**AzureAD-CaptivePortal** のような名前を入力します。
    
    2. **[Identity Provider Metadata]\(ID プロバイダーのメタデータ\)** の隣にある **[Browse]\(参照\)** を選択します。 Azure portal からダウンロードした metadata.xml ファイルを選択します。
    
    3. **[OK]** を選択します。

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Palo Alto Networks Captive Portal のテスト ユーザーの作成

次は、Palo Alto Networks Captive Portal で *Britta Simon* というユーザーを作成します。 Palo Alto Networks Captive Portal では、Just-In-Time のユーザー プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、何も行う必要はありません。 Palo Alto Networks Captive Portal にユーザーがまだ存在していない場合は、認証後に新しいユーザーが作成されます。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[Palo Alto Networks Captive Portal のクライアント サポート チーム](https://support.paloaltonetworks.com/support)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した Palo Alto Networks Captive Portal に自動的にサインインされます

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Palo Alto Networks Captive Portal] タイルをクリックすると、SSO を設定した Palo Alto Networks Captive Portal に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Palo Alto Networks Captive Portal を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。