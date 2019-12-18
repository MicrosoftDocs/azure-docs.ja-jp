---
title: Web API を呼び出す Web アプリを運用環境に移動する - Microsoft ID プラットフォーム | Azure
description: Web API を呼び出す Web アプリを運用環境に移行する方法を学びます。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e40920b7045dad6f64b5699bc3c4c560eb399f2a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964958"
---
# <a name="web-app-that-calls-web-apis---move-to-production"></a>Web API を呼び出す Web アプリ - 運用環境への移行

Web API を呼び出すトークンの取得方法が分かったところで、次に運用環境に移行する方法について説明します。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>次の手順

ASP.NET Core Web アプリの詳細なプログレッシブ チュートリアルをお試しになり、詳細をご確認ください。以下の内容が記載されています。

- 複数の対象ユーザー、各国のクラウドまたはソーシャル ID を持つユーザーをサインインさせる方法
- Microsoft Graph の呼び出し
- 複数の Microsoft API の呼び出し
- 増分同意の取り扱い
- お客様独自の Web API の呼び出し

> [!div class="nextstepaction"]
> [ASP.NET Core Web アプリのチュートリアル](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial#scope-of-this-tutorial)

<!--- Removing this diagram as it's already shown from the next step linked tutorial

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
