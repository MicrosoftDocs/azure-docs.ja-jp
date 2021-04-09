---
title: チュートリアル:Azure Active Directory と TigerConnect Secure Messenger の統合 | Microsoft Docs
description: Azure Active Directory と TigerConnect Secure Messenger の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/21/2020
ms.author: jeedes
ms.openlocfilehash: 6831e96ebd8fd2db51d640ff3d93955b1a9f1477
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182157"
---
# <a name="tutorial-azure-active-directory-integration-with-tigerconnect-secure-messenger"></a>チュートリアル:Azure Active Directory と TigerConnect Secure Messenger の統合

このチュートリアルでは、TigerConnect Secure Messenger と Azure Active Directory (Azure AD) を統合する方法について説明します。

TigerConnect Secure Messenger と Azure AD の統合には、次の利点があります。

* TigerConnect Secure Messenger にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで TigerConnect Secure Messenger に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure ポータル) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

TigerConnect Secure Messenger と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
* シングル サインオンが有効になった TigerConnect Secure Messenger サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストし、TigerConnect Secure Messenger を Azure AD と統合します。

* TigerConnect Secure Messenger では、**SP** Initiated SSO がサポートされます
* TigerConnect Secure Messenger を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-tigerconnect-secure-messenger-from-the-gallery"></a>ギャラリーからの TigerConnect Secure Messenger の追加

Azure AD への TigerConnect Secure Messenger の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に TigerConnect Secure Messenger を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**TigerConnect Secure Messenger**」と入力します。
1. 結果パネルから **[TigerConnect Secure Messenger]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、TigerConnect Secure Messenge で Azure AD のシングル サインオンを構成し、テストします。 シングル サインオンを機能させるには、Azure AD ユーザーと TigerConnect Secure Messenge 内の関連ユーザーとの間にリンク関係を確立する必要があります。

TigerConnect Secure Messenge で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[TigerConnect Secure Messenge の SSO の構成](#configure-tigerconnect-secure-messenger-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[TigerConnect Secure Messenge のテスト ユーザーの作成](#create-a-tigerconnect-secure-messenger-test-user)** - Britta Simon という Azure AD ユーザーにリンクされている Britta Simon というユーザーが TigerConnect Secure Messenge に存在するようにします。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

TigerConnect Secure Messenger で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **TigerConnect Secure Messenger** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    1. **[サインオン URL]** ボックスに、URL を入力します。

       `https://home.tigertext.com`

    1. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > **[識別子 (エンティティ ID)]** の値は実際の値ではありません。 実際の識別子でこの値を更新します。 この値を取得するには、[TigerConnect Secure Messenger サポート チーム](mailto:prosupport@tigertext.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** ウィンドウに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ウィンドウの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** を選択し、特定のオプションの **フェデレーション メタデータ XML** をダウンロードします。

    ![フェデレーション メタデータ XML のダウンロード オプション](common/metadataxml.png)

1. **[TigerConnect Secure Messenger のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、Britta Simon に TigerConnect Secure Messenger へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[TigerConnect Secure Messenger]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-tigerconnect-secure-messenger-sso"></a>TigerConnect Secure Messenger SSO の構成

TigerConnect Secure Messenger 側でシングル サインオンを構成するには、ダウンロードしたフェデレーション メタデータ XML と Azure portal からコピーした適切な URL を [TigerConnect Secure Messenger サポート チーム](mailto:prosupport@tigertext.com)に送信する必要があります。 TigerConnect Secure Messenger チームは、SAML SSO 接続が両方の側で正しく設定されていることを確認します。

## <a name="create-a-tigerconnect-secure-messenger-test-user"></a>TigerConnect Secure Messenger のテスト ユーザーの作成

このセクションでは、TigerConnect Secure Messenger で Britta Simon というユーザーを作成します。 [TigerConnect Secure Messenger サポート チーム](mailto:prosupport@tigertext.com)と協力して、TigerConnect Secure Messenger にユーザーとして Britta Simon を追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、マイ アプリ ポータルを使用して自分の Azure AD のシングル サインオン構成をテストします。

マイ アプリ ポータルで **[TigerConnect Secure Messenger]** を選択すると、シングル サインオンを設定した TigerConnect Messenger サブスクリプションに自動的にサインインします。 マイアプリ ポータルの詳細については、「[マイ アプリ ポータルでアプリにアクセスして使用する](../user-help/my-apps-portal-end-user-access.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory の統合に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で TigerConnect Secure Messenger を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](/cloud-app-security/proxy-intro-aad)