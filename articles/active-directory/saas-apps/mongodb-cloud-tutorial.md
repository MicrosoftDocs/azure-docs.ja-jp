---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と MongoDB Cloud の統合 | Microsoft Docs
description: Azure Active Directory と MongoDB Cloud の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.openlocfilehash: af1c0702929e7cd700c8d19ab24e40f9c6f43a21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96602174"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mongodb-cloud"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と MongoDB Cloud の統合

このチュートリアルでは、MongoDB Cloud と Azure Active Directory (Azure AD) を統合する方法について説明します。 MongoDB Cloud と Azure AD を統合すると、次のことができます。

* MongoDB Cloud、MongoDB Atlas、MongoDB コミュニティ、MongoDB University、および MongoDB サポートにアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して MongoDB Cloud に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、[Azure Active Directory を使用したアプリケーション アクセスとシングル サインオンの概要](../manage-apps/what-is-single-sign-on.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、以下が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効になっている MongoDB Cloud 組織。[無料のクラスター](https://www.mongodb.com/cloud)にサインアップできます

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* MongoDB Cloud では、**SP** Initiated SSO と **IDP** Initiated SSO がサポートされます。
* MongoDB Cloud では、**Just-In-Time** ユーザー プロビジョニングがサポートされます。
* MongoDB Cloud を構成した後、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 詳細については、[Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)に関するページを参照してください。

## <a name="add-mongodb-cloud-from-the-gallery"></a>ギャラリーからの MongoDB Cloud の追加

Azure AD への MongoDB Cloud の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に MongoDB Cloud を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左側のペインで、 **[Azure Active Directory]** を選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**MongoDB Cloud**」と入力します。
1. 結果から **[MongoDB Cloud]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-mongodb-cloud"></a>MongoDB Cloud の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、MongoDB Cloud に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと MongoDB Cloud の関連ユーザーとの間にリンク関係を確立する必要があります。

MongoDB Cloud に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. [Azure AD SSO を構成](#configure-azure-ad-sso)して、ユーザーがこの機能を使用できるようにします。
    1. [Azure AD のテスト ユーザーを作成](#create-an-azure-ad-test-user)して、B.Simon を使って Azure AD のシングル サインオンをテストします。
    1. [Azure AD テスト ユーザーを割り当て](#assign-the-azure-ad-test-user)て、B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. [MongoDB Cloud SSO の構成](#configure-mongodb-cloud-sso) - アプリケーション側でシングル サインオン設定を構成します。
    1. [MongoDB Cloud テスト ユーザーの作成](#create-a-mongodb-cloud-test-user) - MongoDB Cloud で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. [SSO をテスト](#test-sso)して、構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **MongoDB Cloud** アプリケーション統合ページで、 **[管理]** セクションを見つけます。 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンを選択して設定を編集します。

   ![鉛筆アイコンが強調表示された [SAML によるシングル サインオンのセットアップ] ページのスクリーンショット](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://www.okta.com/saml2/service-provider/<Customer_Unique>` という形式で URL を入力します。

    b. **[応答 URL]** ボックスに、次のパターンを使用する URL を入力します: `https://auth.mongodb.com/sso/saml2/<Customer_Unique>`。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** を選択して次の手順を実行します。

    **[サインオン URL]** ボックスに、次のパターンを使用する URL を入力します: `https://cloud.mongodb.com/sso/<Customer_Unique>`

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[MongoDB Cloud クライアント サポート チーム](https://support.mongodb.com/)にご連絡ください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. MongoDB Cloud アプリケーションは特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![既定の属性のスクリーンショット](common/default-attributes.png)

1. 上記の属性に加えて、MongoDB Cloud アプリケーションでは、その他にいくつかの属性が SAML 応答で返されることが想定されています。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前 | ソース属性|
    | ---------------| --------- |
    | email | user.userprincipalname |
    | firstName | User.givenname |
    | lastName | User.surname |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を見つけます。 **[ダウンロード]** を選択して証明書をダウンロードし、コンピューターに保存します。

    ![[ダウンロード] リンクが強調表示された [SAML 署名証明書] セクションのスクリーンショット](common/metadataxml.png)

1. **[MongoDB Cloud のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![URL が強調表示された [MongoDB Cloud のセットアップ] セクションのスクリーンショット](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** を選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[Show password]\(パスワードの表示\)** チェック ボックスをオンにし、パスワードを書き留めます。
   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に MongoDB Cloud へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。
1. アプリケーションの一覧で **[MongoDB Cloud]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] が強調表示された [管理] セクションのスクリーンショット](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択します。 次に、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] が強調表示された [ユーザーとグループ] ページのスクリーンショット](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧から **[B.Simon]** を選択します。 次に、画面の下部にある **[選択]** を選択します。
1. SAML アサーション内にロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 次に、画面の下部にある **[選択]** を選択します。
1. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

## <a name="configure-mongodb-cloud-sso"></a>MongoDB Cloud SSO の構成

MongoDB Cloud 側でシングル サインオンを構成するには、Azure portal から適切な URL をコピーする必要があります。 さらに、MongoDB Cloud 組織のフェデレーション アプリケーションを構成する必要があります。 [MongoDB Cloud ドキュメント](https://docs.atlas.mongodb.com/security/federated-auth-azure-ad/)に記載の手順に従います。 問題が発生した場合は、[MongoDB Cloud サポート チーム](https://support.mongodb.com/)にお問い合わせください。

### <a name="create-a-mongodb-cloud-test-user"></a>MongoDB Cloud テスト ユーザーの作成

MongoDB Cloud では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 追加の操作は必要ありません。 MongoDB Cloud にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [MongoDB Cloud] タイルを選択すると、SSO を設定した MongoDB Cloud に自動的にサインインします。 詳細については、「[マイ アプリ ポータルからアプリにサインインして開始する](../user-help/my-apps-portal-end-user-access.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリケーションと Azure Active Directory との統合に関するチュートリアル](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [MongoDB Atlas on Azure にサインアップする](https://azuremarketplace.microsoft.com/marketplace/apps/mongodb.mdb_atlas_oct2020?tab=Overview)

- [Azure AD で MongoDB Cloud を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって MongoDB Cloud を保護する](/cloud-app-security/proxy-intro-aad)