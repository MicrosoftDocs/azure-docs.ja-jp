---
title: チュートリアル:Azure Active Directory と Atlassian Cloud の統合 | Microsoft Docs
description: Azure Active Directory と Atlassian Cloud の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/02/2020
ms.author: jeedes
ms.openlocfilehash: fe83a012cac68f705bc9fabc7748f5a7c7c61bbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94330571"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>チュートリアル:Atlassian Cloud と Azure Active Directory の統合

このチュートリアルでは、Atlassian Cloud と Azure Active Directory (Azure AD) を統合する方法について説明します。 Atlassian Cloud と Azure AD を統合すると、次のことができます。

* Atlassian Cloud にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して Atlassian Cloud に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションをお持ちでない場合は、[ここ](https://azure.microsoft.com/pricing/free-trial/)から 1 か月間の無料試用版を入手できます。
* Atlassian Cloud でのシングル サインオン (SSO) が有効なサブスクリプション
* Atlassian Cloud 製品の Security Assertion Markup Language (SAML) シングル サインオンを有効にするには、Atlassian Access を設定する必要があります。 詳細については、「[Atlassian Access]( https://www.atlassian.com/enterprise/cloud/identity-manager)」を参照してください。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 

* Atlassian Cloud では、**SP と IDP** によって開始される SSO がサポートされます
* Atlassian Cloud では、[自動化されたユーザー プロビジョニングとプロビジョニング解除](atlassian-cloud-provisioning-tutorial.md)がサポートされます

## <a name="adding-atlassian-cloud-from-the-gallery"></a>ギャラリーからの Atlassian Cloud の追加

Azure AD への Atlassian Cloud の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Atlassian Cloud を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Atlassian Cloud**」と入力します。
1. 結果のパネルから **[Atlassian Cloud]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Atlassian Cloud に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Atlassian Cloud の関連ユーザーとの間にリンク関係を確立する必要があります。

Atlassian Cloud に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD と Atlassian Cloud の SSO の構成](#configure-azure-ad-sso)** - ユーザーが Atlassian Cloud に対して Azure AD ベースの SAML SSO を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Atlassian Cloud のテスト ユーザーの作成](#create-atlassian-cloud-test-user)** - Atlassian Cloud で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Atlassian Cloud 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **マイ アプリによるセキュリティで保護されたサインイン拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

1. ブラウザーに拡張機能を追加した後、 **[Setup Atlassian Cloud]\(Atlassian Cloud のセットアップ\)** をクリックすると、Atlassian Cloud アプリケーションに移動します。 そこから、管理者資格情報を提供して Atlassian Cloud にサインインします。 ブラウザー拡張機能により、アプリケーションが自動的に構成されます。

    ![セットアップの構成](common/setup-sso.png)

1. Atlassian Cloud を手動でセットアップする場合は、管理者として Atlassian Cloud 企業サイトにサインインし、次の手順を実行します。

1. まず、Atlassian 製品インスタンスに移動して、インスタンス URL をコピー、保存します。
   > [!NOTE]
   > URL は `https://<instancename>.atlassian.net` という形式になっている必要があります。

   ![インスタンス名](./media/atlassian-cloud-tutorial/get-atlassian-instance-name.png)

1. [Atlassian Admin Portal](https://admin.atlassian.com/) を開いて自分の組織名をクリックします。

   ![organization](./media/atlassian-cloud-tutorial/click-on-organization-in-atlassian-access.png)

1. シングル サインオンを構成する前に、ドメインを確認する必要があります。 詳細については、[Atlassian の「Domain verification」(ドメインの確認)](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) ドキュメントを参照してください。
1. Atlassian Admin Portal 画面の左側のドロワーから **[Security]\(セキュリティ\)** を選択します。

   ![security](./media/atlassian-cloud-tutorial/click-on-security-in-atlassian-access.png)

1. Atlassian Admin Portal の [Security]\(セキュリティ\) 画面で、左側のドロワーから **[SAML single sign on]\(SAML シングル サインオン\)** を選択します。

   ![SAML SSO](./media/atlassian-cloud-tutorial/click-on-saml-sso-in-atlassian-access-security.png)

1. **[Add SAML Configuration]\(SAML 構成の追加\)** をクリックします。このページは開いたままにしてください。

   ![SAML 構成の追加](./media/atlassian-cloud-tutorial/saml-configuration-in-atlassian-access-security-saml-sso.png)

   ![SAML 構成の追加 2](./media/atlassian-cloud-tutorial/add-saml-configuration.png)

1. Azure portal の **Atlassian Cloud** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[Set up single sign-on]\(シングル サインオンの設定\)** を選択します。

   ![SSO を設定する](./media/atlassian-cloud-tutorial/set-up-sso.png)

1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。

   ![Azure での SAML](./media/atlassian-cloud-tutorial/saml-in-azure.png)

1. **[SAML によるシングル サインオンのセットアップ]** ページで、下へスクロールして **[Atlassian Cloud のセットアップ]** に移動します。
   
   a. **[Configuration URLs]\(構成 URL\)** をクリックします。

   ![urls](./media/atlassian-cloud-tutorial/configuration-urls.png)
   
   b. Azure portal から **[Azure AD 識別子]** の値をコピーして、Atlassian の **[Identity Provider Entity ID]\(ID プロバイダーのエンティティ ID\)** ボックスに貼り付けます。
   
   c. Azure portal から **[ログイン URL]** の値をコピーし、Atlassian の **[Identity Provider SSO URL]\(ID プロバイダーの SSO URL\)** ボックスに貼り付けます。

   ![ID プロバイダーの SSO URL](./media/atlassian-cloud-tutorial/configuration-urls-azure.png)

   ![エンティティ id と ss](./media/atlassian-cloud-tutorial/entity-id-and-ss.png)

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

   ![署名証明書](./media/atlassian-cloud-tutorial/certificate.png)

   ![証明書 1](./media/atlassian-cloud-tutorial/certificate-1.png)

1. Atlassian に SAML 構成を **追加して保存** します。

1. アプリケーションを **IDP** 開始モードで構成する場合は、Azure の **[SAML によるシングル サインオンのセットアップ]** ページの **[基本的な SAML 構成]** セクションを編集して、Atlassian Admin Portal の **SAML シングル サインオン ページ** を開きます。

   a. Atlassian から **[SP Entity ID]\(SP エンティティ ID\)** の値をコピーして Azure の **[識別子 (エンティティ ID)]** ボックスに貼り付け、既定として設定します。
   
   b. Atlassian から **[SP Assertion Consumer Service URL]\(SP アサーション利用者サービス URL\)** の値をコピーして Azure の **[応答 URL (Assertion Consumer Service URL)]** ボックスに貼り付け、既定値として設定します。
   
   c. 手順 1. でコピーした **[インスタンス URL]** の値をコピーし、Azure の **[リレー状態]** ボックスに貼り付けます。

   ![URL をコピーする](./media/atlassian-cloud-tutorial/copy-urls.png)

   ![編集ボタン](./media/atlassian-cloud-tutorial/edit-button.png)

   ![URL の画像](./media/atlassian-cloud-tutorial/urls.png)
   
1. アプリケーションを **SP** 開始モードで構成する場合は、Azure の **[SAML によるシングル サインオンのセットアップ]** ページの **[基本的な SAML 構成]** セクションを編集します。 手順 1. の **[インスタンス URL]** をコピーし、Azure の **[サインオン URL]** ボックスに貼り付けます。

   ![URL の編集ボタン](./media/atlassian-cloud-tutorial/edit-button.png)

   ![サインオン URL](./media/atlassian-cloud-tutorial/sign-on-URL.png)
   
1. Atlassian Cloud アプリケーションでは、特定の形式の SAML アサーションを受け取るため、SAML トークン属性の構成にカスタム属性マッピングを追加する必要があります。 属性マッピングは、 **[編集]** アイコンをクリックして編集できます。 

   ![属性](./media/atlassian-cloud-tutorial/edit-attribute.png)
   
   1. Azure AD テナントの属性マッピング (Microsoft 365 ライセンスあり)
      
      a. **[一意のユーザー識別子 (名前 ID)]** 要求をクリックします。

      ![属性と要求](./media/atlassian-cloud-tutorial/user-attributes-and-claims.png)
      
      b. Atlassian Cloud では、**nameidentifier** (**一意のユーザー ID**) がユーザーのメール (**user.email**) にマップされると想定されています。 **[ソース属性]** を編集して、「**user.mail**」に変更してください。 要求に対する変更を保存します。

      ![一意のユーザー ID](./media/atlassian-cloud-tutorial/unique-user-identifier.png)
      
      c. 最終的な属性マッピングは、次のようになります。

      ![画像 2](./media/atlassian-cloud-tutorial/default-attributes-1.png)
      
   1. Azure AD テナントの属性マッピング (Microsoft 365 ライセンスなし) 

      a. `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` 要求をクリックします。

      ![画像 3](./media/atlassian-cloud-tutorial/email-address.png)
         
      b. Microsoft 365 ライセンスのない Azure AD テナントに作成されたユーザーについては、**user.mail** 属性が Azure によって事前設定されません。そのようなユーザーのメールは、**userprincipalname** 属性に格納されます。 Atlassian Cloud では、**nameidentifier** (**一意のユーザー ID**) がユーザーのメール (**user.userprincipalname**) にマップされると想定されています。  **[ソース属性]** を編集して、「**user.userprincipalname**」に変更してください。 要求に対する変更を保存します。

      ![メールの設定](./media/atlassian-cloud-tutorial/set-email.png)
         
      c. 最終的な属性マッピングは、次のようになります。

      ![画像 4](./media/atlassian-cloud-tutorial/default-attributes-2.png)
     
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

このセクションでは、B.Simon に Atlassian Cloud へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Atlassian Cloud]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-atlassian-cloud-test-user"></a>Atlassian Cloud のテスト ユーザーの作成

Azure AD ユーザーが Atlassian Cloud にサインインできるようにするには、Atlassian Cloud で次の手順を実行して、手動でユーザー アカウントをプロビジョニングします。

1. **[Administration]\(管理\)** ウィンドウで、 **[Users]\(ユーザー\)** を選択します。

    ![Atlassian Cloud の [Users]\(ユーザー\) リンク](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

1. Atlassian Cloud でユーザーを作成するには、 **[Invite user]\(ユーザーの招待\)** を選択します。

    ![Atlassian Cloud ユーザーの作成](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

1. **[Email address]\(電子メール アドレス\)** ボックスにユーザーのメール アドレスを入力してから、アプリケーション アクセスを割り当てます。

    ![Atlassian Cloud ユーザー](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

1. ユーザーに招待メールを送信するには、 **[Invite user]\(ユーザーの招待\)** を選択します。 ユーザーに招待メールが送信され、招待が受け入れられると、システム上でそのユーザーがアクティブになります。

> [!NOTE]
> **[Users]\(ユーザー\)** セクションの **[Bulk Create]\(一括作成\)** を選択することで、まとめて複数のユーザーを作成することもできます。

### <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Atlassian Cloud のサインオン URL にリダイレクトされます。  

* Atlassian Cloud のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Atlassian Cloud に自動的にサインインされるはずです 

また、Microsoft アクセス パネルを使用して、任意のモードでアプリケーションをテストすることもできます。 アクセス パネルで [Atlassian Cloud] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Atlassian Cloud に自動的にサインインされるはずです。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。


## <a name="next-steps"></a>次のステップ

Atlassian Cloud を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。