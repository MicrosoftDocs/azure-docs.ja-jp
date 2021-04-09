---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Parkalot - Car park management の統合 | Microsoft Docs
description: Azure Active Directory と Parkalot - Car park management の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 0098d28d2b211ad9e4bbe60a44c5de4058f2b96b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651423"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-parkalot---car-park-management"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Parkalot - Car park management の統合

このチュートリアルでは、Parkalot - Car park management と Azure Active Directory (Azure AD) を統合する方法について説明します。 Parkalot - Car park management を Azure AD と統合すると、次のことが可能になります。

* Parkalot - Car park management にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Parkalot - Car park management に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な Parkalot - Car park management サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Parkalot - Car park management では、**SP** Initiated SSO がサポートされます

* Parkalot - Car park management では、**Just In Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-parkalot---car-park-management-from-the-gallery"></a>ギャラリーからの Parkalot - Car park management の追加

Azure AD への Parkalot - Car park management の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Parkalot - Car park management を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Parkalot - Car park management**」と入力します。
1. 結果パネルから **[Parkalot - Car park management]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-parkalot---car-park-management"></a>Parkalot - Car park management の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Parkalot - Car park management に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと Parkalot - Car park management の関連ユーザーとの間にリンク関係を確立する必要があります。

Parkalot - Car park management に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Parkalot - Car park management の SSO の構成](#configure-parkalot-car-park-management-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Parkalot - Car park management のテスト ユーザーの作成](#create-parkalot-car-park-management-test-user)** - Parkalot - Car park management で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Parkalot - Car park management** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[識別子 (エンティティ ID)]** ボックスに、次のいずれかのパターンを使用して URL を入力します。

    | 識別子 (エンティティ ID) |
    | -------------- |
    | `https://parkalot.io` |
    | `https://<CUSTOMERNAME>.parkalot.io` |
    |

    b. **[応答 URL]** ボックスに、次のいずれかのパターンを使用して URL を入力します。

    | [応答 URL] |
    | -------------- |
    | `https://<CUSTOMERNAME>.parkalot.io` |
    | `https://parkalot-saml.firebaseapp.com/__/auth/handler` |
    | `https://parkalot-saml.web.app/__/auth/handler` |
    | `https://<CustomerName>.parkalot.io/__/auth/handler` |
    |

    c. **[サインオン URL]** テキスト ボックスに、次のいずれかのパターンを使用して URL を入力します。

    | [サインオン URL] |
    | -------------- |
    | `https://<CUSTOMERNAME>.parkalot.io/#/login` |
    | `https://parkalot-saml.firebaseapp.com/#/login` |
    | `https://parkalot-saml.web.app/#/login` |
    |

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Parkalot - Car park management クライアント サポート チーム](mailto:contact-us@parkalot.io)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Parkalot - Car park management のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Parkalot - Car park management へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Parkalot - Car park management]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-parkalot-car-park-management-sso"></a>Parkalot - Car park management の SSO の構成

1. 別の Web ブラウザー ウィンドウで、Parkalot - Car park management 企業サイトに管理者としてサインインします。

1. **[Setup SAML]\(SAML の設定\)** を選択し、 **[Add New]\(新規追加\)** カードの **[EDIT]\(編集\)** アイコンをクリックします。

    ![[Add New]\(新規追加\) の [EDIT]\(編集\) アイコン。](./media/parkalot-car-park-management-tutorial/setup-saml.png)

1. 次のページで下記の手順を実行します。

    ![Parkalot - Car park management の SSO を構成する。](./media/parkalot-car-park-management-tutorial/configuration.png)

    a. **[Display name]\(表示名\)** ボックスに、有効な名前を入力します。

    b. **[IDP Entity ID]\(IDP エンティティ ID\)** ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    c. **[SSO url]** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    d. Azure portal からダウンロードした **証明書 (Base64)** をメモ帳で開き、その内容を **[Certificate]\(証明書\)** ボックスに貼り付けます。

    e. **[保存]** をクリックします。

### <a name="create-parkalot-car-park-management-test-user"></a>Parkalot - Car park management のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Parkalot - Car park management に作成します。 Parkalot - Car park management では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Parkalot - Car park management にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Parkalot - Car park management のサインオン URL にリダイレクトされます。 

* Parkalot - Car park management のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Parkalot - Car park management] タイルをクリックすると、Parkalot - Car park management のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Parkalot - Car park management を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。