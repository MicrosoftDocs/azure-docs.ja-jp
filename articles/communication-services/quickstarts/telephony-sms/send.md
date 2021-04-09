---
title: クイックスタート - SMS メッセージを送信する
titleSuffix: An Azure Communication Services quickstart
description: Azure Communication Services を使用して SMS メッセージを送信する方法について説明します。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.custom: tracking-python, devx-track-js
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: ed246cfc3929e2893087a99b7876138859d4667a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103488325"
---
# <a name="quickstart-send-an-sms-message"></a>クイック スタート:SMS メッセージを送信する

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

> [!IMPORTANT]
> 米国の電話番号との間で携帯ショートメール (SMS) メッセージを送受信することができます。 他の地域の電話番号はまだ、Communication Services の SMS ではサポートされていません。
> 詳細については、 **[電話番号の種類](../../concepts/telephony-sms/plan-solution.md)** に関すページを参照してください。

::: zone pivot="programming-language-csharp"
[!INCLUDE [Send SMS with .NET client library](./includes/send-sms-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Send SMS with JavaScript client library](./includes/send-sms-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Send SMS with Python client library](./includes/send-sms-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Send SMS with Java client library](./includes/send-sms-java.md)]
::: zone-end

## <a name="troubleshooting"></a>トラブルシューティング

SMS 配信に関連する問題のトラブルシューティングを行うために、[Event Grid で配信レポートを有効にして](./handle-sms-events.md)配信の詳細を取得できます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services サブスクリプションをクリーンアップして解除する場合は、リソースまたはリソース グループを削除できます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 詳細については、[リソースのクリーンアップ](../create-communication-resource.md#clean-up-resources)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

このクイックスタートでは、Azure Communication Services を使用して SMS メッセージを送信する方法について説明しました。

> [!div class="nextstepaction"]
> [SMS イベントをサブスクライブする](./handle-sms-events.md)

> [!div class="nextstepaction"]
> [電話番号の種類](../../concepts/telephony-sms/plan-solution.md)

> [!div class="nextstepaction"]
> [SMS に関する詳細](../../concepts/telephony-sms/concepts.md)
