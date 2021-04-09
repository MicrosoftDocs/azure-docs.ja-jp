---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と SignalFx の統合 | Microsoft Docs
description: Azure Active Directory と SignalFx の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/24/2020
ms.author: jeedes
ms.openlocfilehash: 1fbc42864761360d252ed62cea1aef6f2937b599
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516071"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-signalfx"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と SignalFx の統合

このチュートリアルでは、SignalFx と Azure Active Directory (Azure AD) を統合する方法について説明します。 SignalFx と Azure AD を統合すると、次のことができるようになります。

* SignalFx にアクセスできるユーザーを Azure AD から制御する。
* ユーザーが自分の Azure AD アカウントを使用して SignalFx に自動的にサインインできるようにする。
* 自分のアカウントを 1 か所 (Azure portal) で管理する。

SaaS アプリケーションと Azure AD の統合の詳細については、[Azure Active Directory でのアプリケーション アクセスとシングル サインオンの概要](../manage-apps/what-is-single-sign-on.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

開始する前に、以下が必要になります。

* Azure AD サブスクリプション
    * サブスクリプションをお持ちでない場合は、[こちらで無料アカウント](https://azure.microsoft.com/free/)を入手できます。
* SignalFx でのシングル サインオン (SSO) が有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* SignalFx では、**IDP** によって開始される SSO がサポートされます
* SignalFx では、**Just In Time** ユーザー プロビジョニングがサポートされます
* SignalFx を構成したら、組織の機密データの流出と侵入をリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="step-1-add-the-signalfx-application-in-azure"></a>手順 1:SignalFx アプリケーションを Azure に追加する

次の手順を使用して、マネージド SaaS アプリのリストに SignalFx アプリケーションを追加します。

1. [Azure Portal](https://portal.azure.com) にログインします。
1. 左側のナビゲーション ウィンドウで **[Azure Active Directory]** を選択します。
1. **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションの検索ボックスで、「**SignalFx**」と入力して選択します。
     * アプリケーションがテナントに追加されるまでに数分待つ場合があります。
1. Azure portal は開いたままで、新しい Web タブを開きます。    

## <a name="step-2-begin-signalfx-sso-configuration"></a>手順 2:SignalFx SSO の構成を開始する

次の手順を使用して、SignalFx SSO の構成プロセスを開始します。

1. 新しく開いたタブで、SignalFx UI にアクセスしてログインします。 
1. トップ メニューの **[Integrations]\(統合\)** をクリックします。 
1. 検索フィールドに「**Azure Active Directory**」と入力して選択します。
1. **[Create New Integration]\(新しい統合の作成\)** をクリックします。
1. **[Name]\(名前\)** に、ユーザーにとってわかりやすく認識しやすい名前を入力します。
1. **[Show on login page]\(ログイン ページに表示する\)** をマークします。
    * この機能により、ユーザーがクリックできるカスタマイズされたボタンがログイン ページに表示されます。 
    * **[Name]\(名前\)** に入力した情報がボタンに表示されます。 したがって、ユーザーが認識しやすい **名前** を入力してください。 
    * このオプションが機能するのは、SignalFx アプリケーションにカスタム サブドメイン (**yourcompanyname.signalfx.com** など) を使用している場合のみです。 カスタム サブドメインを取得するには、SignalFx サポートにお問い合わせください。 
1. **[Integration ID]\(統合 ID\)** をコピーします。 この情報は後の手順で必要になります。 
1. SignalFx UI は開いたままにしておきます。 

## <a name="step-3-configure-azure-ad-sso"></a>手順 3:Azure AD SSO の構成

次の手順を使用して、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) に戻り、**SignalFx** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** のペン (編集) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[SAML によるシングル サインオンのセットアップ]** ページで、次のフィールドに情報を入力します。 

    a. **[ID]** に URL 「`https://api.<realm>.signalfx.com/v1/saml/metadata`」を入力し、`<realm>` を実際の SignalFx 領域に置き換えます。 

    b. **[応答 URL]** に URL 「`https://api.<realm>.signalfx.com/v1/saml/acs/<integration ID>`」を入力し、`<realm>` を実際の SignalFx 領域に置き換えるほか、`<integration ID>` を、先ほど SignalFx UI からコピーした **統合 ID** に置き換えます。

1. SignalFx アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 
    
1. 次の要求が、Active Directory に設定されているソース属性と対応していることを確認します。 

    | 名前 |  ソース属性|
    | ------------------- | -------------------- |
    | User.FirstName  | User.givenname |
    | User.email  | User.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | User.surname    |

    > [!NOTE]
    > このプロセスでは、Active Directory が少なくとも 1 つの検証済みカスタム ドメインを使用して構成されていることと、そのドメインのメール アカウントにアクセスできることが必要です。 この構成に確信がない場合や支援が必要な場合は、SignalFx サポートにお問い合わせください。  

1. **[SAML によるシングル サインオンのセットアップ]** ページの **[SAML 署名証明書]** セクションで **[証明書 (Base64)]** を探し、 **[ダウンロード]** を選択します。 証明書をダウンロードして、コンピューターに保存します。 次に、 **[アプリのフェデレーション メタデータ URL]** の値をコピーします。この情報は、SignalFx UI の後続の手順で必要になります。 

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[SignalFx のセットアップ]** セクションで、 **[Azure AD 識別子]** の値をコピーします。 この情報は、SignalFx UI の後続の手順で必要になります。 

## <a name="step-4-create-an-azure-ad-test-user"></a>手順 4:Azure AD のテスト ユーザーの作成

次の手順を使用して、Azure portal で **B.Simon** というテスト ユーザーを作成します。

1. Azure portal の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. ページの上部にある **[新しいユーザー]** を選択します。
1. **ユーザー** のプロパティで:
   1. **[ユーザー名]** に `username@companydomain.extension` を入力します (例: `b.simon@contoso.com`)。
   1. **名前** に`B.Simon`を入力します。
   1. **[Show password]\(パスワードを表示\)** をオンにし、 **[パスワード]** に表示される値をコピーします。 この情報は、この統合をテストするために後続の手順で必要になります。 
   1. **Create** をクリックしてください。

## <a name="step-5-assign-the-azure-ad-test-user"></a>手順 5:Azure AD テスト ユーザーの割り当て

次の手順を使用して、テスト ユーザーが SignalFx に Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[SignalFx]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログ ボックスの **[ユーザー]** リストから **[B.Simon]** を選択し、ページの下部にある **[選択]** をクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、ユーザーに適したロールを一覧から選択し、ページの下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログ ボックスで、 **[割り当て]** をクリックします。

## <a name="step-6-complete-the-signalfx-sso-configuration"></a>手順 6:SignalFx SSO の構成を完了する 

1. 前のタブを開き、SignalFx UI に戻って現在の Azure Active Directory 統合ページを表示します。 
1. **[Certificate (Base64)]\(証明書 (Base64)\)** の横にある **[Upload File]\(ファイルのアップロード\)** をクリックし、以前に Azure portal からダウンロードした **Base64 でエンコードされた証明書** ファイルを検索します。
1. **[Azure AD Identifier]\(Azure AD 識別子\)** の横に、先ほど Azure portal からコピーした **Azure AD 識別子** の値を貼り付けます。 
1. **[Federation Metadata URL]\(フェデレーション メタデータ URL\)** の横に、先ほど Azure portal からコピーした **アプリのフェデレーション メタデータ URL** の値を貼り付けます。 
1. **[保存]** をクリックします。

## <a name="step-7-test-sso"></a>手順 7:SSO のテスト

SSO のテスト方法および SignalFx への初回ログインに対する期待については、次の情報を確認してください。 

### <a name="test-logins"></a>ログインのテスト

* ログインをテストするには、プライベート (シークレット) ウィンドウを使用する必要があります。または、Azure portal からログアウトしてもかまいません。 そうしないと、アプリケーションを構成したユーザーの Cookie が妨げとなり、テスト ユーザーでの正常なログインができなくなります。

* 新しいテスト ユーザーが初めてログインすると、Azure によってパスワードの変更が強制されます。 この場合は SSO ログイン プロセスが完了しないため、テスト ユーザーには Azure portal が表示されます。 トラブルシューティングを行うには、テスト ユーザーが自分のパスワードを変更し、SignalFx のログイン ページまたはアクセス パネルに移動して再試行する必要があります。
    * アクセス パネル上で [SignalFx] タイルをクリックすると、自動的に SignalFx にログインします。 
        * アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

* SignalFx アプリケーションにはアクセス パネルから、または組織に割り当てられたカスタム ログイン ページを介してアクセスできます。 テスト ユーザーは、そのいずれかの場所から統合をテストする必要があります。
    * テスト ユーザーは、先ほどこのプロセスで作成された、**b.simon\@contoso.com** の資格情報を使用できます。

### <a name="first-time-logins"></a>初回ログイン

* ユーザーが SAML SSO から初めて SignalFx にログインすると、リンクが記載された SignalFx のメールがそのユーザーに送信されます。 ユーザーは認証のためにそのリンクをクリックする必要があります。 このメール検証は、初めて使用するユーザーに対してのみ実施されます。 

* SignalFx は **Just-In-Time** のユーザー作成をサポートしています。つまり、SignalFx にユーザーが存在しない場合、初回ログイン試行時にそのユーザーのアカウントが作成されます。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Microsoft Cloud App Security におけるセッション制御とは](/cloud-app-security/proxy-intro-aad)

- [Azure AD で SignalFx を試す](https://aad.portal.azure.com/)