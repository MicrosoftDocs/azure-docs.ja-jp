---
title: クイック スタート - Azure Communication Services での Teams の相互運用
titleSuffix: An Azure Communication Services quickstart
description: このクイックスタートでは、Azure Communication Calling SDK を使用して、Teams の会議に参加する方法について説明します。
author: chpalm
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-web-ios-android
ms.openlocfilehash: a9ef74c04c1f709348ae1d6dd97558ee6bedccf3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104654970"
---
# <a name="quickstart-join-your-calling-app-to-a-teams-meeting"></a>クイックスタート: 通話アプリを Teams の会議に参加させる

> [!IMPORTANT]
> [チームのテナント相互運用性](../../concepts/teams-interop.md)を有効または無効にするには、[このフォーム](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u)を完成させてください。

Azure Communication Services の入門として、JavaScript クライアント ライブラリを使用して通話ソリューションを Microsoft Teams に接続してみましょう。

::: zone pivot="platform-web"
[!INCLUDE [Calling with JavaScript](./includes/teams-interop-javascript.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Calling with Android](./includes/teams-interop-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Calling with iOS](./includes/teams-interop-ios.md)]
::: zone-end

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services サブスクリプションをクリーンアップして解除する場合は、リソースまたはリソース グループを削除できます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 詳細については、[リソースのクリーンアップ](../create-communication-resource.md#clean-up-resources)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。

- [通話のヒーロー サンプル](../../samples/calling-hero-sample.md)を確認する
- [通話クライアント ライブラリ機能](./calling-client-samples.md)について学習する
- [通話のしくみ](../../concepts/voice-video-calling/about-call-types.md)の詳細について確認する
