---
title: Python ADAL から MSAL への移行ガイド | Azure
titleSuffix: Microsoft identity platform
description: Azure Active Directory 認証ライブラリ (ADAL) Python アプリを、Python 用の Microsoft 認証ライブラリ (MSAL) に移行する方法について説明します。
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: marsma, rayluo, nacanuma
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 60d6e40b568c1189cdc01ef4239612d3717063b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578840"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>Python 用の ADAL から MSAL への移行に関するガイド

この記事では、Microsoft Authentication Library (MSAL) を使用するために、Azure Active Directory Authentication Library (ADAL) を使っているアプリを移行するのに必要な変更について重点的に説明します。

## <a name="difference-highlights"></a>主な相違点

ADAL は、Azure Active Directory (Azure AD) v1.0 エンドポイントで動作します。 Microsoft Authentication Library (MSAL) は、以前は Azure Active Directory v2.0 エンドポイントと呼ばれていた、Microsoft ID プラットフォームと連動します。 Microsoft ID プラットフォームは、以下の点で Azure AD v1.0 とは異なります。

サポートするものは次のとおりです。
  - 職場または学校のアカウント (Azure AD によりプロビジョニングされるアカウント)
  - 個人アカウント (Outlook.com や Hotmail.com など)
  - Azure AD B2C サービスから自分のメールやソーシャル ID (LinkedIn、Facebook、Google など) を持ち込むカスタマー

- 標準は以下のものと互換性があります。
  - OAuth v2.0
  - OpenID Connect (OIDC)

詳細については、[Microsoft ID プラットフォームの違い](../azuread-dev/azure-ad-endpoint-comparison.md)に関する記事を参照してください。

### <a name="scopes-not-resources"></a>リソースではなくスコープ

ADAL Python ではリソースのトークンが取得されますが、MSAL Python ではスコープのトークンが取得されます。 MSAL Python の API サーフェスには、リソース パラメーターがなくなりました。 要求される必要なアクセス許可とリソースを宣言する文字列のリストとして、スコープを指定する必要があります。 スコープの例については、[Microsoft Graph のスコープ](/graph/permissions-reference)に関するページを参照してください。

`/.default` スコープ サフィックスをリソースに追加すると、アプリを v1.0 エンドポイント (ADAL) から Microsoft ID プラットフォーム (MSAL) に移行するのに役立ちます。 たとえば、リソース値が `https://graph.microsoft.com`の場合、相当するスコープ値は `https://graph.microsoft.com/.default`になります。  リソースが URL 形式ではなく、リソース ID の形式が `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX`である場合でも、スコープ値として `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX/.default` を使用できます。

さまざまな種類のスコープの詳細については、[Microsoft ID プラットフォームでのアクセス許可と同意](./v2-permissions-and-consent.md)に関する記事および「[v1.0 トークンを受け入れる Web API のスコープ](./msal-v1-app-scopes.md)」の記事を参照してください。

### <a name="error-handling"></a>エラー処理

Python 用 Azure Active Directory 認証ライブラリ (ADAL) では、例外 `AdalError` を使用して問題が発生したことを示します。 Python 用 MSAL では通常、代わりにエラー コードを使用します。 詳細については、[Python 用 MSAL のエラー処理](msal-error-handling-python.md)に関するページを参照してください。

### <a name="api-changes"></a>API の変更

次の表は、Python 用 ADAL の API と、Python 用 MSAL でその代わりに使用する API の一覧です。

| Python 用 ADAL の API  | Python 用 MSAL の API |
| ------------------- | ---------------------------------- |
| [AuthenticationContext](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication or ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| 該当なし  | [get_authorization_request_url()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | 該当なし |
| [acquire_user_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) および [cancel_request_to_get_token_with_device_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) および [acquire_token_with_client_certificate()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| 該当なし | [acquire_token_on_behalf_of()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenCache()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| 該当なし | 永続性を備えたキャッシュ、[MSAL 拡張機能](https://github.com/marstr/original-microsoft-authentication-extensions-for-python)で提供 |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>MSAL Python の既存の更新トークンを移行する

Microsoft Authentication Library (MSAL) では、更新トークンの概念が抽象化されています。 MSAL Python では、更新トークンを保存、検索、または更新する必要がないように、既定でメモリ内トークン キャッシュが提供されます。 更新トークンは通常、ユーザーの介入なしに更新できるため、ユーザーにサインイン プロンプトが表示される回数も少なくなります。 トークン キャッシュの詳細については、[Python 用 MSAL でのカスタム トークン キャッシュのシリアル化](msal-python-token-cache-serialization.md)に関するページを参照してください。

次のコードは、別の OAuth2 ライブラリ (ADAL Python を含むがこれに限らない) によって管理されている更新トークンを、Python 用 MSAL による管理に移行するために役立ちます。 これらの更新トークンを移行する理由の 1 つは、アプリを Python 用 MSAL に移行するときに既存のユーザーがサインインし直す必要をなくすことです。

更新トークンを移行するには、Python 用 MSAL と以前の更新トークンを使用して新しいアクセス トークンを取得します。 新しい更新トークンが返されると、Python 用 MSAL によってキャッシュに保存されます。
MSAL Python 1.3.0 以降では、この目的のための API が MSAL 内に用意されています。
次のコード スニペットを参照してください。[MSAL Python を使用して更新トークンを移行する完全なサンプル](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.3.0/sample/migrate_rt.py#L28-L67)からの抜粋です。

```python
import msal
def get_preexisting_rt_and_their_scopes_from_elsewhere():
    # Maybe you have an ADAL-powered app like this
    #   https://github.com/AzureAD/azure-activedirectory-library-for-python/blob/1.2.3/sample/device_code_sample.py#L72
    # which uses a resource rather than a scope,
    # you need to convert your v1 resource into v2 scopes
    # See https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources
    # You may be able to append "/.default" to your v1 resource to form a scope
    # See https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope

    # Or maybe you have an app already talking to the Microsoft identity platform,
    # powered by some 3rd-party auth library, and persist its tokens somehow.

    # Either way, you need to extract RTs from there, and return them like this.
    return [
        ("old_rt_1", ["scope1", "scope2"]),
        ("old_rt_2", ["scope3", "scope4"]),
        ]


# We will migrate all the old RTs into a new app powered by MSAL
app = msal.PublicClientApplication(
    "client_id", authority="...",
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache
    )

# We choose a migration strategy of migrating all RTs in one loop
for old_rt, scopes in get_preexisting_rt_and_their_scopes_from_elsewhere():
    result = app.acquire_token_by_refresh_token(old_rt, scopes)
    if "error" in result:
        print("Discarding unsuccessful RT. Error: ", json.dumps(result, indent=2))

print("Migration completed")
```


## <a name="next-steps"></a>次のステップ

詳細については、[v1.0 と v2.0 の比較](../azuread-dev/azure-ad-endpoint-comparison.md)に関するページを参照してください。
