---
title: クイックスタート - 電話の呼び出し
titleSuffix: An Azure Communication Services quickstart
description: このクイックスタートでは、Azure Communication Services を使用して、アプリに PSTN 通話機能を追加する方法について説明します。
author: nikuklic
ms.author: nikuklic
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-web-ios-android
ms.openlocfilehash: 65a968747e7ed12e6ee03bd5fea4b7b4af7ee10e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104729517"
---
# <a name="quickstart-call-to-phone"></a>クイックスタート: 電話の呼び出し

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


Communication Services 通話クライアント ライブラリを使用して PSTN 通話をアプリに追加することによって、Azure Communication Services の使用を開始します。

::: zone pivot="platform-web"
[!INCLUDE [Calling with JavaScript](./includes/pstn-call-js.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Calling with Android](./includes/pstn-call-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Calling with iOS](./includes/pstn-call-ios.md)]
::: zone-end

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services サブスクリプションをクリーンアップして解除する場合は、リソースまたはリソース グループを削除できます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 詳細については、[リソースのクリーンアップ](../create-communication-resource.md#clean-up-resources)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。

- [Calling クライアント ライブラリの機能](./calling-client-samples.md)について学習する
- [通話のしくみ](../../concepts/voice-video-calling/about-call-types.md)の詳細について確認する
