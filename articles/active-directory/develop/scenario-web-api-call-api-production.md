---
title: Web API の呼び出す Web API を運用環境へ移行する - Microsoft ID プラットフォーム | Azure
description: Web API を呼び出す Web API を運用環境に移行する方法を学びます。
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
ms.openlocfilehash: 0d59a5b2a74c10e36103713725113cbe8c9cc412
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965171"
---
# <a name="web-api-that-calls-web-apis---move-to-production"></a>Web API を呼び出す Web API - 運用環境への移行

Web API を呼び出すトークンを取得したら、アプリを運用環境に移行することができます。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>詳細情報

独自の Web API から Web API を呼び出す方法の基本が分かったので、次のチュートリアルに興味があるかもしれません。このチュートリアルでは、Web API を呼び出す保護された Web API を構築するために使用されるコードについて説明しています。

| サンプル | プラットフォーム | 説明 |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET Core 2.2 Web API, Desktop (WPF) | ASP.NET Core 2.2 Web API が Microsoft Graph を呼び出し、それ自体は Microsoft ID プラットフォーム (v2.0) を使用して WPF アプリケーションから呼び出される |
