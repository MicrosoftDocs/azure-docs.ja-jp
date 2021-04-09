---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と New Relic の統合 | Microsoft Docs
description: Azure Active Directory と New Relic の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: 29e19eea51b5ee55831bf1d694a9a6473a62d471
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97504051"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と New Relic の統合

このチュートリアルでは、New Relic と Azure Active Directory (Azure AD) を統合する方法について説明します。 New Relic を Azure AD と統合すると、次のことができます。

* New Relic にアクセスするユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して New Relic に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、[Azure Active Directory を使用したアプリケーション アクセスとシングル サインオンの概要](../manage-apps/what-is-single-sign-on.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、以下が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な New Relic サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* New Relic では、SP Initiated SSO または IDP Initiated SSO がサポートされています。
* New Relic を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="add-new-relic-from-the-gallery"></a>ギャラリーからの New Relic の追加

Azure AD への New Relic の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に **New Relic (By Organization)** を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. **Azure Active Directory** サービスを選択します。
1. **[エンタープライズ アプリケーション]**  >  **[新しいアプリケーション]** の順に選択します。
1. **[Azure AD ギャラリーの参照]** ページで、検索ボックスに「**New Relic (By Organization)** 」と入力します。
1. 結果から **[New Relic (By Organization)]** を選択し、 **[作成]** を選択します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-new-relic"></a>New Relic に対する Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、New Relic に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと New Relic の関連ユーザーとの間にリンク関係を確立する必要があります。

New Relic に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. [Azure AD SSO を構成](#configure-azure-ad-sso)して、ユーザーがこの機能を使用できるようにします。
   1. [Azure AD のテスト ユーザーを作成](#create-an-azure-ad-test-user)して、B.Simon を使って Azure AD のシングル サインオンをテストします。
   1. [Azure AD テスト ユーザーを割り当て](#assign-the-azure-ad-test-user)て、B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. [New Relic の SSO の構成](#configure-new-relic-sso) - New Relic 側でシングル サインオン設定を構成します。
   1. [New Relic のテスト ユーザーの作成](#create-a-new-relic-test-user) - New Relic で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. [SSO をテスト](#test-sso)して、構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **New Relic by Organization** アプリケーション統合ページで、 **[管理]** セクションを見つけます。 続けて、 **[シングル サインオン]** を選択します。

1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。

1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンを選択して設定を編集します。

   ![鉛筆アイコンが強調表示された [SAML でシングル サインオンをセットアップします] のスクリーンショット。](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、 **[識別子]** および **[応答 URL]** の値を入力します。

   * これらの値は、New Relic **My Organization** アプリケーションを使用して取得します。 このアプリケーションを使用するには、次の手順を実行します。
      1. New Relic に[サインイン](https://login.newrelic.com/)します。
      1. 上部のメニューで、 **[Apps]\(アプリ\)** を選択します。
      1. **[Your apps]\(ご使用のアプリ\)** セクションで、 **[My Organization]**  >  **[Authentication domains]\(認証ドメイン\)** を選択します。
      1. Azure AD SSO を接続する認証ドメインを選択します (複数の認証ドメインがある場合)。 ほとんどの企業では、**Default (既定値)** という認証ドメインしかありません。 認証ドメインが 1 つしかない場合は、何も選択する必要はありません。
      1. **[Authentication]\(認証\)** セクションの **[Assertion consumer URL]\(アサーション コンシューマー URL\)** には、 **[Reply URL]\(応答 URL\)** に使用する値が含まれています。
      1. **[Authentication]\(認証\)** セクションの **[Our entity ID]\(自分たちのエンティティ ID\)** には、 **[Identifier]\(識別子\)** に使用する値が含まれています。

1. **[User Attributes & Claims]\(ユーザー属性と要求\)** セクションで、 **[Unique User Identifier]\(一意のユーザー識別子\)** が、New Relic で使用されているメール アドレスを含むフィールドにマッピングされていることを確認します。

   * 既定のフィールド **[user.userprincipalname]** の値が New Relic のメール アドレスと同じである場合は、それで適切に機能します。
   * **[user.userprincipalname]** が New Relic のメール アドレスでない場合は、フィールド **[user.mail]** の方が適切に機能する可能性があります。

1. **[SAML Signing Certificate]\(SAML 署名証明書\)** セクションで **[App Federation Metadata Url]\(アプリのフェデレーション メタデータ URL\)** をコピーし、後で使用できるようにその値を保存します。

1. **[Set up New Relic by Organization]\(New Relic by Organization のセットアップ\)** セクションで **[Login URL]\(ログイン URL\)** をコピーし、後で使用できるようにその値を保存します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

ここでは、Azure portal 内で B.Simon というテスト ユーザーを作成する方法を説明します。

1. Azure portal で、 **[Azure Active Directory]** を選択します。
1. **[ユーザー]**  >  **[新しいユーザー]** を選択します。
1. **[新しいユーザー]** ページで、次の手順を実行します。
   1. **[ユーザー名]** フィールドに「`username@companydomain.extension`」と入力します。 たとえば、「 `b.simon@contoso.com` 」のように入力します。 これは、New Relic 側で使用するメール アドレスと一致する必要があります。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[パスワードを表示]** を選択し、表示された値を保存します。
   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

ここでは、B.Simon に New Relic by Organization アプリケーションへのアクセスを許可することで、このユーザーが Azure AD シングル サインオンを使用できるようにする方法を説明します。

1. Azure portal で、 **[Azure Active Directory]** を選択します。
1. **[エンタープライズ アプリケーション]**  >  **[New Relic by Organization]** の順に選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] が強調表示された [管理] セクションのスクリーンショット。](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択します。 **[割り当ての追加]** で、 **[ユーザーとグループ]** (ご使用のプラン レベルによっては **[ユーザー]** ) を選択します。

   ![[ユーザーの追加] オプションのスクリーンショット。](common/add-assign-user.png)

1. **[ユーザーとグループ]** (または **[ユーザー]** ) で、 **[ユーザー]** 一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** を選択します。
1. **[割り当ての追加]** で **[割り当て]** を選択します。

## <a name="configure-new-relic-sso"></a>New Relic SSO の構成

次の手順に従って、New Relic で SSO を構成します。

1. New Relic に[サインイン](https://login.newrelic.com/)します。

1. 上部のメニューで、 **[Apps]\(アプリ\)** を選択します。

1. **[Your apps]\(ご使用のアプリ\)** セクションで、 **[My Organization]**  >  **[Authentication domains]\(認証ドメイン\)** を選択します。

1. Azure AD SSO を接続する認証ドメインを選択します (複数の認証ドメインがある場合)。 ほとんどの企業では、**Default (既定値)** という認証ドメインしかありません。 認証ドメインが 1 つしかない場合は、何も選択する必要はありません。

1. **[Authentication]\(認証\)** セクションで、 **[Configure]\(構成\)** を選択します。

   1. **[Source of SAML metadata]\(SAML メタデータのソース\)** には、以前に Azure AD の **[アプリのフェデレーション メタデータ URL]** フィールドから保存した値を入力します。

   1. **[SSO target URL]\(SSO ターゲット URL\)** には、以前に Azure AD の **[ログイン URL]** フィールドから保存した値を入力します。

   1. Azure AD 側と New Relic 側の両方で設定がうまくいっていることを確認した後、 **[保存]** を選択します。 どちらかの側が適切に構成されていない場合、ユーザーは New Relic にサインインできません。

### <a name="create-a-new-relic-test-user"></a>New Relic テスト ユーザーを作成する

このセクションでは、New Relic で B.Simon というユーザーを作成します。

1. New Relic に[サインイン](https://login.newrelic.com/)します。

1. 上部のメニューで、 **[Apps]\(アプリ\)** を選択します。

1. **[Your apps]\(ご使用のアプリ\)** セクションで、 **[User Management]\(ユーザー管理\)** を選択します。

1. **[ユーザーの追加]** を選択します。

   1. **[Name]\(名前\)** には、「**B.Simon**」と入力します。
   
   1. **[Email]\(メール\)** には、Azure AD SSO によって送信される値を入力します。
   
   1. ユーザーの **[Type]\(種類\)** と **[Group]\(グループ\)** を選択します。 テスト ユーザーの場合、[Type]\(種類\) は **[Basic User]\(基本ユーザー\)** 、[Group]\(グループ\) は **[User]\(ユーザー\)** が適切な選択肢です。
   
   1. ユーザーを保存するには、 **[Add User]\(ユーザーの追加\)** を選択します。

## <a name="test-sso"></a>SSO のテスト 

ここでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストする方法を説明します。

アクセス パネルで **[New Relic by Organization]** を選択すると、New Relic に自動的にサインインします。 アクセス パネルの詳細については、「[マイ アプリ ポータルからアプリにサインインして開始する](../user-help/my-apps-portal-end-user-access.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリケーションと Azure Active Directory との統合に関するチュートリアル](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で New Relic を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](/cloud-app-security/proxy-intro-aad)
