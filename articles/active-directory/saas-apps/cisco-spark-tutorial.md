---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Cisco Webex の統合 | Microsoft Docs
description: Azure Active Directory と Cisco Webex の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: ad6d5308638b112afe2b51c4e149f876651e429d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592525"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Cisco Webex の統合

このチュートリアルでは、Cisco Webex を Azure Active Directory (Azure AD) と統合する方法について説明します。 Cisco Webex を Azure AD と統合すると、次のことができます。

* Cisco Webex にアクセスする Azure AD ユーザーを制御します。
* ユーザーが自分の Azure AD アカウントを使用して Cisco Webex に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Cisco Webex でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Cisco Webex では、**SP** Initiated SSO がサポートされます。
* Cisco Webex では、[**自動化されたユーザー プロビジョニング**](./cisco-webex-provisioning-tutorial.md)がサポートされます。

## <a name="adding-cisco-webex-from-the-gallery"></a>ギャラリーからの Cisco Webex の追加

Azure AD への Cisco Webex の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Cisco Webex を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Cisco Webex**」と入力します。
1. 結果パネルで **[Cisco Webex]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-cisco-webex"></a>Cisco Webex の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Cisco Webex に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと Cisco Webex の関連ユーザーの間で、リンク関係を確立する必要があります。

Cisco Webex に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[Cisco Webex の構成](#configure-cisco-webex)** - アプリケーション側で SSO 設定を構成します。
    1. **[Cisco Webex のテスト ユーザーの作成](#create-cisco-webex-test-user)** - Cisco Webex で B.Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
3. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Cisco Webex** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、ダウンロードした **サービス プロバイダー メタデータ ファイル** をアップロードし、次の手順を実行してアプリケーションを構成します。

    >[!Note]
    >サービス プロバイダーのメタデータ ファイルは、このチュートリアルで後述する「**Cisco Webex の構成**」セクションで取得します。 

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    b. **フォルダー ロゴ** をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。

    c. サービス プロバイダー メタデータ ファイルのアップロードが正常に完了すると、次のように、**識別子** と **応答 URL** の値が **[基本的な SAML 構成]** セクションに自動的に入力されます。

    **[サインオン URL]** テキストボックスに、SP メタデータ ファイルのアップロードによって自動入力される **[応答 URL]** の値を貼り付けます。

1. Cisco Webex アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、Cisco Webex アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
  
    | 名前 |  ソース属性|
    | ---------------|--------- |
    | uid | user.userprincipalname |

    > [!NOTE]
    >  既定では、ソース属性の値が userpricipalname にマップされます。 これは、user.mail か user.onpremiseuserprincipalname のほか、Webex の設定に基づく任意の値に変更することができます。


1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードしてコンピューターに保存します。

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

このセクションでは、B.Simon に Cisco Webex へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Cisco Webex]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-cisco-webex"></a>Cisco Webex の構成

1. 管理者の資格情報を使用して Cisco Webex にサインインします。

1. **[Organization Settings]\(組織の設定\)** を選択し、 **[認証]** セクションの **[変更]** をクリックします。

    ![スクリーンショットは、[変更] を選択できる [Authentication Settings]\(認証設定\) を示しています。](./media/cisco-spark-tutorial/organization-settings.png)
  
1. **[Integrate a 3rd-party identity provider.(Advanced)]\(サード パーティの ID プロバイダーを統合する。(詳細)\)** を選択して、 **[Next]\(次へ\)** をクリックします。

    ![サード パーティの ID プロバイダーの統合を示すスクリーンショット。](./media/cisco-spark-tutorial/enterprise-settings.png)

1. **[Download Metadata File]\(メタデータ ファイルのダウンロード\)** をクリックして **サービス プロバイダー メタデータ ファイル** をダウンロードし、それを自分のコンピューターに保存して、 **[Next]\(次へ\)** をクリックします。

    ![サービス プロバイダー メタデータ ファイルを示すスクリーンショット。](./media/cisco-spark-tutorial/sp-metadata.png)

1. **[file browser]\(ファイル ブラウザー\)** オプションをクリックして Azure AD メタデータ ファイルを検索、アップロードします。 次に、 **[Require certificate signed by a certificate authority in Metadata (more secure)(証明機関の署名付き証明書がメタデータに必要 (高セキュリティ))]** を選択し、 **[Next (次へ)]** をクリックします。

    ![スクリーンショットは、[Import Idp Metadata] \(IdP メタデータのインポート) ページを示しています。](./media/cisco-spark-tutorial/idp-metadata.png)

1. **[Test SSO Connection (SSO 接続のテスト)]** を選択し、ブラウザーの新しいタブが開いたら、サインインして Azure AD で認証します。

1. ブラウザーの **[Cisco Cloud Collaboration Management]** タブに戻ります。テストが成功した場合は、 **[This test was successful.Enable Single Sign-On option] \(このテストは正常に完了しました。シングル サインオン オプションを有効にします)** を選択して、 **[次へ]** をクリックします。

1. **[保存]** をクリックします。

> [!NOTE]
> Cisco Webex の構成方法について詳しくは、[こちら](https://help.webex.com/WBX000022701/How-Do-I-Configure-Microsoft-Azure-Active-Directory-Integration-with-Cisco-Webex-Through-Site-Administration#:~:text=In%20the%20Azure%20portal%2C%20select,in%20the%20Add%20Assignment%20dialog)のページを参照してください。

### <a name="create-cisco-webex-test-user"></a>Cisco Webex のテスト ユーザーの作成

このセクションでは、B.Simon というユーザーが Cisco Webex に作成されます。このアプリケーションは自動ユーザー プロビジョニングをサポートしているため、ビジネス ルールに基づく自動プロビジョニングと自動プロビジョニング解除が可能です。  可能な限り自動プロビジョニングを使用することをお勧めします。 [Cisco Webex](./cisco-webex-provisioning-tutorial.md) の自動プロビジョニングを有効にする方法をご覧ください。

ユーザーを手動で作成する必要がある場合は、次の手順を実行します。

1. 管理者の資格情報を使用して Cisco Webex にサインインします。

2. **[Users]\(ユーザー\)** と **[Manage Users ]\(ユーザーの管理\)** を順にクリックします。
   
    ![スクリーンショットは、[Manage Users ]\(ユーザーの管理\) を設定できる [Users]\(ユーザー\) ページを示しています。](./media/cisco-spark-tutorial/user-1.png) 

3. **[Manage Users]\(ユーザーの管理\)** ウィンドウで **[Manually Add or Modify Users]\(ユーザーを手動で追加または変更\)** を選択します。

    ![[Users]\(ユーザー\) ページのスクリーンショット。[Manage Users]\(ユーザーの管理\) で [Manually Add or Modify Users]\(ユーザーを手動で追加または変更\) を選択することができます。](./media/cisco-spark-tutorial/user-2.png)

4. **[Names and Email address (名前と電子メール アドレス)]** を選択します。 次のようにテキスト ボックスに入力します。

    ![スクリーンショットは、ユーザーを手動で追加または変更できる [Manage User]\(ユーザーの管理\) ダイアログ ボックスを示しています。](./media/cisco-spark-tutorial/user-3.png) 

    a. **[First Name]\(名\)** ボックスに、ユーザーの名を入力します (この例では **B**)。

    b. **[姓]** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。

    c. **[Email address]\(メール アドレス\)** ボックスに、ユーザーのメール アドレス (b.simon@contoso.com など) を入力します。

5. プラス記号をクリックして、B.Simon を追加します。 次に、 **[次へ]** をクリックします。

6. **[Add Services for Users]\(ユーザーのサービスを追加\)** ウィンドウで **[Add Users]\(ユーザーの追加\)** をクリックし、 **[Finish]\(完了\)** をクリックします。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Cisco Webex のサインオン URL にリダイレクトされます。 

* Cisco Webex のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Cisco Webex] タイルをクリックすると、Cisco Webex のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Cisco Webex を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。