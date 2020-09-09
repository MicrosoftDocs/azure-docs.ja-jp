---
title: 'チュートリアル: Azure Active Directory と Keeper Password Manager & Digital Vault の統合 | Microsoft Docs'
description: Azure Active Directory と Keeper Password Manager & Digital Vault の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2020
ms.author: jeedes
ms.openlocfilehash: c6bd0c130e860a5700256a54c081cc046219b41a
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546748"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>チュートリアル: Azure Active Directory と Keeper Password Manager & Digital Vault の統合

このチュートリアルでは、Keeper Password Manager & Digital Vault と Azure Active Directory を統合する方法について説明します。
Keeper Password Manager & Digital Vault と Azure AD の統合には、次の利点があります。

* Keeper Password Manager & Digital Vault にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Keeper Password Manager & Digital Vault に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Keeper Password Manager & Digital Vault と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Keeper Password Manager & Digital Vault でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Keeper Password Manager & Digital Vault で **SP** によって開始される SSO をサポートします

* Keeper Password Manager & Digital Vault では、**Just In Time** ユーザー プロビジョニングがサポートされます

* Keeper Password Manager & Digital Vault を構成したら、ご自分の組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)をご覧ください。

## <a name="adding-keeper-password-manager--digital-vault-from-the-gallery"></a>ギャラリーからの Keeper Password Manager & Digital Vault の追加

Azure AD への Keeper Password Manager & Digital Vault の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Keeper Password Manager &amp;amp; Digital Vault を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Keeper Password Manager & Digital Vault**」と入力します。
1. 結果のパネルから **[Keeper Password Manager & Digital Vault]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-keeper-password-manager--digital-vault"></a>Keeper Password Manager & Digital Vault の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Keeper Password Manager & Digital Vault に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Keeper Password Manager & Digital Vault の関連ユーザーとの間にリンク関係を確立する必要があります。

Keeper Password Manager & Digital Vault に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。

    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。

1. **[Keeper Password Manager & Digital Vault SSO の構成](#configure-keeper-password-manager--digital-vault-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[Keeper Password Manager & Digital Vault テスト ユーザーの作成](#create-keeper-password-manager--digital-vault-test-user)** - Keeper Password Manager & Digital Vault で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Keeper Password Manager & Digital Vault** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。
    * **クラウド SSO** の場合:`https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * **オンプレミス SSO** の場合:`https://<KEEPER_FQDN>/sso-connect/saml/login`

    b. **[識別子 (エンティティ ID)]** テキスト ボックスに、次のパターンで URL を入力します。
    * **クラウド SSO** の場合:`https://keepersecurity.com/api/rest/sso/saml/<CLOUD_INSTANCE_ID>`
    * **オンプレミス SSO** の場合:`https://<KEEPER_FQDN>/sso-connect`

    c. **[応答 URL]** ボックスに、 のパターンを使用して URL を入力します。
    * **クラウド SSO** の場合:`https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * **オンプレミス SSO** の場合:`https://<KEEPER_FQDN>/sso-connect/saml/sso`

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際のサインオン URL、識別子、および応答 URL で更新してください。 これらの値を取得するには、[Keeper Password Manager & Digital Vault クライアント サポート チーム](https://keepersecurity.com/contact.html)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Keeper Password Manager & Digital Vault のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Keeper Password Manager & Digital Vault へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Keeper Password Manager & Digital Vault]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。


## <a name="configure-keeper-password-manager--digital-vault-sso"></a>Keeper Password Manager & Digital Vault SSO の構成

**Keeper Password Manager & Digital Vault Configuration** 側でシングル サインオンを構成するには、『[Keeper Support Guide](https://docs.keeper.io/sso-connect-guide/)』(Keeper サポート ガイド) のガイドラインに従ってください。

### <a name="create-keeper-password-manager--digital-vault-test-user"></a>Keeper Password Manager & Digital Vault テスト ユーザーの作成

Azure AD ユーザーが Keeper Password Manager & Digital Vault にログインできるようにするには、ユーザーを Keeper Password Manager & Digital Vault にプロビジョニングする必要があります。 アプリケーションでは、ジャストインタイムのユーザー プロビジョニングがサポートされ、認証後にユーザーがアプリケーションに自動的に作成されます。 ユーザーを手動でセットアップする場合は、[Keeper のサポート](https://keepersecurity.com/contact.html)に問い合わせてください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Keeper Password Manager & Digital Vault] タイルをクリックすると、SSO を設定した Keeper Password Manager & Digital Vault へのサインインが自動的に行われるはずです。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で Keeper Password Manager & Digital Vault を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
