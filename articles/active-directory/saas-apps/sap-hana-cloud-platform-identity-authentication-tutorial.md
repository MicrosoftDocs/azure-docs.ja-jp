---
title: チュートリアル:Azure Active Directory と SAP Cloud Platform Identity Authentication の統合 | Microsoft Docs
description: Azure Active Directory と SAP Cloud Platform Identity Authentication の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/28/2020
ms.author: jeedes
ms.openlocfilehash: 2d0b53581bbc2211df156b90326fc73d0c2ae091
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548721"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-platform-identity-authentication"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と SAP Cloud Platform Identity Authentication の統合

このチュートリアルでは、SAP Cloud Platform Identity Authentication と Azure Active Directory (Azure AD) を統合する方法について説明します。 SAP Cloud Platform Identity Authentication を Azure AD と統合すると、次のことができます。

* SAP Cloud Platform Identity Authentication にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して SAP Cloud Platform Identity Authentication に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* SAP Cloud Platform Identity Authentication でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* SAP Cloud Platform Identity Authentication では、**SP** および **IDP** によって開始される SSO がサポートされます
* SAP Cloud Platform Identity Authentication を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)をご覧ください。

技術的な詳細の説明に入る前に、調べようとしている事柄の概念を理解する必要があります。 SAP Cloud Platform Identity Authentication と Active Directory フェデレーション サービスにより、SAP Cloud Platform Identity Authentication で保護された SAP アプリケーションおよびサービスに対し、(IdP としての) Azure AD によって保護されたアプリケーションまたはサービス全体で SSO を実装できます。

現時点では、SAP Cloud Platform Identity Authentication は、SAP アプリケーションへのプロキシ ID プロバイダーとして機能します。 さらに Azure Active Directory は、このセットアップにおける主要な ID プロバイダーとして機能します。 

次の図にこの関係を示します。

![Azure AD のテスト ユーザーの作成](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

このセットアップにより、SAP Cloud Platform Identity Authentication テナントは、Azure Active Directory で信頼されたアプリケーションとして構成されます。

この方法で保護するすべての SAP アプリケーションおよびサービスは、その後で SAP Cloud Platform Identity Authentication 管理コンソールで構成されます。

つまり、SAP アプリケーションおよびサービスへのアクセスの許可のための承認は、(Azure Active Directory ではなく) SAP Cloud Platform Identity Authentication で行われる必要があります。

Azure Active Directory Marketplace を通じて SAP Cloud Platform Identity Authentication をアプリケーションとして構成すれば、要求/SAML アサーションを個別に構成する必要がありません。

> [!NOTE]
> 現在、その両者で Web SSO のみがテストされています。 アプリ対API または API 対 API の通信に必要なフローは機能するものの、まだテストされていません。 そのテストは後のアクティビティ中に行われる予定です。

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>ギャラリーからの SAP Cloud Platform Identity Authentication の追加

Azure AD への SAP Cloud Platform Identity Authentication の統合を構成するには、SAP Cloud Platform Identity Authentication をギャラリーから一連のマネージド SaaS アプリに追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**SAP Cloud Platform Identity Authentication**」と入力します。
1. 結果のパネルから **[SAP Cloud Platform Identity Authentication]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-cloud-platform-identity-authentication"></a>SAP Cloud Platform Identity Authentication に対する Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、SAP Cloud Platform Identity Authentication に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと SAP Cloud Platform Identity Authentication の関連ユーザーとの間にリンク関係を確立する必要があります。

SAP Cloud Platform Identity Authentication で Azure AD の SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[SAP Cloud Platform Identity Authentication SSO の構成](#configure-sap-cloud-platform-identity-authentication-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[SAP Cloud Platform Identity Authentication テスト ユーザーの作成](#create-sap-cloud-platform-identity-authentication-test-user)** - SAP Cloud Platform Identity Authentication で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **SAP Cloud Platform Identity Authentication** アプリケーション統合ページで **[管理]** セクションを探し、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、**IDP** Initiated モードで構成する場合は、次の手順を実行します。

    ![[SAP Cloud Platform Identity Authentication] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`<IAS-tenant-id>.accounts.ondemand.com` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[SAP Cloud Platform Identity Authentication クライアント サポート チーム](https://cloudplatform.sap.com/capabilities/security/trustcenter.html)にお問い合わせください。 識別子の値がわからない場合は、[テナントの SAML 2.0 の構成](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html)に関する SAP Cloud Platform Identity Authentication のドキュメントをお読みください。

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[SAP Cloud Platform Identity Authentication] のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、`{YOUR BUSINESS APPLICATION URL}` という形式で URL を入力します。

    > [!NOTE]
    > これは実際の値ではありません。 この値を実際のサインオン URL で更新してください。 特定のビジネス アプリケーションのサインオン URL を使ってください。 わからないことがある場合は、[SAP Cloud Platform Identity Authentication クライアント サポート チーム](https://cloudplatform.sap.com/capabilities/security/trustcenter.html)にお問い合わせください。

1. SAP Cloud Platform Identity Authentication アプリケーションでは、特定の形式の SAML アサーションを受け取るため、SAML トークン属性の構成にカスタム属性マッピングを追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. 上記に加えて、SAP Cloud Platform Identity Authentication アプリケーションでは、以下に示す SAML 応答で返される属性がほとんどないことが想定されます。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 | ソース属性|
    | ---------------| --------------- |
    | firstName | User.givenname |

8. **[Set up Single Sign-On with SAML]\(SAML でシングル サインオンをセットアップします\)**  ページの **[SAML Signing Certificate]\(SAML 署名証明書\)** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションから**メタデータ XML**をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

9. **[SAP Cloud Platform Identity Authentication のセットアップ]** セクションで、要件のとおりに適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

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

このセクションでは、B.Simon に SAP Cloud Platform Identity Authentication へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[SAP Cloud Platform Identity Authentication]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-sap-cloud-platform-identity-authentication-sso"></a>SAP Cloud Platform Identity Authentication SSO の構成

1. アプリケーション用に構成された SSO を入手するために、SAP Cloud Platform Identity Authentication 管理コンソールにアクセスします。 URL は `https://<tenant-id>.accounts.ondemand.com/admin` というパターンです。 次に、「[Integration with Microsoft Azure AD (Microsoft Azure AD との統合)](https://developers.sap.com/tutorials/cp-ias-azure-ad.html)」で SAP Cloud Platform Identity Authentication に関するドキュメントを読みます。

2. Azure Portal で **[保存]** ボタンを選択します。

3. 以降は、もう 1 つの SAP アプリケーションに対して SSO を追加して有効にする場合にのみ行います。 「**ギャラリーからの SAP Cloud Platform Identity Authentication の追加**」セクションの手順を繰り返してください。

4. Azure Portal の **SAP Cloud Platform Identity Authentication** アプリケーション統合ページで、 **[リンクされたサインオン]** を選択します。

    ![リンクされたサインオンの構成](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. 構成を保存します。

> [!NOTE]
> 新しいアプリケーションでは、前の SAP アプリケーションのシングル サインオン構成が再利用されます。 SAP Cloud Platform Identity Authentication 管理コンソールで、同じ会社の ID プロバイダーを使用していることを確認してください。

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>SAP Cloud Platform Identity Authentication のテスト ユーザーの作成

SAP Cloud Platform Identity Authentication でユーザーを作成する必要はありません。 Azure AD ユーザー ストアに存在するユーザーは、SSO 機能を使用できます。

SAP Cloud Platform Identity Authentication は、ID フェデレーション オプションをサポートしています。 このオプションにより、アプリケーションは、企業の ID プロバイダーによって認証されたユーザーが SAP Cloud Platform Identity Authentication のユーザー ストアに存在するかどうかを確認できます。

既定では、ID フェデレーション オプションは無効になっています。 ID フェデレーションが有効になっていると、SAP Cloud Platform Identity Authentication にインポートされているユーザーのみがアプリケーションにアクセスできます。

SAP Cloud Platform Identity Authentication との ID フェデレーションを有効または無効にする方法の詳細については、[SAP Cloud Platform Identity Authentication のユーザー ストアとの ID フェデレーションの構成](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/c029bbbaefbf4350af15115396ba14e2.html)に関するページで、SAP Cloud Platform Identity Authentication との ID フェデレーションの有効化に関するセクションを参照してください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルの [SAP Cloud Platform Identity Authentication] タイルをクリックすると、SSO を設定した SAP Cloud Platform Identity Authentication に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で SAP Cloud Platform Identity Authentication を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって SAP Cloud Platform Identity Authentication を保護する方法](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)