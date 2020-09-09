---
title: Web API の呼び出す Web API を運用環境へ移行する - Microsoft ID プラットフォーム | Azure
description: Web API を呼び出す Web API を運用環境に移行する方法を学びます。
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
ms.openlocfilehash: aea3a173fd07b66021d35142a84499ae9c66c014
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518200"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Web API を呼び出す Web API:運用環境に移行する

Web API を呼び出すトークンを取得したら、アプリを運用環境に移行することができます。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>詳細情報

独自の Web API から Web API を呼び出す方法の基本を確認したので、次のチュートリアルをご検討ください。このチュートリアルでは、Web API を呼び出す保護された Web API を構築するために使用されるコードについて説明しています。

| サンプル | プラットフォーム | 説明 |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) の第 1 章 | ASP.NET Core Web API, Desktop (WPF) | ASP.NET Core Web API が Microsoft Graph を呼び出します。これは、Microsoft ID プラットフォーム (v2.0) を使用して WPF アプリケーションから呼び出します。 |
