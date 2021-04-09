---
title: Azure AD B2C でサポートされるアプリケーションの種類
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C で使用できるアプリケーションの種類について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 07897823a3ba3b83e240e8e8dc005ea13b036fce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94952048"
---
# <a name="application-types-that-can-be-used-in-active-directory-b2c"></a>Active Directory B2C で使用できるアプリケーションの種類
 
Azure Active Directory B2C (Azure AD B2C) では、さまざまな最新アプリケーション アーキテクチャの認証がサポートされています。 すべての認証は、業界標準のプロトコルである [OAuth 2.0](protocols-overview.md) または [OpenID Connect](protocols-overview.md) に基づいています。 この記事では、選択する言語またはプラットフォームには関係なく、構築できるアプリケーションの種類について説明します。 また、アプリケーションの構築を始める前にシナリオの概要を理解することもできます。

Azure AD B2C を使用しているすべてのアプリケーションは、[Azure portal](https://portal.azure.com/) を使用して [Azure AD B2C テナント](tutorial-create-tenant.md)に登録する必要があります。 アプリケーション登録プロセスでは、次のような値が収集されて割り当てられます。

* アプリケーションを一意に識別する **アプリケーション ID**。
* 応答をアプリケーションにリダイレクトして戻すために使用できる **応答 URL**。

Azure AD B2C に送信される各要求では、Azure AD B2C の動作を制御する **ユーザー フロー** (組み込みのポリシー) または **カスタム ポリシー** が指定されます。 どちらのポリシーの種類でも、高度にカスタマイズ可能な一連のユーザー エクスペリエンスを作成できます。

すべてのアプリケーションによるやり取りは、次のような大まかなパターンに従って行われます。

1. アプリケーションは、[ポリシー](user-flow-overview.md)を実行するためにユーザーを v2.0 エンドポイントにリダイレクトします。
2. ユーザーがポリシーの定義に従ってポリシーを完了します。
3. アプリケーションが v2.0 エンドポイントからセキュリティ トークンを受け取ります。
4. アプリケーションは、セキュリティ トークンを使って、保護された情報またはリソースにアクセスします。
5. リソース サーバーは、セキュリティ トークンを検証し、アクセスを許可できることを確認します。
6. アプリケーションは、セキュリティ トークンを定期的に更新します。

これらの手順は、構築しているアプリケーションの種類によりわずかに異なることがあります。

## <a name="web-applications"></a>Web アプリケーション

サーバーでホストされ、ブラウザーを通じてアクセスされる Web アプリケーション (.NET、PHP、Java、Ruby、Python、Node.js など) に対して、Azure AD B2C は、すべてのユーザー エクスペリエンスで [OpenID Connect](protocols-overview.md) をサポートします。 Azure AD B2C の OpenID Connect の実装では、Web アプリケーションは、Azure AD に認証要求を発行してユーザー エクスペリエンスを開始します。 要求の結果は `id_token`です。 このセキュリティ トークンは、ユーザーの ID を表します。 また、要求の形式でユーザーに関する情報も提供します。

```json
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

アプリケーションで利用できるトークンと要求の種類の詳細については、[Azure AD B2C トークン リファレンス](tokens-overview.md)のページを参照してください。

Web アプリケーションでは、[ポリシー](user-flow-overview.md)を実行するたびに、次に示す大まかな手順が実行されます。

1. ユーザーが Web アプリケーションに移動する。
2. Web アプリケーションが実行するポリシーを示す Azure AD B2C にユーザーをリダイレクトする。
3. ユーザーがポリシーを完了する。
4. Azure AD B2C が `id_token` をブラウザーに返す。
5. `id_token` がリダイレクト URI にポストされる。
6. `id_token` が検証され、セッション cookie が設定される。
7. セキュリティで保護されたページがユーザーに返される。

ユーザーの ID を確認するには、Azure AD から受け取った公開署名キーを使用して `id_token` を検証するだけで十分です。 このプロセスにより、以降のページ要求でユーザーを識別するために使用できるセッション Cookie も設定されます。

このシナリオを実際に確認するには、[作業開始](overview.md)に関するセクションのいずれかの Web アプリケーション サインイン コード サンプルを試してください。

Web サーバー アプリケーションは、サインインをシンプルにするだけでなく、他のバックエンド Web サービスにアクセスすることが必要な場合もあります。 このような場合、Web アプリケーションでは少し異なる [OpenID Connect フロー](openid-connect.md) を実行し、承認コードと更新トークンを使用してトークンを取得することができます。 このシナリオについては、次の「 [Web API](#web-apis)」セクションで説明します。

## <a name="single-page-applications"></a>シングルページ アプリケーション
多くの最新の Web アプリケーションは、クライアント側のシングル ページ アプリケーション ("SPA") として構築されています。 開発者は、JavaScript または SPA フレームワーク (Angular、Vue、React など) を使用してそれらを作成します。 これらのアプリケーションは Web ブラウザーで実行され、その認証には、従来のサーバー側 Web アプリケーションとは異なる特性があります。

Azure AD B2C により、シングルページ アプリケーションでユーザーをサインインさせ、バックエンド サービスまたは Web API にアクセスするためのトークンを取得する **2 つ** のオプションが提供されます。

### <a name="authorization-code-flow-with-pkce"></a>認可コード フロー (PKCE あり)
- [OAuth 2.0 認証コード フロー (PKCE あり)](./authorization-code-flow.md)。 この認証コード フローでは、認証されたユーザーを表す **ID** トークンと保護されている API を呼び出すために必要な **アクセス** トークンの認証コードを交換することをアプリケーションに許可します。 また、ユーザーが操作しなくてもユーザーの代わりにリソースへの長期間アクセスを提供する **更新** トークンが返されます。 

これが **推奨される** 方法です。 有効期間が制限された更新トークンを使用すると、Safari ITP のような[最新のブラウザーの Cookie プライバシー制限](../active-directory/develop/reference-third-party-cookies-spas.md)にアプリケーションを適合させることができます。

このフローを活用するために、アプリケーションで、これをサポートする認証ライブラリ ([MSAL.js 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) など) を使用できます。

<!-- ![Single-page applications-auth](./media/tutorial-single-page-app/spa-app-auth.svg) -->
![シングルページ アプリケーション認証](./media/tutorial-single-page-app/active-directory-oauth-code-spa.png)

### <a name="implicit-grant-flow"></a>暗黙的な許可のフロー
- [OAuth 2.0 暗黙的フロー](implicit-flow-single-page-application.md)。 [MSAL.js 1.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core) のような一部のフレームワークでは、暗黙的な許可フローのみがサポートされます。 暗黙的な許可フローでは、**ID** と **アクセス** トークンを取得することがアプリケーションに許可されます。 認証コード フロートは異なり、暗黙的な許可フローでは **更新トークン** が返されません。 

この認証フローには、Electron や React-Native などのクロスプラットフォーム JavaScript フレームワークを使用するアプリケーション シナリオは含まれません。 それらのシナリオでは、ネイティブ プラットフォームと対話するための追加の機能が必要になります。

## <a name="web-apis"></a>Web API

Azure AD B2C を使用して、アプリケーションの RESTful Web API などの Web サービスをセキュリティで保護できます。 Web API では、OAuth 2.0 を使用し、トークンによって受信 HTTP 要求を認証することで、データをセキュリティで保護することができます。 Web API の呼び出し元は、HTTP 要求の承認ヘッダーの中にトークンを追加します。

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
``` 

Web API はトークンを使用して API の呼び出し元の ID を検証し、トークン内にエンコードされているクレームから呼び出し元に関する情報を抽出します。 アプリで利用できるトークンと要求の種類の詳細については、 [Azure AD B2C トークン リファレンス](tokens-overview.md)のページを参照してください。

Web API は、Web アプリケーション、デスクトップ アプリケーション、モバイル アプリケーション、シングル ページ アプリケーション、サーバー サイド デーモン、それ以外の Web API など、多くの種類のクライアントからトークンを受信できます。 ここでは、Web API を呼び出す Web アプリケーションの完全なフローの例を示します。

1. Web アプリケーションがポリシーを実行し、ユーザーがユーザー エクスペリエンスを完了する。
2. Azure AD B2C が (OpenID Connect) `id_token` と承認コードをブラウザーに返す。
3. ブラウザーが `id_token` と承認コードをリダイレクト URI にポストする。
4. Web サーバーが `id_token` を検証し、セッション cookie を設定する。
5. Web サーバーが、認可コード、アプリケーション クライアント ID、クライアントの資格情報を提供することで、Azure AD B2C に `access_token` を要求する。
6. `access_token` と `refresh_token` が Web サーバーに返される。
7. Authorization ヘッダーに `access_token` が含まれる Web API が呼び出される。
8. Web API がトークンを検証する。
9. セキュリティで保護されたデータが Web アプリケーションに返される。

承認コード、更新トークン、およびトークンの取得手順については、 [OAuth 2.0 プロトコル](authorization-code-flow.md)に関するページを参照してください。

Azure AD B2C を使用して Web API をセキュリティ保護する方法の詳細については、Web API チュートリアルの「 [作業開始](overview.md)」セクションを参照してください。

## <a name="mobile-and-native-applications"></a>モバイルおよびネイティブ アプリケーション

モバイル アプリケーションやデスクトップ アプリケーションなどのデバイスにインストールされているアプリケーションは、多くの場合、ユーザーの代わりにバックエンド サービスや Web API にアクセスする必要があります。 カスタマイズされた ID 管理エクスペリエンスをネイティブ アプリケーションに追加し、Azure AD B2C と [OAuth 2.0 の承認コード フロー](authorization-code-flow.md)を使用して、バックエンド サービスを安全に呼び出すことができます。

このフローでは、アプリケーションが[ポリシー](user-flow-overview.md)を実行し、ユーザーがポリシーを完了した後、Azure AD から `authorization_code` を受け取ります。 `authorization_code` は、現在サインインしているユーザーに代わってバックエンド サービスを呼び出すためのアプリケーションのアクセス許可を表します。 これにより、アプリケーションはバックグラウンドで `authorization_code` を `access_token` および `refresh_token` と交換できます。  アプリケーションは `access_token` を使用し、HTTP 要求でバックエンド Web API への認証を行うことができます。 また、古い `access_token` の有効期限が切れたときは、`refresh_token` を使用して新しいものを取得できます。

## <a name="current-limitations"></a>現在の制限

### <a name="unsupported-application-types"></a>サポートされていないアプリケーションの種類

#### <a name="daemonsserver-side-applications"></a>デーモン/サーバー側アプリケーション

長時間実行されるプロセスを含んだアプリケーションや、ユーザーの介入なしで動作するアプリケーションも、セキュリティで保護されたリソース (Web API など) にアクセスする必要があります。 これらのアプリケーションは、OAuth 2.0 クライアント資格情報フローを使用することで、(ユーザーの委任 ID ではなく) アプリケーションの ID を使って認証を行い、トークンを取得することができます。 クライアント資格情報フローは、On-Behalf-Of フローと同じではなく、On-Behalf-Of フローは、サーバー対サーバー認証には使用しないようにする必要があります。

OAuth 2.0 クライアント資格情報付与フローは現在 Azure AD B2C 認証サービスによって直接サポートされていませんが、Azure AD B2C テナント内のアプリケーション向けに Azure AD と Microsoft ID プラットフォーム/トークンエンド ポイントを使用して、クライアント資格情報フローを設定できます。 Azure AD B2C テナントは、Azure AD のエンタープライズ テナントと同じいくつかの機能を持っています。

クライアント資格情報フローを設定するには、「[Azure Active Directory v2.0 と OAuth 2.0 クライアント資格情報フロー](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)」を参照してください。 認証に成功した結果として、書式設定されたトークンを受信し、「[Azure AD トークン リファレンス](../active-directory/develop/id-tokens.md)」で説明されているように Azure AD でそれを使用することができます。

管理アプリケーションの登録手順については、[Microsoft Graph を使用した Azure AD B2C の管理](microsoft-graph-get-started.md)に関する記事をご覧ください。

#### <a name="web-api-chains-on-behalf-of-flow"></a>Web API チェーン (On-Behalf-Of フロー)

多くのアーキテクチャには別のダウンストリーム Web API を呼び出す必要がある Web API が含まれ、その場合は両方とも Azure AD B2C によってセキュリティ保護されます。 このシナリオは、バックエンドの Web API から Microsoft オンライン サービス (Microsoft Graph API など) を呼び出すネイティブ クライアントでよく見られます。

このように Web API を連鎖的に呼び出すシナリオは、OAuth 2.0 JWT Bearer Credential Grant (On-Behalf-Of フロー) を使用してサポートできます。  ただし、現時点では、Azure AD B2C に On-Behalf-Of フローは実装されていません。

### <a name="faulted-apps"></a>アプリの障害

Azure AD B2C アプリケーションは、次の方法で編集しないでください。

- 他のアプリケーション管理ポータル ([アプリケーション登録ポータル](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)など)
- Graph API または PowerShell の使用。

Azure portal の外部で Azure AD B2C アプリケーションを編集すると、アプリケーションにエラーが発生し、Azure AD B2C で使用できなくなります。 この場合は、アプリケーションを削除して、もう一度作成してください。

アプリケーションを削除するには、[アプリケーション登録ポータル](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)に移動し、そこでアプリケーションを削除します。 アプリケーションを表示するためには、そのアプリケーションの所有者である必要があります (テナントの管理者であるだけでは不十分です)。

## <a name="next-steps"></a>次のステップ

「[Azure Active Directory B2C のユーザー フロー](user-flow-overview.md)」によって提供される組み込みのポリシーの詳細について学習します。