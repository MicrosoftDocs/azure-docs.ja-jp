---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Cisco AnyConnect の統合 | Microsoft Docs
description: Azure Active Directory と Cisco Cloud の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: a89ab7f2304fa51d3e8c7a968d445c9b40a457a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92456090"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-anyconnect"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Cisco AnyConnect の統合

このチュートリアルでは、Cisco AnyConnect を Azure Active Directory (Azure AD) と統合する方法について説明します。 Cisco AnyConnect を Azure AD と統合すると、次のことができます。

* Cisco AnyConnect にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Cisco AnyConnect に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Cisco AnyConnect でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Cisco AnyConnect では、**IDP** Initiated SSO がサポートされます

## <a name="adding-cisco-anyconnect-from-the-gallery"></a>ギャラリーからの Cisco AnyConnect の追加

Azure AD への Cisco AnyConnect の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Cisco AnyConnect を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Cisco AnyConnect**」と入力します。
1. 結果のパネルから **[Cisco AnyConnect]** を選択し、そのアプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-cisco-anyconnect"></a>Cisco AnyConnect の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Cisco AnyConnect に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Cisco AnyConnect の関連ユーザーとの間にリンク関係を確立する必要があります。

Cisco AnyConnect に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Cisco AnyConnect SSO の構成](#configure-cisco-anyconnect-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Cisco AnyConnect テスト ユーザーの作成](#create-cisco-anyconnect-test-user)** - Cisco AnyConnect で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Cisco AnyConnect** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[SAML でシングル サインオンをセットアップします]** ページで、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`< YOUR CISCO ANYCONNECT VPN VALUE >` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`< YOUR CISCO ANYCONNECT VPN VALUE >` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[Cisco AnyConnect クライアント サポート チーム](https://www.cisco.com/c/en/us/support/index.html)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML によるシングル サインオンのセットアップ]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードしてお使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Cisco AnyConnect のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

> [!NOTE]
> サーバーの複数の TGT をオンボードする場合は、ギャラリーから Cisco AnyConnect アプリケーションの複数のインスタンスを追加する必要があります。 また、これらすべてのアプリケーション インスタンスについて、Azure AD に独自の証明書をアップロードすることもできます。 このようにアプリケーションに同じ証明書を使用できる一方で、アプリケーションごとに異なる識別子と応答 URL を構成することができます。

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

このセクションでは、B.Simon に Cisco AnyConnect へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Cisco AnyConnect]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-cisco-anyconnect-sso"></a>Cisco AnyConnect SSO の構成

1. これは、最初に CLI で実行します。ASDM のチュートリアルは別のタイミングで実行する可能性があります。

1. VPN アプライアンスに接続します。9.8 code train を実行している ASA を使用することになり、VPN クライアントは 4.6 以降になります。

1. 最初に、Trustpoint を作成し、SAML 証明書をインポートします。

   ```
    config t

    crypto ca trustpoint AzureAD-AC-SAML
      revocation-check none
      no id-usage
      enrollment terminal
      no ca-check
    crypto ca authenticate AzureAD-AC-SAML
    -----BEGIN CERTIFICATE-----
    …
    PEM Certificate Text from download goes here
    …
    -----END CERTIFICATE-----
    quit
   ```

1. 次のコマンドを実行すると、SAML IdP がプロビジョニングされます。

   ```
    webvpn
    saml idp https://sts.windows.net/xxxxxxxxxxxxx/ (This is your Azure AD Identifier from the Set up Cisco AnyConnect section in the Azure portal)
    url sign-in https://login.microsoftonline.com/xxxxxxxxxxxxxxxxxxxxxx/saml2 (This is your Login URL from the Set up Cisco AnyConnect section in the Azure portal)
    url sign-out https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0 (This is Logout URL from the Set up Cisco AnyConnect section in the Azure portal)
    trustpoint idp AzureAD-AC-SAML
    trustpoint sp (Trustpoint for SAML Requests - you can use your existing external cert here)
    no force re-authentication
    no signature
    base-url https://my.asa.com
    ```

1. これで、VPN トンネル構成に SAML 認証を適用できるようになります。

    ```
    tunnel-group AC-SAML webvpn-attributes
      saml identity-provider https://sts.windows.net/xxxxxxxxxxxxx/
      authentication saml
    end

    write mem
    ```

    > [!NOTE]
    > SAML IdP の構成を伴う機能があります。IdP の構成に変更を加えた場合は、変更を有効にするために、トンネル グループから SAML ID プロバイダーの構成を削除し、再度適用する必要があります。

### <a name="create-cisco-anyconnect-test-user"></a>Cisco AnyConnect テスト ユーザーの作成

このセクションでは、Cisco AnyConnect で Britta Simon というユーザーを作成します。 [Cisco AnyConnect サポート チーム](https://www.cisco.com/c/en/us/support/index.html)と協力して、Cisco AnyConnect プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した Cisco AnyConnect に自動的にサインインされるはずです
* Microsoft アクセス パネルを使用することができます。 アクセス パネルで [Cisco AnyConnect] タイルをクリックすると、SSO を設定した Cisco AnyConnect に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

Cisco AnyConnect を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。