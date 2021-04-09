---
title: Azure API for FHIR に対する Azure Active Directory の ID 構成
description: Azure FHIR サーバーでの ID、認証、および承認の原則について説明します。
services: healthcare-apis
author: caitlinv39
ms.reviewer: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: cavoeg
ms.openlocfilehash: 277838e3ce870b528f986292f88ad2b2b20f42b1
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019120"
---
# <a name="azure-active-directory-identity-configuration-for-azure-api-for-fhir"></a>Azure API for FHIR に対する Azure Active Directory の ID 構成

医療データを扱う際に重要なことは、データがセキュリティで保護されており、承認されていないユーザーやアプリケーションがアクセスできないようにすることです。 FHIR サーバーは [OAuth 2.0](https://oauth.net/2/) を使用して、このデータのセキュリティを確保します。 [Azure API for FHIR](https://azure.microsoft.com/services/azure-api-for-fhir/) は、OAuth 2.0 ID プロバイダーの一例である [Azure Active Directory](../../active-directory/index.yml) を使用して保護されています。 この記事では、FHIR サーバーの承認の概要と、FHIR サーバーにアクセスするためのトークンを取得するために必要な手順について説明します。 これらの手順はすべての FHIR サーバーと ID プロバイダーに適用されますが、この記事では、Azure API for FHIR を FHIR サーバーとして、Azure AD を ID プロバイダーとして説明します。

## <a name="access-control-overview"></a>アクセス制御の概要

クライアント アプリケーションが Azure API for FHIR にアクセスするためには、アクセス トークンを提示する必要があります。 アクセス トークンは、クライアントの ID とロール、およびクライアントに与えられた特権に関する情報を伝達する、署名付きの、[Base64](https://en.wikipedia.org/wiki/Base64) でエンコードされたプロパティ (要求) のコレクションです。

トークンを取得するにはいくつかの方法がありますが、Azure API for FHIR では、正しい要求を含む適切に署名されたトークンである限り、トークンの取得方法は考慮されません。 

[承認コード フロー](../../active-directory/azuread-dev/v1-protocols-oauth-code.md)を例として使用すると、FHIR サーバーへのアクセスでは、次の 4 つの手順が実行されます。

![FHIR の承認](media/azure-ad-hcapi/fhir-authorization.png)

1. クライアントは Azure AD の `/authorize` エンドポイントに要求を送信します。 Azure AD は、クライアントをサインイン ページにリダイレクトします。そこで、ユーザーは適切な資格情報 (ユーザー名とパスワード、または 2 要素認証など) を使用して認証されます。 [承認コードの取得](../../active-directory/azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code)に関する詳細を参照してください。 認証が成功すると、"*承認コード*" がクライアントに返されます。 この承認コードの返却先として Azure AD で許可されるのは、クライアント アプリケーションの登録で構成された登録済みの応答 URL のみになります (下記参照)。
1. クライアント アプリケーションは、Azure AD の `/token` エンドポイントで "*アクセス トークン*" の承認コードを交換します。 クライアント アプリケーションは、トークンを要求するときに、クライアント シークレット (アプリケーション パスワード) を提供する必要がある場合があります。 [アクセス トークンの取得](../../active-directory/azuread-dev/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token)に関する詳細を参照してください。
1. クライアントは、すべての患者を検索する `GET /Patient` など、Azure API for FHIR に対して要求を行います。 要求を行うときには、HTTP 要求ヘッダーにアクセス トークンが含まれます (`Authorization: Bearer eyJ0e...` など)。この場合、`eyJ0e...` は Base64 でエンコードされたアクセス トークンを表します。
1. Azure API for FHIR は、トークンに適切な要求 (トークン内のプロパティ) が含まれていることを検証します。 すべてがチェックされると、要求が完了し、結果と共に FHIR バンドルがクライアントに返されます。

Azure API for FHIR はユーザーの資格情報の検証には関与せず、トークンの発行も行わないことに注意してください。 認証とトークンの作成は Azure AD によって行われます。 Azure API for FHIR は、トークンが正しく署名されている (本物である) こと、および適切な要求が含まれていることを検証するだけです。

## <a name="structure-of-an-access-token"></a>アクセス トークンの構造

FHIR アプリケーションの開発には、多くの場合、アクセスに関する問題のデバッグが伴います。 クライアントが Azure API for FHIR へのアクセスを拒否された場合に、アクセス トークンの構造と、トークンの内容 (要求) を検査するためにそれをデコードする方法について理解しておくと役立ちます。 

FHIR サーバーでは、通常、[JSON Web トークン](https://en.wikipedia.org/wiki/JSON_Web_Token) (JWT、"ジョット" と発音する場合がある) が想定されています。 これは、次の 3 つの部分で構成されています。

1. ヘッダー。次のようになります。
    ```json
    {
      "alg": "HS256",
      "typ": "JWT"
    }
    ```
1. ペイロード (要求)。次に例を示します。
    ```json
    {
     "oid": "123",
     "iss": "https://issuerurl",
     "iat": 1422779638,
     "roles": [
        "admin"
      ]
    }
    ```
1. 署名。これは、Base64 でエンコードされたヘッダーとペイロードのコンテンツを連結し、ヘッダーに指定されたアルゴリズム (`alg`) に基づいてそれらの暗号化ハッシュを計算することによって計算されます。 サーバーは、ID プロバイダーから公開キーを取得し、このトークンが特定の ID プロバイダーによって発行されたものであり、改ざんされていないことを検証できます。

完全なトークンは、これら 3 つのセグメントの Base64 でエンコードされた (実際には Base64 URL でエンコードされた) バージョンで構成されます。 これらの 3 つのセグメントは連結され、`.` (ドット) で区切られます。

トークンの例を次に示します。

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOiIxMjMiLCAiaXNzIjoiaHR0cHM6Ly9pc3N1ZXJ1cmwiLCJpYXQiOjE0MjI3Nzk2MzgsInJvbGVzIjpbImFkbWluIl19.gzSraSYS8EXBxLN_oWnFSRgCzcmJmMjLiuyu5CSpyHI
```

トークンは、[https://jwt.ms](https://jwt.ms) などのツールを使用してデコードおよび検査できます。 トークンをデコードした結果は次のようになります。

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "oid": "123",
  "iss": "https://issuerurl",
  "iat": 1422779638,
  "roles": [
    "admin"
  ]
}.[Signature]
```

## <a name="obtaining-an-access-token"></a>アクセス トークンを取得する

前述のように、Azure AD からトークンを取得するにはいくつかの方法があります。 詳細については、[Azure AD 開発者向けドキュメント](../../active-directory/develop/index.yml)を参照してください。

Azure AD には、`v1.0` および `v2.0` と呼ばれる、2 つの異なるバージョンの OAuth 2.0 エンドポイントがあります。 これらのバージョンはいずれも OAuth 2.0 エンドポイントであり、`v1.0` と `v2.0` の指定は、Azure AD でのその標準の実装方法における違いを示しています。 

FHIR サーバーを使用する場合は、`v1.0` または `v2.0` エンドポイントのいずれかを使用できます。 その選択は、クライアント アプリケーションで使用している認証ライブラリによって異なる場合があります。

Azure AD ドキュメントの該当するセクションは次のとおりです。

* `v1.0` エンドポイント:
    * [承認コード フロー](../../active-directory/azuread-dev/v1-protocols-oauth-code.md)。
    * [クライアントの資格情報フロー](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)。
* `v2.0` エンドポイント:
    * [承認コード フロー](../../active-directory/develop/v2-oauth2-auth-code-flow.md)。
    * [クライアントの資格情報フロー](../../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)。

トークンを取得するためのその他のバリエーション (フローの代わりなど) もあります。 詳細については、Azure AD のドキュメントを参照してください。 Azure API for FHIR を使用している場合、[Azure CLI を使用して](get-healthcare-apis-access-token-cli.md) (デバッグ目的で) アクセス トークンを取得するためのいくつかのショートカットもあります。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Azure AD を使用した Azure API for FHIR へのアクセスのセキュリティ保護に関連する基本的な概念をいくつか学習しました。 Azure API for FHIR のインスタンスをデプロイする方法については、デプロイのクイックスタートに進んでください。

>[!div class="nextstepaction"]
>[Azure API for FHIR をデプロイする](fhir-paas-portal-quickstart.md)