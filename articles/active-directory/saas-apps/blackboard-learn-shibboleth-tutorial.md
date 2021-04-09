---
title: 'チュートリアル: Azure Active Directory と Blackboard Learn - Shibboleth の統合 | Microsoft Docs'
description: Azure Active Directory と Blackboard Learn - Shibboleth の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: b1b3f265d0e1fcad2953292c5227c2630c6df229
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649903"
---
# <a name="tutorial-azure-active-directory-integration-with-blackboard-learn---shibboleth"></a>チュートリアル: Azure Active Directory と Blackboard Learn - Shibboleth との統合

このチュートリアルでは、Blackboard Learn - Shibboleth を Azure Active Directory (Azure AD) と統合する方法について説明します。 Azure AD と Blackboard Learn - Shibboleth を統合すると、次のことができます。

* Blackboard Learn - Shibboleth にアクセスできる Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して Blackboard Learn - Shibboleth に自動的にサインインできるように設定する。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。
 
* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Blackboard Learn - Shibboleth でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Blackboard Learn - Shibboleth では、**SP** Initiated SSO がサポートされます

## <a name="add-blackboard-learn---shibboleth-from-the-gallery"></a>ギャラリーからの Blackboard Learn - Shibboleth の追加

Azure AD への Blackboard Learn - Shibboleth の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Blackboard Learn - Shibboleth を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Blackboard Learn - Shibboleth**」と入力します。
1. 結果のパネルから **[Blackboard Learn - Shibboleth]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-blackboard-learn---shibboleth"></a>Blackboard Learn - Shibboleth の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Blackboard Learn - Shibboleth に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Blackboard Learn - Shibboleth の関連ユーザーとの間にリンク関係を確立する必要があります。

Blackboard Learn - Shibboleth で Azure AD SSO を構成およびテストするには、次の手順に従います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Blackboard Learn - Shibboleth の SSO の構成](#configure-blackboard-learn---shibboleth-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Blackboard Learn - Shibboleth のテスト ユーザーの作成](#create-blackboard-learn---shibboleth-test-user)** - Blackboard Learn - Shibboleth で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Blackboard Learn - Shibboleth で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. Azure portal の **Blackboard Learn - Shibboleth** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

3. **[SAML でシングル サインオンをセットアップします]** ページで、鉛筆アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、`https://<yourblackoardlearnserver>.blackboardlearn.com/Shibboleth.sso/Login` という形式で URL を入力します。

    b. **[識別子]** ボックスに、`https://<yourblackoardlearnserver>.blackboardlearn.com/shibboleth-sp` という形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<yourblackoardlearnserver>.blackboardlearn.com/Shibboleth.sso/SAML2/POST` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 これらの値を取得するには、[Blackboard Learn - Shibboleth クライアント サポート チーム](https://www.blackboard.com/forms/contact-us_form.aspx)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Blackboard Learn - Shibboleth のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Blackboard Learn - Shibboleth へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Blackboard Learn - Shibboleth]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="configure-blackboard-learn---shibboleth-sso"></a>Blackboard Learn - Shibboleth SSO の構成

**Blackboard Learn - Shibboleth** 側でシングル サインオンを構成するには、ダウンロードした **フェデレーション メタデータ XML** と Azure portal からコピーした適切な URL を [Blackboard Learn - Shibboleth サポート チーム](https://www.blackboard.com/forms/contact-us_form.aspx)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-blackboard-learn---shibboleth-test-user"></a>Blackboard Learn - Shibboleth のテスト ユーザーの作成

このセクションでは、Blackboard Learn - Shibboleth で Britta Simon というユーザーを作成します。 [Blackboard Learn - Shibboleth サポート チーム](https://www.blackboard.com/forms/contact-us_form.aspx)と協力して、Blackboard Learn - Shibboleth プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Blackboard Learn - Shibboleth のサインオン URL にリダイレクトされます。 

* Blackboard Learn - Shibboleth のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Blackboard Learn - Shibboleth] タイルをクリックすると、SSO を設定した Blackboard Learn - Shibboleth に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Blackboard Learn - Shibboleth を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。