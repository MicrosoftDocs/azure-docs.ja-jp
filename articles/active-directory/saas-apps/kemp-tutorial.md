---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Kemp LoadMaster Azure AD integration の統合 | Microsoft Docs
description: Azure Active Directory と Kemp LoadMaster Azure AD integration の間でシングル サインオンを構成する方法について確認します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: aa36d8522f548101ef3354237d93128b0f041eac
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648962"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-kemp-loadmaster-azure-ad-integration"></a>チュートリアル:Azure Active Directory SSO と Kemp LoadMaster Azure AD integration の統合

このチュートリアルでは、Kemp LoadMaster Azure AD integration と Azure Active Directory (Azure AD) を統合する方法について説明します。 Kemp LoadMaster Azure AD integration と Azure AD を統合すると、次のことができます。

* Kemp LoadMaster Azure AD integration にアクセスできる Azure AD を制御します。
* ユーザーが自分の Azure AD アカウントを使用して Kemp LoadMaster Azure AD integration に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Kemp LoadMaster Azure AD integration でのシングル サインオン (SSO) が有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Kemp LoadMaster Azure AD integration では、**IDP** Initiated SSO がサポートされます

## <a name="add-kemp-loadmaster-azure-ad-integration-from-the-gallery"></a>ギャラリーからの Kemp LoadMaster Azure AD integration の追加

Azure AD への Kemp LoadMaster Azure AD integration の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Kemp LoadMaster Azure AD integration を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Kemp LoadMaster Azure AD integration**」と入力します。
1. 結果のパネルから **[Kemp LoadMaster Azure AD integration]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-kemp-loadmaster-azure-ad-integration"></a>Kemp LoadMaster Azure AD integration の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Kemp LoadMaster Azure AD integration に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Kemp LoadMaster Azure AD integration の関連ユーザーとの間にリンク関係を確立する必要があります。

Kemp LoadMaster Azure AD integration に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Kemp LoadMaster Azure AD integration の SSO の構成](#configure-kemp-loadmaster-azure-ad-integration-sso)** - アプリケーション側でシングル サインオン設定を構成します。

1. **[Web サーバーを公開する](#publishing-web-server)**
    1. **[仮想サーバーを作成する](#create-a-virtual-service)**
    1. **[証明書とセキュリティ](#certificates-and-security)**
    1. **[Kemp LoadMaster Azure AD integration の SAML プロファイル](#kemp-loadmaster-azure-ad-integration-saml-profile)**
    1. **[変更を確認する](#verify-the-changes)**
1. **[Kerberos ベースの認証の構成](#configuring-kerberos-based-authentication)**
    1. **[Kemp LoadMaster Azure AD integration 用の Kerberos 委任アカウントを作成する](#create-a-kerberos-delegation-account-for-kemp-loadmaster-azure-ad-integration)**
    1. **[Kemp LoadMaster Azure AD integration KCD (Kerberos 委任アカウント)](#kemp-loadmaster-azure-ad-integration-kcd-kerberos-delegation-accounts)**
    1. **[Kemp LoadMaster Azure AD integration ESP](#kemp-loadmaster-azure-ad-integration-esp)**
    1. **[Kemp LoadMaster Azure AD integration のテスト ユーザーの作成](#create-kemp-loadmaster-azure-ad-integration-test-user)** - Kemp LoadMaster Azure AD integration で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Kemp LoadMaster Azure AD integration** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[識別子 (エンティティ ID)]** ボックスに、`https://<KEMP-CUSTOMER-DOMAIN>.com/` という URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<KEMP-CUSTOMER-DOMAIN>.com/` という URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 この値を取得するには、[Kemp LoadMaster Azure AD integration クライアント サポート](mailto:support@kemp.ax) チームに問い合わせてください。 Azure portal の [基本的な SAML 構成] セクションに示されているパターンを参照することもできます。

1. **[SAML によるシングル サインオンのセットアップ]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** と **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書とフェデレーション メタデータ XML をダウンロードして自分のコンピューターに保存します。

    ![証明書のダウンロードのリンク](./media/kemp-tutorial/certificate-base-64.png)

1. **[Kemp LoadMaster Azure AD integration のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、Kemp LoadMaster Azure AD integration へのアクセスを許可することで、B.Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Kemp LoadMaster Azure AD integration]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-kemp-loadmaster-azure-ad-integration-sso"></a>Kemp LoadMaster Azure AD integration の SSO の構成

## <a name="publishing-web-server"></a>Web サーバーを公開する 

### <a name="create-a-virtual-service"></a>仮想サーバーを作成する 

1. Kemp LoadMaster Azure AD integration の LoadMaster Web UI > [Virtual Services]\(仮想サービス\) > [Add New]\(新規追加\) に移動します。

1. [Add New]\(新規追加\) をクリックします。

1. [Virtual Services]\(仮想サービス\) の [Parameters]\(パラメーター\) を指定します。

    ![ボックスに例の値が設定されている [Please Specify the Parameters for the Virtual Service]\(仮想サービスのパラメーターを指定してください\) ページを示すスクリーンショット。](./media/kemp-tutorial/kemp-1.png)

    a. Virtual Address (仮想アドレス)
    
    b. Port
    
    c. Service Name (Optional) (サービス名 (省略可能))
    
    d. Protocol 

1. [Real Servers] セクションに移動します。

1. [Add New]\(新規追加\) をクリックします。

1. [Real Server] の [Parameters]\(パラメーター\) を指定します。
    
    ![ボックスに例の値が設定されている [Please Specify the Parameters for the Real Server]\(Real Server のパラメーターを指定してください\) ページを示すスクリーンショット。](./media/kemp-tutorial/kemp-2.png)

    a. [Allow Remote Addresses]\(リモート アドレスを許可する\) を選択します
    
    b. [Real Server Address]\(Real Server のアドレス\) を入力します
    
    c. Port
    
    d. Forwarding method (転送方法)
    
    e. Weight
    
    f. Connection Limit (接続の制限)
    
    g. [Add This Real Server]\(この Real Server を追加する\) をクリックします

## <a name="certificates-and-security"></a>証明書とセキュリティ
 
### <a name="import-certificate-on-kemp-loadmaster-azure-ad-integration"></a>Kemp LoadMaster Azure AD integration に証明書をインポートする 
 
1. Kemp LoadMaster Azure AD integration Web Portal > [Certificates & Security]\(証明書とセキュリティ\) > [SSL Certificates]\(SSL 証明書\) に移動します。

1. [Manage Certificates]\(証明書の管理\) > [Certificate Configuration]\(証明書の構成\) に移動します。

1. [Import Certificate]\(証明書のインポート\) をクリックします。

1. 証明書を含むファイルの名前を指定します。 このファイルには秘密キーも保持できます。 ファイルに秘密キーが含まれていない場合は、秘密キーを含むファイルも指定する必要があります。 証明書は、.PEM または .PFX (IIS) のいずれかの形式を使用できます。

1. [Certificate File]\(証明書ファイル\) の [Choose file]\(ファイルの選択\) をクリックします。

1. [Key File (optional)]\(キー ファイル (省略可能)\) をクリックします。

1. [保存] をクリックします。

### <a name="ssl-acceleration"></a>SSL アクセラレーション
 
1. Kemp LoadMaster Web UI > [Virtual Services]\(仮想サービス\) > [View/Modify Services]\(サービスの表示または変更\) に移動します。

1. [Operation]\(操作\) の [Modify]\(変更\) をクリックします。

1. [SSL Properties]\(SSL のプロパティ\) (レイヤー7 で動作します) をクリックします。
    
    ![[SSL Acceleration]\(SSL アクセラレーション\) の [Enabled]\(有効\) が選択され、証明書の例が選択されている [SSL Properties]\(SSL のプロパティ\) を示すスクリーンショット。](./media/kemp-tutorial/kemp-3.png)
    
    a. [SSL Acceleration]\(SSL アクセラレーション\) の [Enabled]\(有効\) をクリックします。
    
    b. [Available Certificates]\(使用可能な証明書\) で、インポートした証明書を選択し、`>` 記号をクリックします。
    
    c. 目的の SSL 証明書が []\(割り当てられた証明書\) に表示されたら、 **[Set Certificates]\(証明書の設定\)** をクリックします。

    > [!NOTE]
    > 必ず **[Set Certificates]\(証明書の設定\)** をクリックしてください。

## <a name="kemp-loadmaster-azure-ad-integration-saml-profile"></a>Kemp LoadMaster Azure AD integration の SAML プロファイル
 
### <a name="import-idp-certificate"></a>IdP 証明書をインポートする

Kemp LoadMaster Azure AD integration Web Console に移動する 

1. [Certificates and Authority]\(証明書と機関\) の [Intermediate Certificates]\(中間証明書\) をクリックします。

    ![選択した証明書の例が選択されている [Currently installed Intermediate Certificates]\(現在インストールされている中間証明書\) セクションを示すスクリーンショット。](./media/kemp-tutorial/kemp-6.png)

    a. [Add a new Intermediate Certificate]\(新しい中間証明書の追加\) の [Choose File]\(ファイルの選択\) をクリックします。
    
    b. 以前に Azure AD エンタープライズ アプリケーションからダウンロードした証明書ファイルに移動します。
    
    c. [Open]\(開く\) をクリックします。
    
    d. [Certificate Name]\(証明書名\) に [Name]\(名前\) を指定します。
    
    e. [Add Certificate]\(証明書の追加\) をクリックします。

### <a name="create-authentication-policy"></a>認証ポリシーの作成 
 
[Virtual Services]\(仮想サービス\) の [Manage SSO]\(SSO の管理\) にアクセスします。

   ![[Manage SSO]\(SSO の管理\) ページを示すスクリーンショット。](./media/kemp-tutorial/kemp-7.png)
   
   a. 名前を指定した後、[Add new Client Side Configuration]\(新しいクライアント側の構成の追加\) の [追加] をクリックします。

   b. [Authentication Protocol]\(認証プロトコル\) で [SAML] を選択します。

   c. [IdP Provisioning]\(IdP のプロビジョニング\) で [MetaData File]\(メタデータ ファイル\) を選択します。 

   d. [Choose File]\(ファイルの選択\) をクリックします。

   e. Azure portal から以前にダウンロードした XML に移動します。

   f. [Open]\(開く\) をクリックし、[Import IdP MetaData]\(IdP メタデータ ファイルのインポート\) をクリックします。

   g. [IdP Certificate]\(IdP 証明書\) から中間証明書を選択します。

   h. Azure portal で作成された ID と一致するように [SP Entity ID]\(SP エンティティ ID\) を設定します。 

   i. [Set SP Entity ID]\(SP エンティティ ID の設定\) をクリックします。

### <a name="set-authentication"></a>認証を設定する  
 
Kemp LoadMaster Azure AD integration Web Console で次の手順を実行します

1. [Virtual Services]\(仮想サービス\) をクリックします。

1. [View/Modify Services]\(サービスの表示と変更\) をクリックします。

1. [Modify]\(変更\) をクリックし、[ESP Options]\(ESP のオプション\) に移動します。
    
    ![[ESP Options]\(ESP オプション\) および [Real Servers] セクションが展開されている、[View/Modify Services]\(サービスの表示と変更\) ページを示すスクリーンショット。](./media/kemp-tutorial/kemp-8.png)

    a. [Enable ESP]\(ESP を有効にする\) をクリックします。
    
    b. [Client Authentication Mode]\(クライアント認証モード\) で [SAML] を選択します。
    
    c. [SSO Domain]\(SSO ドメイン\) で以前に作成した [Client Side Authentication]\(クライアント側認証\) を選択します。
    
    d. [Allowed Virtual Hosts]\(許可される仮想ホスト\) にホスト名を入力し、[Set Allowed Virtual Hosts]\(許可される仮想ホストの設定\) をクリックします。
    
    e. (アクセス要件に基づいて) [Allowed Virtual Directories]\(許可される仮想ディレクトリ\) に「/*」と入力し、[Set Allowed Directories]\(許可されるディレクトリの設定\) をクリックします。

### <a name="verify-the-changes"></a>変更を確認する 
 
アプリケーションの URL にアクセスします 

以前の認証されていないアクセスではなく、テナントのログイン ページが表示されます。 

![テナントの [サインイン] ページを示すスクリーンショット。](./media/kemp-tutorial/kemp-9.png)

## <a name="configuring-kerberos-based-authentication"></a>Kerberos ベースの認証の構成 
 
### <a name="create-a-kerberos-delegation-account-for-kemp-loadmaster-azure-ad-integration"></a>Kemp LoadMaster Azure AD integration 用の Kerberos 委任アカウントを作成する 

1. ユーザー アカウントを作成します (この例では AppDelegation)。
    
    ![[Account]\(アカウント\) タブが選択されている [kcd user Properties]\(kcd ユーザーのプロパティ\) ウィンドウを示すスクリーンショット。](./media/kemp-tutorial/kemp-10.png)


    a. [属性エディター] タブを選択します。

    b. servicePrincipalName に移動します。 

    c. [servicePrincipalName] を選択し、[編集] をクリックします。

    d. [追加する値] フィールドに「http/kcduser」と入力し、[追加] をクリックします。 

    e. [適用] をクリックし、[OK] をクリックします。 ウィンドウを閉じてから再度開く必要があります (新しい [委任] タブを表示するため)。 

1. [ユーザーのプロパティ] ウィンドウをもう一度開くと、[委任] タブを使用できるようになります。 

1. [委任] タブを選択します。

    ![[Delegation]\(委任\) タブが選択されている [kcd user Properties]\(kcd ユーザーのプロパティ\) ウィンドウを示すスクリーンショット。](./media/kemp-tutorial/kemp-11.png)

    a. [指定されたサービスへの委任でのみこのユーザーを信頼する] を選択します。

    b. [任意の認証プロトコルを使う] を選択します。

    c. Real Server を追加し、サービスとして http を追加します。 
    
    d. [展開済み] チェックボックスをオンにします。 

    e. ホスト名と FQDN の両方を含むすべてのサーバーを表示できます。
    
    f. [OK] をクリックします。

> [!NOTE]
> 必要に応じて、[アプリケーション] と [Web サイト] の SPN を設定します。 アプリケーション プール ID が設定されている場合にアプリケーションにアクセスするには。 FQDN 名を使用して IIS アプリケーションにアクセスするには、Real Server コマンド プロンプトにアクセスし、必要なパラメーターを指定して SetSpn を入力します。 例: Setspn –S HTTP/sescoindc.sunehes.co.in suneshes\kdcuser 

### <a name="kemp-loadmaster-azure-ad-integration-kcd-kerberos-delegation-accounts"></a>Kemp LoadMaster Azure AD integration KCD (Kerberos 委任アカウント) 

Kemp LoadMaster Azure AD integration Web Console > [Virtual Services]\(仮想サービス\) > [Manage SSO]\(SSO の管理\) に移動します。

![[Manage SSO]\(SSO の管理\) の [Manage Domain]\(ドメインの管理\) ページを示すスクリーンショット。](./media/kemp-tutorial/kemp-12.png)

a. [Server Side Single Sign On Configurations]\(サーバー側のシングル サインオンの構成\) に移動します。

b. [Add new Server-Side Configuration]\(新しいサーバー側構成の追加\) に名前を入力し、[Add]\(追加\) をクリックします。

c. [Authentication Protocol]\(認証プロトコル\) で [Kerberos Constrained Delegation]\(Kerberos の制約付き委任\) を選択します。

d. [Kerberos Realm]\(Kerberos 領域\) にドメイン名を入力します。

e. [Set Kerberos realm]\(Kerberos 領域の設定\) をクリックします。

f. [Kerberos Key Distribution Center]\(Kerberos キー配布センター\) にドメイン コントローラーの IP アドレスを入力します。

g. [Set Kerberos KDC]\(Kerberos KDC の設定\) をクリックします。

h. [Kerberos Trusted User Name]\(Kerberos の信頼されたユーザー名\)に KCD ユーザー名を入力します。

i. [Set KDC trusted user name]\(KDC の信頼されたユーザー名の設定\) をクリックします。

j. [Kerberos Trusted User Password]\(Kerberos の信頼されたユーザー パスワード\) にパスワードを入力します。

k. [Set KCD trusted user password]\(KCD の信頼されたユーザー パスワードの設定\) をクリックします。

### <a name="kemp-loadmaster-azure-ad-integration-esp"></a>Kemp LoadMaster Azure AD integration ESP 

[Virtual Services]\(仮想サービス\) > [View/Modify Services]\(サービスの表示と変更\) に移動します。

![Kemp LoadMaster Azure AD integration Web サーバー](./media/kemp-tutorial/kemp-13.png)

a. [Virtual Services]\(仮想サービス\) の [Nick Name]\(ニックネーム\) の [Modify]\(変更\) をクリックします。
    
b. [ESP Options]\(ESP オプション\) をクリックします。
    
c. [Server Authentication Mode]\(サーバー認証モード\) で、[KCD] を選択します。
        
d. [Server-Side configuration]\(サーバー側の構成\) で、以前に作成したサーバー側のプロファイルを選択します。

### <a name="create-kemp-loadmaster-azure-ad-integration-test-user"></a>Kemp LoadMaster Azure AD integration のテスト ユーザーを作成する

このセクションでは、Kemp LoadMaster Azure AD integration で B.Simon というユーザーを作成します。 [Kemp LoadMaster Azure AD integration クライアント サポート チーム](mailto:support@kemp.ax)と連携して、Kemp LoadMaster Azure AD integration プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した Kemp LoadMaster Azure AD integration に自動的にサインインされます。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Kemp LoadMaster Azure AD integration] タイルをクリックすると、SSO を設定した Kemp LoadMaster Azure AD integration に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Kemp LoadMaster Azure AD integration を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。