---
title: Atlassian Jira/Confluence 管理ガイド - Azure Active Directory| Microsoft Docs
description: Azure Active Directory (Azure AD) で Atlassian Jira および Confluence を使用するための管理ガイド。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2018
ms.author: jeedes
ms.openlocfilehash: 8e73ea3650e631bed277ab95092b714eef7596d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94359159"
---
# <a name="atlassian-jira-and-confluence-admin-guide-for-azure-active-directory"></a>Azure Active Directory 向け Atlassian Jira および Confluence 管理ガイド

## <a name="overview"></a>概要

Azure Active Directory (Azure AD) シングル サインオン (SSO) プラグインを使用することで、Microsoft Azure AD ユーザーは、Atlassian Jira および Confluence サーバー ベースの製品へのサインインに、職場または学校アカウントを使用できます。 SAML 2.0 ベースの SSO が実装されています。

## <a name="how-it-works"></a>しくみ

ユーザーが Atlassian Jira または Confluence アプリケーションにサインインしようとすると、サインイン ページに **[Login with Azure AD]\(Azure AD でログイン\)** ボタンが表示されます。 このボタンを選択すると、Azure AD の組織サインイン ページ (つまり、職場または学校アカウント) を使用してサインインする必要があります。

ユーザーは、認証された後、アプリケーションにサインインできます。 職場または学校アカウントの ID とパスワードで既に認証されている場合は、アプリケーションに直接サインインします。 

サインインは Jira と Confluence の間でも機能します。 Jira アプリケーションと Confluence が同じブラウザー ウィンドウで開かれている場合、他のアプリで資格情報を入力する必要はありません。 

また、職場または学校アカウントの [マイ アプリ] から Atlassian 製品にアクセスすることもできます。 資格情報を要求されることなくサインインできます。

> [!NOTE]
> このプラグインからユーザーのプロビジョニングが行われることはありません。

## <a name="audience"></a>対象ユーザー

Jira と Confluence の管理者が、このプラグインを使用して Azure AD を使用することにより SSO を有効にできます。

## <a name="assumptions"></a>前提条件

* Jira と Confluence のインスタンスで HTTPS が有効である。
* Jira または Confluence でユーザーが既に作成されている。
* Jira または Confluence でユーザーにロールが割り当てられている。
* プラグインを構成するために必要な情報に管理者がアクセスできる。
* 会社のネットワークの外部でも Jira または Confluence を利用できる。
* このプラグインはオンプレミス バージョンの Jira と Confluence でのみ動作します。

## <a name="prerequisites"></a>前提条件

プラグインをインストールする前に、次の情報に注意してください。

* Jira と Confluence が、64 ビット バージョンのWindows にインストールされていること。
* Jira と Confluence のバージョンで HTTPS が有効であること。
* Jira と Confluence がインターネット上で使用できること。
* Jira と Confluence の管理者資格情報があること。
* Azure AD の管理者資格情報があること。
* Jira と Confluence で WebSudo が無効になっていること。

## <a name="supported-versions-of-jira-and-confluence"></a>Jira と Confluence のサポートされているバージョン

このプラグインは、次のバージョンの Jira と Confluence をサポートしています。

* JIRA Core と Software:6.0 から 7.12
* Jira Service Desk:3.0.0 から 3.5.0
* JIRA は 5.2 もサポートします。 詳細については、[Microsoft Azure Active Directory single sign-on for JIRA 5.2](./jira52microsoft-tutorial.md) に関する記事を参照してください。
* Confluence: 5.0 から 5.10
* Confluence: 6.0.1
* Confluence: 6.1.1
* Confluence: 6.2.1
* Confluence: 6.3.4
* Confluence: 6.4.0
* Confluence: 6.5.0
* Confluence: 6.6.2
* Confluence: 6.7.0
* Confluence: 6.8.1
* Confluence: 6.9.0
* Confluence: 6.10.0
* Confluence: 6.11.0
* Confluence: 6.12.0

## <a name="installation"></a>インストール

このプラグインをインストールするには、次の手順を実行します。

1. 管理者として、Jira または Confluence のインスタンスにサインインします。

2. Jira/Confluence の管理コンソールに移動し、 **[アドオン]** を選択します。

3. Microsoft ダウンロード センターから、[Microsoft SAML SSO Plugin for Jira](https://www.microsoft.com/download/details.aspx?id=56506)/ [Microsoft SAML SSO Plugin for Confluence](https://www.microsoft.com/download/details.aspx?id=56503) をダウンロードします。

   適切なバージョンのプラグインが検索結果に表示されます。

4. プラグインを選択すると、Universal Plug-in Manager (UPM) によりプラグインがインストールされます。

プラグインがインストールされると、 **[アドオンの管理]** の **[User Installed add-ons]\(ユーザーがインストールしたアドオン\)** セクションに表示されます。

## <a name="plug-in-configuration"></a>プラグイン構成

プラグインの使用を開始する前に、プラグインを構成する必要があります。 プラグインを選択して、 **[構成]** ボタンを選択し、構成の詳細を入力します。

次の図は、Jira と Confluence 両方の構成画面です。

![プラグインの構成画面](./media/ms-confluence-jira-plugin-adminguide/jira.png)

* **メタデータ URL**:Azure AD からフェデレーション メタデータを取得するための URL です。

* **識別子**:Azure AD が要求のソースを検証するために使用する URL です。 これは、Azure AD の **識別子** 要素にマップされます。 このプラグインはこの URL を https:// *\<domain:port>* / として自動的に派生させます。

* **応答 URL**:SAML サインインを開始する ID プロバイダー (IdP) の応答 URL です。 これは、Azure AD の **応答 URL** 要素にマップされます。 このプラグインはこの URL を https:// *\<domain:port>* /plugins/servlet/saml/auth として自動的に派生させます。

* **サインオン URL**:SAML サインインを開始する IdP のサインオン URL です。 これは、Azure AD の **サインオン** 要素にマップされます。 このプラグインはこの URL を https:// *\<domain:port>* /plugins/servlet/saml/auth として自動的に派生させます。

* **IdP Entity ID (IdP エンティティ ID)** :IdP が使うエンティティ ID です。 このボックスは、メタデータ URL が解決されると設定されます。

* **ログイン URL**:IdP からのサインイン URL です。 このボックスは、メタデータ URL が解決されると Azure AD から設定されます。

* **ログアウト URL**:IdP からのログアウト URL です。 このボックスは、メタデータ URL が解決されると Azure AD から設定されます。

* **X.509 証明書**:IdP の X.509 証明書です。 このボックスは、メタデータ URL が解決されると Azure AD から設定されます。

* **Login Button Name (ログイン ボタン名)** :組織がサインイン ページでユーザーに表示するサインイン ボタンの名前です。

* **SAML User ID Locations (SAML ユーザー ID の場所)** :SAML の応答で Jira または Confluence のユーザー ID が必要となる場所です。 **NameID** またはカスタム属性名を使用できます。

* **属性名**:ユーザー ID が格納されている属性の名前です。

* **Enable Home Realm Discovery (ホーム領域の検出を有効にする)** :Active Directory フェデレーション サービス (AD FS) ベースのサインインを使用する場合に選択します。

* **ドメイン名**:サインインが AD FS ベースの場合のドメイン名です。

* **Enable Single Signout (シングル サインアウトを有効にする)** :ユーザーが Jira または Confluence からサインアウトしたときに Azure AD からサインアウトする場合に選択します。

## <a name="troubleshooting"></a>トラブルシューティング

* **複数証明書のエラーが発生する**:Azure AD にサインインし、アプリに対して使用可能な複数の証明書を削除します。 証明書が 1 つだけであることを確認します。

* **Azure AD で証明書の有効期限がまもなく切れる**:アドオンは、証明書の自動ロールオーバーを処理します。 証明書の有効期限が近づいたら、新しい証明書はアクティブとしてマークし、使われていない証明書を削除する必要があります。 この状況でユーザーが Jira にサインインしようとすると、プラグインは新しい証明書を取得して保存します。

* **WebSudo を無効にしたい (セキュリティで保護された管理者セッションを無効にする)** :

  * Jira の場合、セキュリティで保護された管理者セッション (つまり、管理機能にアクセスする前のパスワードの確認) は既定で有効になります。 Jira インスタンスでこの機能を削除する場合は、jira-config.properties ファイルに次の行を指定します: `jira.websudo.is.disabled = true`

  * Confluence の場合は、[Confluence のサポート サイト](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html)の手順に従います。

* **メタデータ URL で設定されることになっているフィールドが設定されない**:

  * URL が正しいことを確認します。 正しいテナントとアプリ ID をマップしていることを確認します。

  * ブラウザーに URL を入力し、フェデレーション メタデータ XML を受け取ることを確認します。

* **内部サーバー エラーが発生する**:インストールのログ ディレクトリにあるログを確認します。 ユーザーが Azure AD の SSO を使ってサインインしようとするとエラーが発生する場合は、サポート チームにログを提供します。

* **ユーザーがサインインしようとすると "ユーザー ID が見つからない" というエラーが発生する**:Jira または Confluence でユーザー ID を作成します。

* **Azure AD で "アプリが見つからない" というエラーが発生する**:適切な URL が Azure AD のアプリにマップされていることを確認します。

* **サポートを必要とする**:[Azure AD SSO 統合チーム](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)までお問い合わせください。 チームが 24 から 48 営業時間以内に応答します。

  Azure Portal チャネルを通じて Microsoft にサポート チケットを提出することもできます。

## <a name="plug-in-faq"></a>プラグインに関するよくあるご質問

このプラグインについて質問がある場合は、次の FAQ を参照してください。

### <a name="what-does-the-plug-in-do"></a>このプラグインは何を行いますか。

このプラグインは、Atlassian Jira (Jira Core、Jira Software、Jira Service Desk を含む) および Confluence オンプレミス ソフトウェアのシングル サインオン (SSO) 機能を提供します。 このプラグインは Azure Active Directory (Azure AD) で ID プロバイダー (IdP) として動作します。

### <a name="which-atlassian-products-does-the-plug-in-work-with"></a>このプラグインはどの Atlassian 製品で動作しますか。

このプラグインはオンプレミス バージョンの Jira と Confluence で動作します。

### <a name="does-the-plug-in-work-on-cloud-versions"></a>このプラグインはクラウド バージョンで動作しますか。

いいえ。 このプラグインはオンプレミス バージョンの Jira と Confluence のみサポートしています。

### <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>このプラグインはどのバージョンの Jira と Confluence をサポートしていますか。

このプラグインは次のバージョンをサポートしています。

* JIRA Core と Software:6.0 から 7.12
* Jira Service Desk:3.0.0 から 3.5.0
* JIRA は 5.2 もサポートします。 詳細については、[Microsoft Azure Active Directory single sign-on for JIRA 5.2](./jira52microsoft-tutorial.md) に関する記事を参照してください。
* Confluence: 5.0 から 5.10
* Confluence: 6.0.1
* Confluence: 6.1.1
* Confluence: 6.2.1
* Confluence: 6.3.4
* Confluence: 6.4.0
* Confluence: 6.5.0
* Confluence: 6.6.2
* Confluence: 6.7.0
* Confluence: 6.8.1
* Confluence: 6.9.0
* Confluence: 6.10.0
* Confluence: 6.11.0
* Confluence: 6.12.0

### <a name="is-the-plug-in-free-or-paid"></a>このプラグインは無料ですか、有料ですか。

このアドオンは無料です。

### <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>プラグインを展開した後に Jira または Confluence を再起動する必要はありますか。

再起動は必要ありません。 すぐにプラグインの使用を開始できます。

### <a name="how-do-i-get-support-for-the-plug-in"></a>プラグインのサポートを受けるにはどうすればよいですか。

このプラグインに関するサポートが必要な場合は、[Azure AD SSO 統合チーム](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)までお問い合わせください。 チームが 24 から 48 営業時間以内に応答します。

Azure Portal チャネルを通じて Microsoft にサポート チケットを提出することもできます。

### <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>このプラグインは、Mac または Ubuntu にインストールされている Jira と Confluence で動作しますか。

このプラグインは、64 ビット版 Windows Server にインストールされた Jira および Confluence でのみテストされています。

### <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>このプラグインは Azure AD 以外の IdP で動作しますか。

いいえ。 Azure AD でのみ動作します。

### <a name="what-version-of-saml-does-the-plug-in-work-with"></a>このプラグインはどのバージョンの SAML で動作しますか。

SAML 2.0 で動作します。

### <a name="does-the-plug-in-do-user-provisioning"></a>このプラグインはユーザー プロビジョニングを行いますか。

いいえ。 このプラグインは SAML 2.0 ベースの SSO のみを提供します。 SSO サインイン前にアプリケーションでユーザーをプロビジョニングする必要があります。

### <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>このプラグインはクラスター バージョンの Jira と Confluence をサポートしていますか。

いいえ。 このプラグインはオンプレミス バージョンの Jira と Confluence で動作します。

### <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>このプラグインは HTTP バージョンの Jira と Confluence で動作しますか。

いいえ。 このプラグインは、HTTPS 対応のインストールでのみ動作します。