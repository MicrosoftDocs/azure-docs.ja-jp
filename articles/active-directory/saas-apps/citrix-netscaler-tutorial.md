---
title: チュートリアル:Azure Active Directory シングル サインオンと Citrix ADC の統合 (Kerberos ベースの認証) | Microsoft Docs
description: Kerberos ベースの認証を使用して Azure Active Directory と Citrix ADC の間でシングル サインオン (SSO) を構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/15/2020
ms.author: jeedes
ms.openlocfilehash: 75d46edb332fb28132592e414e78bad64e75fef5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736425"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-citrix-adc-kerberos-based-authentication"></a>チュートリアル:Azure Active Directory シングル サインオンと Citrix ADC の統合 (Kerberos ベースの認証)

このチュートリアルでは、Citrix ADC と Azure Active Directory (Azure AD) を統合する方法について学習します。 Azure AD と Citrix ADC を統合すると、次のことができます。

* Citrix ADC にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Citrix ADC に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Citrix ADC でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 チュートリアルには、これらのシナリオが含まれています。

* Citrix ADC の **SP Initiated** SSO

* Citrix ADC の **Just-In-Time** ユーザー プロビジョニング

* [Citrix ADC の Kerberos ベースの認証](#publish-the-web-server)

* [Citrix ADC のヘッダーベースの認証](header-citrix-netscaler-tutorial.md#publish-the-web-server)


## <a name="add-citrix-adc-from-the-gallery"></a>ギャラリーからの Citrix ADC の追加

Citrix ADC を Azure AD に統合するには、まずギャラリーからマネージド SaaS アプリの一覧に Citrix ADC を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。

1. 左側のメニューで、 **[Azure Active Directory]** を選択します。

1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。

1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Citrix ADC**」と入力します。

1. 結果から **[Citrix ADC]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-citrix-adc"></a>Citrix ADC の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Citrix ADC に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Citrix ADC の関連ユーザーの間で、リンク関係を確立する必要があります。

Citrix ADC に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. [Azure AD SSO の構成](#configure-azure-ad-sso) - ユーザーがこの機能を使用できるようにします。

    1. [Azure AD テスト ユーザーの作成](#create-an-azure-ad-test-user) - B.Simon を使用して Azure AD SSO をテストします。

    1. [Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user) - B.Simon が Azure AD SSO を使用できるようにします。

1. [Citrix ADC の SSO の構成](#configure-citrix-adc-sso) - アプリケーション側で SSO 設定を構成します。

    * [Citrix ADC のテスト ユーザーの作成](#create-a-citrix-adc-test-user) - Citrix ADC で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。

1. [SSO のテスト](#test-sso) - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

Azure portal を使用して Azure AD SSO を有効にするには、これらの手順を実行します。

1. Azure portal の **Citrix ADC** アプリケーション統合ペインで、 **[管理]** の下にある **[シングル サインオン]** を選択します。

1. **[シングル サインオン方式の選択]** ペインで、 **[SAML]** を選択します。

1. **[SAML でシングル サインオンをセットアップします]** ペインで、 **[基本的な SAML 構成]** の **編集** (ペン) アイコンを選択して設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP 開始** モードで構成するには:

    1. **[識別子]** テキスト ボックスに、`https://<Your FQDN>` の形式で URL を入力します。

    1. **[応答 URL]** テキスト ボックスに、`http(s)://<Your FQDN>.of.vserver/cgi/samlauth` の形式で URL を入力します。

1. アプリケーションを **SP 開始** モードで構成するには、 **[追加の URL を設定します]** を選択して、次の手順を実行します。

    * **[サインオン URL]** テキスト ボックスに、`https://<Your FQDN>/CitrixAuthService/AuthService.asmx` の形式で URL を入力します。

    > [!NOTE]
    > * このセクションで使用される URL は、実際の値ではありません。 これらの値は、実際の識別子、応答 URL、サインオン URL の値で更新してください。 これらの値を取得するには、[Citrix ADC クライアント サポート チーム](https://www.citrix.com/contact/technical-support.html)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。
    > * SSO を設定するには、パブリック Web サイトから URL にアクセスできる必要があります。 Citrix ADC 側でファイアウォールまたは他のセキュリティ設定を有効にし、Azure AD が構成済みの URL にトークンをポストできるようにする必要があります。

1. **[SAML でシングル サインオンをセットアップします]** ペインの **[SAML 署名証明書]** セクションで、 **[アプリのフェデレーション メタデータ URL]** を見つけ、URL をコピーしてメモ帳に保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Citrix ADC のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のメニューで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

1. ペインの上部にある **[新しいユーザー]** を選択します。

1. **[ユーザー]** プロパティで、これらの手順を実行します。

   1. **名前** には、`B.Simon`を入力します。  

   1. **[ユーザー名]** への入力は「 _username@companydomain.extension_ 」の形式にします。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。

   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** に表示された値を書き留めるか、コピーします。

   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B. Simon に Citrix ADC へのアクセスを許可することで、このユーザーが Azure SSO を使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

1. アプリケーション リストで、 **[Citrix ADC]** を選択します。

1. アプリの概要の **[管理]** で、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択します。 次に、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログ ボックスで、 **[ユーザー]** 一覧から **[B.Simon]** を選択します。 **[選択]** を選択します。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

## <a name="configure-citrix-adc-sso"></a>Citrix ADC の SSO の構成

構成したい認証の種類に対応する手順のリンクを選択してください。

- [Kerberos ベースの認証用の Citrix ADC SSO の構成](#publish-the-web-server)

- [ヘッダーベースの認証用の Citrix ADC SSO の構成](header-citrix-netscaler-tutorial.md#publish-the-web-server)

### <a name="publish-the-web-server"></a>Web サーバーを公開する 

仮想サーバーを作成するには:

1. **[Traffic Management]\(トラフィック管理\)**  >  **[Load Balancing]\(負荷分散\)**  >  **[Services]\(サービス\)** を選択します。
    
1. **[追加]** を選択します。

    ![Citrix ADC の構成 - [Services]\(サービス\) ペイン](./media/citrix-netscaler-tutorial/web01.png)

1. アプリケーションを実行している Web サーバーに対して、次の値を設定します。

   * **サービス名**
   * **サーバー IP/ 既存のサーバー**
   * **プロトコル**
   * **[ポート]**

### <a name="configure-the-load-balancer"></a>ロード バランサーを構成します

ロード バランサーを構成するには:

1. **[Traffic Management]\(トラフィック管理\)**  >  **[Load Balancing]\(負荷分散\)**  >  **[Virtual Servers]\(仮想サーバー\)** の順に移動します。

1. **[追加]** を選択します。

1. 下のスクリーンショットに示すように、次の値を設定します。

    * **名前**
    * **プロトコル**
    * **IP アドレス**
    * **[ポート]**

1. **[OK]** を選択します。

    ![Citrix ADC の構成 - [Basic Settings]\(基本設定\) ペイン](./media/citrix-netscaler-tutorial/load01.png)

### <a name="bind-the-virtual-server"></a>仮想サーバーをバインドする

ロード バランサーを仮想サーバーにバインドするには:

1. **[Services and Service Groups]\(サービスとサービス グループ\)** ペインで、 **[No Load Balancing Virtual Server Service Binding]\(負荷分散仮想サーバー サービスのバインドなし\)** を選択します。

   ![Citrix ADC の構成 - [Load Balancing Virtual Server Service Binding]\(負荷分散仮想サーバー サービスのバインド\) ペイン](./media/citrix-netscaler-tutorial/bind01.png)

1. 設定が次のスクリーンショットのとおりであることを確認し、 **[Close]\(閉じる\)** を選択します。

   ![Citrix ADC の構成 - 仮想サーバー サービスのバインドを確認する](./media/citrix-netscaler-tutorial/bind02.png)

### <a name="bind-the-certificate"></a>証明書をバインドする

このサービスを TLS として公開するには、サーバー証明書をバインドしてから自分のアプリケーションをテストします。

1. **[Certificate]\(証明書\)** で、 **[No Server Certificate]\(サーバー証明書なし\)** を選択します。

   ![Citrix ADC の構成 - [Server Certificate]\(サーバー証明書\) ペイン](./media/citrix-netscaler-tutorial/bind03.png)

1. 設定が次のスクリーンショットのとおりであることを確認し、 **[Close]\(閉じる\)** を選択します。

   ![Citrix ADC の構成 - 証明書を確認する](./media/citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Citrix ADC SAML プロファイル

Citrix ADC SAML プロファイルを構成するには、次のセクションを完了します。

### <a name="create-an-authentication-policy"></a>認証ポリシーを作成する

認証ポリシーを作成するには:

1. **[Security]\(セキュリティ\)**  >  **[AAA - Application Traffic]\(AAA - アプリケーション トラフィック\)**  >  **[Policies]\(ポリシー\)**  >  **[Authentication]\(認証\)**  >  **[Authentication Policies]\(認証ポリシー\)** の順に移動します。

1. **[追加]** を選択します。

1. **[Create Authentication Policy]\(認証ポリシーの作成\)** ペインで、次の値を入力または選択します。

    * **Name**:認証ポリシーの名前を入力します。
    * **アクション**:「**SAML**」と入力し、 **[Add]\(追加\)** を選択します。
    * **式**: 「**true**」と入力します。     
    
    ![Citrix ADC の構成 - [Create Authentication Policy]\(認証ポリシーの作成\) ペイン](./media/citrix-netscaler-tutorial/policy01.png)

1. **［作成］** を選択します

### <a name="create-an-authentication-saml-server"></a>認証 SAML サーバーを作成する

認証 SAML サーバーを作成するには **[Create Authentication SAML Server]\(認証 SAML サーバーの作成\)** ペインに移動し、次の手順を実行します。

1. **[Name]\(名前\)** には、認証 SAML サーバーの名前を入力します。

1. **[Export SAML Metadata]\(SAML メタデータのエクスポート\)** で:

   1. **[Import Metadata]\(メタデータのインポート\)** チェック ボックスをオンにします。

   1. 前に自分がコピーした、Azure SAML UI のフェデレーション メタデータ URL を入力します。
    
1. **[Issuer Name]\(発行者名\)** には、関連する URL を入力します。

1. **［作成］** を選択します

![Citrix ADC の構成 - [Create Authentication SAML Server]\(認証 SAML サーバーの作成\) ペイン](./media/citrix-netscaler-tutorial/server01.png)

### <a name="create-an-authentication-virtual-server"></a>認証仮想サーバーを作成する

認証仮想サーバーを作成するには:

1.  **[Security]\(セキュリティ\)**  >  **[AAA - Application Traffic]\(AAA - アプリケーション トラフィック\)**  >  **[Policies]\(ポリシー\)**  >  **[Authentication]\(認証\)**  >  **[Authentication Virtual Servers]\(認証仮想サーバー\)** の順に移動します。

1.  **[Add]\(追加\)** を選択し、次の手順を実行します。

    1. **[Name]\(名前\)** には、認証仮想サーバーの名前を入力します。

    1. **[Non-Addressable]\(アドレス指定不可\)** チェック ボックスをオンにします。

    1. **[Protocol]\(プロトコル\)** では、 **[SSL]** を選択します。

    1. **[OK]** を選択します。
    
1. **[続行]** をクリックします。

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Azure AD を使用するよう認証仮想サーバーを構成する

認証仮想サーバーの 2 つのセクションを変更します。

1.  **[Advanced Authentication Policies]\(高度な認証ポリシー\)** ペインで、 **[No Authentication Policy]\(認証ポリシーなし\)** を選択します。

    ![Citrix ADC の構成 - [Advanced Authentication Policies]\(高度な認証ポリシー\) ペイン](./media/citrix-netscaler-tutorial/virtual01.png)

1. **[Policy Binding]\(ポリシーのバインド\)** ペインで、認証ポリシーを選択し、 **[Bind]\(バインド\)** を選択します。

    ![Citrix ADC の構成 - [Policy Binding]\(ポリシーのバインド\) ペイン](./media/citrix-netscaler-tutorial/virtual02.png)

1. **[Form Based Virtual Servers]\(フォーム ベースの仮想サーバー\)** ペインで、 **[No Load Balancing Virtual Server]\(負荷分散仮想サーバーなし\)** を選択します。

    ![Citrix ADC の構成 - [Form Based Virtual Servers]\(フォーム ベースの仮想サーバー\) ペイン](./media/citrix-netscaler-tutorial/virtual03.png)

1. **[Authentication FQDN]\(認証 FQDN\)** には、完全修飾ドメイン名 (FQDN) を入力します (必須)。

1. Azure AD 認証によって保護する負荷分散仮想サーバーを選択します。

1. **[Bind]\(バインド\)** を選択します。

    ![Citrix ADC の構成 - [Load Balancing Virtual Server Binding]\(負荷分散仮想サーバーのバインド\) ペイン](./media/citrix-netscaler-tutorial/virtual04.png)

    > [!NOTE]
    > **[Authentication Virtual Server Configuration]\(認証仮想サーバーの構成\)** ペインでは、必ず **[Done]\(完了\)** を選択してください。

1. 変更を確認するには、ブラウザーでアプリケーションの URL に移動します。 前に表示されていた非認証アクセスではなく、ご自分のテナントのサインイン ページが表示されます。

    ![Citrix ADC の構成 - Web ブラウザーのサインイン ページ](./media/citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-adc-sso-for-kerberos-based-authentication"></a>Kerberos ベースの認証用の Citrix ADC SSO の構成

### <a name="create-a-kerberos-delegation-account-for-citrix-adc"></a>Citrix ADC 用の Kerberos 委任アカウントを作成する

1. ユーザー アカウントを作成します (この例では _AppDelegation_ を使用します)。

    ![Citrix ADC の構成 - [Properties]\(プロパティ\) ペイン](./media/citrix-netscaler-tutorial/kerberos01.png)

1. このアカウントに HOST SPN を設定します。 

    例: `setspn -S HOST/AppDelegation.IDENTT.WORK identt\appdelegation`
    
    次の点に注意してください。

    * `IDENTT.WORK` はドメインの FQDN です。
    * `identt` はドメインの NetBIOS 名です。
    * `appdelegation` は委任ユーザー アカウント名です。

1. 次のスクリーンショットに示すように、Web サーバーの委任を構成します。
 
    ![Citrix ADC の構成 - [Properties]\(プロパティ\) の [Delegation]\(委任\) ペイン](./media/citrix-netscaler-tutorial/kerberos02.png)

    > [!NOTE]
    > スクリーンショットの例では、Windows 統合認証 (WIA) サイトを実行する内部 Web サーバーの名前は _CWEB2_ になっています。

### <a name="citrix-adc-aaa-kcd-kerberos-delegation-accounts"></a>Citrix ADC AAA KCD (Kerberos 委任アカウント)

Citrix ADC AAA KCD アカウントを構成するには、次の手順を実行します。

1.  **[Citrix Gateway]\(Citrix ゲートウェイ\)**  >  **[AAA KCD (Kerberos Constrained Delegation) Accounts]\(AAA KCD (Kerberos 制約付き委任) アカウント\)** に移動します。

1.  **[Add]\(追加\)** を選択し、次の値を入力または選択します。

    * **Name**:KCD アカウントの名前を入力します。

    * **[Realm]\(領域\)** : ドメインと拡張子を大文字で入力します。

    * **[Service SPN]\(サービス SPN\)** : `http/<host/fqdn>@<DOMAIN.COM>`。
    
        > [!NOTE]
        > `@DOMAIN.COM` は必須です。また、大文字にする必要があります。 例: `http/cweb2@IDENTT.WORK`.

    * **[Delegated User]\(委任されたユーザー\)** : 委任されたユーザーの名前を入力します。

    * **[Password for Delegated User]\(委任されたユーザーのパスワード\)** チェック ボックスをオンにし、パスワードの指定と確認入力を行います。

1. **[OK]** を選択します。
 
    ![Citrix ADC の構成 - [Configure KCD Account]\(KCD アカウントの構成\) ペイン](./media/citrix-netscaler-tutorial/kerberos03.png)

### <a name="citrix-traffic-policy-and-traffic-profile"></a>Citrix トラフィック ポリシーおよびトラフィック プロファイル

Citrix トラフィック ポリシーおよびトラフィック プロファイルを構成するには、次の手順を実行します。

1.  **[Security]\(セキュリティ\)**  >  **[AAA - Application Traffic]\(AAA - アプリケーション トラフィック\)**  >  **[Policies]\(ポリシー\)**  >  **[Traffic Policies, Profiles and Form SSO ProfilesTraffic Policies]\(トラフィック ポリシー、プロファイル、およびフォーム SSO プロファイルのトラフィック ポリシー\)** の順に移動します。

1.  **[Traffic Profiles]\(トラフィック プロファイル\)** を選択します。

1.  **[追加]** を選択します。

1.  トラフィック プロファイルを構成するには、次の値を入力または選択します。

    * **Name**:トラフィック プロファイルの名前を入力します。

    * **[Single Sign-on]\(シングル サインオン\)** : **[ON]\(オン\)** を選択します。

    * **[KCD Account]\(KCD アカウント\)** : 前のセクションで作成した KCD アカウントを選択します。

1. **[OK]** を選択します。

    ![Citrix ADC の構成 - [Configure Traffic Profile]\(トラフィック プロファイルの構成\) ペイン](./media/citrix-netscaler-tutorial/kerberos04.png)
 
1.  **[Traffic Policy]\(トラフィック ポリシー\)** を選択します。

1.  **[追加]** を選択します。

1.  トラフィック ポリシーを構成するには、次の値を入力または選択します。

    * **Name**:トラフィック ポリシーの名前を入力します。

    * **プロファイル**:前のセクションで作成したトラフィック プロファイルを選択します。

    * **式**: 「**true**」と入力します。

1. **[OK]** を選択します。

    ![Citrix ADC の構成 - [Configure Traffic Policy]\(トラフィック ポリシーの構成\) ペイン](./media/citrix-netscaler-tutorial/kerberos05.png)

### <a name="bind-a-traffic-policy-to-a-virtual-server-in-citrix"></a>Citrix でトラフィック ポリシーを仮想サーバーにバインドする

GUI を使用してトラフィック ポリシーを仮想サーバーにバインドするには、次の手順を実行します。

1. **[Traffic Management]\(トラフィック管理\)**  >  **[Load Balancing]\(負荷分散\)**  >  **[Virtual Servers]\(仮想サーバー\)** の順に移動します。

1. 仮想サーバーの一覧で、書き換えポリシーをバインドする仮想サーバーを選択し、 **[Open]\(開く\)** を選択します。

1. **[Load Balancing Virtual Server]\(負荷分散仮想サーバー\)** ペインの **[Advanced Settings]\(詳細設定\)** で、 **[Policies]\(ポリシー\)** を選択します。 自分の NetScaler インスタンス用に構成されているすべてのポリシーが、一覧に表示されます。
 
    ![Citrix ADC の構成 - [Load Balancing Virtual Server]\(負荷分散仮想サーバー\) ペイン](./media/citrix-netscaler-tutorial/kerberos06.png)

    ![Citrix ADC の構成 - [Policies]\(ポリシー\) ダイアログ ボックス](./media/citrix-netscaler-tutorial/kerberos07.png)

1.  この仮想サーバーにバインドするポリシーの名前の横にあるチェック ボックスをオンにします。
 
    ![Citrix ADC の構成 - [Load Balancing Virtual Server Traffic Policy Binding]\(負荷分散仮想サーバー トラフィック ポリシーのバインド\) ペイン](./media/citrix-netscaler-tutorial/kerberos09.png)

1. **[Choose Type]\(種類の選択\)** ダイアログ ボックスで:

    1. **[Choose Policy]\(ポリシーの選択\)** に **[Traffic]\(トラフィック\)** を選択します。

    1. **[Choose Type]\(種類の選択\)** に **[Request]\(要求\)** を選択します。

    ![Citrix ADC の構成 - [Choose Type]\(種類の選択\) ペイン](./media/citrix-netscaler-tutorial/kerberos08.png)

1. ポリシーをバインドしたら、 **[Done]\(完了\)** を選択します。
 
    ![Citrix ADC の構成 - [Policies]\(ポリシー\) ペイン](./media/citrix-netscaler-tutorial/kerberos10.png)

1. WIA Web サイトを使用してバインドをテストします。

    ![Citrix ADC の構成 - Web ブラウザーのテスト ページ](./media/citrix-netscaler-tutorial/kerberos11.png)    

### <a name="create-a-citrix-adc-test-user"></a>Citrix ADC のテスト ユーザーの作成

このセクションでは、B.Simon というユーザーを Citrix ADC に作成します。 Citrix ADC では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションには、ユーザー側で行うアクションはありません。 Citrix ADC にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[Citrix ADC クライアント サポート チーム](https://www.citrix.com/contact/technical-support.html)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Citrix ADC のサインオン URL にリダイレクトされます。 

* Citrix ADC のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Citrix ADC] タイルをクリックすると、Citrix ADC サインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Citrix ADC を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。