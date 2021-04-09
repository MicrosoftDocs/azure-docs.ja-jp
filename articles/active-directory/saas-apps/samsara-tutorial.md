---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Samsara の統合 | Microsoft Docs
description: Azure Active Directory と Samsara の間にシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: jeedes
ms.openlocfilehash: 9512287f656c6c64399cb8749b7451a5a780bba8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92677630"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-samsara"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Samsara の統合

このチュートリアルでは、Samsara と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Samsara を統合すると、次のことができます。

* Samsara にアクセスできる Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して Samsara に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Samsara でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Samsara では、**SP** と **IDP** によって開始される SSO がサポートされます
* Samsara では、**Just In Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-samsara-from-the-gallery"></a>ギャラリーからの Samsara の追加

Azure AD への Samsara の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Samsara を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)
    
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Samsara**」と入力します。

     ![結果一覧の OneTrust Privacy Management Software](common/search-new-app.png)

1. 結果のパネルから **[Samsara]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-samsara"></a>Samsara の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Samsara に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Samsara の関連ユーザーとの間にリンク関係が確立されている必要があります。

Samsara に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Samsara SSO の構成](#configure-samsara-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Samsara のテスト ユーザーの作成](#create-samsara-test-user)** - Samsara で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Samsara** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。

    ![シングル サインオン選択モード](common/select-saml-option.png)

1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** テキストボックスに、次のパターンを使用して URL を入力します。米国のクラウドのお客様の場合は `https://cloud.eu.samsara.com/signin/<ORGID>` ヨーロッパのクラウドのお客様の場合は `https://cloud.samsara.com/signin/<ORGID>`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`urn:auth0:samsara-dev:samlp-orgid-<ORGID>`

    c. **[応答 URL]** ボックスに、`https://samsara-dev.auth0.com/login/callback?connection=samlp-orgid-<ORGID>` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際のサインオン URL、応答 URL、識別子で更新してください。 これらの値を取得するには、[Samsara クライアント サポート チーム](mailto:support@samsara.com)に連絡してください。または、Samsara で、 **[設定]**  >  **[シングル サインオン]**  >  **[New SAML Connection]\(新しい SAML 接続\)** に移動して \<ORGID\> を入手してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Set up Samsara]\(Samsara のセットアップ\)** セクションで、 **[ログイン URL]** をコピーします。

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

このセクションでは、B.Simon に Samsara へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Samsara]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-samsara-sso"></a>Samsara SSO の構成

**Samsara** 側でシングル サインオンを構成するには、ダウンロードした **証明書 (Base64)** と Azure portal からコピーした **ログイン URL** を [Samsara サポート チーム](mailto:support@samsara.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-samsara-test-user"></a>Samsara テスト ユーザーの作成

このセクションでは、B.Simon というユーザーを Samsara に作成します。 Samsara では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ Samsara に存在しない場合は、組織の標準管理者 (車載カメラへのアクセス権なし) の既定のロールを使用して認証後に新しいユーザーが作成されます。 その後、Samsara で、必要に応じてユーザーのアクセス権を増減できます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

1. Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Samsara のサインオン URL にリダイレクトされます。 

2. Samsara のサインオン URL に直接移動し、そこからログイン フローを開始します。

3. Microsoft アクセス パネルを使用することができます。 アクセス パネルで [Samsara] タイルをクリックすると、Samsara サインオン URL にリダイレクトされます。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。


## <a name="next-steps"></a>次のステップ

Samsara を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。