---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と FortiGate SSL VPN の統合 | Microsoft Docs
description: FortiGate SSL VPN と Azure Active Directory (Azure AD) を統合するために必要な手順について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/26/2020
ms.author: jeedes
ms.openlocfilehash: 9852752799fd010ebb069637f55008d9c4f68bf8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98732131"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortigate-ssl-vpn"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と FortiGate SSL VPN の統合

このチュートリアルでは、FortiGate SSL VPN と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と FortiGate SSL VPN を統合すると、次のことができます。

* Azure AD を使用して、FortiGate SSL VPN にアクセスできるユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して FortiGate SSL VPN に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な FortiGate SSL VPN サブスクリプション。

## <a name="tutorial-description"></a>チュートリアルの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

FortiGate SSL VPN では、SP Initiated SSO がサポートされます。


## <a name="add-fortigate-ssl-vpn-from-the-gallery"></a>ギャラリーからの FortiGate SSL VPN の追加

Azure AD への FortiGate SSL VPN の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に FortiGate SSL VPN を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左ウィンドウで、 **[Azure Active Directory]** を選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**FortiGate SSL VPN**」と入力します。
1. 結果のパネルで **[FortiGate SSL VPN]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-fortigate-ssl-vpn"></a>FortiGate SSL VPN に対する Azure AD SSO の構成とテスト

B.Simon というテスト ユーザーを使用して、FortiGate SSL VPN に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと FortiGate SSL VPN の関連ユーザーとの間にリンク関係を確立する必要があります。

FortiGate SSL VPN で Azure AD SSO を構成してテストするには、これらの大まかな手順に従います。

1. **[Azure AD SSO を構成](#configure-azure-ad-sso)** して、この機能をユーザーに対して有効にします。
    1. Azure AD のシングル サインオンをテストするための **[Azure AD テスト ユーザーを作成](#create-an-azure-ad-test-user)** します。
    1. **[テスト ユーザーにアクセス権を付与](#grant-access-to-the-test-user)** して、そのユーザーに対して Azure AD シングル サインオンを有効にします。
1. アプリケーション側で **[FortiGate SSL VPN SSO を構成](#configure-fortigate-ssl-vpn-sso)** します。
    1. Azure AD のユーザーに対応するユーザーとして、**FortiGate SSL VPN テスト ユーザーを作成** します。
1. **[SSO をテスト](#test-sso)** して、構成が正しく機能することを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **FortiGate SSL VPN** アプリケーション統合ページで、 **[管理]** セクションの **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の鉛筆ボタンを選択して設定を編集します。

   ![基本的な SAML 構成を編集するための鉛筆ボタンを示すスクリーンショット。](common/edit-urls.png)

1. **[SAML によるシングル サインオンのセットアップ]** ページで、次の値を入力します。

    a. **[サイン オン URL]** ボックスに、`https://<FQDN>/remote/login` というパターンの URL を入力します。

    b. **[識別子]** ボックスに、`https://<FQDN>/remote/saml/metadata` というパターンで URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<FQDN>/remote/saml/login` というパターンで URL を入力します。

    d. **[ログアウト URL]** ボックスに、`https://<FQDN>/remote/saml/logout` というパターンで URL を入力します。

    > [!NOTE]
    > これらの値は、ただのパターンです。 実際の **サインオン URL**、**識別子**、**応答 URL**、および **ログアウト URL** を使用する必要があります。 ガイダンスについては、[Fortinet サポート](https://support.fortinet.com)にお問い合わせください。 Fortinet のドキュメントや Azure portal の **[基本的な SAML 構成]** セクションに示されているパターン例を参照することもできます。

1. FortiGate SSL VPN アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![既定の属性を示しているスクリーンショット。](common/default-attributes.png)

1. 次の表に、FortiGate SSL VPN に追加で必要な要求を 2 つ示します。 これらの要求の名前は、このチュートリアルの **FortiGate コマンドライン構成の実行** に関するセクションで使用されている名前と一致する必要があります。 

   | 名前 |  ソース属性|
   | ------------ | --------- |
   | username | user.userprincipalname |
   | group | user.groups |
   
   これらの追加の要求を作成するには、次の操作を実行します。

   a. **[ユーザー属性と要求]** の横にある **[編集]** を選択します。

   b. **[新しい要求の追加]** を選択します。

   c. **[名前]** で、**ユーザー名** を入力します。

   d. **[ソース属性]** で、**user.userprincipalname** を選択します。

   e. **[保存]** を選択します。

   f. **[グループ要求を追加する]** を選択します。

   g. **[すべてのグループ]** を選択します。

   h. **[グループ要求の名前をカスタマイズする]** チェック ボックスをオンにします。

   i. **[名前]** で、**グループ** を入力します。
   
   j. **[保存]** を選択します。   

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** の横にある **[ダウンロード]** リンクを選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロード リンクを示すスクリーンショット。](common/certificatebase64.png)

1. **[FortiGate SSL VPN のセットアップ]** セクションで、要件に基づいて適切な 1 つまたは複数の URL をコピーします。

    ![構成 URL を示すスクリーンショット。](common/copy-configuration-urls.png)

#### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal で B. Simon というテスト ユーザーを作成します。

1. Azure portal の左ペインで、 **[Azure Active Directory]** を選択します。 **[ユーザー]** を選択し、 **[すべてのユーザー]** を選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、次の手順を実行します。
   1. **[名前]** ボックスに「**B.Simon**」と入力します。  
   1. **[ユーザー名]** ボックスに「\<username>@\<companydomain>.\<extension>」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** をオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **［作成］** を選択します

#### <a name="grant-access-to-the-test-user"></a>テスト ユーザーへのアクセス権の付与

このセクションでは、FortiGate SSL VPN へのアクセスを B.Simon に許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[FortiGate SSL VPN]** を選択します。
1. アプリの概要ページの **[管理]** セクションで、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログ ボックスで、**ユーザー** の一覧で **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内にロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 画面の下部にある **[選択]** ボタンをクリックします。
1. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

#### <a name="create-a-security-group-for-the-test-user"></a>テスト ユーザー用のセキュリティ グループを作成する

このセクションでは、テスト ユーザー用に Azure Active Directory にセキュリティ グループを作成します。 FortiGate では、このセキュリティ グループを使用して、VPN 経由のネットワーク アクセスをユーザーに許可します。

1. Azure portal の左ペインで、 **[Azure Active Directory]** を選択します。 次に、 **[グループ]** を選択します。
1. 画面の上部にある **[新しいグループ]** を選択します。
1. **[新しいグループ]** のプロパティで、これらの手順を完了します。
   1. **[グループの種類]** リストで **[セキュリティ]** を選択します。
   1. **[グループ名]** ボックスに、「**FortiGateAccess**」と入力します。
   1. **[グループの説明]** ボックスに、**FortiGate VPN アクセスを許可するグループ** を入力します。
   1. **[Azure AD roles can be assigned to the group (Preview)]\(Azure AD ロールをグループに割り当てることができる (プレビュー)\)** 設定には **[いいえ]** を選択します。
   1. **[メンバーシップの種類]** ボックスで、 **[割り当て済み]** を選択します。
   1. **[メンバー]** には **[メンバーが選択されていません]** を選択します。
   1. **[ユーザーとグループ]** ダイアログ ボックスの **[ユーザー]** の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
   1. **［作成］** を選択します
1. Azure Active Directory の **[グループ]** セクションに戻った後、**FortiGate Access** グループを見つけ、**オブジェクト ID** を書き留めておきます。この情報は後で必要になります。

### <a name="configure-fortigate-ssl-vpn-sso"></a>FortiGate SSL VPN の SSO の構成

#### <a name="upload-the-base64-saml-certificate-to-the-fortigate-appliance"></a>Base64 SAML 証明書を FortiGate アプライアンスにアップロードする

自分のテナントで FortiGate アプリの SAML 構成を完了した後に、Base64 でエンコードされた SAML 証明書をダウンロードしました。 この証明書を FortiGate アプライアンスにアップロードする必要があります。

1. FortiGate アプライアンスの管理ポータルにサインインします。
1. 左側のペインで、 **[System]\(システム\)** を選択します。
1. **[System]\(システム\)** で **[Certificates]\(証明書\)** を選択します。
1. **[Import]\(インポート\)**  >  **[Remote Certificate]\(リモート証明書\)** の順に選択します。
1. Azure テナントの FortiGate アプリのデプロイからダウンロードした証明書に移動して選択し、 **[OK]** を選択します。

証明書がアップロードされたら、 **[System]\(システム\)**  >  **[Certificates]\(証明書\)**  >  **[Remote Certificate]\(リモート証明書\)** の名前をメモします。 既定では、REMOTE_Cert_ *N* (*N* は整数値です) という名前です。

#### <a name="complete-fortigate-command-line-configuration"></a>FortiGate のコマンドライン構成を実行する

以下の手順では、Azure ログアウト URL を構成する必要があります。 この URL には疑問符 (?) が含まれています。 この記号を正常に送信するには、特定の手順を実行する必要があります。 それらの手順は、FortiGate CLI コンソールからは実行できません。 代わりに、PuTTY などのツールを使用して、FortiGate アプライアンスへの SSH セッションを確立します。 ご利用の FortiGate アプライアンスが Azure 仮想マシンである場合、Azure 仮想マシンのシリアル コンソールから以下の手順を完了できます。

これらの手順を完了するには、以前にメモした値が必要になります。

- エンティティ ID
- [応答 URL]
- ログアウト URL
- Azure ログイン URL
- Azure AD 識別子
- Azure ログアウト URL
- Base64 SAML 証明書名 (REMOTE_Cert_ *N*)

1. FortiGate アプライアンスへの SSH セッションを確立し、FortiGate 管理者アカウントでサインインします。
1. これらのコマンドを実行します。

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
   > Azure ログアウト URL には `?` 文字が含まれています。 FortiGate シリアル コンソールへのこの URL を正しく指定するには、特殊なキー シーケンスを入力する必要があります。 通常、URL は `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` です。
   >
   > シリアル コンソールで Azure ログアウト URL を入力するには、「`set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation`」と入力します。
   > 
   > 次に、Ctrl + V キーを押し、残りの URL を貼り付けて、`set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` のように行を完成させます。

#### <a name="configure-fortigate-for-group-matching"></a>グループ照合のために FortiGate を構成する

このセクションでは、テスト ユーザーを含むセキュリティ グループのオブジェクト ID を認識するように、FortiGate を構成します。 この構成により、FortiGate ではグループ メンバーシップに基づいてアクセスを判断できるようになります。

これらの手順を完了するには、このチュートリアルの前の方で作成した FortiGateAccess セキュリティ グループのオブジェクト ID が必要となります。

1. FortiGate アプライアンスへの SSH セッションを確立し、FortiGate 管理者アカウントでサインインします。
1. これらのコマンドを実行します。

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

#### <a name="create-a-fortigate-vpn-portals-and-firewall-policy"></a>FortiGate VPN ポータルとファイアウォール ポリシーの作成

このセクションでは、FortiGate VPN ポータルと、このチュートリアルの前の方で作成した FortiGateAccess セキュリティ グループへのアクセス権を付与するファイアウォール ポリシーを構成します。

[FortiGate サポート チーム](mailto:tac_amer@fortinet.com)と連携して、VPN ポータルとファイアウォール ポリシーを FortiGate VPN プラットフォームに追加してください。 シングル サインオンを使用する前に、この手順を完了する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる FortiGate VPN のサインオン URL にリダイレクトされます。 

* FortiGate VPN のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [FortiGate VPN] タイルをクリックすると、FortiGate VPN のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

FortiGate VPN を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。