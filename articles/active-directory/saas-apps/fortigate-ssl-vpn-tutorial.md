---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と FortiGate SSL VPN の統合 | Microsoft Docs
description: Azure Active Directory と FortiGate SSL VPN の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 18a3d9d5-d81c-478c-be7e-ef38b574cb88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecb53d661b1171f9c1b18d37d0bb35952645ba7e
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299713"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortigate-ssl-vpn"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と FortiGate SSL VPN の統合

このチュートリアルでは、FortiGate SSL VPN と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と FortiGate SSL VPN を統合すると、次のことができます。

* FortiGate SSL VPN にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して FortiGate SSL VPN に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* FortiGate SSL VPN でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* FortiGate SSL VPN では、**SP** initiated SSO がサポートされます。
* FortiGate SSL VPN を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-fortigate-ssl-vpn-from-the-gallery"></a>ギャラリーからの FortiGate SSL VPN の追加

Azure AD への FortiGate SSL VPN の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に FortiGate SSL VPN を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**FortiGate SSL VPN**」と入力します。
1. 結果のパネルから **[FortiGate SSL VPN]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-fortigate-ssl-vpn"></a>FortiGate SSL VPN に対する Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、FortiGate SSL VPN に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと FortiGate SSL VPN の関連ユーザーとの間にリンク関係を確立する必要があります。

FortiGate SSL VPN に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[FortiGate SSL VPN の SSO の構成](#configure-fortigate-ssl-vpn-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **FortiGate SSL VPN のテスト ユーザーの作成** - FortiGate SSL VPN で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **FortiGate SSL VPN** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[SAML でシングル サインオンをセットアップします]** ページで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<FQDN>/remote/login`

    b. **[識別子]** ボックスに、`https://<FQDN>/remote/saml/metadata` の形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://><FQDN/remote/saml/login` のパターンを使用して URL を入力します

    d. **[ログアウト URL]** テキスト ボックスに、`https://<FQDN>/remote/saml/logout` のパターンを使用して URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値は、実際のサインオン URL、識別子、応答 URL、およびログアウト URL で更新してください。 これらの値を取得するには、[FortiGate SSL VPN クライアント サポート チーム](mailto:tac_amer@fortinet.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. FortiGate SSL VPN アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、FortiGate SSL VPN アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらを次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前 |  ソース属性|
    | ------------ | --------- |
    | username | user.userprincipalname |
    | group | user.groups |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[FortiGate SSL VPN のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、FortiGate SSL VPN へのアクセスを許可することで、B.Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[FortiGate SSL VPN]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-a-security-group-for-the-test-user"></a>テスト ユーザー用のセキュリティ グループを作成する

このセクションでは、テスト ユーザー用に Azure Active Directory にセキュリティ グループを作成します。 このセキュリティ グループは、FortiGate で VPN 経由のユーザー ネットワーク アクセスを許可するために使用されます。

1. Azure portal の左側のペインで **[Azure Active Directory]** を選択し、 **[グループ]** を選択します。
1. 画面の上部にある **[新しいグループ]** を選択します。
1. **[新しいグループ]** のプロパティで、以下の手順を実行します。
   1. **[グループの種類]** フィールドには **[セキュリティ]** を選択します。
   1. **[名前]** フィールドに「`FortiGateAccess`」と入力します。
   1. **[グループの説明]** フィールドに「`Group for granting FortiGate VPN access`」と入力します。
   1. **[Azure AD roles can be assigned to the group (Preview)]\(Azure AD ロールをグループに割り当てることができる (プレビュー)\)** 設定には **[いいえ]** を選択します。
   1. **[メンバーシップの種類]** フィールドには **[割り当て済み]** を選択します。
   1. **[メンバー]** には **[メンバーが選択されていません]** を選択します。
   1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
   1. **［作成］** を選択します
1. Azure Active Directory の **[グループ]** ブレードに戻ったら、**FortiGate Access** グループを見つけ、後で使用できるように **[オブジェクト ID]** をメモします。

## <a name="configure-fortigate-ssl-vpn-sso"></a>FortiGate SSL VPN の SSO の構成

### <a name="upload-the-base64-saml-certificate-to-the-fortigate-appliance"></a>Base64 SAML 証明書を FortiGate アプライアンスにアップロードする

テナントで FortiGate アプリの SAML 構成を完了した後に、Base64 でエンコードされた SAML 証明書をダウンロードしました。 これを FortiGate アプライアンスにアップロードする必要があります。

1. FortiGate アプライアンスの管理ポータルにサインインします。
1. 左側のメニューで、 **[System]\(システム\)** をクリックします
1. **[System]\(システム\)** で **[Certificates]\(証明書\)** をクリックします
1. **[Import]\(インポート\)**  ->  **[Remote Certificate]\(リモート証明書\)** をクリックします。
1. Azure テナントの FortiGate アプリのデプロイからダウンロードした証明書を参照して選択し、 **[OK]** をクリックします

証明書がアップロードされたら、 **[System]\(システム\)**  >  **[Certificates]\(証明書\)**  >  **[Remote Certificate]\(リモート証明書\)** の名前をメモします。 既定では、REMOTE_Cert_**N** (**N** は整数値です) という名前です。

### <a name="perform-fortigate-command-line-configuration"></a>FortiGate のコマンドライン構成を実行する

次の手順では、Azure ログアウト URL を構成する必要があります。 この URL には疑問符 (?) が含まれています。 この文字を送信するには、特別な手順が必要です。 この手順は、FortiGate CLI コンソールからは実行できません。 代わりに、PuTTY などのツールを使用して FortiGate アプライアンスへの SSH セッションを確立します。 FortiGate アプライアンスが Azure 仮想マシンの場合、Azure 仮想マシンのシリアル コンソールから次の手順を実行できます。

これらの手順を実行するには、以前にメモした値が必要です。

- エンティティ ID
- [応答 URL]
- ログアウト URL
- Azure ログイン URL
- Azure AD 識別子
- Azure ログアウト URL
- Base64 SAML 証明書名 (REMOTE_Cert_N)

1. FortiGate アプライアンスへの SSH セッションを確立し、FortiGate 管理者アカウントでサインインします。
1. 次のコマンドを実行します。

   ```console
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert <Base64 SAML Certificate Name>
    set user-name username
    set group-name group
    end

   ```

   > [!NOTE]
   > **Azure ログアウト URL** には `?` 文字が含まれています。 FortiGate シリアル コンソールへの URL を正しく指定するには、特殊なキー シーケンスを入力する必要があります。 通常、URL は `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` です。
   >
   > シリアル コンソールで Azure ログアウト URL を入力するには、「`set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation`」と入力します。
   > 
   > 次に、Ctrl + V キーを押し、残りの URL を貼り付けて次のように行を完成させます。`set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`

### <a name="configure-fortigate-for-group-matching"></a>グループ照合のために FortiGate を構成する

このセクションでは、テスト ユーザーが属するセキュリティ グループのオブジェクト ID を認識するように FortiGate を構成します。 その結果、FortiGate ではこのグループ メンバーシップに基づいてアクセスを判断できるようになります。

これらの手順を実行するには、以前に作成した **FortiGateAccess** セキュリティ グループのオブジェクト ID が必要です

1. FortiGate アプライアンスへの SSH セッションを確立し、FortiGate 管理者アカウントでサインインします。
1. 次のコマンドを実行します。

   ```
    config user group
    edit FortiGateAccess
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <Object Id>
    next
    end
    next
    end
   ```

### <a name="create-fortigate-vpn-portals-and-firewall-policy"></a>FortiGate VPN ポータルとファイアウォール ポリシーを作成する

このセクションでは、以前に作成したセキュリティ グループ **FortiGateAccess** へのアクセス権を付与する FortiGate VPN ポータルとファイアウォール ポリシーを構成します。

 [FortiGate サポート チーム](mailto:tac_amer@fortinet.com)と連携して、VPN ポータルとファイアウォール ポリシーを FortiGate VPN プラットフォームに追加します。 シングル サインオンを使用する前に、これらの手順を完了する必要があります。

## <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [FortiGate SSL VPN] タイルをクリックすると、SSO を設定した FortiGate SSL VPN に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

Microsoft と FortiGate では、最適なエンド ユーザー エクスペリエンスを実現するために、Fortinet VPN クライアントである FortiClient を使用することをお勧めしています。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で FortiGate SSL VPN を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
