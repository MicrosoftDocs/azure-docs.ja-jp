---
title: Web API を呼び出すデーモン アプリを運用環境へ移行する | Azure
titleSuffix: Microsoft identity platform
description: Web API を呼び出すデーモン アプリを運用環境へ移行する方法 について学ぶ
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
ms.openlocfilehash: d903f04055d1607ee782bd502d99a8fd9cde87ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578449"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Web API を呼び出すデーモン アプリ - 運用環境への移行

これでトークンを取得してサービス間の呼び出しに使用する方法がわかったので、運用環境にアプリを移行する方法について説明します。

## <a name="deployment---multitenant-daemon-apps"></a>デプロイ - マルチテナント デーモン アプリ

複数のテナントで実行できるデーモン アプリケーションを作成している ISV では、テナントによって確実に以下の管理を行ってください。

- アプリケーション用にサービス プリンシパルをプロビジョニングする。
- アプリケーションへの同意を許可する。

顧客に対しては、これらの操作の実行方法を説明する必要があります。 詳しくは、「[テナント全体の同意を要求する](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)」をご覧ください。

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="code-samples"></a>コード サンプル

# <a name="net"></a>[.NET](#tab/dotnet)

- 以下に関するリファレンス ドキュメント:
  - [ConfidentialClientApplication](/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) のインスタンス化
  - [AcquireTokenForClient](/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder) の呼び出し
- 他のサンプル/チュートリアル:
  - [microsoft-identity-platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) では、Microsoft Graph へのクエリを実行するテナントのユーザーを表示する小さい .NET Core デーモン コンソール アプリケーションを取り上げています。

    ![サンプル デーモン アプリのトポロジ](media/scenario-daemon-app/daemon-app-sample.svg)

    同じサンプルには、証明書を利用したバリエーションも示されています。

    ![サンプル デーモン アプリのトポロジ - 証明書](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [microsoft-identity-platform-aspnet-Webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) では、ユーザーの代理ではなく、アプリケーションの ID を使用して Microsoft Graph からのデータを同期する ASP.NET MVC の Web アプリケーションを取り上げています。 このサンプルには、管理者の同意プロセスも示されています。

    ![トポロジ](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="java"></a>[Java](#tab/java)

クイックスタート「[トークンを取得し、Java コンソール アプリからアプリの ID を使用して Microsoft Graph API を呼び出す](quickstart-v2-java-daemon.md)」をお試しください。

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

- 詳細については、次を参照してください。
  - [構成](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md)について
  - [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md) のインスタンス化
  - [FAQ](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/faq.md)
- 他のサンプル/チュートリアル:
  - [MSAL ノード コンソール デーモンのサンプル](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console)

# <a name="python"></a>[Python](#tab/python)

クイックスタート「[トークンを取得し、Python コンソール アプリからアプリの ID を使用して Microsoft Graph API を呼び出す](quickstart-v2-python-daemon.md)」をお試しください。

---

## <a name="next-steps"></a>次のステップ

詳細については、次のリンクを参照してください。

# <a name="net"></a>[.NET](#tab/dotnet)

クイックスタート「[トークンを取得し、.NET Core コンソール アプリからアプリの ID を使用して Microsoft Graph API を呼び出す](quickstart-v2-netcore-daemon.md)」をお試しください。

# <a name="java"></a>[Java](#tab/java)

クイックスタート「[トークンを取得し、Java コンソール アプリからアプリの ID を使用して Microsoft Graph API を呼び出す](quickstart-v2-java-daemon.md)」をお試しください。

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

クイックスタート「[トークンを取得し、Node.js コンソール アプリからアプリの ID を使用して Microsoft Graph API を呼び出す](quickstart-v2-nodejs-console.md)」をお試しください。

# <a name="python"></a>[Python](#tab/python)

クイックスタート「[トークンを取得し、Python コンソール アプリからアプリの ID を使用して Microsoft Graph API を呼び出す](quickstart-v2-python-daemon.md)」をお試しください。

---
