---
title: セキュリティ トークン | Azure
titleSuffix: Microsoft identity platform
description: Microsoft ID プラットフォーム (v2.0) のセキュリティ トークンの基本について説明します。
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
ms.openlocfilehash: 88dc4bb86459cd0390c4c01deb871aa93e39c6d1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84266748"
---
# <a name="security-tokens"></a>セキュリティ トークン

一元化された ID プロバイダーは、ユーザーが世界中にいて、企業のネットワークから必ずしもサインインするわけではないアプリにとって特に役に立ちます。 Microsoft ID プラットフォームではユーザーを認証し、[アクセス トークン](developer-glossary.md#access-token)、[リフレッシュトークン](developer-glossary.md#refresh-token)、[ID トークン](developer-glossary.md#id-token)など、セキュリティ トークンを提供します。[クライアント アプリケーション](developer-glossary.md#client-application)では、セキュリティ トークンが与えられることで[リソース サーバー](developer-glossary.md#resource-server)上の保護されたリソースにアクセスできます。

**アクセス トークン**は、[OAuth 2.0](active-directory-v2-protocols.md) フローの一部として[承認サーバー](developer-glossary.md#authorization-server)によって発行されるセキュリティ トークンです。 トークンの対象となるユーザーとアプリに関する情報が含まれ、Web API やその他の保護されたリソースにアクセスするために使用できます。 Microsoft ID プラットフォームがアクセス トークンを発行する方法の詳細については、[アクセス トークン](access-tokens.md)に関するページを参照してください。

アクセス トークンは短時間のみ有効であるため、承認サーバーでは、アクセス トークンの発行と同時に**リフレッシュトークン**を発行することがあります。 クライアント アプリケーションでは、必要に応じて、このリフレッシュトークンを新しいアクセス トークンに交換できます。 Microsoft ID プラットフォームでリフレッシュトークンを使用してアクセス許可を取り消す方法の詳細については、「[トークンの失効](access-tokens.md#token-revocation)」を参照してください。

**ID トークン**は、[OpenID Connect](v2-protocols-oidc.md) フローの中でクライアント アプリケーションに送信されます。 これは、アクセス トークンと一緒に、またはアクセス トークンのかわりに送信することができ、ユーザーを認証するためにクライアントによって使用されます。 Microsoft ID プラットフォームが ID トークンを発行する方法の詳細については、[ID トークン](id-tokens.md)に関するページを参照してください。

> [!NOTE]
> この記事では、OAuth2 プロトコルと OpenID Connect プロトコルで使用されるセキュリティ トークンについて説明します。 多くのエンタープライズ アプリケーションは、SAML を使用してユーザーを認証します。 SAML アサーションの詳細については、「[Azure AD SAML トークンのリファレンス](reference-saml-tokens.md)」を参照してください。

## <a name="validating-security-tokens"></a>セキュリティ トークンを検証する

トークンを検証するには、トークンが生成されたアプリ、ユーザーをサインインさせた Web アプリ、または呼び出される Web API が必要です。 トークンは、秘密キーを使用して**セキュリティ トークン サーバー (STS)** によって署名されます。 STS により、対応する公開キーが発行されます。 トークンを検証するには、アプリでは、秘密キーを使用して署名が作成されたことを検証するために、STS 公開キーを使用して署名が検証されます。

トークンが有効な時間は限られています。 通常、STS からは一組のトークンが与えられます。

* アプリケーションまたは保護されているリソースにアクセスするためのアクセス トークン
* アクセス トークンの有効期限が近づいたとき、アクセス トークンを更新するためのリフレッシュトークン

アクセス トークンは、`Authorization` ヘッダーのベアラー トークンとして Web API に渡されます。 アプリでは、STS にリフレッシュトークンを提供できます。アプリへのユーザー アクセスが取り消されていない場合、新しいアクセス トークンと新しいリフレッシュトークンが返されます。 ユーザーが退職する場合、この方法を使用して対処します。 ユーザーが承認されなくなると、STS でリフレッシュトークンを受け取っても、有効なアクセス トークンは新たに発行されません。

## <a name="json-web-tokens-jwts-and-claims"></a>JSON Web トークン (JWT) と要求

Microsoft ID プラットフォームでは、**要求**を含む **JSON Web トークン (JWT)** としてセキュリティ トークンを実装します。 JWT はセキュリティ トークンとして使用されるため、この認証形式は **JWT 認証**と呼ばれることがあります。

[要求](developer-glossary.md#claim)では、一方のエンティティ (クライアント アプリケーション、[リソース所有者](developer-glossary.md#resource-owner)など) に関するアサーションが、もう一方のエンティティ (リソース サーバーなど) に渡されます。 要求は、JWT 要求または JSON Web トークン要求と呼ばれることもあります。

要求は、トークンのサブジェクトに関する事実を伝達する名前と値のペアです。 たとえば、要求には、承認サーバーによって認証されたセキュリティ プリンシパルに関する事実が含まれることがあります。 特定のトークン内にある要求は、多くの要素 (トークンの種類、サブジェクトの認証に使用された資格情報の種類、アプリケーション構成など) に依存します。

アプリケーションでは、次のようなさまざまなタスクの要求を使用できます。

* トークンを検証する
* トークン サブジェクトの[テナント](developer-glossary.md#tenant)を識別する
* ユーザー情報を表示する
* サブジェクトの承認を判断する

要求は、次のような情報を提供するキーと値のペアで構成されます。

* トークンを生成したセキュリティ トークン サーバー
* トークンが生成された日付
* サブジェクト (ユーザーなど、デーモンを除く)
* 対象ユーザー。トークンが生成されたアプリです
* トークンを要求したアプリ (クライアント)。 Web アプリの場合、これは対象ユーザーと同じである可能性があります

Microsoft ID プラットフォームがトークンと要求の情報を実装する方法の詳細については、[アクセス トークン](access-tokens.md)と[ID トークン](id-tokens.md)に関する記事を参照してください。

## <a name="how-each-flow-emits-tokens-and-codes"></a>各フローがトークンとコードを生成する方法

クライアントの構築方法に応じて、Microsoft ID プラットフォームでサポートされている認証フローの 1 つ (または複数) を使用できます。 これらのフローでは、さまざまなトークン (ID トークン、リフレッシュトークン、アクセス トークン) と承認コードを生成し、異なるトークンを使用して動作させるようにすることができます。 このグラフで概要を示します。

|Flow | 必要 | ID トークン | アクセス トークン | リフレッシュトークン | 承認コード |
|-----|----------|----------|--------------|---------------|--------------------|
|[承認コード フロー](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[暗黙的なフロー](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[ハイブリッド OIDC フロー](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition)| | x  | |          |            x   |
|[リフレッシュトークンの使用](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | リフレッシュトークン | x | x | x| |
|[On-Behalf-Of フロー](v2-oauth2-on-behalf-of-flow.md) | アクセス トークン| x| x| x| |
|[クライアントの資格情報](v2-oauth2-client-creds-grant-flow.md) | | | x (アプリのみ)| | |

暗黙的モードで発行されたトークンには、URL を介してブラウザーに返されるために長さの制限があります (`response_mode` は `query` または `fragment`)。  一部のブラウザーでは、ブラウザーのバーに入力できる URL のサイズに制限があり、長すぎると失敗します。  したがって、これらのトークンには `groups` または `wids` 要求がありません。

## <a name="next-steps"></a>次のステップ

認証と承認の基本に関するその他のトピックは、次のとおりです。

* Microsoft ID プラットフォームでの認証と承認の基本的な概念については、「[認証と承認](authentication-vs-authorization.md)」を参照してください。
* Microsoft ID プラットフォームと統合できるようにアプリケーションを登録するプロセスについては、[アプリケーション モデル](application-model.md)に関する記事を参照してください。
* Microsoft ID プラットフォームでの Web アプリ、デスクトップ アプリ、モバイル アプリのサインイン フローの詳細については、[アプリのサインイン フロー](app-sign-in-flow.md)に関するページを参照してください。
