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
ms.date: 03/03/2021
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 376c61f6a5ba94492cac26950465c61e3d8fe4ed
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102038562"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>シナリオ:Web API を呼び出す Web API

Web API を呼び出す Web API を構築するために知っておくべきことについて説明します。

## <a name="prerequisites"></a>前提条件

保護された Web API が他の Web API を呼び出すこのシナリオは、「[シナリオ: 保護された Web API](scenario-protected-web-api-overview.md)」の上に構築されます。

## <a name="overview"></a>概要

- Web、デスクトップ、モバイル、またはシングルページ アプリケーション クライアント (付属図に示されていない) は、保護された Web API を呼び出して、"Authorization" HTTP ヘッダーに JSON Web トークン (JWT) ベアラー トークンを提供します。
- 保護された Web API はトークンを検証し、Microsoft Authentication Library (MSAL) の `AcquireTokenOnBehalfOf` メソッドを使用して Azure Active Directory (Azure AD) から別のトークンを要求することで、保護された Web API がユーザーに代わって 2 つ目の Web API (ダウンストリーム Web API) を呼び出すことができるようにします。 必要な場合は `AcquireTokenOnBehalfOf` でトークンを更新します。
![Web API を呼び出す Web API の図](media/scenarios/web-api.svg)

## <a name="specifics"></a>詳細

API のアクセス許可に関連するアプリの登録部分は、従来と変わりません。 このアプリ構成には、OAuth 2.0 On-Behalf-Of フローを使用して、JWT ベアラー トークンをダウンストリーム API 用のトークンと交換することが含まれます。 このトークンはトークン キャッシュに追加され、Web API のコントローラーで使用できるようになります。また、ダウンストリーム API を呼び出すために、トークンをサイレントに取得できます。

## <a name="next-steps"></a>次のステップ

このシナリオの次の記事「[アプリの登録](scenario-web-api-call-api-app-registration.md)」に進みます。
