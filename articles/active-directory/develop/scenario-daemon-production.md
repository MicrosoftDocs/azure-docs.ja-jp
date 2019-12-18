---
title: Web API を呼び出しているデーモンアプリを運用環境に移動する - Microsoft ID プラットフォーム | Azure
description: Web API を呼び出すデーモン アプリを運用環境へ移行する方法 について学ぶ
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 961928499008445207df3a34a51fc016723c294b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962595"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Web API を呼び出すデーモン アプリ - 運用環境への移行

これでトークンを取得してサービス間の呼び出しに使用する方法がわかったので、運用環境にアプリを移行する方法について説明します。

## <a name="deployment---case-of-multi-tenant-daemon-apps"></a>デプロイ - マルチテナント デーモン アプリの場合

複数のテナントで実行できるデーモン アプリケーションを作成している ISV では、テナントによって確実に以下の管理を行う必要があります。

- アプリケーション用にサービス プリンシパルをプロビジョニングする
- アプリケーションへの同意を許可する

顧客に対しては、これらの操作の実行方法を説明する必要があります。 詳しくは、「[テナント全体の同意を要求する](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)」をご覧ください。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>次の手順

以下に、詳細を学習できるリンクをいくつか示します。

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

- まだ行っていない場合は、クイック スタートの「[トークンを取得し、コンソール アプリからアプリの ID を使用して Microsoft Graph API を呼び出す](./quickstart-v2-netcore-daemon.md)」を試してください。
- 以下に関するリファレンス ドキュメント:
  - [ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) のインスタンス化
  - [AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder) の呼び出し
- 他のサンプル/チュートリアル:
  - [microsoft-identity-platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) では、Microsoft Graph を照会しているテナントのユーザーを表示するシンプルな .NET Core デーモン コンソール アプリケーションを取り上げています。

    ![トポロジ](media/scenario-daemon-app/daemon-app-sample.svg)

    同じサンプルには、証明書を利用したバリエーションも示されています。

    ![トポロジ](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [microsoft-identity-platform-aspnet-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) では、ユーザーの代理ではなく、アプリケーションの ID を使用して Microsoft Graph からのデータを同期する ASP.NET MVC の Web アプリケーションを取り上げています。 サンプルには、管理者の同意プロセスも示されています。

    ![トポロジ](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="pythontabpython"></a>[Python](#tab/python)

クイックスタート「[トークンを取得し、Python コンソール アプリからアプリの ID を使用して Microsoft Graph API を呼び出す](./quickstart-v2-python-daemon.md)」をお試しください。

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java は現在、パブリック プレビュー段階にあります。 詳細については、[MSAL Java dev サンプル](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples)を参照してください。

---
