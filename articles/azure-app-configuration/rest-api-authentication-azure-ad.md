---
title: Azure Active Directory REST API - 認証
description: REST API を使用して、Azure App Configuration に Azure Active Directory を使用して認証する
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: cbf05245768a663e324e9bb6e1ad422eeee3ab1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96930519"
---
# <a name="azure-active-directory-authentication"></a>Azure Active Directory 認証

HTTP 要求は、`Bearer` 認証スキームで、Azure Active Directory (Azure AD) から取得したトークンを使用して認証できます。 これらの要求は、トランスポート層セキュリティ (TLS) 経由で送信する必要があります。

## <a name="prerequisites"></a>前提条件

Azure AD トークンの要求に使用されるプリンシパルを、該当する [Azure App Configuration ロール](./rest-api-authorization-azure-ad.md)の 1 つに割り当てる必要があります。

要求ごとに、認証に必要なすべての HTTP ヘッダーを指定します。 最小要件は次のとおりです。

|  要求ヘッダー | 説明  |
| --------------- | ------------ |
| `Authorization` | `Bearer` スキームに必要な認証情報。 |

**例:**

```http
Host: {myconfig}.azconfig.io
Authorization: Bearer {{AadToken}}
```

## <a name="azure-ad-token-acquisition"></a>Azure AD トークンの取得

Azure AD トークンを取得する前に、どのユーザーとして認証するのか、トークン要求する対象ユーザー、どの Azure AD エンドポイント (機関) を使用するのかを指定する必要があります。

### <a name="audience"></a>対象ユーザー

適切な対象ユーザーを指定して Azure AD トークンを要求します。 Azure App Configuration には、次のいずれかの対象ユーザーを使用します。 対象ユーザーは、トークンの要求対象になっている "*リソース*" と呼ばれることもあります。

- {configurationStoreName}.azconfig.io
- *.azconfig.io

> [!IMPORTANT]
> 要求された対象ユーザーが `{configurationStoreName}.azconfig.io` の場合、要求の送信に使用される `Host` 要求ヘッダーと完全に一致 (大文字と小文字が区別されます) している必要があります。

### <a name="azure-ad-authority"></a>Azure AD 機関

Azure AD 機関は、Azure AD トークンを取得するために使用するエンドポイントです。 これは `https://login.microsoftonline.com/{tenantId}` の形式です。 `{tenantId}` セグメントは、認証しようとしているユーザーまたはアプリケーションが属している Azure AD テナント ID を指します。

### <a name="authentication-libraries"></a>認証ライブラリ

Azure AD トークンを取得するプロセスを簡素化するため、Azure には、Azure Active Directory 認証ライブラリと呼ばれるライブラリのセットが用意されています。 これらの Azure ライブラリは、複数言語用に構築されています。 詳細については、[ドキュメント](../active-directory/azuread-dev/active-directory-authentication-libraries.md)を参照してください。

## <a name="errors"></a>エラー

次のエラーが発生することがあります。

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**理由:** `Bearer` スキームで承認要求ヘッダーが指定されていません。

**解決方法:** 有効な `Authorization` HTTP 要求ヘッダーを指定してください。

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="Authorization token failed validation"
```

**理由:** Azure AD トークンが有効ではありません。

**解決方法:** Azure AD 機関から Azure AD トークンを取得し、適切な対象ユーザーを確実に使用してください。

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="The access token is from the wrong issuer. It must match the AD tenant associated with the subscription to which the configuration store belongs. If you just transferred your subscription and see this error message, please try back later."
```

**理由:** Azure AD トークンが有効ではありません。

**解決方法:** Azure AD 機関から Azure AD トークンを取得します。 Azure AD テナントが、構成ストアが属しているサブスクリプションに確実に関連付けられているようします。 このエラーは、プリンシパルが複数の Azure AD テナントに属している場合に表示されることがあります。