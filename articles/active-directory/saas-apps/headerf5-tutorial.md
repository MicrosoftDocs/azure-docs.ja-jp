---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と F5 の統合 | Microsoft Docs
description: Azure Active Directory と F5 の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 59a87abb-1ec1-4438-be07-5b115676115f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea4167f5a8f4e29641a999c72f57b368190a34e0
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70165525"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と F5 の統合

このチュートリアルでは、F5 と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と F5 を統合すると、次のことができます。

* F5 にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して F5 に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* F5 でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* F5 SSO は、次の 3 つの異なる方法で構成できます。

- [ヘッダー ベースのアプリケーション用に F5 シングル サインオンを構成する](#configure-f5-single-sign-on-for-header-based-application)

- [Kerberos アプリケーション用に F5 シングル サインオンを構成する](kerbf5-tutorial.md)

- [Advanced Kerberos アプリケーション用に F5 シングル サインオンを構成する](advance-kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>ギャラリーからの F5 の追加

Azure AD への F5 の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に F5 を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**F5**」と入力します。
1. 結果のパネルから **[F5]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>F5 の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、F5 に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと F5 の関連ユーザーとの間にリンク関係を確立する必要があります。

F5 に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[F5 SSO の構成](#configure-f5-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[F5 テスト ユーザーの作成](#create-f5-test-user)** - F5 で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **F5** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://<YourCustomFQDN>.f5.com/` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<YourCustomFQDN>.f5.com/` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<YourCustomFQDN>.f5.com/` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 この値を取得するには、[F5 クライアント サポート チーム](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[F5 のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に F5 へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[F5]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-f5-sso"></a>F5 SSO の構成

- [Kerberos アプリケーション用に F5 シングル サインオンを構成する](kerbf5-tutorial.md)

- [Advanced Kerberos アプリケーション用に F5 シングル サインオンを構成する](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>ヘッダー ベースのアプリケーション用に F5 シングル サインオンを構成する

1. 新しい Web ブラウザー ウィンドウを開き、F5 (ヘッダー ベース) 企業サイトに管理者としてサインインして、次の手順を実行します。

1. 後で設定プロセスで使用するメタデータ証明書を F5 (ヘッダー ベース) にインポートする必要があります。 **[System]\(システム\) > [Certificate Management]\(証明書の管理\) > [Traffic Certificate Management]\(トラフィック証明書の管理\) > [SSL Certificate List]\(SSL 証明書の一覧\)** の順に移動します。 右隅にある **[Import]\(インポート\)** をクリックします

    ![F5 (ヘッダー ベース) の構成](./media/headerf5-tutorial/configure12.png)
 
1. さらに、ホスト名 (`headerapp.superdemo.live`) に対する **SSL 証明書**も必要です。この例では、ワイルドカード証明書を使用しました。

    ![F5 (ヘッダー ベース) の構成](./media/headerf5-tutorial/configure13.png)

1. **F5 (ヘッダー ベース) BIG-IP に移動します。[Access]\(アクセス\) > [Guided Configuration]\(ガイド付き構成\) > [Federation]\(フェデレーション\) > [SAML Service Provider]\(SAML サービス プロバイダー\) の順にクリックします**。

    ![F5 (ヘッダー ベース) の構成](./media/headerf5-tutorial/configure01.png)

    ![F5 (ヘッダー ベース) の構成](./media/headerf5-tutorial/configure02.png)
 
1. **エンティティ ID** を指定します (Azure AD アプリケーション構成で構成したものと同じ)

    ![F5 (ヘッダー ベース) の構成](./media/headerf5-tutorial/configure03.png) 

1. 新しい仮想サーバーを作成し、**送信先アドレス**を指定します。**リダイレクト ポート**は省略可能です。 前にアップロードした**ワイルド カード証明書** (またはアプリケーション用にアップロードした**証明書**) と、**関連付けられている秘密キー**を選択します。

    ![F5 (ヘッダー ベース) の構成](./media/headerf5-tutorial/configure04.png) 

1. 構成**メタデータ**をアップロードし、新しい **SAML IDP コネクタの名前**を指定します。前にアップロードしたフェデレーション証明書も指定する必要があります。

    ![F5 (ヘッダー ベース) の構成](./media/headerf5-tutorial/configure05.png)
 
1. バックエンド アプリ プールを**新規作成**し、バックエンド アプリケーション サーバーの **IP アドレス**を指定します。

    ![F5 (ヘッダー ベース) の構成](./media/headerf5-tutorial/configure06.png)

1. [Single Sign-on]\(シングル サインオン\) で、 **[HTTP header-based]\(HTTP ヘッダーベース\)** を選択します。 アプリケーションに基づいて他のヘッダーを追加できます。 SAML セッション変数の一覧については、付録を参照してください

    ![F5 (ヘッダー ベース) の構成](./media/headerf5-tutorial/configure07.png) 

1. **エンドポイント チェック プロパティ**のドキュメントの詳細については、[F5 (ヘッダー ベース) クライアント サポート チーム](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45)にお問い合わせください。

    ![F5 (ヘッダー ベース) の構成](./media/headerf5-tutorial/configure08.png)

1. **セッション管理プロパティ**のドキュメントの詳細については、[F5 (ヘッダー ベース) クライアント サポート チーム](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45)にお問い合わせください。

    ![F5 (ヘッダー ベース) の構成](./media/headerf5-tutorial/configure09.png)

1. **概要を確認**し、 **[Deploy]\(デプロイ\)** をクリックします。

    ![F5 (ヘッダー ベース) の構成](./media/headerf5-tutorial/configure10.png)

    ![F5 (ヘッダー ベース) の構成](./media/headerf5-tutorial/configure11.png)

### <a name="create-f5-test-user"></a>F5 テスト ユーザーの作成

このセクションでは、F5 で B.Simon というユーザーを作成します。  [F5 クライアント サポート チーム](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45)と連携し、F5 プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。 

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [F5] タイルをクリックすると、SSO を設定した F5 に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で F5 を試す](https://aad.portal.azure.com/)

- [Kerberos アプリケーション用に F5 シングル サインオンを構成する](kerbf5-tutorial.md)

- [Advanced Kerberos アプリケーション用に F5 シングル サインオンを構成する](advance-kerbf5-tutorial.md)

