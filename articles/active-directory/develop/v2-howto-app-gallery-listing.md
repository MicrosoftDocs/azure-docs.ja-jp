---
title: アプリを Azure AD アプリ ギャラリーで公開する
description: シングル サインオンをサポートするアプリケーションを Azure Active Directory アプリ ギャラリーに掲載する方法を説明します。
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/14/2020
ms.author: kenwith
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: 1ce8f376d3264a06618cb7321c75142dcf92d9ea
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88891863"
---
# <a name="publish-your-app-to-the-azure-ad-app-gallery"></a>アプリを Azure AD アプリ ギャラリーで公開する

アプリを Azure AD アプリ ギャラリーで公開することができます。 公開されたアプリは、カスタマーがアプリをテナントに追加するときに、オプションとして表示されます。 

Azure AD ギャラリーにアプリを追加すると、次のような利点があります。

- カスタマーは、アプリに対して考えられる最良のシングル サインオン エクスペリエンスを見つけられます。
- アプリケーションの構成は簡単で最小限です。
- クイック検索でギャラリー内のアプリケーションが検索されます。
- Free、Basic、Premium すべての Azure AD ユーザーがこの情報を使用できます。
- 共通の顧客向けの詳細な構成手順チュートリアルがあります。

さらに、カスタマーがアプリの ID プロバイダーとして Azure AD を使用すると、多くの利点があります。 たとえば、次のようなシナリオがあります。

- ユーザーに対してシングル サインオンを提供します。 SSO を使用すると、カスタマーのシングル サインオンが容易になることにより、サポート コストを削減できます。 ワンクリック SSO が有効になっている場合、顧客の IT 管理者がアプリケーションの構成方法を知らなくても、アプリケーションを組織内で使用できます。 シングル サインオンの詳細については、「[シングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
- アプリが、Microsoft 365 アプリ ギャラリー、Microsoft 365 アプリ起動ツール、Office.com の Microsoft Search で見つかるようになります。 
- 統合されたアプリの管理。 Azure AD でのアプリ管理の詳細については、「[アプリケーション管理とは](../manage-apps/what-is-application-management.md)」を参照してください。
- アプリでは、[Graph API](https://docs.microsoft.com/graph/) を使用して、Microsoft のエコシステムでユーザーの生産性を向上させるデータにアクセスできます。
- Azure AD チームと共同で共通のカスタマー向けにアプリケーション固有のドキュメントを作成することで、導入しやすくなります。
- 従業員 ID やゲスト ID の認証と承認を完全に管理する機能を顧客に提供できます。
- すべてアカウント管理とコンプライアンスの責任を顧客の ID 所有者が担います。
- ビジネス ニーズを満たすように、ID プロバイダーごと、グループごと、またはユーザーごとに SSO を有効/無効にできるようにします。
- 市場性と導入可能性を高めることができます。 多くの大規模組織では、従業員がすべてのアプリケーション間でシームレスな SSO エクスペリエンスを利用できることを必要として (切望して) います。 SSO を容易にすることが重要です。
- エンドユーザーの煩雑さを減らすことで、エンドユーザーの利用と収益を拡大することができます。
- System for Cross-domain Identity Management ([SCIM](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)) を使用している顧客は、同じアプリのプロビジョニングを使用できます。
- ユーザーが Azure AD SSO を使用してアプリケーションにサインオンすると、別の資格情報が必要なくなり、セキュリティと利便性が向上します。

> [!TIP]
> 購入またはサブスクリプションを通じて他の会社にアプリケーションを提供する際に、顧客が独自の Azure テナント内でアプリケーションを利用できるようにします。 これは、マルチテナント アプリケーションの作成と呼ばれます。 この概念の概要については、「[Azure 上のマルチテナント アプリケーション](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications)」と「[Azure Active Directory のテナント](single-and-multi-tenant-apps.md)」を参照してください。

> [!IMPORTANT]
> Azure AD ギャラリーでアプリを公開するには、特定の使用条件に同意する必要があります。 始める前に、[使用条件](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)を読んで同意してください。

アプリを Azure AD アプリ ギャラリーで公開する手順は次のとおりです。
1. アプリに適したシングル サインオン標準を選択します。
2. アプリにシングル サインオンを実装します。
3. Azure テナントを作成し、アプリをテストします。
4. ドキュメントを作成して公開します。
5. アプリを送信します。
6. Microsoft Partner Network に参加します。


## <a name="prerequisites"></a>前提条件

テストには、2 人以上のユーザーが登録されている永続的なアカウントが必要です。


## <a name="step-1---choose-the-right-single-sign-on-standard-for-your-app"></a>ステップ 1 - アプリに適したシングル サインオン標準を選択する

Azure AD アプリ ギャラリーにアプリケーションを掲載するには、サポートされているシングル サインオン オプションの少なくとも 1 つを実装する必要があります。 シングル サインオンのオプションと、カスタマーが Azure AD でそれらを構成する方法については、[SSO のオプション](../manage-apps/sso-options.md)に関する記事を参照してください。

次の表は、主な標準を比較したものです: Open Authentication 2.0 (OAuth 2.0) と OpenID Connect (OIDC)、Security Assertion Markup Language (SAML)、Web Services Federation (WS-Fed)。

| 機能| OAuth/OIDC| SAML/WS-Fed |
| - |-|-|
| Web ベースのシングル サインオン| √| √ |
| Web ベースのシングル サインアウト| √| √ |
| モバイルベースのシングル サインオン| √| √* |
| モバイルベースのシングル サインアウト| √| √* |
| モバイル アプリケーションの条件付きアクセス ポリシー| √| X |
| モバイル アプリケーションのシームレスな MFA エクスペリエンス| √| X |
| Microsoft Graph へのアクセス| √| X |

* 可能ですが、Microsoft ではサンプルやガイダンスを提供していません。

### <a name="oauth-20-and-openid-connect"></a>OAuth 2.0 と OpenID Connect
OAuth 2.0 は、認可用の[業界標準](https://oauth.net/2/)プロトコルです。 OpenID Connect (OIDC) は、OAuth 2.0 プロトコルを基盤に構築された、[業界標準](https://openid.net/connect/)の ID 認証レイヤーです。 

**OAuth/OIDC を選択する理由**
- これらのプロトコルに固有の認可を使用することで、アプリケーションは Microsoft Graph API を介して、さまざまなユーザー データや組織データにアクセスし、これらのデータと統合できます。
- アプリケーションに SSO を採用すると、カスタマーのエンド ユーザー エクスペリエンスが簡素化されます。 必要なアクセス許可セットを簡単に定義でき、管理者またはエンド ユーザーの同意を得る際に、これらのアクセス許可セットを自動的に表示できます。
- これらのプロトコルを使用すると、顧客は条件付きアクセスおよび多要素認証 (MFA) ポリシーを使用して、アプリケーションへのアクセスを制御することができます。 
- Microsoft では、開発を支援するために、ライブラリと[複数のテクノロジ プラットフォームにわたるコード サンプル](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples)を提供しています。  

**考慮事項**
- アプリケーションに対して SAML ベースのシングル サインオンを既に実装してある場合、ギャラリーでアプリを取得するために新しい標準を実装する必要がないことがあります。

### <a name="saml-20-or-ws-fed"></a>SAML 2.0 または WS-Fed

SAML は、Web アプリケーション向けの[シングルサインオン標準](https://www.oasis-open.org/standards#samlv2.0)として、成熟し、広く採用されています。 Azure による SAML の使用方法の詳細については、[Azure での SAML プロトコルの使用方法](active-directory-saml-protocol-reference.md)に関する記事を参照してください。 

Web Services Federation (WS-Fed) は、.NET プラットフォームを使用して開発された Web アプリケーションに一般に使用される[業界標準](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html)です。

**SAML を選択する理由**
- SAML 2.0 は成熟した標準であり、ほとんどテクノロジ プラットフォームが SAML 2.0 のオープンソース ライブラリをサポートしています。 
- 開発者は SAML SSO を構成する管理インターフェイスを顧客に提供することができます。 カスタマーは、Microsoft Azure AD だけでなく、SAML をサポートする他の任意の ID プロバイダー向けに SAML SSO を構成することができます。

**考慮事項**
- SAML 2.0 または WSFed プロトコルをモバイル アプリケーションに使用した場合、多要素認証 (MFA) など、特定の条件付きアクセス ポリシーのエクスペリエンスが低下します。
- Microsoft Graph にアクセスする場合、必要なトークンを生成するために OAuth 2.0 を介した認可を実装する必要があります。 

### <a name="password-based"></a>パスワード ベース
パスワード ベースの SSO (パスワード保管ともいう) では、ID フェデレーションをサポートしない Web アプリケーションに対するユーザーのアクセスおよびパスワードを管理できます。 これは、複数のユーザーが、組織のソーシャル メディア アプリ アカウントなど、1 つのアカウントを共有する必要があるシナリオにも便利です。


## <a name="step-2---implement-single-sign-on-in-your-app"></a>ステップ 2 - アプリにシングル サインオンを実装する
ギャラリー内のすべてのアプリでは、サポートされているシングル サインオン オプションのいずれかが実装されている必要があります。 サポートされているオプションの詳細については、[SSO のオプション](../manage-apps/sso-options.md)に関する記事を参照してください。

OAuth および OIDC については、[認証パターンについてのガイダンス](v2-app-types.md)および [Azure Active Directory のコード サンプル](sample-v2-code.md)に関する記事を参照してください。

SAML と WS-Fed の場合、アプリケーションは SP または IDP モードで SSO 統合を行う機能を備えている必要があります。 要求を送信する前に、この機能が正しく動作していることを確認してください。

認証の詳細については、「[認証とは](../azuread-dev/v1-authentication-scenarios.md)」を参照してください。

> [!IMPORTANT]
> フェデレーション アプリケーション (OpenID および SAML/WS-Fed) の場合は、アプリでサービスとしてのソフトウェア (SaaS) モデルがサポートされている必要があります。 Azure AD ギャラリー アプリケーションでは、特定の単一のカスタマーではなく、複数のカスタマーの構成がサポートされている必要があります。

### <a name="implement-oauth-20-and-openid-connect"></a>OAuth 2.0 と OpenID Connect を実装する

OpenID Connect の場合、アプリケーションはマルチテナントである必要があり、[Azure AD 同意フレームワーク](consent-framework.md)がアプリケーションに適切に実装されている必要があります。 ユーザーは、すべての顧客がアプリケーションへの同意を提供できるように、共通エンドポイントにサインイン要求を送信できます。 トークンで受け取ったテナント ID とユーザーの UPN に基づいてユーザー アクセスを制御できます。

具体的な例を確認するには、「[Microsoft ID プラットフォームのコード サンプル](sample-v2-code.md)」を参照してください。 

モバイル固有の例を確認するには、以下を参照してください。 
* [Android](quickstart-v2-android.md)
* [iOS](quickstart-v2-ios.md)
* [ユニバーサル Windows プラットフォーム](quickstart-v2-uwp.md)

### <a name="implement-saml-20"></a>SAML 2.0 を実装する

アプリで SAML 2.0 がサポートされている場合は、Azure AD テナントに直接統合できます。 Azure AD での SAML の構成の詳細については、「[SAML ベースのシングル サインオンの構成](../manage-apps/configure-saml-single-sign-on.md)」を参照してください。

Microsoft では、SAML の実装用のライブラリは提供されていないか、または推奨されていません。 利用できるオープンソース ライブラリは数多くあります。

### <a name="implement-ws-fed"></a>WS-Fed を実装する
ASP.NET Core での WS-Fed の詳細については、「[ASP.NET Core で WS-Federation を使用してユーザーを認証する](https://docs.microsoft.com/aspnet/core/security/authentication/ws-federation)」を参照してください。

### <a name="implement-password-vaulting"></a>パスワードの保管を実装する

HTML のサインイン ページがある Web アプリケーションを作成します。 シングル サインオンが期待どおりに動作するように、アプリケーションでフォーム認証をサポートしてパスワード保管を行えることを確認します。


## <a name="step-3---create-your-azure-tenant-and-test-your-app"></a>ステップ 3 - Azure テナントを作成してアプリをテストする

アプリをテストするには、Azure AD テナントが必要です。 開発環境をセットアップするには、「[クイックスタート: テナントを設定する](quickstart-create-new-tenant.md)」を参照してください。

または、すべての Microsoft 365 サブスクリプションには、Azure AD テナントが付属しています。 無料の Microsoft 365 開発環境をセットアップするには、[Microsoft 365 開発者プログラムへの参加](https://docs.microsoft.com/office/developer-program/microsoft-365-developer-program)に関するページを参照してください。

テナントの用意ができたら、シングル サインオン アクセスを有効にしてテストする必要があります。 

**OIDC または Oath アプリケーション**の場合、マルチテナント アプリケーションとして[アプリケーションを登録](quickstart-register-app.md)します。 ‎[サポートされているアカウントの種類] で、[任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント] オプションを選択します。

**SAML ベース、および WS-Fed ベースのアプリケーション**の場合、Azure AD で汎用 SAML テンプレートを使用して [SAML ベースのシングル サインオン アプリケーションを構成](../manage-apps/configure-saml-single-sign-on.md)します。

必要に応じて、[シングルテナント アプリケーションをマルチテナントに変換](howto-convert-app-to-be-multi-tenant.md)することもできます。


## <a name="step-4---create-and-publish-documentation"></a>ステップ 4 - ドキュメントを作成して公開する

### <a name="documentation-on-your-site"></a>サイト上のドキュメント

導入の容易さは、エンタープライズ ソフトウェアの決定において重要な要素です。 明確で分かりやすいドキュメントは、導入の過程でお客様の役に立ち、サポート コストも削減されます。 Microsoft は、数千のソフトウェア ベンダーと連携する過程で、どのようなものが有効かを見てきました。

サイト上のドキュメントには、少なくとも次の項目を含めることをお勧めします。

* SSO 機能の概要
  * サポート対象プロトコル
  * バージョンと SKU
  * サポートされる ID プロバイダーの一覧とドキュメント リンク
* アプリケーションのライセンス情報
* SSO を構成するためのロールベースのアクセス制御
* SSO の構成手順
  * SAML の UI 構成要素とプロバイダーからの予期される値
  * ID プロバイダーに渡すサービス プロバイダー情報
* OIDC/OAuth の場合
  * 業務上の正当な理由に同意するために必要なアクセス許可の一覧
* パイロット ユーザーのテスト手順
* エラー コードとメッセージを含むトラブルシューティング情報
* お客様のサポート メカニズム

### <a name="documentation-on-the-microsoft-site"></a>Microsoft サイト上のドキュメント

Azure Active Directory アプリケーション ギャラリーにアプリケーションを登録すると (これにより、Azure Marketplace でもアプリケーションが公開されます)、Microsoft により、段階的なプロセスを説明するために、共通のお客様向けのドキュメントが生成されます。 [こちら](https://aka.ms/appstutorial)で例を参照できます。 このドキュメントは、ギャラリーへの送信に基づいて作成されます。GitHub アカウントを使用してアプリケーションに変更を加える場合、このドキュメントを簡単に更新できます。


## <a name="step-5---submit-your-app"></a>ステップ 5 - アプリを送信する

Azure ADでアプリケーションの統合が作動するのをテストした後、[Microsoftアプリケーションネットワークポータル](https://microsoft.sharepoint.com/teams/apponboarding/Apps)へのアクセス要求を送信してください。

ポータルに初めてサインインしようとすると、2 つの画面のいずれかが表示されます。 

"失敗しました" というメッセージが表示される場合は、[Azure AD SSO 統合チーム](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)に連絡する必要があります。 要求の送信に使用する電子メール アカウントを指定します。 `name@yourbusiness.com`などの会社の電子メールアドレスをお勧めします。 Azure AD チームにより、アカウントが Microsoft アプリケーション ネットワーク ポータルに追加されます。

[アクセス権の要求] ページが表示される場合は、業務上の正当な理由を入力し、 **[アクセス権の要求]** を選択します。

アカウントが追加されたら、Microsoft アプリケーション ネットワーク ポータルにサインインし、ホーム ページの **[Submit Request (ISV)]\(要求の送信 (ISV)\)** タイルを選択することで、要求を送信できます。

![ホーム ページの [要求の送信 (ISV)] タイル](./media/howto-app-gallery-listing/homepage.png)

### <a name="issues-on-logging-into-portal"></a>ポータルへのログインに関する問題

ログイン中に次のエラーが表示される問題について、ここで詳細と解決方法を説明します。

* サインインが次に示すようにブロックされた場合。

  ![ギャラリーのアプリケーションの解決に関する問題](./media/howto-app-gallery-listing/blocked.png)

**起きていること:**

ゲスト ユーザーは、ホーム テナントにフェデレーションされます。このテナントも、Azure AD です。 ゲスト ユーザーは高リスクです。 Microsoft では、高リスクのユーザーにリソースへのアクセスを許可していません。 すべての高リスク ユーザー (従業員またはゲストおよびベンダー) が Microsoft リソースにアクセスするには、リスクを修復またはクローズする必要があります。 ゲスト ユーザーの場合、このユーザーのリスクはホーム テナントに由来しており、ポリシーはリソース テナント (この場合は Microsoft) から取得されます。
 
**安全なソリューション:**

* MFA に登録されたゲスト ユーザーは、自分のユーザー リスクを修復します。 そうするには、ゲスト ユーザーがホーム テナントで、セキュリティで保護されたパスワード変更またはリセット (https://aka.ms/sspr) を行います (これにはホーム テナントでの MFA および SSPR が必要です)。 セキュリティで保護されたパスワード変更またはリセットは、オンプレミスではなく Azure AD で開始する必要があります。

* ゲスト ユーザーは、管理者にリスクを修正してもらいます。 この場合、管理者はパスワードのリセット (一時的なパスワードの生成) を行います。 これには Identity Protection は必要ありません。 ゲスト ユーザーの管理者は、 https://aka.ms/RiskyUsers にアクセスして、[パスワードのリセット] をクリックできます。

* ゲスト ユーザーは、管理者にリスクをクローズまたは無視してもらいます。 この場合も、Identity Protection は必要ありません。 管理者は https://aka.ms/RiskyUsers にアクセスして、[ユーザー リスクを無視する] をクリックできます。 ただし、管理者はユーザー リスクをクローズする前にデュー デリジェンスを行い、これが偽陽性のリスク評価であったことを確認する必要があります。 そうしない場合、調査なしでリスク評価を無視することで、自分と Microsoft のリソースをリスクにさらすことになります。

> [!NOTE]
> アクセスに関して問題が発生した場合は、[Azure AD の SSO 統合チーム](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)にお問い合わせください。

### <a name="implementation-specific-options"></a>実装固有のオプション
OpenID Connect を使用してギャラリー内の一覧にご利用のアプリケーションを追加する場合は、上記のように **[OpenID Connect & OAuth 2.0]** を選択します。

![ギャラリーでの OpenID Connect アプリケーションの一覧表示](./media/howto-app-gallery-listing/openid.png)

**SAML 2.0** または **WS-Fed** を使用してギャラリー内の一覧にご利用のアプリケーションを追加する場合は、上記のように **[SAML 2.0/WS-Fed]** を選択します。

![ギャラリーでの SAML 2.0 または WS-Fed アプリケーションの一覧表示](./media/howto-app-gallery-listing/saml.png)

パスワード SSO を使用してギャラリー内の一覧にご利用のアプリケーションを追加する場合は、上記のように **[Password SSO]** を選択します。

![ギャラリーでのパスワード SSO アプリケーションの一覧表示](./media/howto-app-gallery-listing/passwordsso.png)

ユーザー プロビジョニングのために SCIM 2.0 エンドポイントを実装する場合は、示されているようにオプションを選択します。 

   ![ユーザー プロビジョニングの要求](./media/howto-app-gallery-listing/user-provisioning.png)

### <a name="update-or-remove-an-existing-listing"></a>既存の一覧を更新または削除する

[Microsoft アプリケーション ネットワーク ポータル](https://microsoft.sharepoint.com/teams/apponboarding/Apps)で既存のギャラリー アプリを更新または削除できます。

![ギャラリーでの SAML アプリケーションの一覧表示](./media/howto-app-gallery-listing/updateorremove.png)

> [!NOTE]
> アクセスに問題がある場合は、アカウントの作成に関する前のセクションを確認してください。 それでもうまくいかない場合は、[Azure AD SSO 統合チーム](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)にお問い合わせください。


### <a name="timelines"></a>タイムライン

SAML 2.0 または WS-Fed アプリケーションをギャラリーに一覧表示するプロセスのタイムラインは、7 から 10 営業日です。

![ギャラリーに SAML アプリケーションを一覧するタイムライン](./media/howto-app-gallery-listing/timeline.png)

OpenID Connect アプリケーションをギャラリーに一覧表示するプロセスのタイムラインは、2 から 5 営業日です。

![ギャラリーに OpenID Connect アプリケーションを一覧表示するタイムライン](./media/howto-app-gallery-listing/timeline2.png)

### <a name="escalations"></a>エスカレーション

すべてのエスカレーションについては、[Azure AD SSO 統合チーム](mailto:SaaSApplicationIntegrations@service.microsoft.com)にメールでご連絡いただけば、可能な限り早く対応します。


## <a name="step-6---join-the-microsoft-partner-network"></a>ステップ 6 - Microsoft Partner Network に参加する
Microsoft Partner Network では、排他的なリソース、プログラム、ツール、接続にすばやくアクセスできます。 ネットワークに参加して、市場投入プランを作成するには、「[法人のお客様にリーチする](https://partner.microsoft.com/explore/commercial#gtm)」を参照してください。


## <a name="next-steps"></a>次のステップ

Azure AD のサインインをサポートするアプリケーションの作成の詳細については、「[Azure AD の認証シナリオ](authentication-flows-app-scenarios.md)」を参照してください。
