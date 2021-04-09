---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Hightail の統合 | Microsoft Docs
description: Azure Active Directory と Hightail の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/06/2020
ms.author: jeedes
ms.openlocfilehash: 29f2610c371ecd567a18dc70e51b3eb34754d8ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96180865"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hightail"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Hightail の統合

このチュートリアルでは、Hightail と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Hightail を統合すると、次のことができます。

* Hightail にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して Hightail に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Hightail でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Hightail では、**SP と IDP** によって開始される SSO がサポートされます
* Hightail では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-hightail-from-the-gallery"></a>ギャラリーから Hightail を追加する

Azure AD への Hightail の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Hightail を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Hightail**」と入力します。
1. 結果ウィンドウで **[Hightail]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-hightail"></a>Hightail の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Hightail に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Hightail の関連ユーザーとの間にリンク関係を確立する必要があります。

Hightail に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Hightail SSO の構成](#configure-hightail-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[Hightail テスト ユーザーの作成](#create-hightail-test-user)** - Hightail で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Hightail** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子 (エンティティ ID)]** ボックスに `https://api.spaces.hightail.com/api/v1/saml/consumer` という URL を入力します。
    
    b. **[応答 URL]** ボックスに、URL として「`https://api.spaces.hightail.com/api/v1/saml/consumer`」と入力します。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** テキスト ボックスに、URL として「`https://spaces.hightail.com/corp-login`」と入力します。

1. Hightail アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、Hightail アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 | ソース属性|
    | -------- |-------- |
    | FirstName | User.givenname |
    | LastName | User.surname |
    | Email | User.mail |
    | UserIdentity | User.mail |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Hightail のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

> [!NOTE]
> Hightail アプリでシングル サインオンを構成する前に、電子メール ドメインを Hightail チームの許可リストに追加し、そのドメインを使用するすべてのユーザーがシングル サインオン機能を利用できるようにします。

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

このセクションでは、B.Simon に Hightail へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Hightail]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-hightail-sso"></a>Hightail SSO の構成

1. Hightail 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

1. ブラウザーに拡張機能を追加した後、 **[Hightail のセットアップ]** をクリックすると、Hightail アプリケーションに移動します。 そこから、管理者の資格情報を入力して Hightail にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

1. Hightail を手動で設定する場合は、別のブラウザー ウィンドウで **Hightail** 管理ポータルを開きます。

1. ページの右上隅の **ユーザー アイコン** をクリックします。 

    ![ユーザー アイコンを示すスクリーンショット。](./media/hightail-tutorial/configure1.png)

1. **[管理コンソールを表示]** タブをクリックします。

    ![[User]\(ユーザー\) の [View Admin Console]\(管理コンソールを表示\) ボタンを示すスクリーンショット。](./media/hightail-tutorial/configure2.png)

1. 上部のメニューで、 **[SAML]** タブをクリックし、次の手順を実行します。

    ![[Login U R L]\(ログイン U R L\) および [SAML Certificate]\(SAML 証明書\) を入力できる [SAML] タブを示すスクリーンショット。](./media/hightail-tutorial/configure3.png)

    a. **[ログイン URL]** テキスト ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    b. Azure portal からダウンロードした Base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーしてから、それを **[SAML Certificate]\(SAML 証明書\)** ボックスに貼り付けます。

    c. **[コピー]** をクリックして、インスタンスの SAML コンシューマー URL をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** ボックスに貼り付けます。

    d. **[構成の保存]** をクリックします。

### <a name="create-hightail-test-user"></a>Hightail テスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Hightail に作成します。 Hightail では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Hightail にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[Hightail サポート チーム](mailto:support@hightail.com)に問い合わせてください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Hightail のサインオン URL にリダイレクトされます。  

* Hightail のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Hightail に自動的にサインインされます 

また、Microsoft アクセス パネルを使用して、任意のモードでアプリケーションをテストすることもできます。 アクセス パネルで Hightail タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Hightail に自動的にサインインされます。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。


## <a name="next-steps"></a>次のステップ

Hightail を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。