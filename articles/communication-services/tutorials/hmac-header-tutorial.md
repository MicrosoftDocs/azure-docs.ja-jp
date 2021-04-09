---
title: C# を使用して HTTP 要求に署名する方法について学ぶ
titleSuffix: An Azure Communication Services tutorial
description: C# で Azure Communication Services の HTTP 要求に署名する方法について学習します。
author: alexandra142
manager: soricos
services: azure-communication-services
ms.author: apistrak
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e9b24e981e472371e477cd4dd895e976f709b2fb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103490511"
---
# <a name="sign-an-http-request"></a>HTTP 要求に署名する

このチュートリアルでは、HMAC 署名を使用して HTTP 要求に署名する方法について学習します。

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


[!INCLUDE [Sign an HTTP request C#](./includes/hmac-header-csharp.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services サブスクリプションをクリーンアップして削除するには、リソースまたはリソース グループを削除します。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 [Azure Communication Services のリソースのクリーンアップ](../quickstarts/create-communication-resource.md#clean-up-resources)と [Azure Functions のリソースのクリーンアップ](../../azure-functions/create-first-function-vs-code-csharp.md#clean-up-resources)に関する詳細を確認できます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [音声通話をアプリに追加する](../quickstarts/voice-video-calling/getting-started-with-calling.md)

次の記事もご覧ください。

- [チャットをアプリに追加する](../quickstarts/chat/get-started.md)
- [ユーザー アクセス トークンを作成する](../quickstarts/access-tokens.md)
- [クライアントとサーバーのアーキテクチャについて学習する](../concepts/client-and-server-architecture.md)
- [認証について学習する](../concepts/authentication.md)
