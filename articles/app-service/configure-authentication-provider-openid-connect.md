---
title: OpenID Connect プロバイダーを構成する (プレビュー)
description: App Service または Azure Functions アプリの ID プロバイダーとして OpenID Connect プロバイダーを構成する方法について説明します。
ms.topic: article
ms.date: 07/08/2020
ms.reviewer: mahender
ms.openlocfilehash: e8112f2dc20175e81cfa8388440b2d9aef6a419c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90983865"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-login-using-an-openid-connect-provider-preview"></a>OpenID Connect プロバイダーを使用してログインするように App Service または Azure Functions アプリを構成する (プレビュー)

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

この記事では、[OpenID Connect の仕様](https://openid.net/connect/)に準拠したカスタム認証プロバイダーを使用するように Azure App Service または Azure Functions を構成する方法について説明します。 OpenID Connect (OIDC) は、多くの ID プロバイダー (IDP) で使用されている業界標準です。 準拠した IDP を使用するようにアプリを構成するために、仕様の詳細を理解する必要はありません。

1 つ以上の OIDC プロバイダーを使用するようにアプリを構成できます。 構成でそれぞれに一意の名前を付ける必要があり、既定のリダイレクト ターゲットとして機能できるのは 1 つだけです。

> [!CAUTION]
> OpenID Connect プロバイダーを有効にすると、Azure portal、Azure CLI、Azure PowerShell などの一部のクライアントを介したアプリケーションの App Service の認証または承認機能の管理が無効になります。 この機能は、すべての管理エクスペリエンスに関してまだ考慮されていない、プレビュー段階の新しい API サーフェスに依存しています。

## <a name="register-your-application-with-the-identity-provider"></a><a name="register"> </a>アプリケーションを ID プロバイダーに登録する

プロバイダーにより、アプリケーションの詳細を登録することを求められます。 そのプロバイダーに関連する指示を参照してください。 アプリケーションの **クライアント ID** と **クライアント シークレット** を収集する必要があります。

> [!IMPORTANT]
> アプリケーション シークレットは、重要なセキュリティ資格情報です。 このシークレットを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。
>

> [!NOTE]
> プロバイダーによっては、構成と提供する値の使用方法について追加の手順が必要になる場合があります。 たとえば、Apple が提供する秘密キーは、それ自体は OIDC クライアント シークレットとしては使用されませんが、代わりに、それを使用して、アプリ構成で指定したシークレットとして扱われる JWT を作成する必要があります ([Apple でサインインのドキュメント](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)の「Creating the Client Secret」 (クライアント シークレットの作成) セクションを参照)。
>

選択した設定名を使用して、クライアント シークレットをアプリの[アプリケーション設定](./configure-common.md#configure-app-settings)として追加します。 後で使用するためにこの名前をメモしておきます。

さらに、プロバイダーの OpenID Connect メタデータも必要になります。 これは、多くの場合、プロバイダーの発行者の URL の末尾に `/.well-known/openid-configuration` が付いた[構成メタデータ ドキュメント](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig)を介して公開されます。 この構成 URL を収集します。

構成メタデータ ドキュメントを使用できない場合は、次の値を個別に収集する必要があります。

- 発行者の URL (`issuer` として表示されることがあります)
- [OAuth 2.0 認証エンドポイント](https://tools.ietf.org/html/rfc6749#section-3.1) (`authorization_endpoint` として表示されることがあります)
- [OAuth 2.0 トークン エンドポイント](https://tools.ietf.org/html/rfc6749#section-3.2) (`token_endpoint` として表示されることがあります)
- [OAuth 2.0 JSON Web Key Set](https://tools.ietf.org/html/rfc8414#section-2) ドキュメントの URL (`jwks_uri` として表示されることがあります)

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>アプリケーションにプロバイダー情報を追加する

> [!NOTE]
> 必要な構成は新しい API 形式ですが、現時点では [ファイルベースの構成 (プレビュー)](.\app-service-authentication-how-to.md#config-file) でのみサポートされています。 このようなファイルを使用して、以下の手順を行う必要があります。

このセクションでは、新しい IDP を含めるように構成を更新する手順について説明します。 構成の例を次に示します。

1. `identityProviders` オブジェクト内に `openIdConnectProviders` オブジェクトがまだ存在しない場合は追加します。
1. `openIdConnectProviders` オブジェクト内に、新しいプロバイダーのキーを追加します。 これは、構成の残りの部分でプロバイダーを参照するために使用されるフレンドリ名です。 たとえば、このプロバイダーですべての要求を認証する必要がある場合は、`globalValidation.unauthenticatedClientAction` を "RedirectToLoginPage" に設定し、`redirectToProvider` を同じフレンドリ名に設定します。
1. オブジェクトを `registration` オブジェクトが含まれているそのキーに割り当て、オプションで `login` オブジェクトを割り当てます。
    
    ```json
    "myCustomIDP" : {
       "registration" : {},
       "login": {
             "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
             "scope": [],
             "loginParameterNames": [],
       }
    }
    ```

1. 登録オブジェクト内で、`clientId` を収集したクライアント ID に設定し、`clientCredential.secretSettingName` をクライアント シークレットを保存したアプリケーション設定の名前に設定して、`openIdConnectConfiguration` オブジェクトを作成します。

    ```json
    "registration": {
      "clientId": "bd96cf8a-3f2b-4806-b180-d3c5fd11a2be",
      "clientCredential": {
         "secretSettingName": "IDP_CLIENT_SECRET"
      },
      "openIdConnectConfiguration" : {}
    }
    ```

1. `openIdConnectConfiguration` オブジェクト内で、前に収集した OpenID Connect メタデータを指定します。 これには、収集した情報に基づいて、次の 2 つのオプションがあります。

    - `wellKnownOpenIdConfiguration` プロパティを、先ほど収集した構成メタデータ URL に設定します。
    - または、収集された 4 つの個別の値を次のように設定します。
        - `issuer` を発行者の URL に設定する
        - `authorizationEndpoint` を承認エンドポイントに設定する
        - `tokenEndpoint` をトークン エンドポイントに設定する
        - `certificationUri` を JSON Web Key Set ドキュメントの URL に設定する

    これら 2 つのオプションを同時に使用することはできません。

この構成が設定されると、アプリで認証に OpenID Connect プロバイダーを使用できるようになります。

構成例は次のようになります (Apple でサインインを例として使用しています。ここで、APPLE_GENERATED_CLIENT_SECRET 設定は、[Apple のドキュメント](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)に従って生成された JWT を指します)。

```json
{
    "platform": {
        "enabled": true
    },
    "globalValidation": {
        "redirectToProvider": "apple",
        "unauthenticatedClientAction": "RedirectToLoginPage"
    },
    "identityProviders": {
        "openIdConnectProviders": {
            "apple": {
                "registration": {
                    "clientId": "com.contoso.example.client",
                    "clientCredential": {
                        "secretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
                    },
                    "openIdConnectConfiguration": {
                        "wellKnownOpenIdConfiguration": "https://appleid.apple.com/.well-known/openid-configuration"
                    }
                },
                "login": {
                    "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
                    "scope": [],
                    "loginParameterNames": []
                }
            }
        }
    },
    "login": {
        "tokenStore": {
            "enabled": true
        }
    }     
}
```

## <a name="next-steps"></a><a name="related-content"> </a>次の手順

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
