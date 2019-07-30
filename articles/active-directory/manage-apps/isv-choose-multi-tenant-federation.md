---
title: マルチテナント アプリケーションに適切なフェデレーション プロトコルを選択する
description: Azure Active Directory との統合に関する独立系ソフトウェア ベンダー向けガイダンス
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53b315b87200b37cda215a29a65be9babaf54f43
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795195"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>マルチテナント アプリケーションに適切なフェデレーション プロトコルを選択する

サービスとしてのソフトウェア (SaaS)、アプリケーションを開発する場合、開発者と顧客のニーズに最も適合するフェデレーション プロトコルを選択する必要があります。 プロトコルの決定は、ご使用の開発プラットフォームと、顧客の Office 365 および Azure AD エコシステム内で使用可能なデータと統合したいというご希望に基づきます。

Azure Active Directory との SSO 統合で使用できるプロトコルの完全な一覧については、[こちら](what-is-single-sign-on.md)を参照してください。
下の表では次のプロトコルを比較しています 
* Open Authorization 2.0 (OAuth 2.0)
* Open ID Connect (OIDC)
* Security Assertion Markup Language (SAML)
* Web Services Federation (WSFed)

| 機能| OAuth/OIDC| SAML/WSFed |
| - |-|-|
| Web ベースのシングル サインオン| √| √ |
| Web ベースのシングル サインアウト| √| √ |
| モバイルベースのシングル サインオン| √| √* |
| モバイルベースのシングル サインアウト| √| √* |
| モバイル アプリケーションの条件付きアクセス ポリシー| √| X |
| モバイル アプリケーションのシームレスな MFA エクスペリエンス| √| X |
| Microsoft Graph へのアクセス| √| X |

\* 可能ですが、Microsoft ではサンプルやガイダンスを提供していません。

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2.0 と Open ID Connect

OAuth 2.0 は、認可用の[業界標準](https://oauth.net/2/)プロトコルです。 OIDC (OpenID Connect) は、Oath 2.0 プロトコルを基盤に構築された、[業界標準](https://openid.net/connect/)の ID 認証レイヤーです。

### <a name="benefits"></a>メリット

認証と認可がプロトコルに組み込まれているため、Microsoft では OIDC/OAuth 2.0 の使用を推奨しています。 SAML では、さらに認可を実装する必要があります。

これらのプロトコルに固有の認可を使用することで、アプリケーションは Microsoft Graph API を介して、さまざまなユーザー データや組織データにアクセスし、これらのデータと統合できます。

アプリケーションに SSO を採用する場合、OAuth 2.0 と OIDC を使用すると、顧客のエンド ユーザー エクスペリエンスが簡素化されます。 必要なアクセス許可セットを簡単に定義でき、管理者またはエンド ユーザーの同意を得る際に、これらのアクセス許可セットを自動的に表示できます。

さらに、これらのプロトコルを使用すると、顧客は条件付きアクセスおよび MFA ポリシーを使用して、アプリケーションへのアクセスを制御することができます。 Microsoft では、開発を支援するために、ライブラリと[複数のテクノロジ プラットフォームにわたるコード サンプル](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples)を提供しています。  

### <a name="implementation"></a>実装

アプリケーションを登録するには Microsoft ID を使用しますが、これが OAuth 2.0 プロバイダーとなります。 その後は、統合対象の任意の ID プロバイダーを使用して OAuth 2.0 ベースのアプリケーションを登録できます。 

アプリケーションを登録し、これらの SSO 用プロトコルを Web アプリに実装する方法については、[OpenID Connect と Azure Active Directory を使用した Web アプリケーションへのアクセスの承認](../develop/sample-v2-code.md)に関するページをご覧ください。  これらの SSO 用プロトコルをモバイル アプリに実装する方法については、次を参照してください。 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [ユニバーサル Windows プラットフォーム](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2.0 と WSFed

Security Assertion Markup Language (SAML) は、通常、Web アプリケーションに使用します。 概要については、[Azure での SAML プロトコルの使用方法](../develop/active-directory-saml-protocol-reference.md)に関するページをご覧ください。 

Web Services Federation (WSFed) は、一般に、.Net プラットフォームを使用して開発された Web アプリケーションに使用される[業界標準](http://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html)です。

### <a name="benefits"></a>メリット

SAML 2.0 は成熟した標準であり、ほとんどテクノロジ プラットフォームが SAML 2.0 のオープンソース ライブラリをサポートしています。 開発者は SAML SSO を構成する管理インターフェイスを顧客に提供することができます。 顧客は、Microsoft Azure AD だけでなく、SAML 2.0 をサポートする他の任意の ID プロバイダー向けに SAML SSO を構成することができます。

### <a name="trade-offs"></a>トレードオフ

SAML 2.0 または WSFed プロトコルをモバイル アプリケーションに使用した場合、多要素認証 (MFA) など、特定の条件付きアクセス ポリシーのエクスペリエンスが低下します。 また、Microsoft Graph にアクセスする場合、必要なトークンを生成するために OAuth 2.0 を介した認可を実装する必要があります。 

### <a name="implementation"></a>実装

Microsoft では SAML 実装のライブラリを提供していません。また、特定のライブラリも推奨していません。 利用できるオープンソース ライブラリは数多くあります。

## <a name="sso-and-using-microsoft-graph-rest-api"></a>SSO と Microsoft Graph REST API の使用 

Microsoft Graph は、Office 365、Windows 10、Enterprise Mobility + Security を含むすべての Microsoft 365 やその他の製品 (Dynamics 365 など) のデータ ファブリックです。 ここには、ユーザーの生産性を向上するユーザー、グループ、予定表、メール、ファイルといったエンティティのコア スキーマが含まれます。 Microsoft Graph では、3 つのインターフェイス (REST ベースの API、Microsoft Graph データ接続、および開発者が Microsoft Graph に独自のデータを追加できるようにするコネクタ) を提供します。  

アプリケーションで上記のどの SSO 用プロトコルを使用した場合でも、Microsoft Graph REST API を介して豊富なデータにアクセスし、利用することができます。 これにより、顧客は Microsoft 365 への投資からより多くの価値を得ることができます。 たとえば、アプリケーションで Microsoft Graph API を呼び出して顧客の Office 365 のインスタンスと統合し、アプリケーション内でユーザーの Microsoft Office アイテムや SharePoint アイテムを表示することができます。 

認証に Open ID Connect を使用している場合、開発エクスペリエンスはシームレスになります。Open ID Connect の基盤である OAuth2 を使用してトークンを取得し、これを使って Microsoft Graph API を呼び出すことができるからです。 アプリケーションで SAML または WSFed を使用している場合は、アプリケーションにコードを追加して OAuth2 を取得し、Microsoft Graph API の呼び出しに必要なトークンを手に入れる必要があります。 

## <a name="next-steps"></a>次の手順

[マルチテナント アプリケーションの SSO を有効にする](isv-sso-content.md)

[マルチテナント アプリケーション用のドキュメントを作成する](isv-create-sso-documentation.md)
