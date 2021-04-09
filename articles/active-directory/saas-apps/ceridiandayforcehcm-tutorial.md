---
title: 'チュートリアル: Azure Active Directory と Ceridian Dayforce HCM の統合 | Microsoft Docs'
description: Azure Active Directory と Ceridian Dayforce HCM の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 1f2e01a79f980e63102bb6538859f0da30c555c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651849"
---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>チュートリアル: Azure Active Directory と Ceridian Dayforce HCM の統合

このチュートリアルでは、Ceridian Dayforce HCM と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Ceridian Dayforce HCM を統合すると、次のことができます。

* Ceridian Dayforce HCM にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで Ceridian Dayforce HCM に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Ceridian Dayforce HCM でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Ceridian Dayforce HCM では、**SP** によって開始される SSO がサポートされます

## <a name="add-ceridian-dayforce-hcm-from-the-gallery"></a>ギャラリーからの Ceridian Dayforce HCM の追加

Azure AD への Ceridian Dayforce HCM の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Ceridian Dayforce HCM を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Ceridian Dayforce HCM**」と入力します。
1. 結果のパネルから **[Ceridian Dayforce HCM]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-ceridian-dayforce-hcm"></a>Ceridian Dayforce HCM の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Ceridian Dayforce HCM に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Ceridian Dayforce HCM の関連ユーザーとの間にリンク関係を確立する必要があります。

Ceridian Dayforce HCM で Azure AD SSO を構成してテストするには、次の手順に従います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Ceridian Dayforce HCM の SSO の構成](#configure-ceridian-dayforce-hcm-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Ceridian Dayforce HCM のテスト ユーザーの作成](#create-ceridian-dayforce-hcm-test-user)** - Ceridian Dayforce HCM で Britta Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成 

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Ceridian Dayforce HCM** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Ceridian Dayforce HCM のドメインと URL] のシングル サインオン情報](common/sp-identifier-reply.png)

    a. **[サインオン URL]** ボックスに、ユーザーが Ceridian Dayforce HCM アプリケーションへのサインオンに使用する URL を入力します。

    | 環境 | URL |
    | :-- | :-- |
    | 実稼動用 | `https://sso.dayforcehcm.com/<DayforcehcmNamespace>` |
    | テスト用 | `https://ssotest.dayforcehcm.com/<DayforcehcmNamespace>` |

    b. **[識別子]** ボックスに、 のパターンを使用して URL を入力します。

    | 環境 | URL |
    | :-- | :-- |
    | 実稼動用 | `https://ncpingfederate.dayforcehcm.com/sp` |
    | テスト用 | `https://fs-test.dayforcehcm.com/sp` |

    c. **[応答 URL]** テキストボックスで、Azure AD が応答を投稿するために使用する URL を入力します。

    | 環境 | URL |
    | :-- | :-- |
    | 実稼動用 | `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` |
    | テスト用 | `https://fs-test.dayforcehcm.com/sp/ACS.saml2` |

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 これらの値を取得するには、[Ceridian Dayforce HCM クライアント サポート チーム](https://www.ceridian.com/support)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. Ceridian Dayforce HCM アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** ボタンをクリックして **[ユーザー属性]** ダイアログを開きます。

    ![このスクリーンショットは、[編集] アイコンが選択された状態の [User Attributes]\(ユーザー属性\) を示しています。](common/edit-attribute.png)

6. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、上の図のように SAML トークン属性を構成し、次の手順を実行します。

    | 名前 | ソース属性|
    | ---------| --------- |
    | name  | user.extensionattribute2 |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![[新しい要求の追加] オプションが備わっている [ユーザー要求] のスクリーンショット。](common/new-save-attribute.png)

    ![スクリーンショットは、説明されている値を入力できる [ユーザー要求の管理] ダイアログ ボックスを示しています。](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** 一覧から、実装で使用するユーザー属性を選択します。 たとえば、一意のユーザー識別子として EmployeeID を使用し、その属性値を ExtensionAttribute2 に保存している場合、[user.extensionattribute2] を選択します。

    f. **[OK]** をクリックします。

    g. **[保存]** をクリックします。

7. **[Set up Single Sign-On with SAML]\(SAML でシングル サインオンをセットアップします\)**  ページの **[SAML Signing Certificate]\(SAML 署名証明書\)** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションから **メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

8. **[Ceridian Dayforce HCM のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Ceridian Dayforce HCM へのアクセスを許可することによって、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Ceridian Dayforce HCM]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="configure-ceridian-dayforce-hcm-sso"></a>Ceridian Dayforce HCM の SSO の構成

**Ceridian Dayforce HCM** 側でシングル サインオンを構成するには、ダウンロードした **メタデータ XML** と Azure portal からコピーした適切な URL を [Ceridian Dayforce HCM サポート チーム](https://www.ceridian.com/support)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-ceridian-dayforce-hcm-test-user"></a>Ceridian Dayforce HCM のテスト ユーザーの作成

このセクションでは、Ceridian Dayforce HCM で Britta Simon というユーザーを作成します。 [Ceridian Dayforce HCM サポート チーム](https://www.ceridian.com/support)と連携して、Ceridian Dayforce HCM プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Ceridian Dayforce HCM のサインオン URL にリダイレクトされます。 

* Ceridian Dayforce HCM のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Ceridian Dayforce HCM] タイルをクリックすると、Ceridian Dayforce HCM のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Ceridian Dayforce HCM を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。