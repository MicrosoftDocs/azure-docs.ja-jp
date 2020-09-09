---
title: Web API を呼び出す Web アプリを運用環境に移行する - Microsoft ID プラットフォーム
description: Web API を呼び出す Web アプリを運用環境に移行する方法について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6404d00b87b9ee745b9e3a92c646404e574417c1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "80881663"
---
# <a name="a-web-app-that-calls-web-apis-move-to-production"></a>Web API を呼び出す Web アプリ: 運用環境に移行する

Web API を呼び出すトークンの取得方法が分かったところで、次に、運用環境に移行する方法を学びます。

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>次のステップ

ASP.NET Core Web アプリの詳細なプログレッシブ チュートリアルを試して、さらに詳しく学習します。 このチュートリアルでは以下を行います。

- 複数の対象ユーザーや各国のクラウドにユーザーをサインインさせたり、ソーシャル ID を使用してユーザーをサインインさせたりする方法を示します。
- Microsoft Graph を呼び出します。
- 複数の Microsoft API を呼び出します。
- 増分同意を処理します。
- ユーザー自身の Web API を呼び出します。

> [!div class="nextstepaction"]
> [ASP.NET Core Web アプリのチュートリアル](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial#scope-of-this-tutorial)

<!--- Removing this diagram as it's already shown from the next step linked tutorial

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
