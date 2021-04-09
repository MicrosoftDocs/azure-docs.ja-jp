---
title: チュートリアル:Azure Active Directory と Splunk Enterprise and Splunk Cloud の統合 | Microsoft Docs
description: Azure Active Directory と Splunk Enterprise and Splunk Cloud の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: jeedes
ms.openlocfilehash: 81107b3655ae86d51e36445ce46661d553ab3b5a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101649855"
---
# <a name="tutorial-azure-active-directory-integration-with-splunk-enterprise-and-splunk-cloud"></a>チュートリアル:Azure Active Directory と Splunk Enterprise and Splunk Cloud の統合

このチュートリアルでは、Splunk Enterprise and Splunk Cloud と Azure Active Directory (Azure AD) を統合する方法について説明します。 Splunk Enterprise and Splunk Cloud と Azure AD を統合すると、次のことができます。

* Splunk Enterprise and Splunk Cloud にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して自動的に Splunk Enterprise and Splunk Cloud にサインインできるように設定する。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Splunk Enterprise and Splunk Cloud でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Splunk Enterprise and Splunk Cloud では、**SP** によって開始される SSO がサポートされます

## <a name="add-splunk-enterprise-and-splunk-cloud-from-the-gallery"></a>ギャラリーからの Splunk Enterprise and Splunk Cloud の追加

Azure AD への Splunk Enterprise and Splunk Cloud の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Splunk Enterprise and Splunk Cloud を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Splunk Enterprise and Splunk Cloud**」と入力します。
1. 結果のパネルから **Splunk Enterprise and Splunk Cloud** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-splunk-enterprise-and-splunk-cloud"></a>Splunk Enterprise and Splunk Cloud の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Splunk Enterprise and Splunk Cloud に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと Splunk Enterprise and Splunk Cloud の関連ユーザーとの間にリンク関係を確立する必要があります。

Splunk Enterprise and Splunk Cloud で Azure AD SSO を構成してテストするには、次の手順に従います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Splunk Enterprise and Splunk Cloud SSO の構成](#configure-splunk-enterprise-and-splunk-cloud-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Splunk Enterprise and Splunk Cloud のテスト ユーザーの作成](#create-splunk-enterprise-and-splunk-cloud-test-user)** - Splunk Enterprise and Splunk Cloud で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Splunk Enterprise and Splunk Cloud** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)
4. **[基本的な SAML 構成]** セクションで、次のパターンを実行します。

    a. **[サインオン URL]** ボックスに、`https://<splunkserverUrl>/app/launcher/home` という形式で URL を入力します。

    b. **[識別子]** ボックスに、`<splunkserverUrl>` という形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<splunkserver>/saml/acs` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 これらの値を取得するには、[Splunk Enterprise and Splunk Cloud クライアント サポート チーム](https://www.splunk.com/en_us/about-splunk/contact-us.html)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

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

このセクションでは、B.Simon に Splunk Enterprise and Splunk Cloud へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Splunk Enterprise and Splunk Cloud]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-splunk-enterprise-and-splunk-cloud-sso"></a>Splunk Enterprise and Splunk Cloud SSO の構成

  **Splunk Enterprise and Splunk Cloud** 側でシングル サインオンを構成するには、ダウンロードした **フェデレーション メタデータ XML** と Azure portal からコピーした適切な URL を [Splunk Enterprise and Splunk Cloud サポート チーム](https://www.splunk.com/en_us/about-splunk/contact-us.html)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-splunk-enterprise-and-splunk-cloud-test-user"></a>Splunk Enterprise and Splunk Cloud のテスト ユーザーの作成

このセクションでは、Splunk Enterprise and Splunk Cloud で Britta Simon というユーザーを作成します。 [Splunk Enterprise and Splunk Cloud サポート チーム](https://www.splunk.com/en_us/about-splunk/contact-us.html)と連携して、Splunk Enterprise and Splunk Cloud プラットフォームでユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Splunk Enterprise and Splunk Cloud のサインオン URL にリダイレクトされます。 

* Splunk Enterprise and Splunk Cloud のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Splunk Enterprise and Splunk Cloud] タイルをクリックすると、Splunk Enterprise and Splunk Cloud サインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Splunk Enterprise and Splunk Cloud を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。