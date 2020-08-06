---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Chatwork の統合 | Microsoft Docs
description: Azure Active Directory と Chatwork の間でシングル サインオンを構成する方法について確認します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0982ee1d-7ae2-415b-9e47-cfc72e1885ca
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/21/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 368b87a4abd5b55a807f7184c3a56876169b2517
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87293545"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-chatwork"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Chatwork の統合

このチュートリアルでは、Chatwork と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Chatwork を統合すると、次のことができます。

* Chatwork にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Chatwork に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Chatwork でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Chatwork では、**SP** によって開始される SSO がサポートされます

* Chatwork を構成したら、ご自分の組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-chatwork-from-the-gallery"></a>ギャラリーからの Chatwork の追加

Azure AD への Chatwork の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Chatwork を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Chatwork**」と入力します。
1. 結果のパネルから **[Chatwork]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-chatwork"></a>Chatwork の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Chatwork で Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと Chatwork の関連ユーザーとの間にリンク関係を確立する必要があります。

Chatwork で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Chatwork の SSO の構成](#configure-chatwork-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Chatwork テスト ユーザーの作成](#create-chatwork-test-user)** - Chatwork で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Chatwork** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    **[サインオン URL]** ボックスに、`https://www.chatwork.com/s/<TENANT_NAME>` という形式で URL を入力します。

    > [!NOTE]
    > この値は実際のものではありません。**[Chatwork SSO の構成]** を行った後、Chatworkに設定した専用ログインURLに変更してください。
    
1. Chatworkアプリケーションでは **[ユーザー属性とクレーム]** セクションの **[一意のユーザーID]** 属性の値がChatworkに登録されているメールアドレスと一致する必要があります。この属性は既定では **user.principalname** にマップされています。プリンシパル名が登録されているメールアドレスと異なる場合は **[一意のユーザーID]** を **user.mail** にマップしてください。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Chatwork のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Chatwork へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Chatwork]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-chatwork-sso"></a>Chatwork SSO の構成

**Chatwork** 側でシングル サインオンを構成します。[Chatwork 管理者設定ご利用ガイド](https://download.chatwork.com/Chatwork_AdminGuide.pdf)を参照し、Chatworkの専用ログインURLおよびシングルサインオンの設定を行ってください。

Chatworkのシングルサインオン設定画面の **[Identity ProviderのログインURL]** には **[Azure AD SSOの構成]** でコピーした **[Login URL]** を、 **[Identity ProviderのエンティティID]** には **[Azure AD Identifier]** をそれぞれ設定してください。また **[Identity Providerが書名に利用する公開鍵の証明書]** には、Azureからダウンロードした証明書をメモ帳等のテキストエディタで開き、内容をコピーして貼りつけてください。


### <a name="create-chatwork-test-user"></a>Chatwork テスト ユーザーの作成

このセクションでは、Chatwork で B.Simon というユーザーを作成します。 [Chatwork 管理者設定ご利用ガイド](https://download.chatwork.com/Chatwork_AdminGuide.pdf)に従って、Chatwork プラットフォームにこのユーザーを追加してください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Chatwork] タイルをクリックすると、SSO を設定した Chatwork に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で Chatwork を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって Chatwork を保護する方法](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

