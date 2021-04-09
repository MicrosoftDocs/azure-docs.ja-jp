---
title: セキュリティ トークン | Azure
titleSuffix: Microsoft identity platform
description: Microsoft ID プラットフォームでのセキュリティ トークンの基本について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/11/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 6d9f5538d377be1414089e591559344bde4f381a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98795641"
---
# <a name="security-tokens"></a>セキュリティ トークン

一元化された ID プロバイダーは、世界中にいるユーザーが必ずしも企業のネットワークからサインインするわけではないアプリにとって特に役立ちます。 Microsoft ID プラットフォームではユーザーを認証し、セキュリティ トークン ([アクセス トークン](developer-glossary.md#access-token)、[リフレッシュトークン](developer-glossary.md#refresh-token)、[ID トークン](developer-glossary.md#id-token)など) を提供します。 セキュリティ トークンを使用すると、[クライアント アプリケーション](developer-glossary.md#client-application)は、[リソース サーバー](developer-glossary.md#resource-server)上の保護されたリソースにアクセスできます。

**アクセス トークン**:アクセス トークンは、[OAuth 2.0](active-directory-v2-protocols.md) フローの一部として [承認サーバー](developer-glossary.md#authorization-server)によって発行されるセキュリティ トークンです。 これには、そのトークンの対象となるユーザーとリソースに関する情報が含まれています。 この情報を使用すると、Web API やその他の保護されたリソースにアクセスできます。 アクセス トークンは、クライアント アプリにアクセス権を付与するためにリソースによって検証されます。 Microsoft ID プラットフォームがアクセス トークンを発行する方法の詳細については、[アクセス トークン](access-tokens.md)に関するページを参照してください。

**リフレッシュトークン**: アクセス トークンは短時間しか有効でないため、承認サーバーでは、アクセス トークンが発行されると同時にリフレッシュトークンを発行する場合があります。 クライアント アプリケーションでは、必要に応じて、このリフレッシュトークンを新しいアクセス トークンに交換できます。 Microsoft ID プラットフォームがリフレッシュトークンを使用してアクセス許可を取り消す方法の詳細については、「[トークンの失効](access-tokens.md#token-revocation)」を参照してください。

**ID トークン**: ID トークンは、[OpenID Connect](v2-protocols-oidc.md) フローの一部としてクライアント アプリケーションに送信されます。 これらは、アクセス トークンの代わりに、またはアクセス トークンと共に送信できます。 ID トークンは、ユーザーを認証するためにクライアントによって使用されます。 Microsoft ID プラットフォームが ID トークンを発行する方法の詳細については、[ID トークン](id-tokens.md)に関するページを参照してください。

> [!NOTE]
> この記事では、OAuth2 プロトコルと OpenID Connect プロトコルで使用されるセキュリティ トークンについて説明します。 多くのエンタープライズ アプリケーションは、SAML を使用してユーザーを認証します。 SAML アサーションについては、[Azure Active Directory SAML トークンのリファレンス](reference-saml-tokens.md)に関するページを参照してください。

## <a name="validate-security-tokens"></a>セキュリティ トークンを検証する

トークンの検証は、そのトークンが生成されたアプリ、ユーザーをサインインさせた Web アプリ、または呼び出される Web API に任されています。 トークンは、秘密キーを使用して承認サーバーによって署名されます。 承認サーバーでは、対応する公開キーを発行します。 トークンを検証するために、アプリでは、承認サーバーの公開キーを使用して署名を検証することによって、その署名が秘密キーを使用して作成されたことを検証します。

トークンは、限られた時間だけ有効になります。 通常、承認サーバーでは、次のようなトークンのペアを提供します。

* アクセス トークン。これは、アプリケーションまたは保護されたリソースにアクセスします。
* リフレッシュトークン。これは、アクセス トークンが有効期限に近づいたとき、そのアクセス トークンを更新するために使用されます。

アクセス トークンは、`Authorization` ヘッダーのベアラー トークンとして Web API に渡されます。 アプリでは、承認サーバーにリフレッシュトークンを提供できます。 アプリへのユーザー アクセスが取り消されなかった場合、そのアプリには新しいアクセス トークンと新しいリフレッシュトークンが返されます。 ユーザーが退職する場合、この方法を使用して対処します。 承認サーバーでは、リフレッシュトークンを受信した場合、ユーザーが承認されなくなっていると別の有効なアクセス トークンを発行しません。

## <a name="json-web-tokens-and-claims"></a>JSON Web トークンと要求

Microsoft ID プラットフォームでは、*要求* を含む JSON Web トークン (JWT) としてセキュリティ トークンを実装します。 JWT はセキュリティ トークンとして使用されるため、この認証形式は *JWT 認証* と呼ばれることがあります。

[要求](developer-glossary.md#claim)では、一方のエンティティ (クライアント アプリケーション、[リソース所有者](developer-glossary.md#resource-owner)など) に関するアサーションが、もう一方のエンティティ (リソース サーバーなど) に渡されます。 要求は、JWT 要求または JSON Web トークン要求と呼ばれることもあります。

要求は、トークン サブジェクトに関するファクトを中継する名前または値のペアです。 たとえば、ある要求に、承認サーバーによって認証されたセキュリティ プリンシパルに関するファクトが含まれていることがあります。 特定のトークン内に存在する要求は、トークンの種類、サブジェクトを認証するために使用される資格情報の種類、アプリケーション構成など、多くのものによって異なります。

アプリケーションでは、次のようなさまざまなタスクのために要求を使用できます。

* トークンを検証する。
* トークン サブジェクトの[テナント](developer-glossary.md#tenant)を識別する。
* ユーザー情報を表示する。
* サブジェクトの承認を判定する。

要求は、次のような情報を提供するキーと値のペアで構成されます。

* トークンを生成したセキュリティ トークン サーバー。
* トークンが生成された日付。
* サブジェクト (ユーザーなど、デーモンを除く)。
* 対象ユーザー。これは、トークンが生成されたアプリです。
* トークンを要求したアプリ (クライアント)。 Web アプリの場合、このアプリは対象ユーザーと同じである可能性があります。

Microsoft ID プラットフォームがトークンと要求の情報を実装する方法の詳細については、[アクセス トークン](access-tokens.md)および[ID トークン](id-tokens.md)に関するページを参照してください。

## <a name="how-each-flow-emits-tokens-and-codes"></a>各フローがトークンとコードを生成する方法

クライアントの構築方法に応じて、Microsoft ID プラットフォームでサポートされている認証フローの 1 つ (または複数) を使用できます。 これらのフローでは、さまざまなトークン (ID トークン、リフレッシュトークン、アクセス トークン) と承認コードを生成できます。 これらには、それ自体を機能させるための別のトークンが必要です。 次の表は、その概要を示しています。

|Flow | 必要 | ID トークン | アクセス トークン | リフレッシュトークン | Authorization code (承認コード) |
|-----|----------|----------|--------------|---------------|--------------------|
|[承認コード フロー](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[暗黙的なフロー](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[ハイブリッド OIDC フロー](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition)| | x  | |          |            x   |
|[更新トークンの使用](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | リフレッシュトークン | x | x | x| |
|[On-Behalf-Of フロー](v2-oauth2-on-behalf-of-flow.md) | アクセス トークン| x| x| x| |
|[クライアントの資格情報](v2-oauth2-client-creds-grant-flow.md) | | | x (アプリのみ)| | |

暗黙モードで発行されたトークンは、URL 経由でブラウザーに戻される (ここで、`response_mode` は `query` または `fragment`) ため、長さに制限があります。 一部のブラウザーは、ブラウザー バーに表示できる URL のサイズに制限があるため、それが長すぎると失敗します。 その結果、これらのトークンに `groups` または `wids` 要求はありません。

## <a name="next-steps"></a>次のステップ

Microsoft ID プラットフォームでの認証と承認の詳細については、次の記事を参照してください。

* 認証と承認の基本的な概念については、「[認証と承認](authentication-vs-authorization.md)」を参照してください。
* 統合のためのアプリケーションの登録については、「[アプリケーション モデル](application-model.md)」を参照してください。
* Web アプリ、デスクトップ アプリ、モバイル アプリのサインイン フローについては、[アプリのサインイン フロー](app-sign-in-flow.md)に関するページを参照してください。
