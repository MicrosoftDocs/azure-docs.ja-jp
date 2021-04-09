---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Single Sign-on for Skytap の統合 | Microsoft Docs
description: Azure Active Directory と Single Sign-on for Skytap の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/13/2020
ms.author: jeedes
ms.openlocfilehash: 5577b0cb480b372b64a9cb25f52617edfca3f2e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98108162"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-single-sign-on-for-skytap"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Single Sign-on for Skytap の統合

このチュートリアルでは、Single Sign-on for Skytap と Azure Active Directory (Azure AD) を統合する方法について説明します。 Single Sign-on for Skytap を Azure AD と統合すると、次のことができます。

* Single Sign-on for Skytap にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Single Sign-on for Skytap に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、[Azure Active Directory を使用したアプリケーション アクセスとシングル サインオンの概要](../manage-apps/what-is-single-sign-on.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Single Sign-on for Skytap でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Single Sign-on for Skytap では、SP Initiated SSO と IDP Initiated SSO がサポートされます。
* Single Sign-on for Skytap を構成した後、セッション制御を適用できます。 これにより、組織の機密データを流出と侵入からリアルタイムで保護することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="add-single-sign-on-for-skytap-from-the-gallery"></a>ギャラリーからの Single Sign-on for Skytap の追加

Azure AD への Single Sign-on for Skytap の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Single Sign-on for Skytap を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Single Sign-on for Skytap**」と入力します。
1. 結果のパネルから **[Single Sign-on for Skytap]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-single-sign-on-for-skytap"></a>Single Sign-on for Skytap の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Single Sign-on for Skytap に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Single Sign-on for Skytap の関連ユーザーとの間にリンク関係を確立します。

Single Sign-on for Skytap で Azure AD SSO を構成してテストする一般的な手順を次に示します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。

    a. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。

    b. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Single Sign-on for Skytap SSO の構成](#configure-single-sign-on-for-skytap-sso)** - アプリケーション側でシングル サインオン設定を構成します。

    a. **[Single Sign-on for Skytap のテスト ユーザーの作成](#create-single-sign-on-for-skytap-test-user)** - Single Sign-on for Skytap で B.Simon に対応するユーザーを作成します。 このユーザーを Azure AD の対応するユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Single Sign-on for Skytap** アプリケーション統合ページで、 **[管理]** セクションを見つけます。 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンを選択して設定を編集します。

   ![鉛筆アイコンが強調表示された [SAML でシングル サインオンをセットアップします] ページのスクリーンショット](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`http://pingone.com/<custom EntityID>` という形式で URL を入力します。

    b. **[応答 URL]** ボックスに、次のパターンを使用する URL を入力します: `https://sso.connect.pingidentity.com/sso/sp/ACS.saml2`。

1. 必要に応じて、 **[追加の URL を設定します]** を選択し、次の手順を実行することにより、**SP** Initiated モードでアプリケーションを構成することができます。

    a. **[サインオン URL]** ボックスに、`https://sso.connect.pingidentity.com/sso/sp/initsso?saasid=<saasid>&idpid=<idpid>` という形式で URL を入力します。

    
    b. **[リレー状態]** ボックスに、次のパターンを使用する URL を入力します: `https://pingone.com/1.0/<custom ID>`

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL、リレー状態でこれらの値を更新します。 これらの値を取得するには、[Single Sign-on for Skytap クライアント サポート チーム](mailto:support@skytap.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を見つけます。 **[ダウンロード]** を選択してメタデータ ファイルをダウンロードし、お使いのコンピューターに保存します。

    ![証明書のダウンロード リンクのスクリーンショット](common/metadataxml.png)

1. **[Single Sign-on for Skytap のセットアップ]** セクションで、要件に基づいて適切な 1 つまたは複数の URL をコピーします。

    ![構成 URL のコピーのスクリーンショット](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** を選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** フィールドに表示された値を書き留めます。
   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Single Sign-on for Skytap へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。
1. アプリケーションの一覧で **[Single Sign-on for Skytap]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] が強調表示された [管理] セクションのスクリーンショット](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択します。 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] が強調表示された [ユーザーとグループ] ページのスクリーンショット](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧から **[B.Simon]** を選択します。 次に、画面の下部にある **[選択]** ボタンを選択します。
1. SAML アサーション内にロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 次に、画面の下部にある **[選択]** ボタンを選択します。
1. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

## <a name="configure-single-sign-on-for-skytap-sso"></a>Single Sign-on for Skytap SSO の構成

Single Sign-on for Skytap 側でシングル サインオンを構成するには、ダウンロードした **フェデレーション メタデータ XML** と Azure portal からコピーした適切な URL を [Single Sign-on for Skytap サポート チーム](mailto:support@skytap.com)に送信する必要があります。 この設定が構成され、SAML SSO 接続が両側で正しく行われます。


### <a name="create-single-sign-on-for-skytap-test-user"></a>Single Sign-on for Skytap のテスト ユーザーの作成

このセクションでは、Single Sign-on for Skytap で B.Simon というユーザーを作成します。 [Single Sign-on for Skytap クライアント サポート チーム](mailto:support@skytap.com)と連携して、Single Sign-on for Skytap プラットフォームにユーザーを追加してください。 ユーザーを作成してアクティブ化するまでは、シングル サインオンを使用できません。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Single Sign-on for Skytap] タイルを選択すると、SSO を設定した Single Sign-on for Skytap に自動的にサインインします。 詳細については、「[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリケーションと Azure Active Directory との統合に関するチュートリアル](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Slack を試す](https://aad.portal.azure.com/)
