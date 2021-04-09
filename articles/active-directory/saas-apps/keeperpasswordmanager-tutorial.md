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
ms.date: 11/13/2020
ms.author: jeedes
ms.openlocfilehash: b70c50e7c2900f884dd4d91c6650205bc626326e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96178043"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>チュートリアル: Azure Active Directory と Keeper Password Manager & Digital Vault の統合

このチュートリアルでは、Keeper Password Manager & Digital Vault と Azure Active Directory を統合する方法について説明します。
この統合には次のようなメリットがあります。

* Keeper Password Manager & Digital Vault にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Keeper Password Manager & Digital Vault に自動的にサインイン (シングル サインオン) できるように設定できます。
* 1 つの中央サイト (Azure ポータル) でアカウントを管理できます。


## <a name="prerequisites"></a>前提条件

Keeper Password Manager & Digital Vault と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD 環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。
* シングル サインオン (SSO) が有効な Keeper Password Manager & Digital Vault サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Keeper Password Manager & Digital Vault では、SP Initiated SSO がサポートされます。

* Keeper Password Manager & Digital Vault では、Just In Time ユーザー プロビジョニングがサポートされます。

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>ギャラリーから Keeper Password Manager & Digital Vault を追加する

Azure AD への Keeper Password Manager & Digital Vault の統合を構成するには、ギャラリーからマネージド SaaS (サービスとしてのソフトウェア) アプリの一覧にこのアプリケーションを追加します。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左側のペインで、 **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** で、検索ボックスに「**Keeper Password Manager & Digital Vault**」と入力します。
1. 結果パネルから **[Keeper Password Manager & Digital Vault]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-keeper-password-manager--digital-vault"></a>Keeper Password Manager & Digital Vault の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Keeper Password Manager & Digital Vault に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと Keeper Password Manager & Digital Vault の関連ユーザーとの間にリンク関係を確立する必要があります。

Keeper Password Manager & Digital Vault に対する Azure AD SSO を構成してテストするには、次の手順に従います。

1. [Azure AD SSO を構成](#configure-azure-ad-sso)して、ユーザーがこの機能を使用できるようにします。

    * [Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user) - Britta Simon で Azure AD のシングル サインオンをテストします。
    * [Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user) - Britta Simon が Azure AD シングル サインオンを使用できるようにします。

1. [Keeper Password Manager & Digital Vault SSO の構成](#configure-keeper-password-manager--digital-vault-sso) - アプリケーション側で SSO 設定を構成します。
    * [Keeper Password Manager & Digital Vault のテスト ユーザーの作成](#create-a-keeper-password-manager--digital-vault-test-user) - Keeper Password Manager & Digital Vault で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. [SSO をテスト](#test-sso)して、構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Keeper Password Manager & Digital Vault** アプリケーション統合ページで、 **[管理]** セクションを見つけます。 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンを選択して設定を編集します。

   ![鉛筆アイコンが強調表示された [SAML でシングル サインオンをセットアップします] のスクリーンショット。](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** に、次のパターンを使用して URL を入力します。
    * クラウド SSO の場合: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * オンプレミス SSO の場合: `https://<KEEPER_FQDN>/sso-connect/saml/login`

    b. **[識別子 (エンティティ ID)]** に、次のパターンを使用して URL を入力します。
    * クラウド SSO の場合: `https://keepersecurity.com/api/rest/sso/saml/<CLOUD_INSTANCE_ID>`
    * オンプレミス SSO の場合: `https://<KEEPER_FQDN>/sso-connect`

    c. **[応答 URL]** に、次のパターンを使用して URL を入力します。
    * クラウド SSO の場合: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * オンプレミス SSO の場合: `https://<KEEPER_FQDN>/sso-connect/saml/sso`

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値は、実際のサインオン URL、識別子、応答 URL で更新してください。 これらの値を取得するには、[Keeper Password Manager & Digital Vault クライアント サポート チーム](https://keepersecurity.com/contact.html)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. Keeper Password Manager & Digital Vault アプリケーションでは、特定の形式の SAML アサーションを要求するため、SAML トークン属性の構成にカスタム属性マッピングを追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![[ユーザー属性とクレーム] のスクリーンショット。](common/default-attributes.png)

1. さらに、Keeper Password Manager & Digital Vault アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 それらを次の表に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 | ソース属性|
    | ------------| --------- |
    | First | User.givenname |
    | Last (最後へ) | User.surname |
    | Email | User.mail |

5. **[SAML によるシングル サインオンのセットアップ]** の **[SAML 署名証明書]** セクションで、 **[ダウンロード]** を選択します。 これにより、要件に応じたオプションから **フェデレーション メタデータ XML** がダウンロードされ、コンピューターに保存されます。

    ![[ダウンロード] が強調表示された [SAML 署名証明書] のスクリーンショット。](common/metadataxml.png)

6. **[Keeper Password Manager & Digital Vault のセットアップ]** で、要件に従って適切な URL をコピーします。

    ![URL が強調表示された [Keeper Password Manager & Digital Vault のセットアップ] のスクリーンショット。](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションでは、Azure portal で `B.Simon` というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** を選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **名前** には、`B.Simon`を入力します。  
   1. **[ユーザー名]** に「`username@companydomain.extension`」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** を選び、表示された値を書き留めます。
   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Keeper Password Manager & Digital Vault へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。
1. アプリケーションの一覧で **[Keeper Password Manager & Digital Vault]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択します。 **[割り当ての追加]** で、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** で、ユーザーの一覧から **[B.Simon]** を選択します。 次に、画面の下部にある **[選択]** を選択します。
1. ユーザーにロールを割り当てる必要がある場合は、 **[ロールの選択]** の一覧から選択できます。 このアプリに対してロールが設定されていない場合は、 **[既定のアクセス]** ロールが選択されます。
1. **[割り当ての追加]** で **[割り当て]** を選択します。


## <a name="configure-keeper-password-manager--digital-vault-sso"></a>Keeper Password Manager & Digital Vault SSO の構成

アプリの SSO を構成するには、[Keeper サポート ガイド](https://docs.keeper.io/sso-connect-guide/)のガイドラインを参照してください。

### <a name="create-a-keeper-password-manager--digital-vault-test-user"></a>Keeper Password Manager & Digital Vault のテスト ユーザーの作成

Azure AD ユーザーが Keeper Password Manager & Digital Vault にサインインできるようにするには、ユーザーをプロビジョニングする必要があります。 このアプリケーションでは、Just-In-Time ユーザー プロビジョニングがサポートされているので、認証後にユーザーがアプリケーションに自動的に作成されます。 ユーザーを手動で設定する場合は、[Keeper サポート](https://keepersecurity.com/contact.html)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で、 **[このアプリケーションをテストします]** を選択します。 これにより、サインオンを開始できる Keeper Password Manager & Digital Vault のサインオン URL にリダイレクトされます。 

* アプリケーションのサインオン URL に直接移動し、そこからサインインを開始できます。

* Microsoft アクセス パネルを使用することができます。 アクセス パネルで **[Keeper Password Manager & Digital Vault]** タイルを選択すると、アプリケーションのサインオン URL にリダイレクトされます。 アクセス パネルの詳細については、「[マイ アプリ ポータルからアプリにサインインして開始する](../user-help/my-apps-portal-end-user-access.md)」をご覧ください。


## <a name="next-steps"></a>次のステップ

Keeper Password Manager & Digital Vault を構成したら、セッション制御を適用できます。 これにより、組織の機密データを流出と侵入からリアルタイムで保護することができます。 セッション制御は、条件付きアクセスを拡張したものです。 詳細については、[Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)に関するページを参照してください。