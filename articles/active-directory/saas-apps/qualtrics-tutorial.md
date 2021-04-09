---
title: チュートリアル:Azure Active Directory と SAP Qualtrics の統合 | Microsoft Docs
description: Azure Active Directory と SAP Qualtrics の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 2031864ea57a2f061c69219a2382429ee035804b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101652542"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-qualtrics"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と SAP Qualtrics の統合

このチュートリアルでは、SAP Qualtrics と Azure Active Directory (Azure AD) を統合する方法について説明します。 SAP Qualtrics と Azure AD を統合すると、次のことができます。

* SAP Qualtrics にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して SAP Qualtrics に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

## <a name="prerequisites"></a>前提条件

開始するには、以下が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な SAP Qualtrics サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* SAP Qualtrics では、**SP** Initiated SSO と **IDP** Initiated SSO がサポートされます。
* SAP Qualtrics では、**Just In Time** ユーザー プロビジョニングがサポートされます。

## <a name="add-sap-qualtrics-from-the-gallery"></a>ギャラリーからの SAP Qualtrics の追加

Azure AD への SAP Qualtrics の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に SAP Qualtrics を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左側のペインで、 **[Azure Active Directory]** を選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**SAP Qualtrics**」と入力します。
1. 結果から **[SAP Qualtrics]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-qualtrics"></a>SAP Qualtrics の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、SAP Qualtrics に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと SAP Qualtrics の関連ユーザーとの間にリンク関係を確立する必要があります。

SAP Qualtrics で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. [Azure AD SSO を構成](#configure-azure-ad-sso)して、ユーザーがこの機能を使用できるようにします。
    1. [Azure AD のテスト ユーザーを作成](#create-an-azure-ad-test-user)して、B.Simon を使って Azure AD のシングル サインオンをテストします。
    1. [Azure AD テスト ユーザーを割り当て](#assign-the-azure-ad-test-user)て、B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. [SAP Qualtrics の SSO を構成](#configure-sap-qualtrics-sso)して、アプリケーション側でシングル サインオン設定を構成します。
    1. [SAP Qualtrics のテスト ユーザーを作成](#create-sap-qualtrics-test-user)し、B.Simon に対応するユーザーを SAP Qualtrics に作成して、Azure AD の B.Simon にリンクさせます。
1. [SSO をテスト](#test-sso)して、構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **SAP Qualtrics** アプリケーション統合ページで、 **[管理]** セクションを見つけます。 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンを選択して設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[SAML によるシングル サインオンのセットアップ]** ページで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。
    
    a. **[識別子]** ボックスに、次の形式で URL を入力します。

    `https://< DATACENTER >.qualtrics.com`
   
    b. **[応答 URL]** ボックスに、次の形式で URL を入力します。

    `https://< DATACENTER >.qualtrics.com/login/v1/sso/saml2/default-sp`

    c. **[リレー状態]** ボックスに、次の形式で URL を入力します。

    `https://< brandID >.< DATACENTER >.qualtrics.com`

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** を選択して次の手順を実行します。

    **[サインオン URL]** ボックスに、次の形式で URL を入力します。

    `https://< brandID >.< DATACENTER >.qualtrics.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、応答 URL、リレー状態でこれらの値を更新します。 これらの値を取得するには、[Qualtrics クライアント サポート チーム](https://www.qualtrics.com/support/)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML によるシングル サインオンのセットアップ]** ページの **[SAML 署名証明書]** セクションで、コピー アイコンを選択して **[アプリのフェデレーション メタデータ URL]** をコピーし、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

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

このセクションでは、SAP Qualtrics へのアクセスを許可することで、B.Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。
1. アプリケーションの一覧で **[SAP Qualtrics]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択します。 次に、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧から **[B.Simon]** を選択します。 次に、画面の下部にある **[選択]** を選択します。
1. SAML アサーション内にロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 次に、画面の下部にある **[選択]** を選択します。
1. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

## <a name="configure-sap-qualtrics-sso"></a>SAP Qualtrics の SSO の構成

SAP Qualtrics 側でシングル サインオンを構成するには、Azure portal からコピーした **アプリのフェデレーション メタデータ URL** を [SAP Qualtrics サポート チーム](https://www.qualtrics.com/support/)に送信します。 サポート チームは、SAML SSO 接続が両方の側で正しく設定されていることを確認します。

### <a name="create-sap-qualtrics-test-user"></a>SAP Qualtrics のテスト ユーザーの作成

SAP Qualtrics では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 追加の操作は必要ありません。 SAP Qualtrics にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる SAP Qualtrics のサインオン URL にリダイレクトされます。  

* SAP Qualtrics のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した SAP Qualtrics に自動的にサインインされます。

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [SAP Qualtrics] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した SAP Qualtrics に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

SAP Qualtrics を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 詳細については、[Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)に関するページを参照してください。