---
title: Web API を呼び出す Web API を登録する | Azure
titleSuffix: Microsoft identity platform
description: ダウンストリーム Web API を呼び出す Web API を構築する方法について説明します (アプリの登録)
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
ms.openlocfilehash: 8349b7a53eb7b03b27c695bb24c8cb9bc665a1ac
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756364"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Web API を呼び出す Web API: アプリの登録

ダウンストリーム Web API を呼び出す Web API は、保護された Web API と同じ登録を持ちます。 そのため、「[保護された Web API: アプリの登録](scenario-protected-web-api-app-registration.md)」の手順に従う必要があります。

Web アプリは現在 Web API を呼び出すため、機密クライアント アプリケーションになります。 そのため、追加の登録情報が必要になります。このアプリでは、Microsoft ID プラットフォームとシークレット (クライアントの資格情報) を共有する必要があります。

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API のアクセス許可

Web アプリは、ベアラー トークンを受信したユーザーの代わりに API を呼び出します。 Web アプリは、委任されたアクセス許可を要求する必要があります。 詳細については、「[Web API にアクセスするためのアクセス許可を追加する](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)」を参照してください。

## <a name="next-steps"></a>次のステップ

このシナリオの次の記事である[アプリ コードの構成](scenario-web-api-call-api-app-configuration.md)に関する記事に進みます。
