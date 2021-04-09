---
title: Azure API for FHIR アクセス トークンの検証
description: トークンの検証について説明し、アクセスの問題をトラブルシューティングするためのヒントを示します
services: healthcare-apis
author: caitlinv39
ms.reviewer: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: cavoeg
ms.openlocfilehash: 426ec0c2d6cc274aa0b6829eb4a30fd29b9ba8e2
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019130"
---
# <a name="azure-api-for-fhir-access-token-validation"></a>Azure API for FHIR アクセス トークンの検証

Azure API for FHIR でのアクセス トークンの検証方法は、実装と構成によって異なります。 この記事では、アクセスの問題のトラブルシューティングを行う際に役立つ検証手順について説明します。

## <a name="validate-token-has-no-issues-with-identity-provider"></a>検証トークンには ID プロバイダーに関する問題はない

トークン検証の最初の手順では、トークンが正しい ID プロバイダーによって発行されたこと、およびトークンが変更されていないことを確認します。 FHIR サーバーは、`Authority` と呼ばれる特定の ID プロバイダーを使用するように構成されます。 FHIR サーバーでは、`/.well-known/openid-configuration` エンドポイントから ID プロバイダーに関する情報が取得されます。 Azure AD を使用する場合、完全な URL は次のようになります。

```
GET https://login.microsoftonline.com/<TENANT-ID>/.well-known/openid-configuration
```

ここで `<TENANT-ID>` は特定の Azure AD テナント (テナント ID またはドメイン名) です。

Azure AD によって、次のようなドキュメントが FHIR サーバーに返されます。

```json
{
    "authorization_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/token",
    "token_endpoint_auth_methods_supported": [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys",
    "response_modes_supported": [
        "query",
        "fragment",
        "form_post"
    ],
    "subject_types_supported": [
        "pairwise"
    ],
    "id_token_signing_alg_values_supported": [
        "RS256"
    ],
    "http_logout_supported": true,
    "frontchannel_logout_supported": true,
    "end_session_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/logout",
    "response_types_supported": [
        "code",
        "id_token",
        "code id_token",
        "token id_token",
        "token"
    ],
    "scopes_supported": [
        "openid"
    ],
    "issuer": "https://sts.windows.net/<TENANT-ID>/",
    "claims_supported": [
        "sub",
        "iss",
        "cloud_instance_name",
        "cloud_instance_host_name",
        "cloud_graph_host_name",
        "msgraph_host",
        "aud",
        "exp",
        "iat",
        "auth_time",
        "acr",
        "amr",
        "nonce",
        "email",
        "given_name",
        "family_name",
        "nickname"
    ],
    "microsoft_multi_refresh_token": true,
    "check_session_iframe": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/checksession",
    "userinfo_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/openid/userinfo",
    "tenant_region_scope": "WW",
    "cloud_instance_name": "microsoftonline.com",
    "cloud_graph_host_name": "graph.windows.net",
    "msgraph_host": "graph.microsoft.com",
    "rbac_url": "https://pas.windows.net"
}
``` 
FHIR サーバーの重要なプロパティである、`jwks_uri` によってトークン署名を検証するために必要な暗号化キーをフェッチする場所がサーバーに伝えられ、`issuer` によってこのサーバーによって発行されたトークンの発行者要求 (`iss`) に含まれる内容がサーバーに伝えられます。 FHIR サーバーによってこれが使用され、トークンが本物であることが検証されます。

## <a name="validate-claims-of-the-token"></a>トークンの要求を検証する

FHIR サーバーによってトークンの信頼性が検証されると、次にクライアントがトークンにアクセスするために必要な要求があるかどうかが検証されます。

Azure API for FHIR を使用すると、サーバーによって次のことが検証されます。

1. トークンに適切な `Audience` (`aud` 要求) があること。
1. トークンが発行されたユーザーまたはプリンシパルが、FHIR サーバーのデータ プレーンにアクセスできること。 トークンの `oid` 要求には、ユーザーまたはプリンシパルを一意に識別する ID オブジェクト ID が含まれています。

データ プレーンのロールの割り当てを管理するために、FHIR サービスで [Azure RBAC を使用するように構成](configure-azure-rbac.md)することをお勧めします。 ただし、FHIR サービスで外部またはセカンダリの Azure Active Directory テナントを使用している場合は、[ローカル RBAC を構成する](configure-local-rbac.md)こともできます。 

OSS Microsoft FHIR server for Azure を使用している場合、サーバーで次のことが検証されます。

1. トークンに適切な `Audience` (`aud` 要求) があること。
1. このトークンに、FHIR サーバーへのアクセスが許可されている `roles` 要求内にロールがあること。

FHIR サーバーでロールを定義する方法の詳細については、[こちら](https://github.com/microsoft/fhir-server/blob/master/docs/Roles.md)を参照してください。

また、FHIR サーバーでは、アクセス トークンに、クライアントがアクセスしようとしている FHIR API の一部にアクセスするためのスコープ (トークン要求 `scp` 内) があることも検証されます。 現時点では、Azure API for FHIR および FHIR server for Azure では、トークン スコープは検証されません。

## <a name="next-steps"></a>次のステップ
これで、トークン検証の手順を理解したので、チュートリアルを完了して JavaScript アプリケーションを作成し、FHIR データを読み取ることができます。

>[!div class="nextstepaction"]
>[Web アプリケーションのチュートリアル](tutorial-web-app-fhir-server.md)