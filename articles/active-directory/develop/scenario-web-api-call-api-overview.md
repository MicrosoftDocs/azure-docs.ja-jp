---
title: Web API を呼び出す Web API を構築する | Azure
titleSuffix: Microsoft identity platform
description: ダウンストリーム Web API を呼び出す Web API を構築する方法 (概要) について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: a66f0a2de1d8239baffbe53dfb5d6f2dd275d448
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756347"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>シナリオ:Web API を呼び出す Web API

Web API を呼び出す Web API を構築するために知っておくべきことについて説明します。

## <a name="prerequisites"></a>前提条件

保護された Web API が他の Web API を呼び出すこのシナリオは、「[シナリオ: 保護された Web API](scenario-protected-web-api-overview.md)」の上に構築されます。

## <a name="overview"></a>概要

- Web、デスクトップ、モバイル、またはシングルページ アプリケーション クライアント (付属図に示されていない) は、保護された Web API を呼び出して、"Authorization" HTTP ヘッダーに JSON Web トークン (JWT) ベアラー トークンを提供します。
- 保護された Web API はトークンを検証し、Microsoft Authentication Library (MSAL) の `AcquireTokenOnBehalfOf` メソッドを使用して Azure Active Directory (Azure AD) から別のトークンを要求することで、保護された Web API がユーザーに代わって 2 つ目の Web API (ダウンストリーム Web API) を呼び出すことができるようにします。
- また、保護された Web API は、後で `AcquireTokenSilent` を呼び出して、同じユーザーに代わって他のダウンストリーム API のトークンを要求することもできます。 必要な場合は `AcquireTokenSilent` でトークンを更新します。

![Web API を呼び出す Web API の図](media/scenarios/web-api.svg)

## <a name="specifics"></a>詳細

API のアクセス許可に関連するアプリの登録部分は、従来と変わりません。 このアプリ構成には、OAuth 2.0 On-Behalf-Of フローを使用して、JWT ベアラー トークンをダウンストリーム API 用のトークンと交換することが含まれます。 このトークンはトークン キャッシュに追加され、Web API のコントローラーで使用できるようになります。また、ダウンストリーム API を呼び出すために、トークンをサイレントに取得できます。

## <a name="next-steps"></a>次のステップ

このシナリオの次の記事「[アプリの登録](scenario-web-api-call-api-app-registration.md)」に進みます。
