---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Meraki Dashboard の統合 | Microsoft Docs
description: Azure Active Directory と Meraki Dashboard の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: 74009c7e7f2ad28655c9c5322a063a17da96e0c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493924"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-meraki-dashboard"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Meraki Dashboard の統合

このチュートリアルでは、Meraki Dashboard と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Meraki Dashboard を統合すると、次のことができます。

* Meraki Dashboard にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Meraki Dashboard に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Meraki Dashboard でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Meraki Dashboard では、**IDP** Initiated SSO がサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-meraki-dashboard-from-the-gallery"></a>ギャラリーからの Meraki Dashboard の追加

Azure AD への Meraki Dashboard の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Meraki Dashboard を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Meraki Dashboard**」と入力します。
1. 結果のパネルから **[Meraki Dashboard]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-meraki-dashboard"></a>Meraki Dashboard の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Meraki Dashboard に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Meraki Dashboard の関連ユーザーとの間にリンク関係を確立する必要があります。

Meraki Dashboard に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Meraki Dashboard SSO の構成](#configure-meraki-dashboard-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Meraki Dashboard のテスト ユーザーの作成](#create-meraki-dashboard-test-user)** - Meraki Dashboard で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Meraki Dashboard** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。
     
    **[応答 URL]** ボックスに、`https://n27.meraki.com/saml/login/m9ZEgb/< UNIQUE ID >` のパターンを使用して URL を入力します。

    > [!NOTE]
    > 応答 URL 値は、実際の値ではありません。 この値を実際の応答 URL の値で更新します。これについては、このチュートリアルの後半で説明します。

1. **[保存]** ボタンをクリックします。

1. Meraki Dashboard アプリケーションは特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、Meraki Dashboard アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前 | ソース属性|
    | ---------------| --------- |
    | `https://dashboard.meraki.com/saml/attributes/username` | user.userprincipalname |
    | `https://dashboard.meraki.com/saml/attributes/role` | user.assignedroles |

    > [!NOTE]
    > Azure AD でロールを構成する方法については、[こちら](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)を参照してください。

1. **[SAML 署名証明書]** セクションで **[編集]** ボタンをクリックして、 **[SAML 署名証明書]** ダイアログを開きます。

    ![SAML 署名証明書の編集](common/edit-certificate.png)

1. **[SAML 署名証明書]** セクションで **[Thumbprint Value]\(拇印の値\)** をコピーし、お使いのコンピューターに保存します。 この値にはコロンを含めることで、Meraki Dashboard で認識されるように変換する必要があります。 たとえば、Azure の拇印が `C2569F50A4AAEDBB8E` の場合、後で Meraki Dashboard で使用できるようにするには `C2:56:9F:50:A4:AA:ED:BB:8E` に変更する必要があります。

    ![[Thumbprint]\(拇印\) の値をコピーする](common/copy-thumbprint.png)

1. **[Meraki Dashboard のセットアップ]** セクションで、[ログアウト URL] の値をコピーしてそれを自分のコンピューターに保存します。

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

このセクションでは、B.Simon に Meraki Dashboard へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Meraki Dashboard]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。

    ![ユーザー ロール](./media/meraki-dashboard-tutorial/user-role.png)

    > [!NOTE]
    > **[ロールの選択]** オプションは無効になります。選択したユーザーの既定のロールは "ユーザー" です。

1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-meraki-dashboard-sso"></a>Meraki Dashboard SSO の構成

1. Meraki Dashboard 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Meraki Dashboard のセットアップ]** をクリックすると、Meraki Dashboard アプリケーションに移動します。 そこから、管理者の資格情報を入力して Meraki Dashboard にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 7 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Meraki Dashboard を手動でセットアップする場合は、別の Web ブラウザー ウィンドウで、Meraki Dashboard 企業サイトに管理者としてサインインします。

1. **[Organization]\(組織\)**  ->  **[Settings]\(設定\)** に移動します。

    ![Meraki Dashboard の [Settings]\(設定\) タブ](./media/meraki-dashboard-tutorial/configure-1.png)

1. [Authentication]\(認証\) で、 **[SAML SSO]** を **[SAML SSO enabled]\(SAML SSO が有効\)** に変更します。

    ![Meraki Dashboard の [Authentication]\(認証\)](./media/meraki-dashboard-tutorial/configure-2.png)

1. **[Add a SAML IdP]\(SAML IdP の追加\)** をクリックします。

    ![Meraki Dashboard の [Add a SAML IdP]\(SAML IdP の追加\)](./media/meraki-dashboard-tutorial/configure-3.png)

1. 前のセクションの手順 9. で説明したように、Azure portal からコピーして、指定した形式に変換した **拇印** の値を、 **[X.590 cert SHA1 fingerprint]\(590 証明書 SHA1 拇印\)** テキストボックスに貼り付けます。 **[保存]** をクリックします。 保存すると、コンシューマー URL が表示されます。 [Consumer URL]\(コンシューマー URL\) の値をコピーして、Azure portal の **[基本的な SAML 構成] セクション** の **[応答 URL]** ボックスに貼り付けます。

    ![Meraki Dashboard の構成](./media/meraki-dashboard-tutorial/configure-4.png)

### <a name="create-meraki-dashboard-test-user"></a>Meraki Dashboard のテスト ユーザーの作成

1. 別の Web ブラウザー ウィンドウで、管理者として Meraki Dashboard にサインインします。

1. **[Organization]\(組織\)**  ->  **[Administrators]\(管理者\)** に移動します。

    ![Meraki Dashboard [Administrators]\(管理者\)](./media/meraki-dashboard-tutorial/user-1.png)

1. [SAML administrator roles]\(SAML 管理者ロール\) セクションで、 **[Add SAML role]\(SAML ロールの追加\)** をクリックします。

    ![Meraki Dashboard の [Add SAML role]\(SAML ロールの追加\) ボタン](./media/meraki-dashboard-tutorial/user-2.png)

1. ロール **meraki_full_admin** を入力し、 **[Organization access]\(組織アクセス\)** を **[Full]\(完全\)** としてマークします。次に、 **[Create role]\(ロールの作成\)** をクリックします。 **meraki_readonly_admin** に対してこの手順を繰り返します。今回は **[Organization access]\(組織アクセス\)** を **[Read-only]\(読み取り専用\)** ボックスとしてマークします。
 
    ![Meraki Dashboard のユーザーの作成](./media/meraki-dashboard-tutorial/user-3.png)

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した Meraki Dashboard に自動的にサインインされます

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Meraki Dashboard] タイルをクリックすると、SSO を設定した Meraki Dashboard に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Meraki Dashboard を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
