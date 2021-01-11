---
title: デーモン アプリから Web API を呼び出す - Microsoft ID プラットフォーム | Azure
description: Web API を呼び出すデーモン アプリをビルドする方法について学習します
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7319aacabd2c479c578fbb71ae0bc9fbb2dee9f2
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88119132"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Web API を呼び出すデーモン アプリ - アプリからの Web API 呼び出し

.NET デーモン アプリでは、Web API を呼び出すことができます。 .NET デーモン アプリでは、事前承認されたいくつかの Web API を呼び出すこともできます。

## <a name="calling-a-web-api-from-a-daemon-application"></a>デーモン アプリケーションからの Web API の呼び出し

ここでは、トークンを使用して API を呼び出す方法を示します。

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="python"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="java"></a>[Java](#tab/java)

```Java
HttpURLConnection conn = (HttpURLConnection) url.openConnection();

// Set the appropriate header fields in the request header.
conn.setRequestProperty("Authorization", "Bearer " + accessToken);
conn.setRequestProperty("Accept", "application/json");

String response = HttpClientHelper.getResponseStringFromConn(conn);

int responseCode = conn.getResponseCode();
if(responseCode != HttpURLConnection.HTTP_OK) {
    throw new IOException(response);
}

JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
```

---

## <a name="calling-several-apis"></a>複数の API の呼び出し

デーモン アプリの場合、呼び出す Web API は事前に承認されている必要があります。 デーモン アプリに対する増分同意はありません。 (ユーザーによる操作はありません)。テナント管理者は、アプリケーションとすべての API アクセス許可に事前に同意する必要があります。 複数の API を呼び出す場合、`AcquireTokenForClient` を呼び出すたびに各リソースのトークンを取得する必要があります。 MSAL は、不要なサービスの呼び出しを回避するために、アプリケーションのトークン キャッシュを使用します。

## <a name="next-steps"></a>次のステップ

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [デーモン アプリ - 運用環境への移行](./scenario-daemon-production.md?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [デーモン アプリ - 運用環境への移行](./scenario-daemon-production.md?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [デーモン アプリ - 運用環境への移行](./scenario-daemon-production.md?tabs=java)

---